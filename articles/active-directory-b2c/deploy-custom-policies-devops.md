---
title: 使用 Azure Pipelines 部署自定义策略
titleSuffix: Azure AD B2C
description: 了解如何使用 Azure DevOps Services 中的 Azure Pipelines 在 CI/CD 管道中部署 Azure AD B2C 自定义策略。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 913f21b90043209cae1ec9963619389bcb452781
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529425"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>使用 Azure Pipelines 部署自定义策略

通过使用在 [Azure Pipelines][devops-pipelines] 中设置的持续集成和持续交付 (CI/CD) 管道，可在软件交付和代码控件自动化中包含 Azure AD B2C 自定义策略。 部署到不同的 Azure AD B2C 环境（例如开发、测试和生产环境）时，建议删除手动过程，并使用 Azure Pipelines 执行自动化测试。

使 Azure Pipelines 可以在 Azure AD B2C 中管理自定义策略需要执行三个主要步骤：

1. 在 Azure AD B2C 租户中创建 Web 应用程序注册
1. 配置 Azure 存储库
1. 配置 Azure 管道

> [!IMPORTANT]
> 借助 Azure 管道管理 Azure AD B2C 自定义策略目前使用 Microsoft Graph API `/beta` 终结点上提供的预览操作。 不支持在生产应用程序中使用这些 API。 有关详细信息，请参阅 [Microsoft Graph REST API beta 版本终结点参考](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true)。

## <a name="prerequisites"></a>必备条件

* [Azure AD B2C 租户](tutorial-create-tenant.md)，以及目录中具有 [B2C IEF 策略管理员](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)角色的用户的凭据
* 已上传到租户的[自定义策略](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
* 已在你的租户中注册了[管理应用](microsoft-graph-get-started.md)，具有 Microsoft Graph API 权限 Policy.ReadWrite.TrustFramework
* [Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)以及对 [Azure DevOps Services 项目][devops-create-project]的访问权限

## <a name="client-credentials-grant-flow"></a>客户端凭据授予流

此处所述的方案通过 OAuth 2.0 [客户端凭据授予流](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)，在 Azure Pipelines 与 Azure AD B2C 之间使用服务到服务调用。 此授予流允许 Web 服务（如 Azure Pipelines（机密客户端））在调用其他 Web 服务（在此例中为 Microsoft Graph API）时使用它自己的凭据（而不是模拟用户）进行身份验证。 Azure Pipelines 会以非交互方式获取令牌，然后向 Microsoft Graph API 发出请求。

## <a name="register-an-application-for-management-tasks"></a>为管理任务注册应用程序

如[先决条件](#prerequisites)中所述，你需要 PowerShell 脚本（由 Azure Pipelines 执行）可用于访问租户中的资源的应用程序注册。

如果你已有一个用于自动化任务的应用程序注册，请确保在应用注册的“API 权限”中向它授予了“Microsoft Graph” > “策略” > Policy.ReadWrite.TrustFramework”权限。

有关注册管理应用程序的说明，请参阅[使用 Microsoft Graph 管理 Azure AD B2C](microsoft-graph-get-started.md)。

## <a name="configure-an-azure-repo"></a>配置 Azure 存储库

注册管理应用程序后，便可以为策略文件配置存储库。

1. 登录 Azure DevOps Services 组织。
1. [创建新项目][devops-create-project]或选择现有项目。
1. 在项目中，导航到“存储库”，然后选择“文件”页 。 选择现有存储库，或为此练习创建一个。
1. 创建名为 B2CAssets 的文件夹。 将所需占位符文件命名为 README.md 并“提交”文件。 以后可以根据需要删除此文件。
1. 将 Azure AD B2C 策略文件添加到 B2CAssets 文件夹。 这包括 TrustFrameworkBase.xml、TrustFrameWorkExtensions.xml、SignUpOrSignin.xml、ProfileEdit.xml、PasswordReset.xml 和已创建的任何其他策略    。 记录每个 Azure AD B2C 策略文件的文件名，以便在后面的步骤中使用（它们用作 PowerShell 脚本参数）。
1. 在存储库的根目录中创建名为 Scripts 的文件夹，将占位符文件命名为 DeployToB2c.ps1 。 此时请勿提交文件，你将在后面的步骤中执行此操作。
1. 将以下 PowerShell 脚本粘贴到 DeployToB2c.ps1 中，然后“提交”文件。 该脚本会从 Azure AD 获取令牌，并调用 Microsoft Graph API 以将 B2CAssets 文件夹中的策略上传到 Azure AD B2C 租户。

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>配置 Azure 管道

存储库已初始化并使用自定义策略文件进行填充后，便可以设置发布管道。

### <a name="create-pipeline"></a>创建管道

1. 登录 Azure DevOps Services 组织，并导航到你的项目。
1. 在项目中，选择“管道” > “发布” > “新建管道”。
1. 在“选择模板”下，选择“空作业” 。
1. 输入“阶段名称”（例如 DeployCustomPolicies），然后关闭窗格。
1. 选择“添加项目”，然后在“源类型”下选择“Azure 存储库”  。
    1. 选择包含使用 PowerShell 脚本填充的 Scripts 文件夹的源存储库。
    1. 选择“默认分支”。 如果在上一部分中创建了新存储库，则默认分支为 master。
    1. 将“默认版本”设置保留为“默认分支中的最新内容”。
    1. 为存储库的“源别名”。 例如，policyRepo。 请勿在别名中包含任何空格。
1. 选择“添加”
1. 重命名管道以反映其意向。 例如“部署自定义策略管道”。
1. 选择“保存”以保存管道配置。

### <a name="configure-pipeline-variables"></a>配置管道变量

1. 选择“变量”选项卡。
1. 在“管道变量”下添加以下变量并按指定设置其值：

    | 名称 | 值 |
    | ---- | ----- |
    | `clientId` | 先前注册的应用程序的应用程序（客户端）ID。 |
    | `clientSecret` | 先前创建的客户端密码的值。 <br /> 将变量类型更改为“机密”（选择锁定图标）。 |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`，其中 your-b2c-tenant 是 Azure AD B2C 租户的名称。 |

1. 选择“保存”以保存变量。

### <a name="add-pipeline-tasks"></a>添加管道任务

接下来，添加一个用于部署策略文件的任务。

1. 选择“任务”选项卡。
1. 选择“代理作业”，然后选择加号 (+) 将任务添加到代理作业 。
1. 搜索并选择“PowerShell”。 请勿选择“Azure PowerShell”、“目标计算机上的 PowerShell”或其他 PowerShell 条目。
1. 选择新添加的“PowerShell 脚本”任务。
1. 为 PowerShell 脚本任务输入以下值：
    * 任务版本：2.*
    * 显示名称：此任务应上传的策略的名称。 例如，B2C_1A_TrustFrameworkBase。
    * 类型：文件路径
    * 脚本路径：选择省略号 (...)，导航到 Scripts 文件夹，然后选择 DeployToB2C.ps1 文件 。
    * **参数：**

        为参数输入以下值。 将 `{alias-name}` 替换为在上一部分中指定的别名。

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        例如，如果指定的别名为 policyRepo，则参数行应为：

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. 选择“保存”以保存代理作业。

刚添加的任务会将一个策略文件上传到 Azure AD B2C。 继续操作之前，请手动触发作业（“创建发布”）以确保它在创建其他任务之前成功完成。

如果任务成功完成，则通过对每个自定义策略文件执行前面的步骤来添加部署任务。 为每个策略修改 `-PolicyId` 和 `-PathToFile` 参数值。

`PolicyId` 是在 TrustFrameworkPolicy 节点中的 XML 策略文件开头找到的值。 例如，以下策略 XML 中的 `PolicyId` 为 B2C_1A_TrustFrameworkBase：

```xml
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

运行代理并上传策略文件时，请确保按以下顺序上传它们：

1. TrustFrameworkBase.xml
1. TrustFrameworkExtensions.xml
1. SignUpOrSignin.xml
1. ProfileEdit.xml
1. PasswordReset.xml

Identity Experience Framework 会强制执行此顺序，因为文件结构是基于分层链而构建。

## <a name="test-your-pipeline"></a>测试管道

若要测试发布管道，请执行以下操作：

1. 选择“管道”，然后选择“发布”。 
1. 选择之前创建的管道，例如 DeployCustomPolicies。
1. 选择“创建发布”，然后选择“创建”以对发布进行排队 。

应该会看到一个通知横幅，指出发布已排入队列。 若要查看其状态，请在通知横幅中选择链接，或在“发布”选项卡上的列表中选择它。

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

* [使用客户端凭据进行服务到服务调用](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
