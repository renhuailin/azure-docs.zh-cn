---
title: 内部网络上的入口控制器
titleSuffix: Azure Kubernetes Service
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中安装和配置适用于内部专用网络的 NGINX 入口控制器。
services: container-service
ms.topic: article
ms.date: 04/23/2021
ms.openlocfilehash: b95ae084fa3dd5b2d5308dece2612ab9cbe99a82
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099073"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中创建内部虚拟网络的入口控制器

入口控制器是一个软件片段，为 Kubernetes 服务提供反向代理、可配置的流量路由和 TLS 终止。 Kubernetes 入口资源用于配置各个 Kubernetes 服务的入口规则和路由。 借助入口控制器和入口规则，可以使用单个 IP 地址将流量路由到 Kubernetes 群集中的多个服务。

本文介绍如何在 Azure Kubernetes 服务 (AKS) 群集中部署 [NGINX 入口控制器][nginx-ingress]。 入口控制器在内部专用虚拟网络和 IP 地址上配置。 不允许外部访问。 然后在 AKS 群集中运行两个应用程序（可通过单个 IP 地址访问其中的每个应用程序）。

也可执行以下操作：

- [创建具有外部网络连接的基本入口控制器][aks-ingress-basic]
- [启用 HTTP 应用程序路由附加产品][aks-http-app-routing]
- [创建使用你自己的 TLS 证书的入口控制器][aks-ingress-own-tls]
- 创建一个使用 Let's Encrypt 的入口控制器，以自动生成[具有动态公共 IP 地址][aks-ingress-tls]或[具有静态公共 IP 地址][aks-ingress-static-tls]的 TLS 证书

## <a name="before-you-begin"></a>准备阶段

本文使用 [Helm 3][helm] 在[支持的 Kubernetes 版本][aks-supported versions]上安装 NGINX 入口控制器。 确保使用最新版本的 Helm，并且有权访问 ingress-nginx Helm 存储库。 本文中概述的步骤可能与 Helm chart、NGINX 入口控制器或 Kubernetes 的先前版本不兼容。 有关配置和使用 Helm 的详细信息，请参阅[在 Azure Kubernetes 服务 (AKS) 中使用 Helm 安装应用程序][use-helm]。

本文还要求运行 Azure CLI 2.0.64 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

此外，本文假设你有一个包含[集成 ACR][aks-integrated-acr] 的现有 AKS 群集。

## <a name="import-the-images-used-by-the-helm-chart-into-your-acr"></a>将 Helm 图表使用的映像导入 ACR

通常，在结合使用 AKS 群集与专用网络时，需要管理群集内所使用的容器映像源。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 中容器映像管理和安全性的最佳做法][aks-container-best-practices]。  为了支持此要求并确保完整性，本文中的示例依赖于将 [NGINX 入口控制器 Helm 图表][ingress-nginx-helm-chart]所使用的三个容器映像导入 ACR。

使用 `az acr import` 将这些映像导入 ACR。

```azurecli
REGISTRY_NAME=<REGISTRY_NAME>
CONTROLLER_REGISTRY=k8s.gcr.io
CONTROLLER_IMAGE=ingress-nginx/controller
CONTROLLER_TAG=v0.48.1
PATCH_REGISTRY=docker.io
PATCH_IMAGE=jettech/kube-webhook-certgen
PATCH_TAG=v1.5.1
DEFAULTBACKEND_REGISTRY=k8s.gcr.io
DEFAULTBACKEND_IMAGE=defaultbackend-amd64
DEFAULTBACKEND_TAG=1.5

az acr import --name $REGISTRY_NAME --source $CONTROLLER_REGISTRY/$CONTROLLER_IMAGE:$CONTROLLER_TAG --image $CONTROLLER_IMAGE:$CONTROLLER_TAG
az acr import --name $REGISTRY_NAME --source $PATCH_REGISTRY/$PATCH_IMAGE:$PATCH_TAG --image $PATCH_IMAGE:$PATCH_TAG
az acr import --name $REGISTRY_NAME --source $DEFAULTBACKEND_REGISTRY/$DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG --image $DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG
```

> [!NOTE]
> 除了将容器映像导入 ACR 之外，还可以将 Helm 图表导入 ACR。 有关详细信息，请参阅[将 Helm 图表推送和拉取到 Azure 容器注册表][acr-helm]。

## <a name="create-an-ingress-controller"></a>创建入口控制器

默认情况下，NGINX 入口控制器通过动态公共 IP 地址分配创建。 常见的配置要求是使用内部专用网络和 IP 地址。 此方法仅限内部用户访问你的服务，不允许外部访问。

根据以下示例清单文件创建名为 *internal-ingress.yaml* 的文件。 此示例将 *10.240.0.42* 分配给 *loadBalancerIP* 资源。 提供自己的可以与入口控制器配合使用的内部 IP 地址。 请确保该 IP 地址尚未在虚拟网络中使用。 此外，如果你使用的是现有虚拟网络和子网，则必须使用正确的权限配置 AKS 群集，以管理虚拟网络和子网。 有关详细信息，请参阅[在 Azure Kubernetes 服务 (AKS) 中结合自己的 IP 地址范围使用 kubenet 网络][aks-configure-kubenet-networking]或[在 Azure Kubernetes 服务 (AKS) 中配置 Azure CNI 网络][aks-configure-advanced-networking]。

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

现在，通过 Helm 部署 *nginx-ingress* 图表。 若要使用在上一步创建的清单文件，请添加 `-f internal-ingress.yaml` 参数。 对于增加的冗余，NGINX 入口控制器的两个副本会在部署时具备 `--set controller.replicaCount` 参数。 若要充分利用正在运行的入口控制器副本，请确保 AKS 群集中有多个节点。

还需要在 Linux 节点上计划入口控制器。 Windows Server 节点不应运行入口控制器。 使用 `--set nodeSelector` 参数指定节点选择器，以告知 Kubernetes 计划程序在基于 Linux 的节点上运行 NGINX 入口控制器。

> [!TIP]
> 以下示例为名为 ingress-basic 的入口资源创建 Kubernetes 命名空间，目的是在该命名空间内执行操作。 根据需要为你自己的环境指定一个命名空间。 如果 AKS 群集未启用 Kubernetes RBAC，请将 `--set rbac.create=false` 添加到 Helm 命令中。

> [!TIP]
> 若要为对群集中容器的请求启用[客户端源 IP 保留][client-source-ip]，请将 `--set controller.service.externalTrafficPolicy=Local` 添加到 Helm install 命令中。 客户端源 IP 存储在 X-Forwarded-For 下的请求头中。 使用启用了“客户端源 IP 保留”的入口控制器时，TLS 直通将不起作用。

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Set variable for ACR location to use for pulling images
ACR_URL=<REGISTRY_URL>

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.image.registry=$ACR_URL \
    --set controller.image.image=$CONTROLLER_IMAGE \
    --set controller.image.tag=$CONTROLLER_TAG \
    --set controller.image.digest="" \
    --set controller.admissionWebhooks.patch.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.image.registry=$ACR_URL \
    --set controller.admissionWebhooks.patch.image.image=$PATCH_IMAGE \
    --set controller.admissionWebhooks.patch.image.tag=$PATCH_TAG \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set defaultBackend.image.registry=$ACR_URL \
    --set defaultBackend.image.image=$DEFAULTBACKEND_IMAGE \
    --set defaultBackend.image.tag=$DEFAULTBACKEND_TAG
```

为 NGINX 入口控制器创建 Kubernetes 负载均衡器服务时，系统会分配内部 IP 地址。 若要获取公共 IP 地址，请使用 `kubectl get service` 命令。

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

将 IP 地址分配给服务需要几分钟时间，如以下示例输出中所示：

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

由于尚未创建入口规则，如果浏览到该内部 IP 地址，则会显示 NGINX 入口控制器的默认 404 页面。 入口规则是通过以下步骤配置的。

## <a name="run-demo-applications"></a>运行演示应用程序

若要查看运行中的入口控制器，请在 AKS 群集中运行两个演示应用程序。 此示例使用 `kubectl apply` 来部署一个简单“Hello world”应用程序的两个实例。

创建“aks-helloworld.yaml”文件，并将其复制到以下示例 YAML 中：

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

创建“ingress-demo.yaml”文件，并将其复制到以下示例 YAML 中：

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

使用 `kubectl apply` 运行这两个演示应用程序：

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>创建入口路由

两个应用程序现在都在 Kubernetes 群集中运行。 若要将流量路由到每个应用程序，请创建 Kubernetes 入口资源。 该入口资源配置将流量路由到这两个应用程序之一的规则。

在以下示例中，传往地址 `http://10.240.0.42/` 的流量将路由到名为 `aks-helloworld` 的服务。 传往地址 `http://10.240.0.42/hello-world-two` 的流量将路由到 `ingress-demo` 服务。

创建名为 `hello-world-ingress.yaml` 的文件，并将其复制到以下示例 YAML 中。

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - path: /hello-world-one(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld
            port:
              number: 80
      - path: /hello-world-two(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: ingress-demo
            port:
              number: 80
      - path: /(.*)
        pathType: Prefix
        backend:
          service:
            name: aks-helloworld
            port:
              number: 80
```

使用 `kubectl apply -f hello-world-ingress.yaml` 命令创建入口资源。

```console
kubectl apply -f hello-world-ingress.yaml
```

以下示例输出显示创建了入口资源。

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>测试入口控制器

若要测试入口控制器的路由，请通过一个 Web 客户端浏览到两个应用程序。 可以根据需要通过 AKS 群集上的 Pod 快速测试这项仅限内部使用的功能。 创建一个测试 Pod，并在其上附加一个终端会话：

```console
kubectl run -it --rm aks-ingress-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --namespace ingress-basic
```

使用 `apt-get` 在 Pod 中安装 `curl`：

```console
apt-get update && apt-get install -y curl
```

现在，请使用 `curl` 访问 Kubernetes 入口控制器的地址，例如 *http://10.240.0.42* 。 提供自己的内部 IP 地址，该地址是在本文第一步中部署入口控制器时指定的。

```console
curl -L http://10.240.0.42
```

未为此地址提供其他路径，因此入口控制器默认为 */* 路由。 第一个演示应用程序已返回，如以下精简版示例输出中所示：

```
$ curl -L http://10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

现在向地址添加 */hello-world-two* 路径，例如 *http://10.240.0.42/hello-world-two* 。 第二个使用自定义标题的演示应用程序已返回，如以下精简版示例输出中所示：

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>清理资源

本文使用了 Helm 来安装入口组件。 在部署 Helm 图表时，会创建若干 Kubernetes 资源。 这些资源包括 pod、部署和服务。 若要清理这些资源，可以删除整个示例命名空间，也可以删除单个资源。

### <a name="delete-the-sample-namespace-and-all-resources"></a>删除示例命名空间以及所有资源

若要删除整个示例命名空间，请使用 `kubectl delete` 命令并指定命名空间名称。 将会删除命名空间中的所有资源。

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>单独删除资源

也可采用更细致的方法来删除单个已创建的资源。 使用 `helm list` 命令列出 Helm 版本。 

```console
helm list --namespace ingress-basic
```

查找名为“nginx-ingress”和“aks-helloworld”的图表，如以下示例输出中所示：

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

使用 `helm uninstall` 命令卸载这些版本。

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

下面的示例将卸载 NGINX 入口部署。

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

接下来，删除两个示例应用程序：

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

删除将流量定向到示例应用的入口路由：

```console
kubectl delete -f hello-world-ingress.yaml
```

最后，可以删除自身命名空间。 使用 `kubectl delete` 命令并指定命名空间名称。

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>后续步骤

本文包含 AKS 的一些外部组件。 若要详细了解这些组件，请参阅以下项目页面：

- [Helm CLI][helm-cli]
- [NGINX 入口控制器][nginx-ingress]

也可执行以下操作：

- [创建具有外部网络连接的基本入口控制器][aks-ingress-basic]
- [启用 HTTP 应用程序路由附加产品][aks-http-app-routing]
- [使用动态公共 IP 创建入口控制器并配置 Let 's Encrypt 以自动生成 TLS 证书][aks-ingress-tls]
- [使用静态公共 IP 地址创建入口控制器并配置 Let 's Encrypt 以自动生成 TLS 证书][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-supported versions]: supported-kubernetes-versions.md
[ingress-nginx-helm-chart]: https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx
[aks-integrated-acr]: cluster-container-registry-integration.md?tabs=azure-cli#create-a-new-aks-cluster-with-acr-integration
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-container-best-practices]: operator-best-practices-container-image-management.md
