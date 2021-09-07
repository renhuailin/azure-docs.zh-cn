---
title: 使用 Azure Pipelines 部署自定义策略
titleSuffix: Azure AD B2C
description: 了解如何使用 Azure Pipelines 在 CI/CD 管道中部署 Azure AD B2C 自定义策略。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/26/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d24e1cf8394b697348492ffcddc4634646ebbbb6
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967082"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>使用 Azure Pipelines 部署自定义策略

[Azure Pipelines](/azure/devops/pipelines) 支持持续集成 (CI) 以及持续交付 (CD)，以持续不断地进行测试、编译并将其交付到任意目标。 本文介绍如何使用 Azure Pipelines 对 B2C (Azure AD B2C) [自定义策略](user-flow-overview.md)的部署进程进行自动化。

> [!IMPORTANT]
> 借助 Azure Pipelines 管理 Azure AD B2C 自定义策略目前使用 Microsoft Graph API `/beta` 终结点上提供的预览操作。 不支持在生产应用程序中使用这些 API。 有关详细信息，请参阅 [Microsoft Graph REST API beta 版本终结点参考](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true)。

## <a name="prerequisites"></a>先决条件

* 完成 [Active Directory B2C 中的自定义策略入门](tutorial-create-user-flows.md)中的步骤。
* 如果你尚未创建 DevOps 组织，请按照[注册、登录到 Azure DevOps](/azure/devops/user-guide/sign-up-invite-teammates) 中的说明创建一个。  

## <a name="register-an-application-for-management-tasks"></a>为管理任务注册应用程序

使用 PowerShell 脚本部署 Azure AD B2C 策略。 在 PowerShell 脚本可以与 [Microsoft Graph API](microsoft-graph-operations.md) 交互之前，请在你的 Azure AD B2C 租户中创建应用程序注册。 如果你尚未进行这一步，请[注册 Microsoft Graph 应用程序](microsoft-graph-get-started.md)。

若要让 PowerShell 脚本访问 MS Graph 中的数据，请向已注册的应用程序授予相关[应用程序权限](/graph/permissions-reference)。 在应用程序注册的“API 权限”内授予了“Microsoft Graph” > “策略” > “Policy.ReadWrite.TrustFramework”权限。

## <a name="configure-an-azure-repo"></a>配置 Azure 存储库

注册 Microsoft Graph 应用程序后，便可以为策略文件配置存储库。

1. 登录到你的 [Azure DevOps 组织](https://azure.microsoft.com/services/devops/)。
1. [创建新项目][devops-create-project]，或选择现有项目。
1. 在你的项目中，导航到“存储库”，然后选择“文件”。 
1. 选择现有存储库，或创建一个。
1. 在你存储库的根目录中，创建一个名为 `B2CAssets` 的文件夹。 将你的 Azure AD B2C 自定义策略文件添加到“B2CAssets”文件夹。
1. 在你存储库的根目录中，创建一个名为 `Scripts` 的文件夹。 创建一个 PowerShell 文件“DeployToB2C.ps1”。 将以下 PowerShell 脚本粘贴到“DeployToB2C.ps1”中。 
1. 提交并推送更改。

以下脚本从 Azure AD 获取访问令牌。 借助令牌，该脚本将调用 MS 图形 API 来上传“B2CAssets”文件夹中的策略。 你还可以在上载之前更改策略的内容。 例如，将 `tenant-name.onmicrosoft.com` 替换为你的租户名称。

```PowerShell
[Cmdletbinding()]
Param(
    [Parameter(Mandatory = $true)][string]$ClientID,
    [Parameter(Mandatory = $true)][string]$ClientSecret,
    [Parameter(Mandatory = $true)][string]$TenantId,
    [Parameter(Mandatory = $true)][string]$Folder,
    [Parameter(Mandatory = $true)][string]$Files
)

try {
    $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

    $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
    $token = $response.access_token

    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Content-Type", 'application/xml')
    $headers.Add("Authorization", 'Bearer ' + $token)

    # Get the list of files to upload
    $filesArray = $Files.Split(",")

    Foreach ($file in $filesArray) {

        $filePath = $Folder + $file.Trim()

        # Check if file exists
        $FileExists = Test-Path -Path $filePath -PathType Leaf

        if ($FileExists) {
            $policycontent = Get-Content $filePath

            # Optional: Change the content of the policy. For example, replace the tenant-name with your tenant name.
            # $policycontent = $policycontent.Replace("your-tenant.onmicrosoft.com", "contoso.onmicrosoft.com")     
    
    
            # Get the policy name from the XML document
            $match = Select-String -InputObject $policycontent  -Pattern '(?<=\bPolicyId=")[^"]*'
    
            If ($match.matches.groups.count -ge 1) {
                $PolicyId = $match.matches.groups[0].value
    
                Write-Host "Uploading the" $PolicyId "policy..."
    
                $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
                $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers
    
                Write-Host "Policy" $PolicyId "uploaded successfully."
            }
        }
        else {
            $warning = "File " + $filePath + " couldn't be not found."
            Write-Warning -Message $warning
        }
    }
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

## <a name="configure-azure-pipelines"></a>配置 Azure Pipelines

存储库已初始化并使用自定义策略文件进行填充后，便可以设置发布管道。 按照以下步骤创建管道：

1. 在项目中，选择“管道” > “发布” > “新建管道”。
1. 在“选择模板”下，选择“空作业”，然后选择“应用”。
1. 输入“阶段名称”（例如 DeployCustomPolicies），然后关闭窗格。
1. 选择“添加项目”，然后在“源类型”下选择“Azure 存储库”  。
    1. 在“项目”这一项中选择你的项目。
    1. 选择包含“脚本”文件夹的“源（存储库）”。
    1. 选择“默认分支”，例如“master”。
    1. 将“默认版本”设置保留为“默认分支中的最新内容”。
    1. 为存储库的“源别名”。 例如，policyRepo。 
1. 选择“添加”
1. 重命名管道以反映其意向。 例如“部署自定义策略管道”。
1. 选择“保存”以保存管道配置。

### <a name="configure-pipeline-variables"></a>配置管道变量

管道变量为你提供了一种简便方法，可以将关键数据位导入管道的各个部分。 以下变量提供有关你 Azure AD B2C 环境的信息。

| 名称 | 值 |
| ---- | ----- |
| `clientId` | 先前注册的应用程序的应用程序（客户端）ID。 |
| `clientSecret` | 先前创建的客户端密码的值。 <br /> 将变量类型更改为“机密”（选择锁定图标）。 |
| `tenantId` | `your-b2c-tenant.onmicrosoft.com`，其中 your-b2c-tenant 是 Azure AD B2C 租户的名称。 |

请按照以下步骤添加管道变量：

1. 在你的管道中，选择“变量”选项卡。
1. 在“管道变量”下，添加上面的变量以及它们的值。
1. 选择“保存”以保存变量。

### <a name="add-pipeline-tasks"></a>添加管道任务

管道任务是一种用于执行操作的预打包脚本。 添加一个调用“DeployToB2C.ps1”PowerShell 脚本的任务。

1. 在你创建的管道中，选择“任务”选项卡。
1. 选择“代理作业”，然后选择加号 (+) 将任务添加到代理作业 。
1. 搜索并选择“PowerShell”。 请勿选择“Azure PowerShell”、“目标计算机上的 PowerShell”或其他 PowerShell 条目。
1. 选择新添加的“PowerShell 脚本”任务。
1. 为 PowerShell 脚本任务输入以下值：
    * 任务版本：2.*
    * 显示名称：此任务应上传的策略的名称。 例如，B2C_1A_TrustFrameworkBase。
    * 类型：文件路径
    * 脚本路径：选择省略号 (...)，导航到 Scripts 文件夹，然后选择 DeployToB2C.ps1 文件 。
    * 参数：输入以下 PowerShell 脚本。 


        ```PowerShell
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -Folder $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/ -Files "TrustFrameworkBase.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml,ProfileEdit.xml,PasswordReset.xml"
        ```
        
        `-Files` 参数是要部署的策略文件的逗号分隔列表。 使用策略文件更新该列表。
        
        > [!IMPORTANT]
        >  确保按正确的顺序上传策略。 先是基本策略、扩展策略，然后是信赖方策略。 例如，`TrustFrameworkBase.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml`。
        
1. 选择“保存”以保存代理作业。

## <a name="test-your-pipeline"></a>测试管道

若要测试发布管道，请执行以下操作：

1. 选择“管道”，然后选择“发布”。 
1. 选择之前创建的管道，例如 DeployCustomPolicies。
1. 选择“创建发布”，然后选择“创建”以对发布进行排队 。

应该会看到一个通知横幅，指出发布已排入队列。 若要查看其状态，请在通知横幅中选择链接，或在“发布”选项卡上的列表中选择它。


## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

* [使用客户端凭据进行服务到服务调用](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
