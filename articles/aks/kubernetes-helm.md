---
title: 使用 Helm 在 AKS 中安装现有应用程序
description: 了解如何使用 Helm 打包工具在 Azure Kubernetes 服务 (AKS) 群集中部署容器
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: c1370182d8ca13acb3d94856df784ecea34252ae
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724715"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>使用 Helm 在 Azure Kubernetes 服务 (AKS) 中安装现有应用程序

[Helm][helm] 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。 与诸如 *APT* 和 *Yum* 的 Linux 包管理器类似，Helm 用于管理 Kubernetes 图表，这些图表是预配置的 Kubernetes 资源包。

本文介绍如何在 AKS 上的 Kubernetes 群集中配置和使用 Helm。

## <a name="before-you-begin"></a>准备阶段

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

此外，本文假设你有一个包含集成 ACR 的现有 AKS 群集。 有关创建一个包含集成 ACR 的 AKS 群集的更多详细信息，请参阅[使用 Azure 容器注册表从 Azure Kubernetes 服务进行身份验证][aks-integrated-acr]。

还需要安装 Helm CLI，这是在开发系统上运行的客户端。 它允许你使用 Helm 启动、停止和管理应用程序。 如果使用 Azure Cloud Shell，则已安装 Helm CLI。 有关本地平台上的安装说明，请参阅[安装 Helm][helm-install]。

> [!IMPORTANT]
> Helm 要在 Linux 节点上运行。 如果群集中有 Windows Server 节点，则必须确保 Helm Pod 仅计划在 Linux 节点上运行。 还需要确保所安装的所有 Helm 图表也计划在正确的节点上运行。 本文中的命令使用 [节点选择器][k8s-节点选择器]，以确保将 Pod 安排到正确的节点，但并非所有 Helm 图表都可以公开节点选择器。 还可以考虑使用群集上的其他选项，例如[排斥][taints]。

## <a name="verify-your-version-of-helm"></a>验证 Helm 版本

使用 `helm version` 命令来验证已安装 Helm 3：

```console
helm version
```

以下示例演示了安装的 Helm 版本 3.0.0：

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>使用 Helm v3 安装应用程序

### <a name="add-helm-repositories"></a>添加 Helm 存储库

使用 [helm repo][helm-repo-add] 命令添加 ingress-nginx 存储库。

```console
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>查找 Helm 图表

Helm 图表用来将应用程序部署到 Kubernetes 群集中。 若要搜索预创建的 Helm 图表，请使用 [helm search][helm-search] 命令：

```console
helm search repo ingress-nginx
```

以下精简示例输出显示了一些可供使用的 Helm 图表：

```console
$ helm search repo ingress-nginx

NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ingress-nginx/ingress-nginx     2.12.0          0.34.1          Ingress controller for Kubernetes using NGINX a...
```

若要更新图表列表，请使用 [helm repo update][helm-repo-update] 命令。

```console
helm repo update
```

以下示例显示了成功的存储库更新：

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

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

az acr import --name $REGISTRY_NAME --source $CONTROLLER_REGISTRY/$CONTROLLER_IMAGE:$CONTROLLER_TAG --image $CONTROLLER_IMAGE:$CONTROLLER_TAG
az acr import --name $REGISTRY_NAME --source $PATCH_REGISTRY/$PATCH_IMAGE:$PATCH_TAG --image $PATCH_IMAGE:$PATCH_TAG
az acr import --name $REGISTRY_NAME --source $DEFAULTBACKEND_REGISTRY/$DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG --image $DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG
```

> [!NOTE]
> 除了将容器映像导入 ACR 之外，还可以将 Helm 图表导入 ACR。 有关详细信息，请参阅[将 Helm 图表推送和拉取到 Azure 容器注册表][acr-helm]。

### <a name="run-helm-charts"></a>运行 Helm 图表

若要使用 Helm 安装图表，请使用 [helm install][helm-install-command] 命令并指定版本名称和要安装的图表的名称。 若要查看实际安装的 Helm 图表，让我们使用 Helm 图表安装基本的 nginx 部署。

> [!TIP]
> 以下示例为名为 ingress-basic 的入口资源创建 Kubernetes 命名空间，目的是在该命名空间内执行操作。 根据需要为你自己的环境指定一个命名空间。

```console
ACR_URL=<REGISTRY_URL>

# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

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

以下精简示例输出显示了 Helm 图表创建的 Kubernetes 资源的部署状态：

```console
NAME: nginx-ingress
LAST DEPLOYED: Wed Jul 28 11:35:29 2021
NAMESPACE: ingress-basic
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The ingress-nginx controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller'
...
```

使用 `kubectl get services` 命令获取服务的 *EXTERNAL-IP*。

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

例如，以下命令显示 nginx-ingress-ingress-nginx-controller 服务的 EXTERNAL-IP：

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.254.93   <EXTERNAL_IP>   80:30004/TCP,443:30348/TCP   61s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>列出版本

若要查看群集上已安装的版本列表，请使用 `helm list` 命令。

```console
helm list --namespace ingress-basic
```

以下示例显示了上一步中部署的 *my-nginx-ingress* 版本：

```console
$ helm list --namespace ingress-basic
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress   ingress-basic   1               2021-07-28 11:35:29.9623734 -0500 CDT   deployed        ingress-nginx-3.34.0    0.47.0
```

### <a name="clean-up-resources"></a>清理资源

在部署 Helm 图表时，会创建若干 Kubernetes 资源。 这些资源包括 pod、部署和服务。 若要清理这些资源，请使用 [helm uninstall][helm-cleanup] 命令并指定版本名称，如上一个 `helm list` 命令中所示。

```console
helm uninstall --namespace ingress-basic nginx-ingress
```

下面的示例显示了已卸载的名为 *my-nginx-ingress* 的版本：

```console
$ helm uninstall --namespace ingress-basic nginx-ingress

release "nginx-ingress" uninstalled
```

若要删除整个示例命名空间，请使用 `kubectl delete` 命令并指定命名空间名称。 将会删除命名空间中的所有资源。

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>后续步骤

有关使用 Helm 管理 Kubernetes 应用程序部署的详细信息，请参阅 Helm 文档。

> [!div class="nextstepaction"]
> [Helm 文档][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[ingress-nginx-helm-chart]: https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx
            
<!-- LINKS - internal -->
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-integrated-acr]: cluster-container-registry-integration.md?tabs=azure-cli#create-a-new-aks-cluster-with-acr-integration
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md