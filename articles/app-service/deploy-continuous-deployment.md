---
title: 配置持续部署
description: 了解如何启用从 GitHub、BitBucket、Azure Repos 或其他存储库到 Azure 应用服务的 CI/CD。 选择符合需要的生成管道。
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: aa058f0de74941a8ac9fe711c7b6de59240680bb
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110536666"
---
# <a name="continuous-deployment-to-azure-app-service"></a>持续部署到 Azure 应用服务

[Azure 应用服务](overview.md)通过拉取最新的更新，让你能够从 [GitHub](https://help.github.com/articles/create-a-repo)、[BitBucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)和 [Azure Repos](/azure/devops/repos/git/creatingrepo) 存储库进行持续部署。

> [!NOTE]
> Azure 门户中的“开发中心(经典)”页面（部署体验的较早版本）已于 2021 年 3 月弃用。 此更改不会影响应用中的现有部署设置，你可以继续在门户中的“部署中心”页中管理应用部署。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>配置部署源

1. 在 [Azure 门户](https://portal.azure.com)中，导航到应用服务应用的管理页。

1. 从左侧菜单中单击“部署中心” > “设置”。  

1. 在“源”中，选择 CI/CD 选项之一。

    ![演示如何在部署中心为 Azure 应用服务选择部署源](media/app-service-continuous-deployment/choose-source.png)

选择与你的选择相对应的选项卡来了解相关步骤。

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub Actions](#how-the-github-actions-build-provider-works) 是默认的生成提供程序。 若要更改它，请单击“更改提供程序” > “应用服务生成服务(Kudu)”>“确定”。  

    > [!NOTE]
    > 若要使用 Azure Pipelines 作为应用服务应用的生成提供程序，请不要在应用服务中配置它， 而是直接从 Azure Pipelines 配置 CI/CD。 “Azure Pipelines”选项就会为你指引正确的方向。

1. 如果你是第一次从 GitHub 进行部署，请单击“授权”并按授权提示操作。 如果要从另一个用户的存储库进行部署，请单击“更改帐户”。

1. 通过 GitHub 授权 Azure 帐户后，选择要为其配置 CI/CD 的“组织”、“存储库”和“分支”。   如果找不到组织或存储库，可能需要在 GitHub 上启用附加权限。 有关详细信息，请参阅[管理对组织的存储库的访问权限](https://docs.github.com/organizations/managing-access-to-your-organizations-repositories)

1. 当 GitHub Actions 是所选的生成提供程序时，你可以在“运行时堆栈”和“版本”下拉列表中选择所需的工作流文件。 Azure 会将此工作流文件提交到选定的 GitHub 存储库中来处理生成和部署任务。 若要在保存更改之前查看文件，请单击“预览文件”。

    > [!NOTE]
    > 应用服务会检测应用的[语言堆栈设置](configure-common.md#configure-language-stack-settings)，并选择最合适的工作流模板。 如果选择其他模板，则可能会部署不能正常运行的应用。 有关详细信息，请参阅 [GitHub Actions 生成提供程序工作原理](#how-the-github-actions-build-provider-works)。

1. 单击“保存”。
   
    选定存储库和分支中的新提交现在将持续部署到应用服务应用中。 可以在“日志”选项卡中跟踪提交和部署。

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

BitBucket 集成使用应用服务生成服务 (Kudu) 实现生成自动化。

4. 如果你是第一次从 BitBucket 进行部署，请单击“授权”并按授权提示操作。 如果要从另一个用户的存储库进行部署，请单击“更改帐户”。

1. 对于 Bitbucket，请选择要持续部署的 Bitbucket **团队**、**存储库** 和 **分支**。

1. 单击“保存”。
   
    选定存储库和分支中的新提交现在将持续部署到应用服务应用中。 可以在“日志”选项卡中跟踪提交和部署。
   
# <a name="local-git"></a>[本地 Git](#tab/local)

请参阅[从本地 Git 部署到 Azure 应用服务](deploy-local-git.md)。

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)

> [!NOTE]
> 作为部署源的 Azure Repos 是对 Windows 应用的支持。
>

4. 应用服务生成服务 (Kudu) 是默认的生成提供程序。

    > [!NOTE]
    > 若要使用 Azure Pipelines 作为应用服务应用的生成提供程序，请不要在应用服务中配置它， 而是直接从 Azure Pipelines 配置 CI/CD。 “Azure Pipelines”选项就会为你指引正确的方向。

1. 选择要持续部署的“Azure DevOps 组织”、“项目”、“存储库”和“分支”。 

    如果未列出 DevOps 组织，则表明它尚未关联到你的 Azure 订阅。 有关详细信息，请参阅[创建 Azure 服务连接](/azure/devops/pipelines/library/connect-to-azure)。

-----

## <a name="disable-continuous-deployment"></a>禁用持续部署

1. 在 [Azure 门户](https://portal.azure.com)中，导航到应用服务应用的管理页。

1. 从左侧菜单中单击“部署中心” > “设置” > “断开连接”。   

    ![演示如何在 Azure 门户中将 Cloud Folder Sync 与应用服务应用断开连接。](media/app-service-continuous-deployment/disable.png)

1. 默认情况下，GitHub Actions 工作流文件保留在你的存储库中，但会持续触发向你的应用进行部署。 若要将其从存储库中删除，请选择“删除工作流文件”。

1. 单击 **“确定”** 。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>GitHub Actions 生成提供程序工作原理

GitHub Actions 生成提供程序是适用于[从 GitHub 进行 CI/CD](#configure-deployment-source) 的选项，可通过执行以下操作来设置 CI/CD：

- 将 GitHub Actions 工作流文件存储到 GitHub 存储库中，以便处理目标为应用服务的生成和部署任务。
- 添加应用的发布配置文件作为 GitHub 机密。 工作流文件使用此机密通过应用服务进行身份验证。
- 捕获[工作流运行日志](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs)中的信息，将其显示在应用的“部署中心”的“日志”选项卡中。

可通过以下方式自定义 GitHub Actions 生成提供程序：

- 在 GitHub 存储库中生成工作流文件后，对该工作流文件进行自定义。 有关详细信息，请参阅 [GitHub Actions 的工作流语法](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions)。 只需确保工作流通过 [azure/webapps-deploy](https://github.com/Azure/webapps-deploy) 操作部署到应用服务。
- 如果所选分支受保护，则仍可在不保存配置的情况下预览工作流文件，然后将其手动添加到存储库中。 此方法不提供与 Azure 门户的日志集成。
- 请使用 Azure Active Directory 中的[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)进行部署，而不是使用发布配置文件。

#### <a name="authenticate-with-a-service-principal"></a>使用服务主体进行身份验证

此可选配置会将默认身份验证替换为生成的工作流文件中的发布配置文件。

1. 在 [Azure CLI](/cli/azure/) 中使用 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 命令生成服务主体。 在以下示例中，请将 \<subscription-id>、\<group-name> 和 \<app-name> 替换为你自己的值：

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > 为了安全起见，请为服务主体授予所需的最低访问权限。 上一个示例中的范围仅限于特定的应用服务应用而不是整个资源组。
    
1. 保存整个 JSON 输出（包括顶级 `{}`）供下一步使用。

1. 在 [GitHub](https://github.com/) 中，浏览存储库，选择“设置”>“机密”>“添加新机密”。

1. 将 Azure CLI 命令的整个 JSON 输出粘贴到机密的值字段中。 为机密指定名称，如 `AZURE_CREDENTIALS`。

1. 在 **部署中心** 生成的工作流文件中，使用类似于以下示例所示的代码（修改自 Node.js 工作流文件）修改 `azure/webapps-deploy` 步骤：

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>从其他存储库进行部署

对于 Windows 应用，你可以从门户不直接支持的云 Git 或 Mercurial 存储库（例如 [GitLab](https://gitlab.com/)）手动配置持续部署。 为此，请在“源”下拉列表中选择“外部 Git”。 有关详细信息，请参阅[使用手动步骤设置持续部署](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)。

## <a name="more-resources"></a>更多资源

* [从 Azure Pipelines 部署到 Azure 应用服务](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [调查持续部署的常见问题](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [使用 Azure PowerShell](/powershell/azure/)
* [项目 Kudu](https://github.com/projectkudu/kudu/wiki)
