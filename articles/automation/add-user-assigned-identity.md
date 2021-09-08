---
title: 为 Azure 自动化帐户使用用户分配的托管标识（预览版）
description: 本文介绍如何为 Azure 自动化帐户设置用户分配的托管标识。
services: automation
ms.subservice: process-automation
ms.date: 08/26/2021
ms.topic: conceptual
ms.openlocfilehash: ce409853cddfd0278692e2c6e233331530296d6b
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214258"
---
# <a name="using-a-user-assigned-managed-identity-for-an-azure-automation-account-preview"></a>为 Azure 自动化帐户使用用户分配的托管标识（预览版）

本文介绍如何为 Azure 自动化帐户添加用户分配的托管标识，以及如何使用该标识来访问其他资源。 有关托管标识如何与 Azure 自动化配合使用的详细信息，请参阅[托管标识](automation-security-overview.md#managed-identities-preview)。

> [!NOTE]
> 只有云作业支持用户分配的托管标识。  

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 自动化帐户。 有关说明，请参阅[创建 Azure 自动化帐户](automation-quickstart-create-account.md)。

- 系统分配的托管标识。 有关说明，请参阅[为 Azure 自动化帐户使用系统分配的托管标识（预览版）](enable-managed-identity-for-automation.md)。

- 用户分配的托管标识。 有关说明，请参阅[创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

- 用户分配的托管标识以及 Runbook 使用该标识管理的目标 Azure 资源必须在同一个 Azure 订阅中。

- Azure 帐户模块的最新版本。 当前版本为 2.2.8。 （请参阅 [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/)，了解此版本的相关详情。）

- 要从自动化 runbook 访问的 Azure 资源。 此资源需要为用户分配的托管标识定义角色，以帮助自动化 Runbook 对资源访问进行身份验证。 若要添加角色，需要成为相应 Azure AD 租户中资源的所有者。

- 如果要使用用户分配的托管标识执行混合作业，请将混合 Runbook 辅助角色更新到最新版本。 所需最低版本为：

  - Windows 混合 Runbook 辅助角色：版本 7.3.1125.0
  - Linux 混合 Runbook 辅助角色：版本 1.7.4.0

## <a name="add-user-assigned-managed-identity-for-azure-automation-account"></a>为 Azure 自动化帐户添加用户分配的托管标识

可以使用 Azure 门户、PowerShell、Azure REST API 或 ARM 模板为 Azure 自动化帐户添加用户分配的托管标识。 如需有关在 PowerShell 中启用的示例，请先使用 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet 以交互方式登录到 Azure，然后按照说明进行操作。

```powershell
# Sign in to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount -Subscription
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

然后初始化一组要在整个示例中使用的变量。 修改以下值，然后执行

```powershell
$subscriptionID = "subscriptionID"
$resourceGroup = "resourceGroupName"
$automationAccount = "automationAccountName"
$userAssignedOne = "userAssignedIdentityOne"
$userAssignedTwo = "userAssignedIdentityTwo"
```

### <a name="add-using-the-azure-portal"></a>使用 Azure 门户添加

执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户中，导航到自动化帐户。

1. 在“帐户设置”下，选择“标识” ****  **** 。

1. 选择“用户分配”选项卡，然后选择“添加” 。

1. 选择现有的用户分配的托管标识，然后选择“添加”。 随后你将返回到“用户分配”选项卡。

   :::image type="content" source="media/add-user-assigned-identity/user-assigned-managed-identity.png" alt-text="门户的输出。":::

### <a name="add-using-powershell"></a>使用 PowerShell 添加

使用 PowerShell cmdlet [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) 添加用户分配的托管标识。 首先必须考虑是否有现有的系统分配的托管标识。 以下示例将两个现有的用户分配的托管标识添加到现有自动化帐户，并禁用系统分配的托管标识（如果存在）。

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignUserIdentity "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedOne", `
        "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedTwo"

$output
```

若要保留现有的系统分配的托管标识，请使用以下命令：

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignUserIdentity "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedOne", `
        "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedTwo" `
    -AssignSystemIdentity

$output
```

输出应如下所示：

:::image type="content" source="media/add-user-assigned-identity/set-azautomationaccount-output.png" alt-text="Set-AzAutomationAccount 命令的输出。":::

对于其他输出，请执行：`$output.identity | ConvertTo-Json`。

### <a name="add-using-a-rest-api"></a>使用 REST API 添加

下面提供了语法和示例步骤。

#### <a name="syntax"></a>语法

以下示例正文语法启用系统分配的托管标识（如果尚未启用），并将两个现有的用户分配的托管标识分配给现有自动化帐户。

修补程序

```json
{
  "identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": {},
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/secondIdentity": {}
    }
  }
}
```

API 的语法如下：

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview 
```

#### <a name="example"></a>示例

执行以下步骤。

1. 修改上面的正文语法并将其保存到名为 `body_ua.json` 的文件中。 将该文件保存在本地计算机或 Azure 存储帐户中。

1. 修改以下变量值，然后执行。

    ```powershell
    $file = "path\body_ua.json"
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
    $response = Invoke-RestMethod -Uri $URI -Method PATCH -Headers $authHeader -Body $body
    
    # Review output
    $response.identity | ConvertTo-Json
    ```

    输出应如下所示：

    ```json
    {
    "type": "SystemAssigned, UserAssigned",
    "principalId": "00000000-0000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "userAssignedIdentities":  {
        "/subscriptions/ContosoID/resourcegroups/ContosoLab/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ContosoUAMI1":  {
                "PrincipalId":  "00000000-0000-0000-0000-000000000000",
                "ClientId":  "00000000-0000-0000-0000-000000000000"
                    },
        "/subscriptions/ContosoID/resourcegroups/ContosoLab/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ContosoUAMI2":  {
                "PrincipalId":  "00000000-0000-0000-0000-000000000000",
                "ClientId":  "00000000-0000-0000-0000-000000000000"
                    }
        }
    }
    ```

### <a name="add-using-an-arm-template"></a>使用 ARM 模板添加

下面提供了语法和示例步骤。

#### <a name="template-syntax"></a>模板语法

以下示例模板语法启用系统分配的托管标识（如果尚未启用），并将两个现有的用户分配的托管标识分配给现有自动化帐户。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "automationAccountName": {
     "defaultValue": "YourAutomationAccount",
      "type": "String",
      "metadata": {
        "description": "Automation account name"
      }
    },
    "userAssignedOne": {
     "defaultValue": "userAssignedOne",
      "type": "String",
      "metadata": {
        "description": "User-assigned managed identity"
      }
      },
    "userAssignedTwo": {
     "defaultValue": "userAssignedTwo",
      "type": "String",
      "metadata": {
        "description": "User-assigned managed identity"
      }
      }
   },
  "resources": [
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2020-01-13-preview",
      "name": "[parameters('automationAccountName')]",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned, UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {},
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedTwo'))]": {}
        }
      },
      "properties": {
        "sku": {
          "name": "Basic"
        },
        "encryption": {
          "keySource": "Microsoft.Automation",
          "identity": {}
        }
      }
    }
  ]
}
```

#### <a name="example"></a>示例

执行以下步骤。

1. 将模板复制并粘贴到名为 `template_ua.json` 的文件中。 将该文件保存在本地计算机或 Azure 存储帐户中。

1. 修改以下变量值，然后执行。

    ```powershell
    $templateFile = "path\template_ua.json"
    ```

1. 使用 PowerShell cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 部署模板。

    ```powershell
    New-AzResourceGroupDeployment `
        -Name "UserAssignedDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile `
        -automationAccountName $automationAccount `
        -userAssignedOne $userAssignedOne `
        -userAssignedTwo $userAssignedTwo
    ```

   该命令不会生成输出；但是，你可以使用以下代码进行验证：

    ```powershell
    (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity | ConvertTo-Json
    ```

    输出类似于前面 REST API 示例显示的输出。

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>通过获取令牌来向标识授予对 Azure 资源的访问权限

自动化帐户可以使用其用户分配的托管标识获取令牌，以访问其他受 Azure AD 保护的资源（例如 Azure 密钥保管库）。 这些令牌不代表应用程序的任何特定用户。 它们代表访问资源的应用程序。 例如，在本例中，令牌表示自动化帐户。

在能够使用用户分配的托管标识进行身份验证之前，需要为该标识设置对你打算在其中使用该标识的 Azure 资源的访问权限。 若要完成此任务，请在目标 Azure 资源上向该标识分配适当的角色。

遵循最小权限原则，谨慎分配执行 runbook 仅需的权限。 例如，如果自动化帐户仅需要用于启动或停止 Azure VM，则分配给运行方式帐户或托管标识的权限需仅用于启动或停止 VM。 同样，如果 runbook 要从 Blob 存储读取，则分配只读权限。

此示例使用 Azure PowerShell 演示如何将订阅中的参与者角色分配给目标 Azure 资源。 “参与者”角色用作示例，在你的案例中不一定需要用到。 或者，也可以将角色分配给 [Azure 门户](../role-based-access-control/role-assignments-portal.md)中的目标 Azure 资源。

```powershell
New-AzRoleAssignment `
    -ObjectId <automation-Identity-object-id> `
    -Scope "/subscriptions/<subscription-id>" `
    -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-user-assigned-managed-identity"></a>使用用户分配的托管标识对访问进行身份验证

为自动化帐户启用用户分配的托管标识并向标识授予对目标资源的访问权限后，可以在 Runbook 中针对支持托管标识的资源指定该标识。 若要确认标识支持情况，请使用 Az cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount)。

```powershell
Connect-AzAccount -Identity `
    -AccountId <user-assigned-identity-ClientId> 
```

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>在不使用 Azure cmdlet 的情况下生成访问令牌

对于 HTTP 终结点，请确保以下事项。
- 元数据标头必须存在并且应设置为“true”。
- 资源必须作为 GET 请求的查询参数和 POST 请求的表单数据，与请求一起传递。
- Post 请求的内容类型必须是 `application/x-www-form-urlencoded`。

### <a name="get-access-token-for-user-assigned-managed-identity-using-http-get"></a>使用 HTTP Get 获取用户分配的托管标识的访问令牌  

```powershell
$resource= "?resource=https://management.azure.com/"
$client_id="&client_id=<ClientId of USI>"
$url = $env:IDENTITY_ENDPOINT + $resource + $client_id 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"  
$Headers.Add("Metadata", "True")
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token 
```

### <a name="get-access-token-for-user-assigned-managed-identity-using-http-post"></a>使用 HTTP Post 获取用户分配的托管标识的访问令牌

```powershell
$url = $env:IDENTITY_ENDPOINT
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Metadata", "True")
$body = @{'resource'='https://management.azure.com/' 
'client_id'='<ClientId of USI>'}
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body
Write-Output $accessToken.access_token 
```

### <a name="using-user-assigned-managed-identity-in-azure-powershell"></a>在 Azure PowerShell 中使用用户分配的托管标识

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity -AccountId <ClientId of USI>"  
Connect-AzAccount -Identity -AccountId <ClientId of USI> 
Write-Output "Successfully connected with Automation account's Managed Identity"  
Write-Output "Trying to fetch value from key vault using User Assigned Managed identity. Make sure you have given correct access to Managed Identity"  
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>'  
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)  
try {  
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)  
    Write-Output $secretValueText  
} finally {  
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)  
} 
```

### <a name="using-user-assigned-managed-identity-in-python-runbook"></a>在 Python Runbook 中使用用户分配的托管标识

```python
#!/usr/bin/env python3  
import os  
import requests   

resource = "?resource=https://management.azure.com/" 
client_id = "&client_id=<ClientId of USI>" 
endPoint = os.getenv('IDENTITY_ENDPOINT')+ resource +client_id 
payload={}  
headers = {  
  'Metadata': 'True'  
}  
response = requests.request("GET", endPoint, headers=headers, data=payload)  
print(response.text) 
```

## <a name="next-steps"></a>后续步骤

- 如果 runbook 没有成功完成，请查看[排查 Azure 自动化托管标识问题（预览）](troubleshoot/managed-identity.md)。

- 如果需要禁用托管标识，请参阅[禁用 Azure 自动化帐户托管标识（预览版）](disable-managed-identity-for-automation.md)。

- 有关 Azure 自动化帐户安全性的概述，请参阅[自动化帐户身份验证概述](automation-security-overview.md)。
