---
title: GitHub Actions 和 Azure Kubernetes 服务（预览版）
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: 使用 GitHub Actions 和 Azure Dev Spaces 直接在 Azure Kubernetes 服务中查看和测试拉取请求中的更改
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, GitHub Actions, Helm, 服务网格, 服务网格路由, kubectl, k8s
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 84d4f94cdb756b0bc11026baaa3acf065604c421
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777538"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub Actions 和 Azure Kubernetes 服务（预览版）

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces 提供了一个使用 GitHub Actions 的工作流，该工作流允许你在将拉取请求合并到存储库的主分支之前直接在 AKS 中测试拉取请求中的更改。 让正在运行的应用程序检查拉取请求的更改可以增加开发人员和团队成员的信心。 此正在运行的应用程序还可以帮助团队成员（例如产品经理和设计人员）在开发的早期阶段加入评审过程。

本指南介绍如何：

* 在 Azure 中的托管 Kubernetes 群集上设置 Azure Dev Spaces。
* 将包含多个微服务的大型应用程序部署到开发空间。
* 通过 GitHub Actions 设置 CI/CD。
* 在完整应用程序的上下文中的隔离开发空间内测试单个微服务。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
* [已安装 Azure CLI][azure-cli-installed]。
* [已安装 Helm 3][helm-installed]。
* [启用了 GitHub Actions][github-actions-beta-signup] 的 GitHub 帐户。
* 在 AKS 群集上运行的 [Azure Dev Spaces 共享自行车示例应用程序](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md)。

## <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表

创建 Azure 容器注册表 (ACR)：

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> ACR 名称在 Azure 中必须独一无二，包含 5-50 个字母数字字符。 你使用的任何字母都必须为小写。

保存输出中的 loginServer 值，因为后面的步骤中需要使用它。

## <a name="create-a-service-principal-for-authentication"></a>创建用于身份验证的服务主体

使用 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 创建服务主体。 例如：

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

保存 JSON 输出，因为在稍后的步骤中需要使用它。

使用 [az aks show][az-aks-show] 显示你的 AKS 群集的 ID：

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

使用 [az acr show][az-acr-show] 显示 ACR 的 ID：

```azurecli
az acr show --name <acrName> --query id
```

使用 [az role assignment create][az-role-assignment-create] 提供对 AKS 群集的“参与者”访问权限，以及对 ACR 的“AcrPush”访问权限。

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> 只有 AKS 群集和 ACR 的所有者才能向服务主体授予对这些资源的访问权限。

## <a name="configure-your-github-action"></a>配置 GitHub 操作

> [!IMPORTANT]
> 你必须为存储库启用 GitHub Actions。 若要为存储库启用 GitHub Actions，请导航到 GitHub 上的存储库，单击“操作”选项卡，然后选择为此存储库启用操作。

导航到你的分叉存储库，并单击“设置”。 单击左侧边栏中的“机密”。 单击“添加新机密”，添加下面的每个新机密：

1. AZURE_CREDENTIALS：创建服务主体后显示的整个输出。
1. RESOURCE_GROUP：你的 AKS 群集的资源组，在本例中为 MyResourceGroup。
1. CLUSTER_NAME：你的 AKS 群集的名称，在本例中为 MyAKS。
1. CONTAINER_REGISTRY：ACR 的 loginServer。
1. HOST：你的 Dev Space 的主机，采用 <MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX> 形式，在本例中为 dev.bikesharingweb.fedcab0987.eus.azds.io。
1. IMAGE_PULL_SECRET：要使用的机密的名称，例如 demo-secret。
1. MASTER_SPACE：你的父级 Dev Space 的名称，在本例中为 dev。
1. REGISTRY_USERNAME：创建服务主体时的 JSON 输出中的 clientId。
1. REGISTRY_PASSWORD：创建服务主体时的 JSON 输出中的 clientSecret。

> [!NOTE]
> 所有这些机密都由 GitHub 操作使用，是在 [.github/workflows/bikes.yml][github-action-yaml] 中配置的。

（可选）如果你希望在合并 PR 后更新主空间，请添加 GATEWAY_HOST 机密，它采用 <MASTER_SPACE>.gateway.<HOST_SUFFIX> 形式，在本例中为 dev.gateway.fedcab0987.eus.azds.io。 将所做的更改合并到分叉中的主分支后，会运行另一个操作，在主开发空间中重建和运行整个应用程序。 在本例中，主空间为 dev。 此操作在 [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml] 中配置。

另外，如果你希望在孙空间中运行 PR 中的更改，请更新 MASTER_SPACE 和 HOST 机密。 例如，如果你的应用程序在具有子空间 dev/azureuser1 的 dev 中运行，则若要在 dev/azureuser1 的子空间中运行 PR，请执行以下操作：

* 将 MASTER_SPACE 更新为要充当父空间的子空间，在本例中为 azureuser1。
* 将 HOST 更新为 <GRANDPARENT_SPACE>.<APP_NAME>.<HOST_SUFFIX>，在本例中为 dev.bikesharingweb.fedcab0987.eus.azds.io。

## <a name="create-a-new-branch-for-code-changes"></a>创建用于代码更改的新分支

导航到 `BikeSharingApp/`，创建一个名为 bike-images 的新分支。

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

编辑 [Bikes/server.js][bikes-server-js]，删除第 232 和第 233 行：

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

该节现在应如下所示：

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

保存该文件，然后使用 `git add` 和 `git commit` 暂存你的更改。

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>推送你的更改

使用 `git push` 将新分支推送到分叉存储库：

```cmd
git push origin bike-images
```

在推送完成后，导航到你在 GitHub 上的分叉存储库，以便在该分叉存储库（包含用作基础分支的主分支（相对于 bike-images 分支相而言））中创建拉取请求。

打开拉取请求后，导航到“操作”选项卡。验证新操作是否已启动并正在生成 Bikes 服务 。

## <a name="view-the-child-space-with-your-changes"></a>查看包含更改的子空间

在操作完成后，你会看到一个注释，其中包含基于拉取请求中的更改的新子空间的 URL。

> [!div class="mx-imgBorder"]
> ![GitHub 操作 URL](../media/github-actions/github-action-url.png)

通过打开注释中的 URL 导航到 bikesharingweb 服务。 选择“Aurelia Briggs (客户)”作为用户，然后选择要租赁的自行车。 验证是否不再显示自行车的占位符图像。

如果你将所做的更改合并到分叉中的主分支，则会运行另一个操作，在父开发空间中重建和运行整个应用程序。 在本例中，父空间为 *dev*。 此操作在 [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml] 中配置。

## <a name="clean-up-your-azure-resources"></a>清理 Azure 资源

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

深入了解 Azure Dev Spaces 的工作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 工作原理](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
