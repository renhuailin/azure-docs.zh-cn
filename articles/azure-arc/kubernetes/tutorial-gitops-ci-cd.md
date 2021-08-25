---
title: 教程：使用已启用 Azure Arc 的 Kubernetes 群集通过 GitOps 实现 CI/CD
description: 本教程逐步介绍如何将 GitOps 与已启用 Azure Arc 的 Kubernetes 群集配合使用来设置 CI/CD 解决方案。 有关此工作流的概念性说明，请参阅“使用 GitOps 的 CI/CD 工作流 - 已启用 Azure Arc 的 Kubernetes”一文。
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: 8b62437fc8bcad406750101eb72b1ef8d48c102f
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322201"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>教程：使用已启用 Azure Arc 的 Kubernetes 群集通过 GitOps 实现 CI/CD


在本教程中，你会将 GitOps 与已启用 Azure Arc 的 Kubernetes 群集配合使用，来设置一个 CI/CD 解决方案。 你将使用示例 Azure 投票应用执行以下操作：

> [!div class="checklist"]
> * 创建一个已启用 Azure Arc 的 Kubernetes 群集。
> * 将应用程序和 GitOps 存储库连接到 Azure Repos。
> * 导入 CI/CD 管道。
> * 将 Azure 容器注册表 (ACR) 连接到 Azure DevOps 和 Kubernetes。
> * 创建环境变量组。
> * 部署 `dev` 和 `stage` 环境。
> * 测试应用程序环境。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>准备阶段

本教程假设读者熟悉 Azure DevOps、Azure Repos 和管道以及 Azure CLI。

* 登录到 [Azure DevOps Services](https://dev.azure.com/)。
* 完成[上一篇教程](./tutorial-use-gitops-connected-cluster.md)，了解如何为 CI/CD 环境部署 GitOps。
* 了解此功能的[优势和体系结构](./conceptual-configurations.md)。
* 验证是否具有：
  * 一个 [已连接且已启用 Azure Arc 的 Kubernetes 群集](./quickstart-connect-cluster.md#3-connect-an-existing-kubernetes-cluster)，名为 **arc-cicd-cluster**。
  * 一个使用 [AKS 集成](../../aks/cluster-container-registry-integration.md)或[非 AKS 群集身份验证](../../container-registry/container-registry-auth-kubernetes.md)的已连接 Azure 容器注册表 (ACR)。
  * 对 [Azure Repos](/azure/devops/repos/get-started/what-is-repos) 和 [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started) 的“生成管理员”与“项目管理员”权限。
* 安装以下版本 >= 1.0.0 的已启用 Azure Arc 的 Kubernetes CLI 扩展：

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * 若要将这些扩展更新到最新版本，请运行以下命令：

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-configuration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>将应用程序存储库和 GitOps 存储库导入 Azure Repos

将[应用程序存储库](./conceptual-gitops-ci-cd.md#application-repo)和 [GitOps 存储库](./conceptual-gitops-ci-cd.md#gitops-repo)导入 Azure Repos。 对于本教程，请使用以下示例存储库：

* **arc-cicd-demo-src** 应用程序存储库
   * URL： https://github.com/Azure/arc-cicd-demo-src
   * 包含要使用 GitOps 部署的示例 Azure 投票应用。
* **arc-cicd-demo-gitops** GitOps 存储库
   * URL： https://github.com/Azure/arc-cicd-demo-gitops
   * 充当容装 Azure 投票应用的群集资源的基础。

详细了解如何[导入 Git 存储库](/azure/devops/repos/git/import-git-repository)。

>[!NOTE]
> 为应用程序和 GitOps 导入并使用两个独立的存储库能够提高安全性和简易性。 可以分别优化应用程序和 GitOps 存储库的权限与可见性。
> 例如，群集管理员可能在应用程序代码中发现不了与群集所需状态相关的更改。 相反，应用程序开发人员无需知道每个环境的特定参数 - 一组能够涵盖各个参数的测试值可能就已足够。

## <a name="connect-the-gitops-repo"></a>连接 GitOps 存储库

若要持续部署应用，请使用 GitOps 将应用程序存储库连接到群集。 **arc-cicd-demo-gitops** GitOps 存储库包含可使应用在 **arc-cicd-cluster** 群集上启动并运行的基本资源。

初始 GitOps 存储库仅包含一个 [清单](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml)，该清单用于创建对应于部署环境的 **dev** 和 **stage** 命名空间。

创建的 GitOps 连接将自动执行以下操作：
* 同步清单目录中的清单。
* 更新群集状态。

CI/CD 工作流将在清单目录中填充额外的清单，以部署应用。


1. 与 Azure Repos 中新导入的 **arc-cicd-demo-gitops** 存储库 [建立新的 GitOps 连接](./tutorial-use-gitops-connected-cluster.md)。

   ```azurecli
   az k8s-configuration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/<Your project>/_git/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. 确保 Flux 仅使用 `arc-cicd-cluster/manifests` 目录作为基路径。 使用以下运算符参数定义路径：

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > 如果你使用 HTTPS 连接字符串并出现连接问题，请确保在 URL 中省略用户名前缀。 例如，必须删除 `https://alice@dev.azure.com/contoso/project/_git/arc-cicd-demo-gitops` 中的 `alice@`。 在此情况下，将由 `--https-user` 指定用户，例如 `--https-user alice`。

1. 在 Azure 门户中检查部署状态。
   * 如果成功，你会发现群集中创建了 `dev` 和 `stage` 命名空间。

## <a name="import-the-cicd-pipelines"></a>导入 CI/CD 管道

同步 GitOps 连接后，需要导入用于创建清单的 CI/CD 管道。

应用程序存储库包含一个 `.pipeline` 文件夹，该文件夹中包含用于 PR、CI 和 CD 的管道。 导入示例存储库中提供的三个管道并将其重命名：

| 管道文件名 | 说明 |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | 应用程序 PR 管道，名为 **arc-cicd-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | 应用程序 CI 管道，名为 **arc-cicd-demo-src CI** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | 应用程序 CD 管道，名为 **arc-cicd-demo-src CD** |



## <a name="connect-your-acr"></a>连接 ACR
管道和群集都将利用 ACR 来存储和检索 Docker 映像。

### <a name="connect-acr-to-azure-devops"></a>将 ACR 连接到 Azure DevOps
在 CI 过程中，你要将应用程序容器部署到注册表。 首先创建 Azure 服务连接：

1. 在 Azure DevOps 中，从项目设置页打开“服务连接”页。 在 TFS 中，通过顶部菜单栏中的“设置”图标打开“服务”页。 
2. 选择“+ 新建服务连接”，然后选择所需的服务连接类型。
3. 填写该服务连接的参数。 对于本教程：
   * 将服务连接命名为 **arc-demo-acr**。 
   * 选择“myResourceGroup”作为资源组。
4. 选择“授予对所有管道的访问权限”。 
   * 此选项为用于服务连接的 YAML 管道文件授权。 
5. 选择“确定”以创建连接。

### <a name="connect-acr-to-kubernetes"></a>将 ACR 连接到 Kubernetes
使 Kubernetes 群集能够从 ACR 中拉取映像。 如果该 ACR 是专用的，则需要完成身份验证。

#### <a name="connect-acr-to-existing-aks-clusters"></a>将 ACR 连接到现有 AKS 群集

使用以下命令将现有 ACR 与现有 AKS 群集相集成：

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>创建映像拉取机密

若要将非 AKS 群集和本地群集连接到 ACR，请创建映像拉取机密。 Kubernetes 使用映像拉取机密来存储对注册表进行身份验证所需的信息。

使用以下 `kubectl` 命令创建映像拉取机密。 对 `dev` 和 `stage` 命名空间重复此操作。
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

为了避免对每个 Pod 都设置一个 imagePullSecret，请考虑将 imagePullSecret 添加到 `dev` 和 `stage` 命名空间中的服务帐户。 有关详细信息，请参阅 [Kubernetes 教程](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account)。

## <a name="create-environment-variable-groups"></a>创建环境变量组

### <a name="app-repo-variable-group"></a>应用存储库变量组
[创建](/azure/devops/pipelines/library/variable-groups)名为 **az-vote-app-dev** 的变量组。 设置以下值：

| 变量 | 值 |
| -------- | ----- |
| AZ_ACR_NAME | （你的 ACR 实例，例如 azurearctest.azurecr.io） |
| AZURE_SUBSCRIPTION | （你的 Azure 服务连接，根据本教程前面所述，应是 **arc-demo-acr**） |
| AZURE_VOTE_IMAGE_REPO | Azure 投票应用存储库的完整路径，例如 azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Dev |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_FOLDER | `azure-vote-manifests` |
| MANIFESTS_REPO | `arc-cicd-demo-gitops` |
| ORGANIZATION_NAME | Azure DevOps 组织的名称 |
| PROJECT_NAME | Azure DevOps 中 GitOps 项目的名称 |
| REPO_URL | GitOps 存储库的完整 URL |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

### <a name="stage-environment-variable-group"></a>暂存环境变量组

1. 克隆 **az-vote-app-dev** 变量组。
1. 将名称更改为 **az-vote-app-stage**。
1. 确保相应变量使用以下值：

| 变量 | 值 |
| -------- | ----- |
| ENVIRONMENT_NAME | 阶段 |
| TARGET_NAMESPACE | `stage` |

现已准备好部署到 `dev` 和 `stage` 环境。

## <a name="give-more-permissions-to-the-build-service"></a>向生成服务授予更多权限
CD 管道使用正在运行的生成的安全令牌对 GitOps 存储库进行身份验证。 管道需要更多的权限来创建新分支、推送更改和创建拉取请求。

1. 从 Azure DevOps 项目主页中转到 `Project settings`。
1. 选择 `Repositories`。
1. 选择 `<GitOps Repo Name>`。
1. 选择 `Security`。 
1. 对于 `<Project Name> Build Service (<Organization Name>)`，允许 `Contribute`、`Contribute to pull requests` 和 `Create branch`。

有关详细信息，请参阅：
- [向生成服务授予 VC 权限](/azure/devops/pipelines/scripts/git-commands?preserve-view=true&tabs=yaml&view=azure-devops#version-control )
- [管理生成服务帐户权限](/azure/devops/pipelines/process/access-tokens?preserve-view=true&tabs=yaml&view=azure-devops#manage-build-service-account-permissions)


## <a name="deploy-the-dev-environment-for-the-first-time"></a>首次部署开发环境
创建 CI 和 CD 管道后，运行 CI 管道以首次部署应用。

### <a name="ci-pipeline"></a>CI 管道

在初始 CI 管道运行期间，读取服务连接名称时可能会出现资源授权错误。
1. 请验证所访问的变量是否为 AZURE_SUBSCRIPTION。
1. 授权使用。
1. 请重新运行管道。

CI 管道：
* 确保应用程序更改通过了所有自动化的部署质量检查。
* 执行无法在 PR 管道中完成的任何额外验证。
    * 该管道特定于 GitOps，它还会发布将由 CD 管道部署的提交项目。
* 验证是否已更改 Docker 映像并已推送新映像。

### <a name="cd-pipeline"></a>CD 管道
在初始 CD 管道运行过程中，系统会要求你为管道提供对 GitOps 存储库的访问权限。 当提示管道需要访问资源的权限时，请选择“查看”。 然后，选择“允许”，向当前和将来的管道运行授予使用 GitOps 存储库的权限。

成功的 CI 管道运行会触发 CD 管道来完成部署过程。 你将以增量方式部署到每个环境。

> [!TIP]
> 如果 CD 管道未自动触发：
> 1. 验证名称是否与 [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) 中的分支触发器相匹配
>    * 它应为 `arc-cicd-demo-src CI`。
> 1. 重新运行 CI 管道。

生成对 GitOps 存储库所做的模板和清单更改后，CD 管道将创建一个提交项，推送该提交项，然后创建 PR 供审批。
1. 打开 `Create PR` 任务输出中提供的 PR 链接。
1. 验证对 GitOps 存储库所做的更改。 应会看到：
   * 高级 Helm 模板更改。
   * 低级 Kubernetes 清单，显示对所需状态做出的基础更改。 Flux 部署这些清单。
1. 如果一切正常，请批准并完成 PR。

1. 几分钟后，Flux 将拾取更改并开始部署。
1. 使用 `kubectl` 在本地转发端口，并使用以下命令来确保应用正常工作：

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. 在浏览器中使用 `http://localhost:8080/` 查看 Azure 投票应用。

1. 为收藏项投票，并准备好对应用做出一些更改。

## <a name="set-up-environment-approvals"></a>设置环境审批
部署应用后，你不仅可能会对代码或模板做出更改，而且还可能在无意中使群集处于错误状态。

如果开发环境在部署后出现损坏情况，请使用环境审批来避免这种情况漫延到后面的环境。

1. 在 Azure DevOps 项目中，转到需要保护的环境。
1. 导航到资源对应的“审批和检查”。
1. 选择“创建”。
1. 提供审批者和可选消息。
1. 再次选择“创建”以完成添加手动审批检查。

有关更多详细信息，请参阅[定义审批和检查](/azure/devops/pipelines/process/approvals)教程。

下一次 CD 管道运行时，该管道将在创建 GitOps PR 后暂停。 验证更改是否已正确同步并通过了基本的功能检查。 批准管道中的检查，使更改流向下一环境。

## <a name="make-an-application-change"></a>做出应用程序更改

你将根据模板以及用于表示群集中状态的清单的这一套基线，对应用进行少量的更改。

1. 在 **arc-cicd-demo-src** 存储库中编辑 [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg) 文件。

2. 由于“Cats vs Dogs”未获得足够多的投票，请将其更改为“Tabs vs Spaces”以提高投票数。

3. 在新分支中提交更改，推送更改，然后创建拉取请求。
   * 这是启动 CI/CD 生命周期的典型开发人员流。

## <a name="pr-validation-pipeline"></a>PR 验证管道

PR 管道是避免出现错误更改的第一道防线。 一般的应用程序代码质量检查包括 Lint 分析和静态分析。 从 GitOps 的角度看，还需确保所要部署的最终基础结构的质量相同。

应用程序的 Dockerfile 和 Helm 图表可以像应用程序一样使用 Lint 分析。

Lint 分析期间出现的错误包括：
* 指出 YAML 文件格式不正确；
* 提供最佳做法建议，例如，为应用程序设置 CPU 和内存限制。

> [!NOTE]
> 若要在真实应用程序中通过 Helm Lint 分析获得最全面的问题覆盖范围，需要替代真实环境中使用的相当类似的值。

在管道执行期间发现的错误将显示在运行的“测试结果”部分。 你可以在此处：
* 跟踪有关错误类型的有用统计信息。
* 找到在其中检测到了这些错误的第一个提交项。
* 找到导致错误的代码部分的堆栈跟踪式链接。

管道运行完成后，即可保证应用程序代码以及要部署该代码的模板的质量。 现在可以批准并完成 PR。 CI 将再次运行，重新生成模板和清单，然后触发 CD 管道。

> [!TIP]
> 在真实环境中，请记得设置分支策略，以确保 PR 通过质量检查。 有关详细信息，请参阅[设置分支策略](/azure/devops/repos/git/branch-policies)一文。

## <a name="cd-process-approvals"></a>CD 过程审批

成功的 CI 管道运行会触发 CD 管道来完成部署过程。 与首次运行 CD 管道类似，你将以增量方式部署到每个环境。 这一次，管道要求你批准每个部署环境。

1. 批准部署到 `dev` 环境。
1. 生成对 GitOps 存储库所做的模板和清单更改后，CD 管道将创建一个提交项，推送该提交项，然后创建 PR 供审批。
1. 打开任务中提供的 PR 链接。
1. 验证对 GitOps 存储库所做的更改。 应会看到：
   * 高级 Helm 模板更改。
   * 低级 Kubernetes 清单，显示对所需状态做出的基础更改。
1. 如果一切正常，请批准并完成 PR。
1. 等待部署完成。
1. 作为基本的冒烟测试，请导航到应用程序页，并验证投票应用现在是否显示“Tabs vs Spaces”。
   * 使用 `kubectl` 在本地转发端口，并使用以下命令来确保应用正常工作：`kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * 在浏览器中使用 http://localhost:8080/ 查看 Azure 投票应用，验证投票选项是否已更改为“Tabs vs Spaces”。 
1. 对 `stage` 环境重复步骤 1-7。

部署现已完成。 CI/CD 工作流到此结束。

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除所有资源：

1. 删除 Azure Arc GitOps 配置连接：
   ```azurecli
   az k8s-configuration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. 删除 `dev` 命名空间：
   * `kubectl delete namespace dev`

3. 删除 `stage` 命名空间：
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>后续步骤

在本教程中，你已设置一个完整的 CI/CD 工作流，该工作流可实现从应用程序开发到部署的整个 DevOps。 对应用程序所做的更改会自动触发验证和部署，这些操作通过手动审批进行限制。

请转到我们的概念文章，详细了解使用已启用 Azure Arc 的 Kubernetes 进行的 GitOps 和配置。

> [!div class="nextstepaction"]
> [使用 GitOps 的 CI/CD 工作流 - 已启用 Azure Arc 的 Kubernetes](./conceptual-gitops-ci-cd.md)
