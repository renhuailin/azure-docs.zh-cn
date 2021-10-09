---
title: 使用 Azure 应用程序网关入口
description: 如何将 Azure 应用程序网关入口与 Open Service Mesh 一起使用
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 765eb53098f757d29072d736d50086f31bb11dc3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649744"
---
# <a name="deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>使用 Azure 应用程序网关入口 AKS 加载项部署由 Open Service Mesh (OSM) 管理的应用程序

在本教程中，将：

> [!div class="checklist"]
>
> - 查看当前 OSM 群集配置
> - 为 OSM 创建命名空间，以在命名空间中管理已部署的应用程序
> - 加入要由 OSM 管理的命名空间
> - 部署示例应用程序
> - 验证应用程序是否正在 AKS 群集中运行
> - 创建要用作应用程序的入口控制器的 Azure 应用程序网关
> - 通过 Azure 应用程序网关入口在 Internet 上公开服务

## <a name="before-you-begin"></a>开始之前

本演练中详细介绍的步骤假定你之前已为 AKS 群集启用了 OSM AKS 附加产品。 如果未启用，请在继续操作前查看[部署 OSM AKS 加载项](./open-service-mesh-deploy-addon-az-cli.md)一文。 此外，你的 AKS 群集必须是 Kubernetes `1.19+` 和更高版本，已启用 Kubernetes RBAC，与该群集建立了 `kubectl` 连接（如果需要获取有关任意这些项的帮助，请参阅 [AKS 快速入门](./kubernetes-walkthrough.md)），并且安装了 AKS OSM 附加产品。

必须已安装以下资源：

- Azure CLI 2.20.0 或更高版本
- `aks-preview` 扩展 0.5.5 或更高版本
- OSM 版本 v0.8.0 或更高版本
- JSON 处理器“jq”版本 1.6+

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="view-and-verify-the-current-osm-cluster-configuration"></a>查看并验证当前 OSM 群集配置

在 AKS 群集上启用适用于 AKS 的 OSM 加载项后，可以在 osm-mesh-config 资源中查看当前的配置参数。 运行以下命令，查看属性：

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

输出将显示群集的当前 OSM MeshConfig。

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

请注意 enablePermissiveTrafficPolicyMode 配置为“true” 。 在 OSM 中的宽松流量策略模式下，系统会绕过 [SMI](https://smi-spec.io/) 流量策略强制执行。 在此模式下，OSM 会自动发现属于服务网格一部分的服务，并在每个 Envoy 代理挎斗上对流量策略规则进行编程，以便能够与这些服务通信。

## <a name="create-namespaces-for-the-application"></a>获取应用程序的命名空间

在本教程中，我们将使用具有以下应用程序组件的 OSM bookstore 应用程序：

- `bookbuyer`
- `bookthief`
- `bookstore`
- `bookwarehouse`

为每个应用程序组件创建命名空间。

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

应该会看到以下输出：

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

## <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>加入要由 OSM 管理的命名空间

将命名空间添加到 OSM 网格后，OSM 控制器将能自动将 Envoy 挎斗代理容器与应用程序一起注入。 运行以下命令，加入 OSM bookstore 应用程序命名空间。

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

## <a name="deploy-the-bookstore-application"></a>部署 Bookstore 应用程序

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookwarehouse.yaml
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

## <a name="update-the-bookbuyer-service"></a>更新 `Bookbuyer` 服务

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

## <a name="verify-the-bookstore-application"></a>验证 Bookstore 应用程序

到目前为止，我们部署了 bookstore 多容器应用程序，但只能从 AKS 群集中访问它。 稍后我们将添加 Azure 应用程序网关入口控制器，以在 AKS 群集外公开应用程序。 为了验证此应用程序是否正在群集中运行，我们将使用端口转发来查看 `bookbuyer` 组件 UI。

首先，让我们获取 `bookbuyer` Pod 的名称

```azurecli-interactive
kubectl get pod -n bookbuyer
```

应该会看到与下面类似的输出。 `bookbuyer` Pod 会附加一个唯一名称。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

获取 Pod 的名称后，现在可以使用 port-forward 命令来设置从本地系统到 AKS 群集中的应用程序的隧道。 运行以下命令，为本地系统端口 8080 设置端口转发。 再次使用特定 `bookbuyer` Pod 名称。

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

应该会看到与下面类似的输出。

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

当端口转发会话就绪时，从浏览器导航到以下 URL：`http://localhost:8080`。 现在，你应该可以在浏览器中看到 `bookbuyer` 应用程序 UI，如下图所示。

![用于应用程序网关 的 OSM bookbuyer 应用程序的 UI 图像](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

## <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application"></a>创建 Azure 应用程序网关，以公开 `bookbuyer` 应用程序

> [!NOTE]
> 按照以下说明创建用于入口的 Azure 应用程序网关的新实例。 如果要使用现有 Azure 应用程序网关，请跳到有关启用应用程序网关入口控制器附加产品一节。

### <a name="deploy-a-new-application-gateway"></a>部署新的应用程序网关

> [!NOTE]
> 我们将参考现有文档来为现有 AKS 群集启用应用程序网关入口控制器附加产品。 我们已根据 OSM 材料对现有文档进行了一些修改。 可在[此处](../application-gateway/tutorial-ingress-controller-add-on-existing.md)找到有关该主题的更详细文档。

现在，你将部署新的应用程序网关，以模拟你有一个现有的应用程序网关且需要使用它对发往 AKS 群集 _myCluster_ 的流量进行负载均衡的情况。 应用程序网关的名称将是 _myApplicationGateway_，但你需要首先创建一个名为 _myPublicIp_ 的公共 IP 资源、一个名为 _myVnet_ 且地址空间为 11.0.0.0/8 的新虚拟网络、一个名为 _mySubnet_ 且地址空间为 11.1.0.0/16 的子网，然后使用 _myPublicIp_ 在 _mySubnet_ 中部署你的应用程序网关。

在不同虚拟网络中使用 AKS 群集和应用程序网关时，两个虚拟网络的地址空间不得重叠。 AKS 群集在其中进行部署的默认地址空间为 10.0.0.0/8，因此，我们将应用程序网关虚拟网络地址前缀设置为 11.0.0.0/8。

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> 应用程序网关入口控制器 (AGIC) 加载项 **仅** 支持应用程序网关 v2 SKU（标准版和 WAF 版），**不** 支持应用程序网关 v1 SKU。

### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>通过 Azure CLI 为现有 AKS 群集启用 AGIC 附加产品

如果要继续使用 Azure CLI，可以继续在已创建的 AKS 群集“myCluster”中启用 AGIC 加载项，并指定该 AGIC 加载项使用已创建的现有应用程序网关“myApplicationGateway” 。

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

可以通过以下命令验证 Azure 应用程序网关 AKS 附加产品是否已启用。

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

此命令的输出应显示 `true`。

### <a name="peer-the-two-virtual-networks-together"></a>将两个虚拟网络对等互连在一起

由于我们已将 AKS 群集部署在其自己的虚拟网络中，并将应用程序网关部署在另一个虚拟网络中，因此你需要将这两个虚拟网络对等互连在一起，以便流量从应用程序网关流向群集中的 Pod。 将两个虚拟网络对等互连需要两次运行 Azure CLI 命令，以确保连接是双向的。 第一个命令将创建从应用程序网关虚拟网络到 AKS 虚拟网络的对等连接；第二个命令将创建另一个方向的对等连接。

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="expose-the-bookbuyer-service-to-the-internet"></a>向 Internet 公开 `bookbuyer` 服务

将以下入口清单应用到 AKS 群集，通过 Azure 应用程序网关在 Internet 上公开 `bookbuyer` 服务。

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

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

你应会看到以下输出

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

由于入口清单中的主机名是用于测试的伪名称，因此 DNS 名称在 Internet 上将不可用。 或者，我们可以使用 curl 程序，将主机名标头传递到 Azure 应用程序网关公共 IP 地址并接收 200 代码，以确认我们已成功连接到 `bookbuyer` 服务。

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

你应会看到以下输出

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
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

## <a name="troubleshooting"></a>疑难解答

- [AGIC 故障排除文档](../application-gateway/ingress-controller-troubleshoot.md)
- [AGIC 的 GitHub 存储库中提供了其他故障排除工具](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)
