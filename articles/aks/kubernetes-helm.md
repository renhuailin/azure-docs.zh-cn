---
title: 使用 Helm 在 AKS 中安装现有应用程序
description: 了解如何使用 Helm 打包工具在 Azure Kubernetes 服务 (AKS) 群集中部署容器
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: f12dffe0b538738a8f6dd00cd3d87d44da828f21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96779161"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>使用 Helm 在 Azure Kubernetes 服务 (AKS) 中安装现有应用程序

[Helm][helm] 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。 与诸如 *APT* 和 *Yum* 的 Linux 包管理器类似，Helm 用于管理 Kubernetes 图表，这些图表是预配置的 Kubernetes 资源包。

本文介绍如何在 AKS 上的 Kubernetes 群集中配置和使用 Helm。

## <a name="before-you-begin"></a>准备阶段

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

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

### <a name="run-helm-charts"></a>运行 Helm 图表

若要使用 Helm 安装图表，请使用 [helm install][helm-install-command] 命令并指定版本名称和要安装的图表的名称。 若要查看实际安装的 Helm 图表，让我们使用 Helm 图表安装基本的 nginx 部署。

```console
helm install my-nginx-ingress ingress-nginx/ingress-nginx \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

以下精简示例输出显示了 Helm 图表创建的 Kubernetes 资源的部署状态：

```console
$ helm install my-nginx-ingress ingress-nginx/ingress-nginx \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller'
...
```

使用 `kubectl get services` 命令获取服务的 *EXTERNAL-IP*。

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller
```

例如，下面的命令显示 my-nginx-ingress-ingress-nginx-controller 服务的 EXTERNAL-IP ：

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller

NAME                                        TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)                      AGE   SELECTOR
my-nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.2.237   <EXTERNAL-IP>    80:31380/TCP,443:32239/TCP   72s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=my-nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>列出版本

若要查看群集上已安装的版本列表，请使用 `helm list` 命令。

```console
helm list
```

以下示例显示了上一步中部署的 *my-nginx-ingress* 版本：

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>清理资源

在部署 Helm 图表时，会创建若干 Kubernetes 资源。 这些资源包括 pod、部署和服务。 若要清理这些资源，请使用 [helm uninstall][helm-cleanup] 命令并指定版本名称，如上一个 `helm list` 命令中所示。

```console
helm uninstall my-nginx-ingress
```

下面的示例显示了已卸载的名为 *my-nginx-ingress* 的版本：

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
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
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
