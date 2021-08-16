---
title: 使用 Helm 在 Azure Kubernetes 服务 (AKS) 上进行开发
description: 结合使用 Helm 与 AKS 和 Azure 容器注册表，打包和运行群集中的应用程序容器。
services: container-service
author: zr-msft
ms.topic: article
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 248b91be60f4da3ce7dd10212a9db69377651ccb
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110071535"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>快速入门：使用 Helm 在 Azure Kubernetes 服务 (AKS) 上进行开发

[Helm][helm] 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。 与 Linux 包管理器（如 APT 和 Yum）类似，Helm 管理 Kubernetes chart，这些 chart 是预配置的 Kubernetes 资源的包 。

在本快速入门中，你将使用 Helm 在 AKS 上打包并运行应用程序。 若要更详细地了解如何使用 Helm 来安装现有的应用程序，请参阅[在 AKS 中通过 Helm 安装现有应用程序][helm-existing]操作指南。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
* [已安装 Azure CLI](/cli/azure/install-azure-cli)。
* [已安装 Helm v3][helm-install]。

## <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表
需要将容器映像存储在 Azure 容器注册表 (ACR)，才能使用 Helm 在 AKS 群集中运行应用程序。 请提供你自己的注册表名称，这个名称在 Azure 中独一无二，包含 5-50 个字母数字字符。 “基本”SKU 是一个针对成本优化的入口点，适用于可以对存储和吞吐量进行均衡考虑的开发目的。

以下示例使用 [az acr create][az-acr-create] 在使用“基本”SKU 的 MyResourceGroup 中创建名为“MyHelmACR”的 ACR  。

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

输出将会类似于以下示例。 请记下 ACR 的 loginServer 值，因为稍后的步骤会用到它。 在以下示例中，myhelmacr.azurecr.io 是 MyHelmACR 的 loginServer  。

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

新 AKS 群集需要访问 ACR 来拉取并运行容器映像。 使用以下命令：
* 创建名为“MyAKS”的 AKS 群集并附加 MyHelmACR 。
* 授予 MyAKS 群集对 MyHelmACR ACR 的访问权限 。


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>连接到 AKS 群集

若要在本地连接 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl][kubectl]。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 

1. 在本地使用 `az aks install-cli` 命令安装 `kubectl`：

    ```azurecli
    az aks install-cli
    ```

2. 使用 `az aks get-credentials` 命令将 `kubectl` 配置为连接到你的 Kubernetes 群集。 以下命令示例获取 MyResourceGroup 中名为“MyAKS”的 AKS 群集的凭据 ：  

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>下载示例应用程序

本快速入门使用[一个示例 Node.js 应用程序][example-nodejs]。 从 GitHub 克隆该应用程序，然后导航到 `dev-spaces/samples/nodejs/getting-started/webfrontend` 目录。

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>创建 Dockerfile

使用以下命令创建新的 Dockerfile 文件：

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>生成并将示例应用程序推送到 ACR

使用前面的 Dockerfile，运行 [az acr build][az-acr-build] 命令来生成映像并将该映像推送到注册表。 命令末尾处的 `.` 设置 Dockerfile 的位置（在本例中为当前目录）。

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>创建 Helm 图表

使用 `helm create` 命令生成 Helm 图表。

```console
helm create webfrontend
```

更新 webfrontend/values.yaml：
* 替换前面步骤中记下的注册表的 loginServer，如 myhelmacr.azurecr.io。
* 将 `image.repository` 更改为 `<loginServer>/webfrontend`
* 将 `service.type` 更改为 `LoadBalancer`

例如：

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: myhelmacr.azurecr.io/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

在 webfrontend/Chart.yaml 中将 `appVersion` 更新为 `v1`。 例如

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>运行 Helm 图表

通过 `helm install` 命令使用 Helm chart 来安装应用程序。

```console
helm install webfrontend webfrontend/
```

服务可能需要几分钟才能返回公共 IP 地址。 使用带有 `--watch` 参数的 `kubectl get service` 命令来监视进度。

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

使用 `<EXTERNAL-IP>` 在浏览器中导航到应用程序的负载均衡器，以查看示例应用程序。

## <a name="delete-the-cluster"></a>删除群集

使用 [az group delete][az-group-delete] 命令来删除资源组、AKS 群集、容器注册表和 ACR 中存储的容器映像，以及所有相关资源。

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> 删除群集时，AKS 群集使用的 Azure Active Directory 服务主体不会被删除。 有关如何删除服务主体的步骤，请参阅 [AKS 服务主体的注意事项和删除][sp-delete]。
> 
> 如果你使用了托管标识，则该标识由平台托管，不需要删除。

## <a name="next-steps"></a>后续步骤

有关使用 Helm 的详细信息，请参阅 Helm 文档。

> [!div class="nextstepaction"]
> [Helm 文档][helm-documentation]

[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-group-delete]: /cli/azure/group#az_group_delete
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
