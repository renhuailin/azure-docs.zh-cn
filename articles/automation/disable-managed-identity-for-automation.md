---
title: 为 Azure 自动化帐户禁用系统分配的托管标识（预览版）
description: 本文介绍如何禁用 Azure 自动化帐户的系统分配的托管标识。
services: automation
ms.subservice: process-automation
ms.date: 07/24/2021
ms.topic: conceptual
ms.openlocfilehash: 178da223b5d5f14cc27034c39bd4cc3a05f82631
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606582"
---
# <a name="disable-system-assigned-managed-identity-for-azure-automation-account-preview"></a>为 Azure 自动化帐户禁用系统分配的托管标识（预览版）

可以使用 Azure 门户或 REST API 在 Azure 自动化中禁用系统分配的托管标识。

## <a name="disable-using-the-azure-portal"></a>使用 Azure 门户禁用

无论最初如何设置了系统分配的托管标识，都可以从 Azure 门户禁用系统分配的托管标识。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 导航到你的自动化帐户，在“帐户设置”下选择“标识” 。

1. 在“系统分配”选项卡中的“状态”按钮下，依次选择“关闭”、“保存”   。 当系统提示确认时，请选择“是”。

系统分配的托管标识随即已禁用，不再可以访问目标资源。

## <a name="disable-using-rest-api"></a>使用 REST API 禁用

下面提供了语法和示例步骤。

### <a name="request-body"></a>请求正文

以下请求正文使用 HTTP PATCH 方法禁用系统分配的托管标识，并删除任何用户分配的托管标识。

```json
{ 
 "identity": { 
   "type": "None" 
  } 
}

```

如果定义了多个用户分配的标识，若要保留这些标识并仅删除系统分配的标识，则需要使用逗号分隔的列表指定每个用户分配的标识。 下例使用的是 HTTP PATCH 方法。

```json
{ 
"identity" : {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": {},
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/secondIdentity": {}
        }
    }
}
```

下面是用于发送 PATCH 请求的服务 REST API 请求 URI。

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

### <a name="example"></a>示例

执行以下步骤。

1. 根据要执行的操作，复制请求正文并将其粘贴到名为 `body_remove_sa.json` 的文件中。 将该文件保存在本地计算机或 Azure 存储帐户中。

1. 使用 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet 以交互方式登录到 Azure，并按照说明进行操作。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    ```

1. 为变量提供适当的值，然后执行脚本。

    ```powershell
    $subscriptionID = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $file = "path\body_remove_sa.json"
    ```

1. 此示例使用 PowerShell cmdlet [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) 将 PATCH 请求发送到自动化帐户。

    ```powershell
    # build URI
    $URI = "https://management.azure.com/subscriptions/$subscriptionID/resourceGroups/$resourceGroup/providers/Microsoft.Automation/automationAccounts/$automationAccount`?api-version=2020-01-13-preview"
    
    # build body
    $body = Get-Content $file
    
    # obtain access token
    $azContext = Get-AzContext
    $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
    $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
    $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
    $authHeader = @{
        'Content-Type'='application/json'
        'Authorization'='Bearer ' + $token.AccessToken
    }
    
    # Invoke the REST API
    Invoke-RestMethod -Uri $URI -Method PATCH -Headers $authHeader -Body $body
    
    # Confirm removal
    (Get-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount).Identity.Type
    ```

    根据使用的语法，输出将是：`UserAssigned` 或空白。

## <a name="next-steps"></a>后续步骤

- 有关在 Azure 自动化中启用托管标识的详细信息，请参阅[为自动化启用和使用托管标识（预览）](enable-managed-identity-for-automation.md)。

- 有关自动化帐户安全性的概述，请参阅[自动化帐户身份验证概述](automation-security-overview.md)。