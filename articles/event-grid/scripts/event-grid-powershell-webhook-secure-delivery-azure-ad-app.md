---
title: Azure PowerShell - 在 Azure 事件网格中使用 Azure AD 应用程序保障 WebHook 传递的安全
description: 介绍如何使用 Azure 事件网格将事件传送到使用 Azure AD 应用程序保护的 HTTPS 终结点
ms.devlang: powershell
ms.topic: sample
ms.date: 09/29/2021
ms.openlocfilehash: d05b8d5eb42e6b656b07e7d573a4fc67841a071a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621247"
---
# <a name="secure-webhook-delivery-with-azure-ad-application-in-azure-event-grid"></a>在 Azure 事件网格中使用 Azure AD 应用程序保障 WebHook 传递的安全

此脚本提供配置来使用 Azure 事件网格将事件传送到使用 Azure AD 应用程序保护的 HTTPS 终结点。

## <a name="sample-script---stable"></a>示例脚本 - 稳定版

```azurepowershell
# NOTE: Before run this script ensure you are logged in Azure by using "az login" command.

$webhookAppObjectId = "[REPLACE_WITH_YOUR_ID]"
$eventSubscriptionWriterAppId = "[REPLACE_WITH_YOUR_ID]"

# Start execution
try {

    # Creates an application role of given name and description

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

    # Creates Azure Event Grid Azure AD Application if not exists

    $eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7" # You don't need to modify this id
    $eventGridRoleName = "AzureEventGridSecureWebhookSubscriber" # You don't need to modify this role name
    $eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
    if ($eventGridSP -match "Microsoft.EventGrid")
    {
        Write-Host "The Azure AD Application is already defined.`n"
    } else {
        Write-Host "Creating the Azure Event Grid Azure AD Application"
        $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
    }

    # Creates the Azure app role for the webhook Azure AD application

    $app = Get-AzureADApplication -ObjectId $webhookAppObjectId
    $appRoles = $app.AppRoles

    Write-Host "Azure AD App roles before addition of the new role..."
    Write-Host $appRoles
    
    if ($appRoles -match $eventGridRoleName)
    {
        Write-Host "The Azure Event Grid role is already defined.`n"
    } else {      
        Write-Host "Creating the Azure Event Grid role in Azure AD Application: " $webhookAppObjectId
        $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
        $appRoles.Add($newRole)
        Set-AzureADApplication -ObjectId $app.ObjectId -AppRoles $appRoles
    }

    Write-Host "Azure AD App roles after addition of the new role..."
    Write-Host $appRoles

    # Creates the user role assignment for the app that will create event subscription

    $servicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $app.AppId + "'")
    $eventSubscriptionWriterSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventSubscriptionWriterAppId + "'")

    if ($null -eq $eventSubscriptionWriterSP)
    {
        Write-Host "Create new Azure AD Application"
        $eventSubscriptionWriterSP = New-AzureADServicePrincipal -AppId $eventSubscriptionWriterAppId
    }

    try
    {
        Write-Host "Creating the Azure AD Application role assignment: " $eventSubscriptionWriterAppId
        $eventGridAppRole = $app.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
        New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $servicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
    }
    catch
    {
        if( $_.Exception.Message -like '*Permission being assigned already exists on the object*')
        {
            Write-Host "The Azure AD Application role is already defined.`n"
        }
        else
        {
            Write-Error $_.Exception.Message
        }
        Break
    }

    # Creates the service app role assignment for Event Grid Azure AD Application

    $eventGridAppRole = $app.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
    New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $servicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
    # Print output references for backup

    Write-Host ">> Webhook's Azure AD Application Id: $($app.AppId)"
    Write-Host ">> Webhook's Azure AD Application ObjectId Id: $($app.ObjectId)"
}
catch {
  Write-Host ">> Exception:"
  Write-Host $_
  Write-Host ">> StackTrace:"  
  Write-Host $_.ScriptStackTrace
}
```

## <a name="script-explanation"></a>脚本说明

有关更多详细信息，请参阅[在 Azure 事件网格中使用 Azure AD 保障 WebHook 传递的安全](../secure-webhook-delivery.md)
