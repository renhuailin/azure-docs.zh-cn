---
title: 通过静态 IP 使用入口控制器
titleSuffix: Azure Kubernetes Service
description: 了解如何安装和配置具有静态公共 IP 地址的 NGINX 入口控制器，该控制器使用 Let's Encrypt 在 Azure Kubernetes 服务 (AKS) 群集中自动生成 TLS 证书。
services: container-service
ms.topic: article
ms.date: 04/23/2021
ms.openlocfilehash: d56332cecc5938c8f4406f4c36ec16b9516a08b3
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323934"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中使用静态公共 IP 地址创建入口控制器

入口控制器是一个软件片段，为 Kubernetes 服务提供反向代理、可配置的流量路由和 TLS 终止。 Kubernetes 入口资源用于配置各个 Kubernetes 服务的入口规则和路由。 借助入口控制器和入口规则，可以使用单个 IP 地址将流量路由到 Kubernetes 群集中的多个服务。

本文介绍如何在 Azure Kubernetes 服务 (AKS) 群集中部署 [NGINX 入口控制器][nginx-ingress]。 入口控制器配置了一个静态公共 IP 地址。 [cert-manager][cert-manager] 项目用于自动生成和配置 [Let's Encrypt][lets-encrypt] 证书。 最后，在 AKS 群集中运行两个应用程序（可通过单个 IP 地址访问其中的每个应用程序）。

也可执行以下操作：

- [创建具有外部网络连接的基本入口控制器][aks-ingress-basic]
- [启用 HTTP 应用程序路由附加产品][aks-http-app-routing]
- [创建使用你自己的 TLS 证书的入口控制器][aks-ingress-own-tls]
- [创建一个使用 Let's Encrypt 的入口控制器，以自动生成具有动态公共 IP 地址的 TLS 证书][aks-ingress-tls]

## <a name="before-you-begin"></a>准备阶段

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

本文使用 [Helm 3][helm] 在[支持的 Kubernetes 版本][aks-supported versions]上安装 NGINX 入口控制器。 请确保使用最新版本的 Helm，并且有权访问 ingress-nginx 和 jetstack Helm 存储库 。 本文中概述的步骤可能与 Helm chart、NGINX 入口控制器或 Kubernetes 的先前版本不兼容。

有关配置和使用 Helm 的详细信息，请参阅[在 Azure Kubernetes 服务 (AKS) 中使用 Helm 安装应用程序][use-helm]。 有关升级说明，请参阅 [Helm 安装文档][helm-install]。

本文还要求运行 Azure CLI 2.0.64 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

此外，本文假设你有一个包含集成 ACR 的现有 AKS 群集。 有关创建一个包含集成 ACR 的 AKS 群集的更多详细信息，请参阅[使用 Azure 容器注册表从 Azure Kubernetes 服务进行身份验证][aks-integrated-acr]。

## <a name="import-the-images-used-by-the-helm-chart-into-your-acr"></a>将 Helm 图表使用的映像导入 ACR

本文使用 [NGINX 入口控制器 Helm 图表][ingress-nginx-helm-chart]，它依赖于三个容器映像。 使用 `az acr import` 将这些映像导入 ACR。

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
CERT_MANAGER_REGISTRY=quay.io
CERT_MANAGER_TAG=v1.3.1
CERT_MANAGER_IMAGE_CONTROLLER=jetstack/cert-manager-controller
CERT_MANAGER_IMAGE_WEBHOOK=jetstack/cert-manager-webhook
CERT_MANAGER_IMAGE_CAINJECTOR=jetstack/cert-manager-cainjector

az acr import --name $REGISTRY_NAME --source $CONTROLLER_REGISTRY/$CONTROLLER_IMAGE:$CONTROLLER_TAG --image $CONTROLLER_IMAGE:$CONTROLLER_TAG
az acr import --name $REGISTRY_NAME --source $PATCH_REGISTRY/$PATCH_IMAGE:$PATCH_TAG --image $PATCH_IMAGE:$PATCH_TAG
az acr import --name $REGISTRY_NAME --source $DEFAULTBACKEND_REGISTRY/$DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG --image $DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_CONTROLLER:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_CONTROLLER:$CERT_MANAGER_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_WEBHOOK:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_WEBHOOK:$CERT_MANAGER_TAG
az acr import --name $REGISTRY_NAME --source $CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_CAINJECTOR:$CERT_MANAGER_TAG --image $CERT_MANAGER_IMAGE_CAINJECTOR:$CERT_MANAGER_TAG
```

> [!NOTE]
> 除了将容器映像导入 ACR 之外，还可以将 Helm 图表导入 ACR。 有关详细信息，请参阅[将 Helm 图表推送和拉取到 Azure 容器注册表][acr-helm]。

## <a name="create-an-ingress-controller"></a>创建入口控制器

默认情况下，NGINX 入口控制器通过新的公共 IP 地址分配创建。 此公共 IP 地址仅对入口控制器的生命周期而言是静态的，如果删除并重新创建控制器，它将会丢失。 常见的配置要求是为 NGINX 入口控制器提供现有的静态公共 IP 地址。 如果删除入口控制器，静态公共 IP 地址仍存在。 此方法允许在应用程序的整个生命周期中以一致的方式使用现有的 DNS 记录和网络配置。

如果需要创建静态公共 IP 地址，请首先使用 [az aks show][az-aks-show] 命令获取 AKS 群集的资源组名称：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

接下来，使用 [az network public-ip create][az-network-public-ip-create] 命令通过静态分配方法创建一个公共 IP 地址。 以下示例在前面的步骤中获取的 AKS 群集资源组中创建名为 *myAKSPublicIP* 的公共 IP 地址：

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

> [!NOTE]
> 以上命令创建一个 IP 地址，如果你删除了 AKS 群集，该 IP 地址会被删除。 或者，你可以在不同的资源组中创建一个 IP 地址，该资源组可以与你的 AKS 群集分开管理。 如果在不同的资源组中创建 IP 地址，请确保 AKS 群集使用的群集标识已将权限委托给其他资源组，例如“网络参与者”。 有关详细信息，请参阅[将静态公共 IP 地址和 DNS 标签用于 AKS 负载均衡器][aks-static-ip]。

现在，通过 Helm 部署 *nginx-ingress* 图表。 对于增加的冗余，NGINX 入口控制器的两个副本会在部署时具备 `--set controller.replicaCount` 参数。 若要充分利用正在运行的入口控制器副本，请确保 AKS 群集中有多个节点。

### <a name="ip-and-dns-label"></a>IP 和 DNS 标签
必须将另外两个参数传递给 Helm 版本，以便入口控制器知道要分配给入口控制器服务的负载均衡器的静态 IP 地址，以及应用到公共 IP 地址资源的 DNS 名称标签的静态 IP 地址。 为使 HTTPS 证书正常工作，使用 DNS 名称标签来配置用于入口控制器 IP 地址的 FQDN。

1. 添加 `--set controller.service.loadBalancerIP` 参数。 指定在前面的步骤中创建的你自己的公共 IP 地址。
1. 添加 `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` 参数。 指定要应用于在前面的步骤中创建的公共 IP 地址的 DNS 名称标签。  此标签将创建表单 `<LABEL>.<AZURE REGION NAME>.cloudapp.azure.com` 的 DNS 名称

还需要在 Linux 节点上计划入口控制器。 Windows Server 节点不应运行入口控制器。 使用 `--set nodeSelector` 参数指定节点选择器，以告知 Kubernetes 计划程序在基于 Linux 的节点上运行 NGINX 入口控制器。

> [!TIP]
> 以下示例为名为 ingress-basic 的入口资源创建 Kubernetes 命名空间，目的是在该命名空间内执行操作。 根据需要为你自己的环境指定一个命名空间。 如果 AKS 群集未启用 Kubernetes RBAC，请将 `--set rbac.create=false` 添加到 Helm 命令中。

> [!TIP]
> 若要为对群集中容器的请求启用[客户端源 IP 保留][client-source-ip]，请将 `--set controller.service.externalTrafficPolicy=Local` 添加到 Helm install 命令中。 客户端源 IP 存储在 X-Forwarded-For 下的请求头中。 使用启用了“客户端源 IP 保留”的入口控制器时，TLS 直通将不起作用。

使用入口控制器的 IP 地址以及要用于 FQDN 前缀的唯一名称来更新以下脚本 。

> [!IMPORTANT]
> 运行命令时，必须将 `<STATIC_IP>` 和 `<DNS_LABEL>` 更新替换为自己的 IP 地址和唯一名称。  DNS_LABEL 在 Azure 区域中必须具有唯一性。

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Set variable for ACR location to use for pulling images
ACR_URL=<REGISTRY_URL>
STATIC_IP=<STATIC_IP>
DNS_LABEL=<DNS_LABEL>

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
    --set defaultBackend.image.tag=$DEFAULTBACKEND_TAG \
    --set controller.service.loadBalancerIP=$STATIC_IP \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"=$DNS_LABEL
```

为 NGINX 入口控制器创建 Kubernetes 负载均衡器服务时，会分配你的静态 IP 地址，如以下示例输出中所示：

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

由于尚未创建入口规则，如果浏览到该公共 IP 地址，则会显示 NGINX 入口控制器的默认 404 页面。 入口规则是通过以下步骤配置的。

你可以通过查询公共 IP 地址上的 FQDN 来验证是否已应用 DNS 名称标签，如下所示：

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?name=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

现在可以通过 IP 地址或 FQDN 访问入口控制器。

## <a name="install-cert-manager"></a>安装证书管理器

NGINX 入口控制器支持 TLS 终止。 可通过多种方法为 HTTPS 检索和配置证书。 本文演示如何使用[证书管理器][cert-manager]，该管理器提供自动 [Lets Encrypt][lets-encrypt] 证书生成和管理功能。

> [!NOTE]
> 本文为 Let's Encrypt 使用 `staging` 环境。 在生产部署中，请在安装 Helm 图表时使用资源定义中的 `letsencrypt-prod` 和 `https://acme-v02.api.letsencrypt.org/directory`。

若要在启用了 Kubernetes RBAC 的群集中安装 cert-manager 控制器，请使用以下 `helm install` 命令：

```console
# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install cert-manager jetstack/cert-manager \
  --namespace ingress-basic \
  --version $CERT_MANAGER_TAG \
  --set installCRDs=true \
  --set nodeSelector."kubernetes\.io/os"=linux \
  --set image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_CONTROLLER \
  --set image.tag=$CERT_MANAGER_TAG \
  --set webhook.image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_WEBHOOK \
  --set webhook.image.tag=$CERT_MANAGER_TAG \
  --set cainjector.image.repository=$ACR_URL/$CERT_MANAGER_IMAGE_CAINJECTOR \
  --set cainjector.image.tag=$CERT_MANAGER_TAG 
```

若要详细了解证书管理器配置，请参阅[证书管理器项目][cert-manager]。

## <a name="create-a-ca-cluster-issuer"></a>创建 CA 群集证书颁发者

证书管理器需要 [Issuer][cert-manager-issuer] 或 [ClusterIssuer][cert-manager-cluster-issuer] 资源，才能颁发证书。 这两种 Kubernetes 资源的功能完全相同，区别在于 `Issuer` 适用于单一命名空间，而 `ClusterIssuer` 适用于所有命名空间。 有关详细信息，请参阅[证书管理器颁发者][cert-manager-issuer]文档。

使用以下示例清单创建群集证书颁发者，例如 `cluster-issuer.yaml`。 将电子邮件地址更新为组织提供的有效地址：

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

若要创建证书颁发者，请使用 `kubectl apply` 命令。

```
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

输出应类似于以下示例：

```
clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>运行演示应用程序

入口控制器和证书管理解决方案已配置完毕。 现在让我们在你的 AKS 群集中运行两个演示应用程序。 此示例使用 Helm 来部署一个简单“Hello world”应用程序的两个实例。

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

两个应用程序现在都在 Kubernetes 群集中运行，但它们配置了服务类型 `ClusterIP`。 因此，无法通过 Internet 访问它们。 若要公开发布这两个应用程序，请创建 Kubernetes 入口资源。 该入口资源配置将流量路由到这两个应用程序之一的规则。

在以下示例中，传往地址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/` 的流量将路由到名为 `aks-helloworld` 的服务。 传往地址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` 的流量将路由到 `ingress-demo` 服务。 将 *hosts* 和 *host* 更新为在前面步骤中创建的 DNS 名称。

创建名为 `hello-world-ingress.yaml` 的文件，并将其复制到以下示例 YAML 中。

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
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

使用 `kubectl apply` 命令创建入口资源。

```
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

输出应类似于以下示例：

```
ingress.extensions/hello-world-ingress created
```

## <a name="verify-certificate-object"></a>验证证书对象

接下来，必须创建证书资源。 证书资源定义了必需的 X.509 证书。 有关详细信息，请参阅[证书管理器证书][cert-manager-certificates]。

证书管理器可能已使用 ingress-shim（自 v0.2.2 以来随证书管理器自动部署）为你自动创建了证书对象。 有关详细信息，请参阅 [ingress-shim 文档][ingress-shim]。

若要验证证书是否已成功创建，请使用 `kubectl describe certificate tls-secret --namespace ingress-basic` 命令。

如果颁发了证书，你将看到如下输出：
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

## <a name="test-the-ingress-configuration"></a>测试入口配置

打开 Web 浏览器，访问 Kubernetes 入口控制器的 FQDN，例如 *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* 。

由于这些示例使用 `letsencrypt-staging`，浏览器不信任颁发的 TLS/SSL 证书。 接受警告提示以继续运行应用程序。 证书信息显示这个 *伪 LE 中间 X1* 证书是由 Let's Encrypt 颁发的。 此伪证书指出 `cert-manager` 正确处理了请求并接收了提供程序提供的证书：

![Let's Encrypt 临时证书](media/ingress/staging-certificate.png)

如果将 Let's Encrypt 更改为使用 `prod` 而不是 `staging`，则会使用由 Let's Encrypt 颁发的可信证书，如以下示例所示：

![Let's Encrypt 证书](media/ingress/certificate.png)

演示应用程序显示在 Web 浏览器中：

![应用程序示例 1](media/ingress/app-one.png)

现在向 FQDN 添加 */hello-world-two* 路径，例如 *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`* 。 下面显示了带自定义标题的第二个演示应用程序：

![应用程序示例 2](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>清理资源

本文使用 Helm 来安装入口组件、证书和示例应用。 在部署 Helm 图表时，会创建若干 Kubernetes 资源。 这些资源包括 pod、部署和服务。 若要清理这些资源，可以删除整个示例命名空间，也可以删除单个资源。

### <a name="delete-the-sample-namespace-and-all-resources"></a>删除示例命名空间以及所有资源

若要删除整个示例命名空间，请使用 `kubectl delete` 命令并指定命名空间名称。 将会删除命名空间中的所有资源。

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>单独删除资源

也可采用更细致的方法来删除单个已创建的资源。 首先，请删除证书资源：

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

现在使用 `helm list` 命令列出 Helm 版本。 查找名为“nginx-ingress”和“cert-manager”的图表，如以下示例输出中所示 ：

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

使用 `helm uninstall` 命令卸载这些版本。 下面的示例将卸载 NGINX 入口部署和证书管理器部署。

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

接下来，删除两个示例应用程序：

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

删除命名空间自身。 使用 `kubectl delete` 命令并指定命名空间名称。

```console
kubectl delete namespace ingress-basic
```

最后，删除为入口控制器创建的静态公共 IP 地址。 提供在本文第一步中获取的 MC_ 群集资源组名，例如 MC_myResourceGroup_myAKSCluster_eastus：

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>后续步骤

本文包含 AKS 的一些外部组件。 若要详细了解这些组件，请参阅以下项目页面：

- [Helm CLI][helm-cli]
- [NGINX 入口控制器][nginx-ingress]
- [证书管理器][cert-manager]

也可执行以下操作：

- [创建具有外部网络连接的基本入口控制器][aks-ingress-basic]
- [启用 HTTP 应用程序路由附加产品][aks-http-app-routing]
- [创建使用内部、专用网络和 IP 地址的入口控制器][aks-ingress-internal]
- [创建使用你自己的 TLS 证书的入口控制器][aks-ingress-own-tls]
- [使用动态公共 IP 创建入口控制器并配置 Let 's Encrypt 以自动生成 TLS 证书][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[ingress-nginx-helm-chart]: https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-static-ip]: static-ip.md
[aks-supported versions]: supported-kubernetes-versions.md
[aks-integrated-acr]: cluster-container-registry-integration.md?tabs=azure-cli#create-a-new-aks-cluster-with-acr-integration
[acr-helm]: ../container-registry/container-registry-helm-repos.md