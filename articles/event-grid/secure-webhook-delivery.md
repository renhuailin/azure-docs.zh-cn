---
title: 在 Azure 事件网格中使用 Azure AD 进行安全的 WebHook 传递
description: 介绍如何将事件传递到受到 Azure Active Directory 通过 Azure 事件网格进行保护的 HTTPS 终结点
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 0d92b89b1df6b6969491d39b04764f15b7a510d1
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108125798"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>将事件发布到受 Azure Active Directory 保护的终结点
本文介绍如何使用 Azure Active Directory (Azure AD) 来保护事件订阅和 Webhook 终结点之间的连接 。 有关 Azure AD 应用程序和服务主体的概述，请参阅 [Microsoft 标识平台 (v2.0) 概述](../active-directory/develop/v2-overview.md)。

本文使用 Azure 门户进行演示，但也可通过 CLI、PowerShell 或 SDK 来启用此功能。

> [!IMPORTANT]
> 2021 年 3 月 30 日，事件创建或更新中引入了附加访问检查，以解决安全漏洞。 订阅服务器客户端的服务主体需要是所有者或已分配有目标应用程序服务主体中的角色。 请按照下面的新说明重新配置 AAD 应用程序。


## <a name="create-an-azure-ad-application"></a>创建 Azure AD 应用程序
通过为受保护的终结点创建 Azure AD 应用程序，向 Azure AD 注册 Webhook。 请参阅[方案：受保护的 Web API](../active-directory/develop/scenario-protected-web-api-overview.md)。 将受保护的 API 配置为通过守护程序应用进行调用。
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>允许事件网格使用 Azure AD 应用程序
本部分说明如何启用事件网格来使用 Azure AD 应用程序。 

> [!NOTE]
> 你必须是 [Azure AD 应用程序管理员角色](../active-directory/roles/permissions-reference.md#all-roles)的成员才能执行此脚本。

### <a name="connect-to-your-azure-tenant"></a>连接到 Azure 租户
首先，使用 `Connect-AzureAD` 命令连接到 Azure 租户。 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>创建 Microsoft.EventGrid 服务主体
运行以下脚本，为 Microsoft.EventGrid 创建服务主体（如果尚不存在）。 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else {
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>为应用程序创建角色   
运行以下脚本，为你的 Azure AD 应用程序创建角色。 在此示例中，角色名称为：AzureEventGridSecureWebhookSubscriber。 修改 PowerShell 脚本的 `$myTenantId` 以使用 Azure AD 租户 ID，并使用 Azure AD 应用程序的对象 ID 修改 `$myAzureADApplicationObjectId`

```PowerShell
# This is your Webhook's Azure AD Application's ObjectId. 
$myWebhookAadApplicationObjectId = "<Your webhook's aad application object id>"

# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhookSubscriber"
       
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.AllowedMemberTypes.Add("User");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
       
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myWebhookAadApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
       
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
} else {      
    # Add our new role to the Azure AD Application
    Write-Host "Creating the Azure Event Grid role in Azure Ad Application: " $myWebhookAadApplicationObjectId
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles

```

### <a name="create-role-assignment-for-the-client-creating-event-subscription"></a>为创建事件订阅的客户端创建角色分配
应在 Webhook Azure AD 应用中为创建事件订阅的 AAD 应用或 AAD 用户创建角色分配。 根据是 AAD 应用还是 AAD 用户在创建事件订阅，使用以下脚本之一。

> [!IMPORTANT]
> 2021 年 3 月 30 日，事件创建或更新中引入了附加访问检查，以解决安全漏洞。 订阅服务器客户端的服务主体需要是所有者或已分配有目标应用程序服务主体中的角色。 请按照下面的新说明重新配置 AAD 应用程序。

#### <a name="create-role-assignment-for-an-event-subscription-aad-app"></a>为事件订阅 AAD 应用创建角色分配 

```powershell
# This is the app id of the application which will create event subscription. Set to $null if you are not assigning the role to app.
$eventSubscriptionWriterAppId = "<the app id of the application which will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")

$eventSubscriptionWriterSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventSubscriptionWriterAppId + "'")
if ($eventSubscriptionWriterSP -eq $null)
{
        $eventSubscriptionWriterSP = New-AzureADServicePrincipal -AppId $eventSubscriptionWriterAppId
}

Write-Host "Creating the Azure Ad App Role assignment for application: " $eventSubscriptionWriterAppId
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="create-role-assignment-for-an-event-subscription-aad-user"></a>为事件订阅 AAD 用户创建角色分配 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADUserAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="create-role-assignment-for-event-grid-service-principal"></a>为事件网格服务主体创建角色分配
运行 New-AzureADServiceAppRoleAssignment 命令，将事件网格服务主体分配给你在上一步中创建的角色。

```powershell
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

运行以下命令以输出稍后将使用的信息。

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>配置事件订阅
创建事件订阅时，请执行以下步骤：

1. 选择“Web Hook”作为终结点类型。 
1. 指定终结点 URI。

    ![选择终结点类型 webhook](./media/secure-webhook-delivery/select-webhook.png)
1. 选择“创建事件订阅”页顶部的“其他功能”选项卡 。
1. 在“其他功能”选项卡上，执行以下步骤：
    1. 选择“使用 AAD 身份验证”，并配置租户 ID 和应用程序 ID：
    1. 从脚本输出中复制 Azure AD 租户 ID，将其输入“AAD 租户 ID”字段中。
    1. 从脚本输出中复制 Azure AD 应用程序 ID，将其输入“AAD 应用程序 ID”字段中。 或者，可以使用 AAD 应用程序 ID URI。 有关应用程序 ID URI 的详细信息，请参阅[本文](../app-service/configure-authentication-provider-aad.md)。

        ![保护 Webhook 操作](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>后续步骤

* 有关监视事件传送的信息，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 有关身份验证密钥的详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。