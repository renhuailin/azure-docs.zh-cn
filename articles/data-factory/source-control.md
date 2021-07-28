---
title: 源代码管理
description: 了解如何在 Azure 数据工厂中配置源代码管理
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/26/2021
ms.openlocfilehash: 77f5d940c06ef5a2a504033225b42b7ddd2c17c1
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107903265"
---
# <a name="source-control-in-azure-data-factory"></a>Azure 数据工厂中的源代码管理
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

默认情况下，Azure 数据工厂用户界面体验 (UX) 直接面向数据工厂服务。 此体验具有以下限制：

- 数据工厂服务不包括存储所做的更改的 JSON 实体的存储库。 保存更改的唯一方法是通过“全部发布”按钮将所有更改都直接发布到数据工厂服务。
- 数据工厂服务未优化协作和版本控制。
- 部署数据工厂本身所需的 Azure 资源管理器模板没有包括在内。

为了提供更好的创作体验，Azure 数据工厂支持使用 Azure Repos 或 GitHub 配置 Git 存储库。 Git 是一个支持简化变更跟踪和协作的版本控制系统。 本文将概述 Git 存储库的配置和工作原理，并重点介绍最佳做法和故障排除指南。

> [!NOTE]
> 对于 Azure 政府版云，仅 GitHub Enterprise Server 可用。

若要详细了解 Azure 数据工厂如何与 Git 集成，请观看下面时长 15 分钟的教程视频：

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4GNKv]

## <a name="advantages-of-git-integration"></a>Git 集成的优点

以下是 git 集成为创作体验提供的一些优势：

-   **源代码管理：** 随着数据工厂工作负载变得至关重要，你可能需要将工厂与 Git 集成，以利用以下几种源代码管理优势：
    -   跟踪/审核更改的功能。
    -   还原导致 bug 的更改的功能。
-   **部分保存：** 在通过数据工厂服务进行创作时，不能将更改保存为草稿，所有发布都必须通过数据工厂验证。 无论是管道尚未完成还是你只是不想在计算机崩溃时丢失更改，Git 集成都支持对数据工厂资源进行增量更改，无论资源处于何种状态均是如此。 配置 git 存储库可以保存更改，让你可以在执行满意的更改测试后才进行发布。
-   **协作和控制：** 如果有多个团队成员参与同一工厂，则可能需要通过代码评审流程让团队成员相互协作。 你还可以对工厂进行设置，使参与者拥有不同的权限。 可以仅允许某些团队成员通过 Git 进行更改，并只允许团队中的某些人将更改“发布”到工厂。
-   **更好的 CI/CD：** 如果要通过 [持续交付过程](continuous-integration-deployment.md)部署到多个环境中，git 集成可使某些操作更简单。 部分此类操作包括：
    -   将发布管道配置为开发工厂中发生任何更改后立即自动触发。
    -   自定义工厂中可用作资源管理器模板的参数的属性。 仅将所需属性集保留为参数并将其他所有内容进行硬编码可能很有用。
-   **性能更好：** 使用 git 集成的工厂平均加载速度是通过数据工厂服务创作的 10 倍。 性能提升的原因在于资源是通过 Git 下载的。

> [!NOTE]
> 配置有 Git 存储库时，Azure 数据工厂 UX 中将禁用直接使用数据工厂服务进行创作。 通过 PowerShell 或 SDK 进行的更改将直接发布到数据工厂服务，而不会输入到 Git 中。

## <a name="connect-to-a-git-repository"></a>连接到 Git 存储库

可通过四种不同的方法将 Git 存储库连接到 Azure Repos 和 GitHub 的数据工厂。 连接到 Git 存储库后，可以在[管理中心](author-management-hub.md)内“源代码管理”部分中的“Git 配置”下查看和管理配置

### <a name="configuration-method-1-home-page"></a>配置方法 1：主页

在 Azure 数据工厂主页中，选择“设置代码存储库”。

![从主页配置代码存储库](media/author-visually/configure-repo.png)

### <a name="configuration-method-2-authoring-canvas"></a>配置方法 2：创作画布

在 Azure 数据工厂用户体验创作画布中，依次选择“数据工厂”下拉菜单和“设置代码存储库”。

![从创作画布配置代码存储库设置](media/author-visually/configure-repo-2.png)

### <a name="configuration-method-3-management-hub"></a>配置方法 3：管理中心

转到 ADF 用户体验中的管理中心。 选择“源代码管理”部分的“Git 配置”。 如果没有连接的存储库，请单击“配置”。

![通过管理中心配置代码存储库设置](media/author-visually/configure-repo-3.png)

### <a name="configuration-method-4-during-factory-creation"></a>配置方法 4：在创建工厂期间

在 Azure 门户中新建数据工厂时，可以在“Git 配置”选项卡中配置 Git 存储库信息。

> [!NOTE]
> 在 Azure 门户中配置 git 时，必须手动输入项目名称和存储库名称等设置，而不是从下拉列表中选择。

![在 Azure 门户中配置代码存储库设置](media/author-visually/configure-repo-4.png)

## <a name="author-with-azure-repos-git-integration"></a>使用 Azure Repos Git 集成进行创作

使用 Azure Repos Git 集成进行创作允许在创作数据工厂管道时进行源代码管理和协作。 用户可以选择将数据工厂与 Azure Repos Git 组织存储库相关联，以进行源代码管理、协作和版本控制等。 单个 Azure Repos Git 组织可以有多个存储库，但 Azure Repos Git 存储库只能与一个数据工厂相关联。 如果没有 Azure Repos 组织或存储库，请按照[这些说明](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)创建资源。

> [!NOTE]
> 可以在 Azure Repos Git 存储库中存储脚本和数据文件。 但是，必须手动将文件上传到 Azure 存储。 数据工厂管道不会自动将存储在 Azure Repos Git 存储库中的脚本或数据文件上传到 Azure 存储。

### <a name="azure-repos-settings"></a>Azure Repos 设置

![配置存储库设置](media/author-visually/repo-settings.png)

配置窗格将显示以下 Azure Repos 代码存储库设置：

| 设置 | 说明 | 值 |
|:--- |:--- |:--- |
| **存储库类型** | Azure Repos 代码存储库的类型。<br/> | Azure DevOps Git 或 GitHub |
| **Azure Active Directory** | Azure AD 租户的名称。 | `<your tenant name>` |
| **Azure Repos 组织** | Azure Repos 组织名称。 可以在 `https://{organization name}.visualstudio.com` 中找到 Azure Repos 组织名称。 可以[登录到 Azure Repos 组织](https://www.visualstudio.com/team-services/git/)来访问 Visual Studio 配置文件并查看存储库和项目。 | `<your organization name>` |
| **ProjectName** | Azure Repos 项目名称。 可以在 `https://{organization name}.visualstudio.com/{project name}` 中找到 Azure Repos 项目名称。 | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos 代码存储库名称。 Azure Repos 项目包含 Git 存储库，随着项目的发展来管理源代码。 要么创建一个新的存储库，要么使用项目中已有的存储库。 | `<your Azure Repos code repository name>` |
| **协作分支** | 将用于发布的 Azure Repos 协作分支。 该名称默认为 `main`。 如果希望从其他分支发布资源，可更改此设置。 | `<your collaboration branch name>` |
| **根文件夹** | Azure Repos 协作分支中的根文件夹。 | `<your root folder name>` |
| 确认选中“将现有的数据工厂资源导入存储库”选项。 | 指定是否从 UX 创作画布将现有数据工厂资源导入 Azure Repos Git 存储库。 选择相应的框以将你的数据工厂资源导入 JSON 格式关联的 Git 存储库。 此操作单独导出每个资源（即，链接的服务和数据集导出到单独的 JSON）。 如果未选中此框，不能导入现有的资源。 | 已选择（默认） |
| **要将资源导入到的分支** | 指定要将数据工厂资源（管道、数据集、链接服务等）导入哪个分支。 可将资源导入以下分支之一：a. 协作；b. 新建；c. 使用现有项 |  |

> [!NOTE]
> 如果你使用的是 Microsoft Edge，并且在 Azure DevOps 帐户下拉列表中未看到任何值，请将 https://*.visualstudio.com 添加到受信任站点列表中。

### <a name="use-a-different-azure-active-directory-tenant"></a>使用不同的 Azure Active Directory 租户

Azure Repos Git 存储库可以位于不同的 Azure Active Directory 租户中。 若要指定不同的 Azure AD 租户，必须对所用 Azure 订阅拥有管理员权限。 有关详细信息，请参阅[更改订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator)

> [!IMPORTANT]
> 若要连接到另一个 Azure Active Directory，登录的用户必须是该 Active Directory 的一部分。 

### <a name="use-your-personal-microsoft-account"></a>使用 Microsoft 个人帐户

若要将 Microsoft 个人帐户用于 Git 集成，可以将你的 Azure 个人存储库链接到贵组织的 Active Directory。

1. 将你的 Microsoft 个人帐户添加到贵组织的 Active Directory 以作为来宾。 有关详细信息，请参阅[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../active-directory/external-identities/add-users-administrator.md)。

2. 使用 Microsoft 个人帐户登录 Azure 门户。 然后切换到贵组织的 Active Directory。

3. 请转到 Azure DevOps 部分，现在你可以在其中看到个人存储库。 选择存储库并连接到 Active Directory。

执行这些配置步骤之后，在数据工厂 UI 中设置 Git 集成时个人存储库可用。

有关将 Azure 存储库连接到贵组织的 Active Directory 的详细信息，请参阅[将 Azure DevOps 组织连接到 Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)。

## <a name="author-with-github-integration"></a>使用 GitHub 集成进行创作

使用 GitHub 集成进行创作允许在创作数据工厂管道时进行源代码管理和协作。 用户可以选择将数据工厂与 GitHub 帐户存储库相关联，以进行源代码管理、协作和版本控制。 单个的 GitHub 帐户可以有多个存储库，但 GitHub 存储库可以只有一个数据工厂与相关联。 如果你没有 GitHub 帐户或存储库，请按照[这些说明](https://github.com/join)创建资源。

GitHub 与数据工厂的集成支持公共 GitHub（即 [https://github.com](https://github.com)）和 GitHub Enterprise。 只要你对 GitHub 中的存储库具有读写权限，就可以将公共和专用 GitHub 存储库与数据工厂一起使用。

若要配置 GitHub 存储库，必须对所用 Azure 订阅拥有管理员权限。

### <a name="github-settings"></a>GitHub 设置

![GitHub 存储库设置](media/author-visually/github-integration-image2.png)

配置窗格将显示以下 GitHub 存储库设置：

| **设置** | **说明**  | **值**  |
|:--- |:--- |:--- |
| **存储库类型** | Azure Repos 代码存储库的类型。 | GitHub |
| **使用 GitHub Enterprise** | 用于选择 GitHub Enterprise 的复选框 | 未选择（默认值） |
| **GitHub Enterprise URL** | GitHub Enterprise 根 URL（对于本地 GitHub 企业服务器，必须是 HTTPS）。 例如：`https://github.mydomain.com`。 仅当选择了“使用 GitHub Enterprise”时才需要 | `<your GitHub enterprise url>` |                                                           
| **GitHub 帐户** | GitHub 帐户名。 可从 https:\//github.com/{account name}/{repository name} 找到此名称。 导航到此页时，系统会提示输入 GitHub 帐户的 GitHub OAuth 凭据。 | `<your GitHub account name>` |
| **存储库名称**  | GitHub 代码存储库名称。 GitHub 帐户包含用于管理源代码的 Git 存储库。 可以创建新存储库，或使用帐户中现有的存储库。 | `<your repository name>` |
| **协作分支** | 将用于发布的 GitHub 协作分支。 默认设置为 main。 如果希望从其他分支发布资源，可更改此设置。 | `<your collaboration branch>` |
| **根文件夹** | GitHub 协作分支中的根文件夹。 |`<your root folder name>` |
| 确认选中“将现有的数据工厂资源导入存储库”选项。 | 指定是否将现有数据工厂资源从 UX 创作画布导入到 GitHub 存储库中。 选择相应的框以将你的数据工厂资源导入 JSON 格式关联的 Git 存储库。 此操作单独导出每个资源（即，链接的服务和数据集导出到单独的 JSON）。 如果未选中此框，不能导入现有的资源。 | 已选择（默认） |
| **要将资源导入到的分支** | 指定要将数据工厂资源（管道、数据集、链接服务等）导入哪个分支。 可将资源导入以下分支之一：a. 协作；b. 新建；c. 使用现有项 |  |

### <a name="github-organizations"></a>GitHub 组织

若要连接到 GitHub 组织，组织必须向 Azure 数据工厂授予权限。 在组织中拥有 ADMIN 权限的用户必须执行以下步骤，才能允许数据工厂进行连接。

#### <a name="connecting-to-github-for-the-first-time-in-azure-data-factory"></a>在 Azure 数据工厂中首次连接到 GitHub

如果是首次从 Azure 数据工厂连接到 GitHub，请按照以下步骤操作来连接到 GitHub 组织。

1. 在“Git 配置”窗格的“GitHub 帐户”字段中，输入组织名称。 此时会显示一个登录 GitHub 的提示。 
1. 使用用户凭据登录。
1. 系统会要求你授权 Azure 数据工厂作为名为“AzureDataFactory”的应用程序。 在此屏幕上，你会看到一个选项，用于授予 ADF 对组织的访问权限。 如果看不到授予权限的选项，请要求管理员通过 GitHub 手动授予权限。

在按照这些步骤操作后，工厂将能够连接到组织中的公用和专用存储库。 如果无法进行连接，请尝试清除浏览器缓存，然后重试。

#### <a name="already-connected-to-github-using-a-personal-account"></a>已使用个人帐户连接到 GitHub

如果你已连接到 GitHub，但仅授予了访问个人帐户的权限，请按照以下步骤授予访问组织的权限。 

1. 转到 GitHub，打开“设置”。

    ![打开 GitHub 设置](media/author-visually/github-settings.png)

1. 选择“应用程序”。 在“授权的 OAuth 应用”选项卡中，应会看到“AzureDataFactory”。

    ![选择 OAuth 应用](media/author-visually/github-organization-select-application.png)

1. 选择此应用程序，并授予其对组织的访问权限。

    ![授予访问权限](media/author-visually/github-organization-grant.png)

在按照这些步骤操作后，工厂将能够连接到组织中的公用和专用存储库。 

### <a name="known-github-limitations"></a>已知 GitHub 限制

- 可在 GitHub 存储库中存储脚本和数据文件。 但是，必须手动将文件上传到 Azure 存储。 数据工厂管道不会自动将 GitHub 存储库中存储的脚本或数据文件上传到 Azure 存储。

- 2\.14.0 以下版本的 GitHub Enterprise 在 Microsoft Edge 浏览器中无法正常运行。

- 数据工厂视觉创作工具与 GitHub 的集成仅适用于正式版数据工厂。


- 每个资源类型（如管道和数据集）最多可以从一个 GitHub 分支获取 1000 个实体。 如果达到此限制，建议将资源拆分成单独的工厂。 Azure DevOps Git 没有此限制。

## <a name="version-control"></a>版本控制

版本控制（也称为 _源代码管理_）系统允许开发人员协作处理代码并跟踪对基本代码所做的更改。 源代码管理是用于多开发人员项目的重要工具。

### <a name="creating-feature-branches"></a>创建功能分支

与数据工厂关联的每个 Azure Repos Git 存储库都有一个协作分支。 （默认协作分支为 `main`）。 用户还可以通过单击分支下拉列表中的“+ 新建分支”来创建功能分支。 出现“新建分支”窗格后，输入功能分支的名称。

![创建新分支](media/author-visually/new-branch.png)

准备好将功能分支的更改合并到协作分支时，单击分支下拉列表并选择“创建拉取请求”。 此操作会将你转到 Azure Repos Git，可以在这里发起拉取请求、完成代码评审并将更改合并至协作分支。 （默认分支为 `main`）。 只能从协作分支发布至数据工厂服务。 

![创建新的拉取请求](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>配置发布设置

默认情况下，数据工厂生成已发布工厂的资源管理器模板，并将它们保存到名为 `adf_publish` 的分支中。 若要配置自定义发布分支，请将 `publish_config.json` 文件添加到协作分支的根文件夹中。 发布时，ADF 会读取此文件，查找字段 `publishBranch`，并将所有资源管理器模板保存到指定位置。 如果该分支不存在，数据工厂会自动创建它。 此文件类似如下示例：

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Azure 数据工厂一次只能有一个发布分支。 当指定新的发布分支，数据工厂不会删除以前的发布分支。 如果想要远程删除以前的发布分支，请手动将其删除。

> [!NOTE]
> 加载工厂时数据工厂仅读取 `publish_config.json` 文件。 如果已在门户中加载工厂，请刷新浏览器，以便所做的更改生效。

### <a name="publish-code-changes"></a>发布代码更改

在将更改合并到协作分支（默认为 `main`）后，单击“发布”，以将主分支中的代码更改手动发布到数据工厂服务中。

![将更改发布至数据更改服务](media/author-visually/publish-changes.png)

此时会打开一个侧窗格，你可以在其中确认发布分支和挂起的更改是否正确。 验证更改后，单击“确定”以确认发布。

![确认正确的发布分支](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> 主分支并不代表数据工厂服务中部署的内容。 必须将主分支手动发布到数据工厂服务。

## <a name="best-practices-for-git-integration"></a>Git 集成的最佳做法

### <a name="permissions"></a>权限

通常，你不希望每个团队成员都有更新数据工厂的权限。 建议使用以下权限设置：

*   所有团队成员都应对数据工厂具有读取权限。
*   只允许选定的一组人员发布到数据工厂。 为此，他们必须在包含数据工厂的资源组上具有“数据工厂参与者”角色。 有关权限的详细信息，请参阅 [Azure 数据工厂的角色和权限](concepts-roles-permissions.md)。

建议不要允许直接签入到协作分支。 此限制有助于防止出现 bug，因为每个签入都将经历[创建功能分支](source-control.md#creating-feature-branches)中描述的拉取请求审阅过程。

### <a name="using-passwords-from-azure-key-vault"></a>使用 Azure Key Vault 中的密码

建议使用 Azure Key Vault 存储数据工厂链接服务的任何连接字符串或密码或托管标识身份验证。 出于安全原因，数据工厂不在 Git 中存储机密。 对包含密码等机密的链接服务所做的任何更改都将立即发布到 Azure 数据工厂服务。

使用 Key Vault 或 MSI 身份验证还可以简化持续集成和部署，因为在资源管理器模板部署期间不必提供这些机密。

## <a name="troubleshooting-git-integration"></a>排查 Git 集成问题

### <a name="stale-publish-branch"></a>过时的发布分支

如果发布分支与主分支不同步，并且包含最近发布的过时资源，请尝试执行以下步骤：

1. 删除当前的 Git 存储库
1. 使用相同的设置重新配置 Git，但确保选择了“将现有数据工厂资源导入到存储库”，然后选择“新建分支” 
1. 创建拉取请求以将更改合并到协作分支 

下面是一些可能导致发布分支过时的示例情形：
- 用户有多个分支。 在一个功能分支中，用户删除了一个与 AKV 不关联的链接服务（无论是否在 Git 中，非 AKV 链接服务都会立即发布），并且从未将功能分支合并到协作分支中。
- 用户使用 SDK 或 PowerShell 修改了数据工厂
- 用户已将所有资源移动到新的分支，并尝试首次发布。 导入资源时应手动创建链接服务。
- 用户手动上传非 AKV 链接服务或集成运行时 JSON。 他们从另一个资源（如数据集、链接服务或管道）引用该资源。 通过 UX 创建的非 AKV 链接服务将立即发布，因为凭据需要加密。 如果上传引用该链接服务的数据集并尝试发布，UX 会允许该数据集，因为它存在于 git 环境中。 在发布时该数据集会被拒绝，因为它不存在于数据工厂服务中。

## <a name="switch-to-a-different-git-repository"></a>切换到不同 Git 存储库

若要切换到不同 Git 存储库，请转到管理中心的“源代码管理”下的“Git 配置”页面。 选择“断开”。 

![“Git”图标](media/author-visually/remove-repository.png)

输入数据工厂名称并单击“确认”以删除与数据工厂关联的 Git 存储库。

![删除与当前 Git 存储库的关联](media/author-visually/remove-repository-2.png)

删除与当前存储库的关联后，可以将 Git 设置配置为使用不同的存储库，然后将现有数据工厂资源导入新存储库。

> [!IMPORTANT]
> 从数据工厂删除 Git 配置不会从存储库中删除任何内容。 工厂将包含所有已发布的资源。 你可以直接通过该服务编辑工厂。

## <a name="next-steps"></a>后续步骤

* 若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](monitor-programmatically.md)。
* 若要实现持续集成和部署，请参阅 [Azure 数据工厂中的持续集成和交付 (CI/CD)](continuous-integration-deployment.md)。
