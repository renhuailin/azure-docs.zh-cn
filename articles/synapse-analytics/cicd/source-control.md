---
title: Synapse Studio 中的源代码管理
description: 了解如何在 Azure Synapse Studio 中配置源代码管理
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: a5cb5831090987f1b620593843ddba817d8e68d4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188116"
---
# <a name="source-control-in-azure-synapse-studio"></a>Azure Synapse Studio 中的源代码管理

默认情况下，Azure Synapse Studio 作者直接针对 Synapse 服务。 但是，这种体验存在以下限制：

- Synapse studio 不包括用于存储更改的临时存储。 保存和共享更改的唯一方法是通过 **发布** ，并将所有更改直接发布到 Synapse 服务。

- Synapse Studio 未针对协作和版本控制进行优化。

为了提供源代码管理功能，Synapse Studio 允许你将工作区与 Git 存储库、Azure DevOps 或 GitHub 关联。 本文概述如何在启用了 git 存储库的 Synapse 工作区中配置和工作。 此外，我们还重点介绍了一些最佳做法和故障排除指南。

> [!NOTE]
> Azure Synapse Studio git 集成在 Azure 政府版云中不可用。

## <a name="configure-git-repository-in-synapse-studio"></a>在 Synapse Studio 中配置 Git 存储库 

启动 Synapse Studio 后，可以在工作区中配置 git 存储库。 Synapse Studio 工作区一次只能与一个 git 存储库相关联。 

### <a name="configuration-method-1-authoring-canvas"></a>配置方法1：创作画布

在 Synapse Studio 创作画布中，选择 " **Synapse Live** " 下拉菜单，然后选择 " **设置代码存储库**"。

![配置代码存储库设置的创作](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>配置方法2：管理中心

请参阅 Synapse Studio 的管理中心。 选择 "**源代码管理**" 部分中的 **Git 配置**。 如果未连接存储库，请单击 " **配置**"。

![从管理中心配置代码存储库设置](media/configure-repo-2.png)

> [!NOTE]
> 被授予工作区参与者、所有者或更高级别的角色的用户可以在 Azure Synapse studio 中配置、编辑设置和断开 git 存储库 

可以在工作区中连接 Azure DevOps 或 GitHub git 存储库。

## <a name="connect-with-azure-devops-git"></a>连接 Azure DevOps Git 

可以将 Synapse 工作区与 Azure DevOps 存储库相关联，以进行源代码管理、协作、版本控制等操作。 如果没有 Azure DevOps 存储库，请按照 [这些说明](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) 操作，首先创建存储库资源。

### <a name="azure-devops-git-repository-settings"></a>Azure DevOps Git 存储库设置

连接到 git 存储库时，请先选择 Azure DevOps git 的存储库类型，然后从下拉列表中选择一个 Azure AD 租户，然后单击 " **继续**"。

![配置存储库设置](media/connect-with-azuredevops-repo-selected.png)

"配置" 窗格显示以下 Azure DevOps git 设置：

| 设置 | 说明 | 值 |
|:--- |:--- |:--- |
| **存储库类型** | Azure Repos 代码存储库的类型。<br/> | Azure DevOps Git 或 GitHub |
| **Azure Active Directory** | Azure AD 租户的名称。 | `<your tenant name>` |
| **Azure DevOps 帐户** | Azure Repos 组织名称。 可以在 `https://{organization name}.visualstudio.com` 中找到 Azure Repos 组织名称。 可以[登录到 Azure Repos 组织](https://www.visualstudio.com/team-services/git/)来访问 Visual Studio 配置文件并查看存储库和项目。 | `<your organization name>` |
| **ProjectName** | Azure Repos 项目名称。 可以在 `https://{organization name}.visualstudio.com/{project name}` 中找到 Azure Repos 项目名称。 | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos 代码存储库名称。 Azure Repos 项目包含 Git 存储库，随着项目的发展来管理源代码。 要么创建一个新的存储库，要么使用项目中已有的存储库。 | `<your Azure Repos code repository name>` |
| **协作分支** | 将用于发布的 Azure Repos 协作分支。 默认值为 `master`。 如果希望从其他分支发布资源，可更改此设置。 您可以选择现有分支，也可以创建新的分支 | `<your collaboration branch name>` |
| **根文件夹** | Azure Repos 协作分支中的根文件夹。 | `<your root folder name>` |
| **将现有资源导入存储库** | 指定是否将 Synapse Studio 中的现有资源导入到 Azure Repos Git 存储库中。 选中此框可将工作区资源 (导入，但池) 导入 JSON 格式的关联 Git 存储库。 此操作单独导出每个资源。 如果未选中此框，则不会导入现有资源。 | 已选中 (默认值)  |
| **将资源导入到此分支** | 选择要将资源 (sql 脚本、笔记本、spark 作业定义、数据集、数据流等 ) 导入到的分支。 

你还可以使用 "存储库" 链接快速指向要连接的 git 存储库。 

### <a name="use-a-different-azure-active-directory-tenant"></a>使用不同的 Azure Active Directory 租户

Azure Repos Git 存储库可以位于不同的 Azure Active Directory 租户中。 若要指定不同的 Azure AD 租户，必须对所用 Azure 订阅拥有管理员权限。 有关详细信息，请参阅 [更改订阅管理员](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator#assign-a-subscription-administrator)

> [!IMPORTANT]
> 若要连接到另一个 Azure Active Directory，登录的用户必须是该 Active Directory 的一部分。 

### <a name="use-your-personal-microsoft-account"></a>使用 Microsoft 个人帐户

若要将 Microsoft 个人帐户用于 Git 集成，可以将你的 Azure 个人存储库链接到贵组织的 Active Directory。

1. 将你的 Microsoft 个人帐户添加到贵组织的 Active Directory 以作为来宾。 有关详细信息，请参阅[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](https://docs.microsoft.com/azure/active-directory/external-identities/add-users-administrator)。

2. 使用 Microsoft 个人帐户登录 Azure 门户。 然后切换到贵组织的 Active Directory。

3. 请转到 Azure DevOps 部分，现在你可以在其中看到个人存储库。 选择存储库并连接到 Active Directory。

完成这些配置步骤后，你的个人存储库将在 Synapse Studio 中设置 Git 集成时可用。

有关将 Azure 存储库连接到贵组织的 Active Directory 的详细信息，请参阅[将 Azure DevOps 组织连接到 Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)。

## <a name="connect-with-github"></a>与 GitHub 连接 

 可以将工作区与 GitHub 存储库相关联，以进行源代码管理、协作和版本控制。 如果你没有 GitHub 帐户或存储库，请按照[这些说明](https://github.com/join)创建资源。

GitHub 与 Synapse Studio 的集成支持 [https://github.com](https://github.com)) 和 GitHub 企业的公共 GitHub (。 你可以使用公共和专用 GitHub 存储库，只要你对 GitHub 中的存储库拥有读取和写入权限即可。

### <a name="github-settings"></a>GitHub 设置

连接到 git 存储库时，请先选择 GitHub 存储库类型，然后提供 GitHub 帐户或 GitHub 企业服务器 URL （如果使用 GitHub 企业服务器），然后单击 " **继续**"。

![GitHub 存储库设置](media/connect-with-github-repo-1.png)

配置窗格将显示以下 GitHub 存储库设置：

| **设置** | **说明**  | **值**  |
|:--- |:--- |:--- |
| **存储库类型** | Azure Repos 代码存储库的类型。 | GitHub |
| **使用 GitHub Enterprise** | 用于选择 GitHub Enterprise 的复选框 | 未选择（默认值） |
| **GitHub Enterprise URL** | GitHub Enterprise 根 URL（对于本地 GitHub 企业服务器，必须是 HTTPS）。 例如：`https://github.mydomain.com`。 仅当选择了“使用 GitHub Enterprise”时才需要 | `<your GitHub enterprise url>` |                                                           
| **GitHub 帐户** | GitHub 帐户名。 可从 https:\//github.com/{account name}/{repository name} 找到此名称。 导航到此页时，系统会提示输入 GitHub 帐户的 GitHub OAuth 凭据。 | `<your GitHub account name>` |
| **存储库名称**  | GitHub 代码存储库名称。 GitHub 帐户包含用于管理源代码的 Git 存储库。 可以创建新存储库，或使用帐户中现有的存储库。 | `<your repository name>` |
| **协作分支** | 将用于发布的 GitHub 协作分支。 默认为其主分支。 如果希望从其他分支发布资源，可更改此设置。 | `<your collaboration branch>` |
| **根文件夹** | GitHub 协作分支中的根文件夹。 |`<your root folder name>` |
| **将现有资源导入存储库** | 指定是否将 Synapse Studio 中的现有资源导入 Git 存储库。 选中此框可将工作区资源 (导入，但池) 导入 JSON 格式的关联 Git 存储库。 此操作单独导出每个资源。 如果未选中此框，则不会导入现有资源。 | 已选择（默认） |
| **将资源导入到此分支** | 选择要导入资源 (sql 脚本、笔记本、spark 作业定义、数据集、数据流 ) 等资源的分支。 

### <a name="github-organizations"></a>GitHub 组织

若要连接到 GitHub 组织，需要组织授予对 Synapse Studio 的权限。 对组织拥有管理员权限的用户必须执行以下步骤。

#### <a name="connecting-to-github-for-the-first-time"></a>首次连接到 GitHub

如果是第一次从 Synapse Studio 连接到 GitHub，请按照以下步骤连接到 GitHub 组织。

1. 在 "Git 配置" 窗格的 " *GitHub 帐户* " 字段中，输入组织名称。 将显示登录 GitHub 的提示。 

1. 使用用户凭据进行登录。

1. 系统会要求你授权 Synapse 作为名为 *Azure Synapse* 的应用程序。 在此屏幕上，你将看到一个选项，用于为 Synapse 授予访问组织的权限。 如果看不到授予权限的选项，请要求管理员通过 GitHub 手动授予权限。

执行这些步骤后，工作区将能够连接到组织中的公共和专用存储库。 如果无法连接，请尝试清除浏览器缓存，然后重试。

## <a name="version-control"></a>版本控制

版本控制系统 (也称为 _源代码管理_) 允许开发人员协作处理代码并跟踪更改。源代码管理是多开发人员项目的重要工具。

### <a name="creating-feature-branches"></a>创建功能分支

与 Synapse Studio 关联的每个 Git 存储库都有一个协作分支。  (`main` 或 `master` 是默认的协作分支) 。 用户还可以通过单击分支下拉列表中的“+ 新建分支”来创建功能分支。 出现“新建分支”窗格后，输入功能分支的名称。

![创建新分支](media/create-new-branch.png)

准备好将功能分支的更改合并到协作分支时，单击分支下拉列表并选择“创建拉取请求”。 此操作会将你转到 Git 提供程序，你可以在其中引发拉取请求、执行代码评审以及将更改合并到协作分支。 只允许从协作分支发布到 Synapse 服务。 

![创建新的拉取请求](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>配置发布设置

默认情况下，Synapse Studio 将生成工作区模板并将其保存到名为的分支中 `workspace_publish` 。 若要配置自定义发布分支，请将 `publish_config.json` 文件添加到协作分支的根文件夹中。 发布时，Synapse Studio 将读取此文件，查找字段 `publishBranch` ，并将工作区模板文件保存到指定位置。 如果该分支不存在，Synapse Studio 将自动创建它。 此文件类似如下示例：

```json
{
    "publishBranch": "workspace_publish"
}
```

Azure Synapse Studio 一次只能有一个发布分支。 指定新的发布分支时，不会删除以前的发布分支。 如果想要远程删除以前的发布分支，请手动将其删除。

### <a name="publish-code-changes"></a>发布代码更改

将更改合并到协作分支后，单击 " **发布** " 以将协作分支中的代码更改手动发布到 Synapse 服务。

![发布更改](media/gitmode-publish.png)

此时会打开一个侧窗格，你可以在其中确认发布分支和挂起的更改是否正确。 验证更改后，单击“确定”以确认发布。

![确认正确的发布分支](media/publish-change.png)

> [!IMPORTANT]
> 协作分支并不代表服务中部署的内容。 *必须* 手动发布协作分支中的更改。

## <a name="switch-to-a-different-git-repository"></a>切换到不同 Git 存储库

若要切换到另一个 Git 存储库，请转到管理中心中的 " **源代码** 管理" 下的 "Git 配置" 页。 选择 " **断开连接**"。 

![“Git”图标](media/remove-repository.png)

输入工作区名称，并单击 " **断开连接** " 以删除与工作区关联的 Git 存储库。

删除与当前存储库的关联后，可以将 Git 设置配置为使用其他存储库，然后将现有资源导入到新存储库。

> [!IMPORTANT]
> 从工作区中删除 Git 配置不会从存储库中删除任何内容。 Synapse 工作区将包含所有已发布的资源。 你可以继续针对服务直接编辑工作区。

## <a name="best-practices-for-git-integration"></a>Git 集成的最佳做法

-   **权限**。 将 git 存储库连接到工作区后，任何能够访问你的 git 存储库中任何角色的 git 存储库的用户都可以在 git 模式下更新项目，如 sql 脚本、笔记本、spark 作业定义、数据集、数据流和管道。 通常不需要每个团队成员都有权更新工作区。 仅授予 Synapse 工作区项目作者的 git 存储库权限。 
-   **协作**。 建议不要允许直接签入到协作分支。 此限制有助于防止出现 bug，因为每个签入都将经历[创建功能分支](source-control.md#creating-feature-branches)中描述的拉取请求审阅过程。
-   **Synapse 实时模式**。 在 git 模式下发布后，所有更改将反映在 Synapse 实时模式下。 在 Synapse 实时模式下，已禁用发布。 如果你已被授予正确的权限，则可以查看在实时模式下运行项目。 
-   **在工作室中编辑项目**。 仅可通过 Synapse studio 启用工作区源控件并自动将更改同步到 git。 通过 SDK、PowerShell 进行的任何更改都不会同步到 git。 建议你始终在启用 git 后在 Studio 中编辑项目。

## <a name="troubleshooting-git-integration"></a>Git 集成故障排除

### <a name="access-to-git-mode"></a>访问 git 模式 

如果已向你授予与工作区链接的 GitHub git 存储库的权限，但不能访问 Git 模式： 

1. 清除缓存并刷新页面。 

1. 登录 GitHub 帐户。

### <a name="stale-publish-branch"></a>过时的发布分支

如果发布分支与协作分支不同步并且包含过期资源（尽管最近发布），请尝试执行以下步骤：

1. 删除当前的 Git 存储库

1. 用相同的设置重新配置 Git，但请确保已选中 " **将现有资源导入存储库** "，并选择相同的分支。  

1. 创建拉取请求以将更改合并到协作分支 

## <a name="unsupported-features"></a>不支持的功能

- Synapse Studio 不允许挑拣提交或选择性地发布资源。 
- Synapse Studio 不支持自定义提交消息。
- 按照设计，Studio 中的 delete 操作将直接提交给 git

## <a name="next-steps"></a>后续步骤

* 若要实现持续集成和部署，请参阅 [持续集成和交付 (CI/CD) ](continuous-integration-deployment.md)。