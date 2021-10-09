---
title: Synapse Studio 中的源代码管理
description: 了解如何在 Azure Synapse Studio 中配置源代码管理
author: liudan66
ms.service: synapse-analytics
ms.subservice: cicd
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 5b03a7c713203dd61eb95fd5422b3002939e9011
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208730"
---
# <a name="source-control-in-synapse-studio"></a>Synapse Studio 中的源代码管理

默认情况下，Synapse Studio 直接面向 Synapse 服务进行创作。 如果你在使用 Git 进行源代码管理方面需要协作，则可使用 Synapse Studio 将工作区与 Git 存储库、Azure DevOps 或 GitHub 相关联。 

本文概述如何在启用了 git 存储库的 Synapse 工作区中进行配置和工作。 此外，我们还重点介绍一些最佳做法和故障排除指南。

> [!NOTE]
> Synapse Studio git 集成在 Azure 政府云中不可用。

## <a name="prerequisites"></a>先决条件
用户必须在 Synapse 工作区中具有 Azure 参与者 (Azure RBAC) 或更高级角色才能配置、编辑设置以及断开 Git 存储库与 Synapse 的连接。 

## <a name="configure-git-repository-in-synapse-studio"></a>在 Synapse Studio 中配置 Git 存储库 

启动 Synapse Studio 后，可以在工作区中配置 git 存储库。 一次只能将一个 Synapse Studio 工作区与一个 git 存储库相关联。 

### <a name="configuration-method-1-global-bar"></a>配置方法 1：全局栏

在 Synapse Studio 全局栏中，选择“Synapse 实时”下拉菜单，然后选择“设置代码存储库”。

![通过创作配置代码存储库设置](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>配置方法 2：管理中心

转到 Synapse Studio 的管理中心。 选择“源代码管理”部分的“Git 配置”。 如果没有连接的存储库，请单击“配置”。

![通过管理中心配置代码存储库设置](media/configure-repo-2.png)

你可以在工作区中连接 Azure DevOps 或 GitHub git 存储库。

## <a name="connect-with-azure-devops-git"></a>连接 Azure DevOps Git 

你可以将 Synapse 工作区与 Azure DevOps 存储库相关联，以进行源代码管理、协作、版本控制，等等。 如果没有 Azure DevOps 存储库，请按照[这些说明](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)创建存储库资源。

### <a name="azure-devops-git-repository-settings"></a>Azure DevOps Git 存储库设置

连接到 git 存储库时，请先选择“Azure DevOps git”作为存储库类型，接着从下拉列表中选择一个 Azure AD 租户，然后单击“继续”。

![配置存储库设置](media/connect-with-azuredevops-repo-selected.png)

配置窗格会显示以下 Azure DevOps git 设置：

| 设置 | 说明 | 值 |
|:--- |:--- |:--- |
| **存储库类型** | Azure Repos 代码存储库的类型。<br/> | Azure DevOps Git 或 GitHub |
| **Azure Active Directory** | Azure AD 租户的名称。 | `<your tenant name>` |
| **Azure DevOps 帐户** | Azure Repos 组织名称。 可以在 `https://{organization name}.visualstudio.com` 中找到 Azure Repos 组织名称。 可以[登录到 Azure Repos 组织](https://www.visualstudio.com/team-services/git/)来访问 Visual Studio 配置文件并查看存储库和项目。 | `<your organization name>` |
| **ProjectName** | Azure Repos 项目名称。 可以在 `https://{organization name}.visualstudio.com/{project name}` 中找到 Azure Repos 项目名称。 | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos 代码存储库名称。 Azure Repos 项目包含 Git 存储库，随着项目的发展来管理源代码。 要么创建一个新的存储库，要么使用项目中已有的存储库。 | `<your Azure Repos code repository name>` |
| **协作分支** | 将用于发布的 Azure Repos 协作分支。 默认值为 `master`。 如果希望从其他分支发布资源，可更改此设置。 可以选择现有分支，也可以新建分支 | `<your collaboration branch name>` |
| **根文件夹** | Azure Repos 协作分支中的根文件夹。 | `<your root folder name>` |
| **将现有资源导入到存储库中** | 指定是否从 Synapse Studio 将现有资源导入 Azure Repos Git 存储库。 选中此框可将工作区资源（池除外）导入 JSON 格式的关联 Git 存储库。 此操作单独导出每个资源。 如果未选中此框，则不能导入现有的资源。 | 已选中（默认设置） |
| **将资源导入此分支** | 选择要将资源（SQL 脚本、笔记本、Spark 作业定义、数据集、数据流等）导入到其中的分支。 

你还可以使用存储库链接快速指向要连接的 git 存储库。 

### <a name="use-a-different-azure-active-directory-tenant"></a>使用不同的 Azure Active Directory 租户

Azure Repos Git 存储库可以位于不同的 Azure Active Directory 租户中。 若要指定不同的 Azure AD 租户，必须对所用 Azure 订阅拥有管理员权限。 有关详细信息，请参阅[更改订阅管理员](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)

> [!IMPORTANT]
> 若要连接到另一个 Azure Active Directory，登录的用户必须是该 Active Directory 的一部分。 

### <a name="use-your-personal-microsoft-account"></a>使用 Microsoft 个人帐户

若要将 Microsoft 个人帐户用于 Git 集成，可以将你的 Azure 个人存储库链接到贵组织的 Active Directory。

1. 将你的 Microsoft 个人帐户添加到贵组织的 Active Directory 以作为来宾。 有关详细信息，请参阅[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../../active-directory/external-identities/add-users-administrator.md)。

2. 使用 Microsoft 个人帐户登录 Azure 门户。 然后切换到贵组织的 Active Directory。

3. 请转到 Azure DevOps 部分，现在你可以在其中看到个人存储库。 选择存储库并连接到 Active Directory。

执行这些配置步骤之后，在 Synapse Studio 中设置 Git 集成时个人存储库可用。

若要详细了解如何将 Azure Repos 连接到组织的 Active Directory，请参阅[将组织连接到 Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)。

## <a name="connect-with-github"></a>连接 GitHub 

 你可以将工作区与 GitHub 存储库相关联，以进行源代码管理、协作和版本控制。 如果你没有 GitHub 帐户或存储库，请按照[这些说明](https://github.com/join)创建资源。

GitHub 与 Synapse Studio 的集成支持公共 GitHub（即 [https://github.com](https://github.com)）和 GitHub Enterprise。 只要你对 GitHub 中的存储库具有读写权限，就可以将公共和专用 GitHub 存储库一起使用。

### <a name="github-settings"></a>GitHub 设置

连接到 git 存储库时，首先选择存储库类型作为 GitHub，然后提供 GitHub 帐户、GitHub Enterprise 服务器 URL（如果使用的是 GitHub Enterprise 服务器）或 GitHub Enterprise 组织名称（如果使用的是 GitHub Enterprise Cloud）。 选择“继续”。

> [!NOTE]
> 如果使用的是 GitHub Enterprise Cloud，请清除“使用 GitHub Enterprise 服务器”复选框。 

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
| **将现有资源导入到存储库中** | 指定是否从 Synapse Studio 将现有资源导入 Git 存储库。 选中此框可将工作区资源（池除外）导入 JSON 格式的关联 Git 存储库。 此操作单独导出每个资源。 如果未选中此框，则不能导入现有的资源。 | 已选择（默认） |
| **将资源导入此分支** | 选择要将资源（SQL 脚本、笔记本、Spark 作业定义、数据集、数据流等）导入到其中的分支。 

### <a name="github-organizations"></a>GitHub 组织

若要连接到 GitHub 组织，需要组织授予对 Synapse Studio 的权限。 在组织中拥有管理员权限的用户必须执行以下步骤。

#### <a name="connecting-to-github-for-the-first-time"></a>首次连接到 GitHub

如果是第一次从 Synapse Studio 连接到 GitHub，请按照以下步骤连接到 GitHub 组织。

1. 在“Git 配置”窗格的“GitHub 帐户”字段中，输入组织名称。 此时会显示一个登录 GitHub 的提示。 

1. 使用用户凭据登录。 

1. 系统会要求你授权 Synapse 作为名为“Azure Synapse”的应用程序。 在此屏幕上，你会看到一个授予 Synapse 访问组织的权限的选项。 如果看不到授予权限的选项，请要求管理员通过 GitHub 手动授予权限。

执行这些步骤后，工作区将能够连接到组织中的公共和专用存储库。 如果无法进行连接，请尝试清除浏览器缓存，然后重试。

#### <a name="already-connected-to-github-using-a-personal-account"></a>已使用个人帐户连接到 GitHub

如果你已连接到 GitHub，但仅授予了访问个人帐户的权限，请按照以下步骤授予访问组织的权限。

1. 转到 GitHub，打开“设置”。

    ![打开 GitHub 设置](media/github-settings.png)

1. 选择“应用程序”。 在“授权的 OAuth 应用”选项卡中，应会看到 Azure Synapse。

    ![授权 OAuth 应用](media/authorize-app.png)

1. 选择“Azure Synapse”并授予其对组织的访问权限。

    ![授予组织权限](media/grant-organization-permission.png)

完成这些步骤后，工作区将能够连接到组织中的公共和专用存储库。

## <a name="version-control"></a>版本控制

版本控制系统（也称为“源代码管理”）允许开发人员协作编码并跟踪更改。源代码管理是用于多开发人员项目的重要工具。

### <a name="creating-feature-branches"></a>创建功能分支

与 Synapse Studio 关联的每个 Git 存储库都有一个协作分支。 （默认协作分支为 `main` 或 `master`）。 用户还可以通过单击分支下拉列表中的“+ 新建分支”来创建功能分支。 

![创建新分支](media/create-new-branch.png)

“新建分支”窗格出现后，输入功能分支的名称，然后选择一个分支作为工作的基础。

![基于专用分支创建分支 ](media/create-branch-from-private-branch.png)

准备好将功能分支的更改合并到协作分支时，单击分支下拉列表并选择“创建拉取请求”。 此操作会将你转到 Git 提供程序，你可以在其中发起拉取请求、完成代码评审，以及将更改合并到协作分支。 只能从协作分支发布到 Synapse 服务。 

![创建新的拉取请求](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>配置发布设置

默认情况下，Synapse Studio 会生成工作区模板，并将它们保存到名为 `workspace_publish` 的分支中。 若要配置自定义发布分支，请将 `publish_config.json` 文件添加到协作分支的根文件夹中。 发布时，Synapse Studio 会读取此文件，查找字段 `publishBranch`，并将工作区模板文件保存到指定位置。 如果该分支不存在，Synapse Studio 会自动创建它。 此文件类似如下示例：

```json
{
    "publishBranch": "workspace_publish"
}
```

Synapse Studio 一次只能有一个发布分支。 指定新的发布分支时，不会删除以前的发布分支。 如果想要远程删除以前的发布分支，请手动将其删除。


### <a name="publish-code-changes"></a>发布代码更改

将更改合并到协作分支后，请单击“发布”，以便将协作分支中的代码更改手动发布到 Synapse 服务。

![发布更改](media/gitmode-publish.png)

此时会打开一个侧窗格，你可以在其中确认发布分支和挂起的更改是否正确。 验证更改后，单击“确定”以确认发布。

![确认正确的发布分支](media/publish-change.png)

> [!IMPORTANT]
> 协作分支不代表服务中部署的内容。 必须手动发布协作分支中的更改。

## <a name="switch-to-a-different-git-repository"></a>切换到不同 Git 存储库

若要切换到另一个 Git 存储库，请转到“源代码管理”下的管理中心的“Git 配置”页。 选择“断开”。 

![“Git”图标](media/remove-repository.png)

输入工作区名称并单击“断开连接”以删除与工作区关联的 Git 存储库。

删除与当前存储库的关联后，可以将 Git 设置配置为使用不同的存储库，然后将现有资源导入新存储库。

> [!IMPORTANT]
> 从工作区删除 Git 配置不会从存储库中删除任何内容。 Synapse 工作区将包含所有已发布的资源。 你可以继续直接通过该服务编辑工作区。

## <a name="best-practices-for-git-integration"></a>Git 集成的最佳做法

-   **权限**。 将 git 存储库连接到工作区后，任何能够通过工作区中的任何角色访问你的 git 存储库的用户都可以在 git 模式下更新项目，例如 sql 脚本、笔记本、spark 作业定义、数据集、数据流和管道。 通常，你不希望每个团队成员都有更新工作区的权限。 仅将 git 存储库权限授予 Synapse 工作区项目创建者。 
-   **协作**。 建议不要允许直接签入到协作分支。 此限制有助于防止出现 bug，因为每个签入都将经历[创建功能分支](source-control.md#creating-feature-branches)中描述的拉取请求审阅过程。
-   **Synapse 实时模式**。 在 git 模式下发布后，所有更改都会反映在 Synapse 实时模式下。 在 Synapse 实时模式下，已禁用发布。 如果你已被授予正确的权限，则可以在实时模式下查看、运行项目。 
-   **在 Studio 中编辑项目**。 仅可通过 Synapse Studio 启用工作区源代码管理并自动将更改同步到 git。 通过 SDK、PowerShell 进行的任何更改都不会同步到 git。 建议你在启用 git 后始终在 Studio 中编辑项目。

## <a name="troubleshooting-git-integration"></a>排查 git 集成问题

### <a name="access-to-git-mode"></a>访问 git 模式 

如果你已被授权访问与工作区关联的 GitHub git 存储库，但不能访问 Git 模式，则请执行以下操作： 

1. 清除缓存并刷新页面。 

1. 登录 GitHub 帐户。

### <a name="stale-publish-branch"></a>过时的发布分支

如果发布分支与协作分支不同步，并且包含过期资源（尽管最近进行过发布），请尝试执行以下步骤：

1. 删除当前的 Git 存储库

1. 使用相同的设置重新配置 Git，但确保选中“将现有资源导入到存储库”，然后选择同一分支。  

1. 创建拉取请求以将更改合并到协作分支 

## <a name="unsupported-features"></a>不支持的功能

- Synapse Studio 不允许挑拣提交内容或选择性地发布资源。 
- Synapse Studio 不支持自定义提交消息。
- 按照设计，Studio 中的删除操作会直接提交到 git

## <a name="next-steps"></a>后续步骤

* 若要实现持续集成和部署，请参阅[持续集成和交付 (CI/CD)](continuous-integration-delivery.md)。
