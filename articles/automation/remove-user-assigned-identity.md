---
title: 删除 Azure 自动化帐户的用户分配的托管标识（预览）
description: 本文介绍如何删除 Azure 自动化帐户的用户分配的托管标识。
services: automation
ms.subservice: process-automation
ms.date: 07/24/2021
ms.topic: conceptual
ms.openlocfilehash: 2f77354e1a0f2c44ff81764fc15a071f72319da1
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674137"
---
# <a name="remove-user-assigned-managed-identity-for-azure-automation-account-preview"></a>删除 Azure 自动化帐户的用户分配的托管标识（预览）

可使用 Azure 门户、PowerShell、Azure REST API 或 Azure 资源管理器 (ARM) 模板删除 Azure 自动化中的用户分配的托管标识。

## <a name="remove-using-the-azure-portal"></a>使用 Azure 门户删除

无论最初添加用户分配的托管标识的方式为何，都可从 Azure 门户中删除该用户分配的托管标识。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 导航到你的自动化帐户，在“帐户设置”下选择“标识” 。

1. 选择“用户分配”选项卡。

1. 从列表中选择要删除的用户分配的托管标识。

1. 选择“删除” 。 当系统提示确认时，请选择“是”。

用户分配的托管标识已删除，不再具有对目标资源的访问权限。

## <a name="remove-using-powershell"></a>使用 PowerShell 删除

使用 PowerShell cmdlet [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) 删除所有用户分配的托管标识，并保留现有的系统分配的托管标识。

1. 使用 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet 以交互方式登录到 Azure，并按照说明进行操作。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount -Subscription
    }
    ```

1. 为变量提供适当的值，然后执行脚本。

    ```powershell
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    ```

1. 执行 [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount)。

    ```powershell
    # Removes all UAs, keeps SA
    $output = Set-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount `
        -AssignSystemIdentity 
    
    $output.identity.Type
    ```

    输出将为 `SystemAssigned`。

## <a name="remove-using-rest-api"></a>使用 REST API 删除

可使用以下 REST API 调用和示例从自动化帐户中删除用户分配的托管标识。

### <a name="request-body"></a>请求正文

场景：系统分配的托管标识已启用或将启用。 在众多用户分配的托管标识中，将删除其中之一。 此示例使用 HTTP PATCH 方法删除名为 `firstIdentity` 的用户分配的托管标识。

```json
{
  "identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": null
    }
  }
}
```

场景：系统分配的托管标识已启用或将启用。 将使用 HTTP PUT 方法删除所有用户分配的托管标识。

```json
{
  "identity": {
    "type": "SystemAssigned"
  }
}
```

场景：系统分配的托管标识已禁用或将禁用。 在众多用户分配的托管标识中，将删除其中之一。 此示例使用 HTTP PATCH 方法删除名为 `firstIdentity` 的用户分配的托管标识。

```json
{
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": null
    }
  }
}

```

场景：系统分配的托管标识已禁用或将禁用。 将使用 HTTP PUT 方法删除所有用户分配的托管标识。

```json
{
  "identity": {
    "type": "None"
  }
}
```

下面是用于发送 PATCH 请求的服务 REST API 请求 URI。

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

### <a name="example"></a>示例

执行以下步骤。

1. 根据要执行的操作，复制请求正文并将其粘贴到名为 `body_remove_ua.json` 的文件中。 执行任何需要的修改，然后将文件保存到本地计算机或 Azure 存储帐户中。

1. 使用 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet 以交互方式登录到 Azure，并按照说明进行操作。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount -Subscription
    }
    ```

1. 为变量提供适当的值，然后执行脚本。

    ```powershell
    $subscriptionID = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $file = "path\body_remove_ua.json"
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

   根据使用的语法，输出将为 `SystemAssignedUserAssigned`、`SystemAssigned`、`UserAssigned` 或空白。

## <a name="remove-using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板删除

如果你已使用 Azure 资源管理器模板为自动化帐户添加了用户分配的托管标识，则可修改模板，然后重新运行它，从而删除该用户分配的托管标识。

场景：系统分配的托管标识已启用或将启用。 在两个用户分配的托管标识中，将删除其中之一。 此语法片段删除所有用户分配的托管标识（作为参数传递给模板的用户分配的托管标识除外） 。

```json
...
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {}
    }
},
...
```

场景：系统分配的托管标识已启用或将启用。 将删除所有用户分配的托管标识。

```json
...
"identity": {
    "type": "SystemAssigned"
},
...
```

场景：系统分配的托管标识已禁用或将禁用。 在两个用户分配的托管标识中，将删除其中之一。 此语法片段删除所有用户分配的托管标识（作为参数传递给模板的用户分配的托管标识除外） 。

```json
...
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {}
    }
},
...
```

使用 [Get-AzAutomationAccount](/powershell/module/az.automation/get-azautomationaccount) cmdlet 进行验证。 根据所使用的语法，输出将为 `SystemAssignedUserAssigned`、`SystemAssigned` 或 `UserAssigned`。

```powershell
(Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity.Type
```

## <a name="next-steps"></a>后续步骤

- 有关在 Azure 自动化中启用托管标识的详细信息，请参阅[为自动化启用和使用托管标识（预览）](enable-managed-identity-for-automation.md)。

- 有关自动化帐户安全性的概述，请参阅[自动化帐户身份验证概述](automation-security-overview.md)。
