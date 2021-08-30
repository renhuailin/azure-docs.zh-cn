---
title: 对 Azure 虚拟桌面入门功能进行故障排除
description: 如何对 Azure 虚拟桌面入门功能的问题进行故障排除。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 21cdccf5a025db4516d53c506c070fcf6f580fb1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778634"
---
# <a name="troubleshoot-the-azure-virtual-desktop-getting-started-feature"></a>对 Azure 虚拟桌面入门功能进行故障排除

Azure 虚拟桌面入门功能使用嵌套模板部署 Azure 资源，以在 Azure 虚拟桌面中进行验证和自动化。 入门功能根据运行它的订阅是否具有现有的 Active Directory 域服务 (AD DS) 或 Azure Active Directory 域服务 (Azure AD DS) 创建两个或三个资源组。 所有资源组以相同的用户定义前缀开头。

运行嵌套模板时，它们会创建三个资源组和一个模板，用于预配 Azure 资源管理器资源。 以下列表显示了每个资源组及其运行的模板。

以“-deployment”结尾的资源组运行以下模板：

- easy-button-roleassignment-job-linked-template
- easy-button-prerequisitecompletion-job-linked-template
- easy-button-prerequisite-job-linked-template
- easy-button-inputvalidation-job-linked-template
- easy-button-deploymentResources-linked-template
- easy-button-prerequisite-user-setup-linked-template

>[!NOTE]
>easy-button-prerequisite-user-setup-linked-template 是可选模板，仅会在创建了验证用户的情况下显示。

以“-wvd”结尾的资源组运行以下模板：

- NSG-linkedTemplate
- vmCreation-linkedTemplate
- Workspace-linkedTemplate
- wvd-resources-linked-template
- easy-button-wvdsetup-linked-template

以“-prerequisite”结尾的资源组运行以下模板：

- easy-button-prerequisite-resources-linked-template

>[!NOTE]
>此资源组是可选的，仅会在订阅没有 Azure AD DS 或 AD DS 的情况下显示。

## <a name="no-subscriptions"></a>无订阅

在此问题中，打开入门功能时会看到一条错误消息，显示“无订阅”。 尝试在没有有效 Azure 订阅的情况下打开该功能时，会发生这种情况。

若要解决此问题，请检查订阅或受影响的用户是否具有有效的 Azure 订阅。 如果没有，请在订阅中为用户分配基于所有者角色的访问控制 (RBAC) 角色。

## <a name="you-dont-have-permissions"></a>你没有权限

此问题在打开入门功能时发生，并会显示错误消息“你没有权限”。 当运行该功能的用户对有效的 Azure 订阅没有所有者权限时，将显示此消息。

若要解决此问题，请使用具有所有者权限的 Azure 帐户登录，然后将“所有者 RBAC”角色分配给受影响的帐户。

## <a name="fields-under-virtual-machine-tab-are-grayed-out"></a>“虚拟机”选项卡下的字段显示为灰色

此问题在打开“虚拟机”选项卡时发生，并且“是否希望用户共享此计算机”下的字段 显示为灰色。然后，此问题会阻止你更改映像类型、选择要使用的映像或更改 VM 大小。

使用已用于启动部署的前缀运行该功能时，会发生此问题。 当该功能创建部署时，它会创建一个对象来表示 Azure 中的部署。 对象中的某些值（如映像）会附加到该对象，以防止多个对象使用相同的映像。

若要解决此问题，可以删除具有现有前缀的所有资源组或使用新前缀。

## <a name="username-must-not-include-reserved-words"></a>用户名不能包含保留字

当入门功能不接受在字段中输入的新用户名时，会发生此问题。

出现此错误消息是因为 Azure 不允许公共终结点的用户名中出现某些字词。 若要获取被阻止字词的完整列表，请参阅[解决保留的资源名称错误](../azure-resource-manager/templates/error-reserved-resource-name.md)。

若要解决此问题，请尝试使用新字词，或向被阻止的字词添加字母，使其具有唯一性。 例如，如果“admin”一词被阻止，请尝试改用“AVDadmin”。

## <a name="the-value-must-be-between-12-and-72-characters-long"></a>值的长度必须介于 12 到 72 个字符

如果输入的密码太长或太短，无法满足字符长度要求，便会出现此错误消息。 Azure 密码长度和复杂性要求甚至适用于稍后在 Windows 使用的字段，尽管这些字段的要求不太严格。

若要解决此问题，请确保使用遵循[Microsoft 密码准则](https://www.microsoft.com/research/publication/password-guidance)的帐户，或使用 [Azure AD 密码保护](../active-directory/authentication/concept-password-ban-bad.md)。

## <a name="error-messages-for-easy-button-prerequisite-user-setup-linked-template"></a>easy-button-prerequisite-user-setup-linked-template 的错误消息

如果使用的 AD DS VM 已有一个名为 Microsoft.Powershell.DSC 的扩展与之关联，则会看到如下所示的错误消息：

```azure
"error": {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.",
        "details": [
            {
                "code": "Conflict",
                "message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \\\"DSC Configuration 'AddADDSUser' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: Some error occurred in DSC CreateUser SetScript: \\r\\n\\r\\nException             : Microsoft.ActiveDirectory.Management.ADIdentityNotFoundException: Cannot find an object with \\r\\n                        identity: 'Adam S' under: 'DC=GT090617,DC=onmicrosoft,DC=com'.\\r\\n                           at Microsoft.ActiveDirectory.Management.Commands.ADFactoryUtil.GetObjectFromIdentitySearcher(\\r\\n                        ADObjectSearcher searcher, ADEntity identityObj, String searchRoot, AttributeSetRequest attrs, \\r\\n                        CmdletSessionInfo cmdletSessionInfo, String[]& warningMessages)\\r\\n                           at \\r\\n                        Microsoft.ActiveDirectory.Management.Commands.ADFactory`1.GetDirectoryObjectFromIdentity(T \\r\\n                        identityObj, String searchRoot, Boolean showDeleted)\\r\\n                           at \\r\\n                        Microsoft.ActiveDirectory.Management.Commands.SetADGroupMember`1.ValidateMembersParameter()\\r\\nTargetObject          : Adam S\\r\\nCategoryInfo          : ObjectNotFound: (Adam S:ADPrincipal) [Add-ADGroupMember], ADIdentityNotFoundException\\r\\nFullyQualifiedErrorId : SetADGroupMember.ValidateMembersParameter,Microsoft.ActiveDirectory.Management.Commands.AddADGro\\r\\n                        upMember\\r\\nErrorDetails          : \\r\\nInvocationInfo        : System.Management.Automation.InvocationInfo\\r\\nScriptStackTrace      : at <ScriptBlock>, C:\\\\Packages\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.83.1.0\\\\DSCWork\\\\DSCADUserCreatio\\r\\n                        nScripts_2020-04-28.2\\\\Script-CreateADDSUser.ps1: line 98\\r\\n                        at <ScriptBlock>, <No file>: line 8\\r\\n                        at ScriptExecutionHelper, C:\\\\Windows\\\\system32\\\\WindowsPowerShell\\\\v1.0\\\\Modules\\\\PSDesiredStateConfi\\r\\n                        guration\\\\DscResources\\\\MSFT_ScriptResource\\\\MSFT_ScriptResource.psm1: line 270\\r\\n                        at Set-TargetResource, C:\\\\Windows\\\\system32\\\\WindowsPowerShell\\\\v1.0\\\\Modules\\\\PSDesiredStateConfigur\\r\\n                        ation\\\\DscResources\\\\MSFT_ScriptResource\\\\MSFT_ScriptResource.psm1: line 144\\r\\nPipelineIterationInfo : {}\\r\\nPSMessageDetails      : \\r\\n\\r\\n\\r\\n\\r\\n  The SendConfigurationApply function did not succeed.\\\"\\r\\n\\r\\nMore information on troubleshooting is available at https://aka.ms/VMExtensionDSCWindowsTroubleshoot \"\r\n      }\r\n    ]\r\n  }\r\n}"
            }
        ]
    }

```

若要解决此问题，请卸载 Microsoft.Powershell.DSC 扩展，然后再次运行入门功能。

## <a name="error-messages-for-easy-button-prerequisite-job-linked-template"></a>easy-button-prerequisite-job-linked-template 的错误消息

如果看到如下所示的错误消息，则意味着 easy-button-prerequisite-job-linked-template 模板的资源操作未成功完成：

```azure
{
    "status": "Failed",
    "error": {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.",
        "details": [
            {
                "code": "Conflict",
                "message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\"\r\n  }\r\n}"
            }
        ]
    }
}

```

若要确保这是要处理的问题，请进行以下操作：

1. 选择“easy-button-prerequisite-job-linked-template”，然后在弹出的错误消息窗口中选择“确定” 。

2. 转到“\<prefix\>-deployment 资源组”，然后选择“resourceSetupRunbook” 。

3. 选择状态，该状态应显示“失败”。

4. 选择“异常”选项卡。应会看到如下所示的错误消息：
   
   ```azure
   The running command stopped because the preference variable "ErrorActionPreference" or common parameter is set to Stop: Error while creating and adding validation user <your-username-here> to group <your-resource-group-here>
   ```

目前无法永久解决此问题。 变通方法是再次运行 Azure 虚拟桌面入门功能，但这一次不要创建验证用户。 之后，请仅使用手动过程创建新用户。

### <a name="validate-that-the-domain-administrator-upn-exists-for-a-new-profile"></a>验证新配置文件是否存在域管理员 UPN

检查 UPN 地址是否导致模板问题：

1. 选择“easy-button-prerequisite-job-linked-template”，然后选择失败的步骤。 确认错误消息。

2. 导航到“\<prefix\>-deployment 资源组”，然后单击“resourceSetupRunbook” 。

3. 选择状态，该状态应显示“失败”。

4. 选择“输出”选项卡。

如果新订阅中存在 UPN，则此问题有两个可能的原因：

- 入门功能未创建域管理员配置文件，因为用户已存在。 若要解决此问题，请再次运行入门功能，但这次请输入标识提供者中不存在的用户名。
- 入门功能未创建验证用户配置文件。 若要解决此问题，请再次运行入门功能，但这次不要创建任何验证用户。 之后，请仅使用手动过程创建新用户。

## <a name="error-messages-for-easy-button-inputvalidation-job-linked-template"></a>easy-button-inputvalidation-job-linked-template 的错误消息

如果 easy-button-inputvalidation-job-linked-template 模板存在问题，则会看到如下所示的错误消息：

```azure
{
    "status": "Failed",
    "error": {
        "code": "ResourceDeploymentFailure",
        "message": "The resource operation completed with terminal provisioning state 'Failed'."
    }
}
```

若要确保这是遇到的问题，请进行以下操作：

1. 打开 \<prefix\>-deployment 资源组并查找 inputValidationRunbook。

2. 在"最近的作业"下，会看到状态为“失败”的作业。 单击“失败”。

3. 在“作业详细信息”窗口中，选择“异常” 。

输入的 Azure 管理员 UPN 不正确时，会发生此错误。 若要解决此问题，请确保输入正确的用户名和密码，然后重试。

## <a name="multiple-vmextensions-per-handler-not-supported"></a>不支持一个处理程序有多个 VMExtension

在具有 Azure AD DS 或 AD DS 的订阅中运行入门功能时，该功能将使用 Microsoft.Powershell.DSC 扩展来创建验证用户并配置 FSLogix。 但是，Azure 中的 Windows VM 不能同时运行多个相同类型的扩展。

如果尝试运行多个版本的 Microsoft.Powershell.DSC，将看到如下所示的错误消息：

```azure
{
    "status": "Failed",
    "error": {
        "code": "BadRequest",
        "message": "Multiple VMExtensions per handler not supported for OS type 'Windows'. VMExtension 'Microsoft.Powershell.DSC' with handler 'Microsoft.Powershell.DSC' already added or specified in input."
    }
}
```

若要解决此问题，在运行入门功能之前，请确保从域控制器 VM 中删除任何当前正在运行的 Microsoft.Powershell.DSC 实例。

## <a name="failure-in-easy-button-prerequisitecompletion-job-linked-template"></a>easy-button-prerequisitecompletion-job-linked-template 中的故障

验证用户的用户组位于“USERS”容器中。 但是，用户组必须同步到 Azure AD 才能正常工作。 如果没有进行同步，则会显示如下所示的错误消息：

```azure
{
    "status": "Failed",
    "error": {
        "code": "ResourceDeploymentFailure",
        "message": "The resource operation completed with terminal provisioning state ‘Failed’."
    }
}
```

若要确保此问题是验证用户组未同步引起的，请打开 \<prefix\>-prerequisites 资源组，并查找名为“prerequisiteSetupCompletionRunbook”的文件。 选择 Runbook，然后选择“所有日志”。

若要解决此问题，请执行下列操作：

1. 为“USERS”容器启用与 Azure AD 的同步。

2. 在与 Azure 同步的组织单位中创建 AVDValidationUsers 组。

## <a name="next-steps"></a>后续步骤

若要详细了解入门功能，请参阅 [使用入门功能部署 Azure 虚拟桌面](getting-started-feature.md)。
