---
title: 使用 NGINX 入口
description: 如何将 NGINX 入口与开放服务网格一同使用
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: c06e6fe787070adbe0817e295380b2a2ef2775b5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439823"
---
# <a name="deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>使用 NGINX 入口部署由 Open Service Mesh (OSM) 管理的应用程序

Open Service Mesh (OSM) 是一种轻型、可扩展的云原生服务网格，可让用户统一地管理、保护和获取高度动态微服务环境现成的可观测性功能。

在本教程中，将：

> [!div class="checklist"]
>
> - 查看当前 OSM 群集配置
> - 为 OSM 创建命名空间，以在命名空间中管理已部署的应用程序
> - 加入要由 OSM 管理的命名空间
> - 部署示例应用程序
> - 验证应用程序是否正在 AKS 群集中运行
> - 创建用于应用程序的 NGINX 入口控制器
> - 通过 Azure 应用程序网关入口在 Internet 上公开服务

## <a name="before-you-begin"></a>开始之前

本文中详细介绍的步骤假定你已创建 AKS 群集（Kubernetes `1.19+` 和更高版本，并且已启用 Kubernetes RBAC），与该群集建立了 `kubectl` 连接（如果需要获取有关任意这些项的帮助，请参阅 [AKS 快速入门](./kubernetes-walkthrough.md)），并且安装了 AKS OSM 附加产品。

必须已安装以下资源：

- Azure CLI 2.20.0 或更高版本
- `aks-preview` 扩展 0.5.5 或更高版本
- OSM 版本 v0.8.0 或更高版本
- JSON processor "jq" version 1.6+

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>查看并验证当前 OSM 群集配置

在 AKS 群集上启用适用于 AKS 的 OSM 附加产品后，可以在 osm-mesh-config 资源中查看当前的配置参数。 运行以下命令，查看属性：

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n osm-system -o yaml
```

输出将显示群集的当前 OSM 配置。

```
apiVersion: config.openservicemesh.io/v1alpha1
kind: MeshConfig
metadata:
  creationTimestamp: "0000-00-00A00:00:00A"
  generation: 1
  name: osm-mesh-config
  namespace: kube-system
  resourceVersion: "2494"
  uid: 6c4d67f3-c241-4aeb-bf4f-b029b08faa31
spec:
  certificate:
    serviceCertValidityDuration: 24h
  featureFlags:
    enableEgressPolicy: true
    enableMulticlusterMode: false
    enableWASMStats: true
  observability:
    enableDebugServer: true
    osmLogLevel: info
    tracing:
      address: jaeger.osm-system.svc.cluster.local
      enable: false
      endpoint: /api/v2/spans
      port: 9411
  sidecar:
    configResyncInterval: 0s
    enablePrivilegedInitContainer: false
    envoyImage: mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    initContainerImage: mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    logLevel: error
    maxDataPlaneConnections: 0
    resources: {}
  traffic:
    enableEgress: true
    enablePermissiveTrafficPolicyMode: true
    inboundExternalAuthorization:
      enable: false
      failureModeAllow: false
      statPrefix: inboundExtAuthz
      timeout: 1s
    useHTTPSIngress: false
```

请注意 enablePermissiveTrafficPolicyMode 配置为“true” 。 在 OSM 中的宽松流量策略模式下，系统会绕过 [SMI](https://smi-spec.io/) 流量策略强制执行。 在此模式下，OSM 会自动发现属于服务网格一部分的服务，并在每个 Envoy 代理挎斗上对流量策略规则进行编程，以便能够与这些服务通信。 有关宽松流量模式的更多详细信息，请访问并阅读[宽松流量策略模式](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/)一文。

## <a name="create-namespaces-for-the-application"></a>获取应用程序的命名空间

在本教程中，我们将使用具有以下应用程序组件的 OSM `bookstore` 应用程序：

- `bookbuyer`
- `bookthief`
- `bookstore`
- `bookwarehouse`

为每个应用程序组件创建命名空间。

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

应会看到以下输出：

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

## <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>加入要由 OSM 管理的命名空间

将命名空间添加到 OSM 网格后，OSM 控制器将能自动将 Envoy 挎斗代理容器与应用程序一起注入。 运行以下命令，加入 OSM `bookstore` 应用程序命名空间。

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

应会看到以下输出：

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

## <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>将 bookstore 应用程序部署到 AKS 群集

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

下面汇总了所有部署输出。

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

## <a name="update-the-bookbuyer-service"></a>更新 bookbuyer 服务

将 `bookbuyer` 服务更新为具有以下服务清单的正确入站端口配置。

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

## <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>验证 bookstore 应用程序是否正在 AKS 群集中运行

到目前为止，我们部署了 `bookstore` 多容器应用程序，但只能从 AKS 群集中访问它。 稍后我们将添加 Azure 应用程序网关入口控制器，以在 AKS 群集外公开应用程序。 为了验证此应用程序是否正在群集中运行，我们将使用端口转发来查看 `bookbuyer` 组件 UI。

首先，让我们获取 `bookbuyer` Pod 的名称

```azurecli-interactive
kubectl get pod -n bookbuyer
```

应该会看到与下面类似的输出。 `bookbuyer` Pod 会附加一个唯一名称。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

获取 Pod 的名称后，现在可以使用 port-forward 命令来设置从本地系统到 AKS 群集中的应用程序的隧道。 运行以下命令，为本地系统端口 8080 设置端口转发。 再次使用指定的 bookbuyer Pod 名称。

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

应看到类似如下的输出：

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

当端口转发会话就绪时，从浏览器导航到以下 URL：`http://localhost:8080`。 现在，你应该可以在浏览器中看到 `bookbuyer` 应用程序 UI，如下图所示。

![用于 NGINX 的 OSM bookbuyer 应用程序的 UI 图像](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

## <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中创建 NGINX 入口控制器

入口控制器是一个软件片段，为 Kubernetes 服务提供反向代理、可配置的流量路由和 TLS 终止。 Kubernetes 入口资源用于配置各个 Kubernetes 服务的入口规则和路由。 借助入口控制器和入口规则，可以使用单个 IP 地址将流量路由到 Kubernetes 群集中的多个服务。

我们将利用入口控制器在 Internet 上公开由 OSM 管理的应用程序。 若要创建入口控制器，请使用 Helm 来安装 nginx-ingress。 对于增加的冗余，NGINX 入口控制器的两个副本会在部署时具备 `--set controller.replicaCount` 参数。 若要充分利用正在运行的入口控制器副本，请确保 AKS 群集中有多个节点。

将在 Linux 节点上计划入口控制器。 Windows Server 节点不应运行入口控制器。 使用 `--set nodeSelector` 参数指定节点选择器，以告知 Kubernetes 计划程序在基于 Linux 的节点上运行 NGINX 入口控制器。

> [!TIP]
> 以下示例为名为 _ingress-basic_ 的入口资源创建 Kubernetes 命名空间。 根据需要为你自己的环境指定一个命名空间。

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

为 NGINX 入口控制器创建 Kubernetes 负载均衡器服务。 分配了动态公共 IP 地址，如以下示例输出所示：

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

尚未创建入口规则。 目前，如果浏览到内部 IP 地址，将显示 NGINX 入口控制器的默认 404 页面。 入口规则是通过以下步骤配置的。

## <a name="expose-the-bookbuyer-service-to-the-internet"></a>在 Internet 上公开 bookbuyer 服务

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

应会看到以下输出：

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

## <a name="view-the-nginx-logs"></a>查看 NGINX 日志

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

如果已成功应用入口规则，输出将显示 NGINX 入口控制器状态：

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

## <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>在外部查看 NGINX 服务和 bookbuyer 服务

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

由于入口清单中的主机名是用于测试的伪名称，因此 DNS 名称在 Internet 上将不可用。 或者，我们可以使用 curl 程序，将主机名标头传递到 NGINX 公共 IP 地址并接收 200 代码，以确认我们已成功连接到 bookbuyer 服务。

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

应会看到以下输出：

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```