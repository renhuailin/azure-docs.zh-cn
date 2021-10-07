---
title: 为 Azure 自动化帐户使用系统分配的托管标识（预览版）
description: 本文介绍如何为 Azure 自动化帐户设置托管标识。
services: automation
ms.subservice: process-automation
ms.date: 09/23/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b84c73e5286dc633b54ade2d59d43957f517361e
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057655"
---
# <a name="using-a-system-assigned-managed-identity-for-an-azure-automation-account-preview"></a>为 Azure 自动化帐户使用系统分配的托管标识（预览版）

本文介绍如何为 Azure 自动化帐户启用系统分配的托管标识，以及如何使用该标识来访问其他资源。 有关托管标识如何与 Azure 自动化配合使用的详细信息，请参阅[托管标识](automation-security-overview.md#managed-identities-preview)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 自动化帐户。 有关说明，请参阅[创建 Azure 自动化帐户](./quickstarts/create-account-portal.md)。

- Azure 帐户模块的最新版本。 当前版本为 2.2.8。 （请参阅 [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/)，了解此版本的相关详情。）

- 要从自动化 runbook 访问的 Azure 资源。 此资源需要为托管标识定义角色，这可帮助自动化 runbook 对资源访问进行身份验证。 若要添加角色，需要成为相应 Azure AD 租户中资源的所有者。

- 如果要使用托管标识执行混合作业，请将混合 Runbook 辅助角色更新到最新版本。 所需最低版本为：

  - Windows 混合 Runbook 辅助角色：版本 7.3.1125.0
  - Linux 混合 Runbook 辅助角色：版本 1.7.4.0

## <a name="enable-a-system-assigned-managed-identity-for-an-azure-automation-account"></a>为 Azure 自动化帐户启用系统分配的托管标识

启用后，以下属性将分配给系统分配的托管标识。

|属性 (JSON) | 值 | 说明|
|----------|-----------|------------|
| principalid | \<principal-ID\> | 系统分配的托管标识的服务主体对象的全局唯一标识符 (GUID)，表示 Azure AD 租户中的自动化帐户。 此 GUID 有时显示为“对象 ID”或 objectID。 |
| tenantid | \<Azure-AD-tenant-ID\> | 全局唯一标识符 (GUID)，表示自动化帐户现在是其中的一名成员的 Azure AD 租户。 在 Azure AD 租户内，服务主体与自动化帐户具有相同名称。 |

可以使用 Azure 门户、PowerShell、Azure REST API 或 ARM 模板为 Azure 自动化帐户启用系统分配的托管标识。 如需有关在 PowerShell 中启用的示例，请先使用 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet 以交互方式登录到 Azure，然后按照说明进行操作。

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

然后初始化一组要在整个示例中使用的变量。 修改以下值，然后执行。

```powershell
$subscriptionID = "subscriptionID"
$resourceGroup = "resourceGroupName"
$automationAccount = "automationAccountName"
```

> [!IMPORTANT]
> 新自动化帐户级别标识会替代以前任何 VM 级别系统分配的标识，这些标识在[将 Runbook 身份验证与托管标识结合使用](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)中进行了介绍。 如果在使用 VM 系统分配的标识访问 runbook 资源的 Azure VM 上运行混合作业，则会将自动化帐户标识用于混合作业。 这意味着，如果在使用自动化帐户的客户管理的密钥 (CMK) 功能，则可能会影响现有作业执行。<br/><br/>如果要继续使用 VM 的托管标识，则不应启用自动化帐户级别标识。 如果已启用该标识，可以禁用自动化帐户的系统分配的托管标识。 请参阅[禁用 Azure 自动化帐户托管标识](./disable-managed-identity-for-automation.md)。

### <a name="enable-using-the-azure-portal"></a>使用 Azure 门户启用

执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户中，导航到自动化帐户。

1. 在“帐户设置”下，选择“标识” ****  **** 。

1. 将“系统分配”选项设置为“开启”，然后按“保存”  。 当系统提示确认时，请选择“是”。

   :::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="在 Azure 门户中启用系统分配的标识。":::

   自动化帐户现在可以使用系统分配的标识，该标识注册到 Azure Active Directory (Azure AD)，由对象 ID 表示。

   :::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="托管标识对象 ID。":::

### <a name="enable-using-powershell"></a>使用 PowerShell 进行启用

使用 PowerShell cmdlet [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) 启用系统分配的托管标识。

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignSystemIdentity

$output
```

输出应如下所示：

:::image type="content" source="media/enable-managed-identity-for-automation/set-azautomationaccount-output.png" alt-text="set-azautomationaccount 命令的输出。":::

对于其他输出，请执行：`$output.identity | ConvertTo-Json`。

### <a name="enable-using-a-rest-api"></a>使用 REST API 启用

下面提供了语法和示例步骤。

#### <a name="syntax"></a>语法

下面的正文语法使用 HTTP PATCH 方法为现有自动化帐户启用系统分配的托管标识。 但是，此语法会删除与该自动化帐户关联的所有现有用户分配的托管标识。

```json
{ 
 "identity": { 
   "type": "SystemAssigned" 
  } 
}
```

如果定义了多个用户分配的标识，而又要保留这些标识并仅删除系统分配的标识，则需要使用逗号分隔的列表指定每个用户分配的标识。 下例使用的是 HTTP PATCH 方法。

```json
{ 
  "identity" : {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/cmkID": {},
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/cmkID2": {}
    }
  }
}

```

API 的语法如下：

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

#### <a name="example"></a>示例

执行以下步骤。

1. 将正文语法复制并粘贴到名为 `body_sa.json` 的文件中。 将该文件保存在本地计算机或 Azure 存储帐户中。

1. 更新以下变量值，然后执行。

    ```powershell
    $file = "path\body_sa.json"
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
        "PrincipalId":  "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "TenantId":  "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "Type":  0,
        "UserAssignedIdentities":  null
    }
    ```

### <a name="enable-using-an-arm-template"></a>使用 ARM 模板启用

下面提供了语法和示例步骤。

#### <a name="template-syntax"></a>模板语法

以下示例模板语法将为现有自动化帐户启用系统分配的托管标识。 但是，此语法会删除与该自动化帐户关联的所有现有用户分配的托管标识。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2020-01-13-preview",
      "name": "yourAutomationAccount",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
        },
      "properties": {
        "sku": {
          "name": "Basic"
        }
      }
    }
  ]
}
```

#### <a name="example"></a>示例

执行以下步骤。

1. 将上述模板语法修改为使用你的自动化帐户，并将其保存到名为 `template_sa.json` 的文件中。

1. 更新以下变量值，然后执行。

    ```powershell
    $templateFile = "path\template_sa.json"
    ```

1. 使用 PowerShell cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 部署模板。

    ```powershell
    New-AzResourceGroupDeployment `
        -Name "SystemAssignedDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile
    ```

   该命令不会生成输出；但是，你可以使用以下代码进行验证：

    ```powershell
    (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity | ConvertTo-Json
    ```

   输出类似于前面 REST API 示例显示的输出。

## <a name="give-access-to-azure-resources-by-obtaining-a-token"></a>通过获取令牌来授予对 Azure 资源的访问权限

自动化帐户可以使用其系统分配的托管标识获取令牌，以访问其他受 Azure AD 保护的资源（例如 Azure 密钥保管库）。 这些令牌不代表应用程序的任何特定用户。 它们代表访问资源的应用程序。 例如，在本例中，令牌表示自动化帐户。

使用系统分配的托管标识进行身份验证之前，请在计划使用该标识的 Azure 资源上为该标识设置访问权限。 若要完成此任务，请在目标 Azure 资源上向该标识分配适当的角色。

遵循最小权限原则，仔细分配仅执行 runbook 所需的权限。 例如，如果启动或停止 Azure VM 仅需自动化帐户，则分配给运行方式帐户或托管标识的权限需仅用于启动或停止 VM。 同样，如果 runbook 正在从 Blob 存储读取，则分配只读权限。此示例使用 Azure PowerShell 演示如何分配参与者

此示例使用 Azure PowerShell 演示如何将订阅中的参与者角色分配给目标 Azure 资源。 参与者角色用作示例，在你的情况中可能需要也可能不需要。

```powershell
New-AzRoleAssignment `
    -ObjectId <automation-Identity-object-id> `
    -Scope "/subscriptions/<subscription-id>" `
    -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-system-assigned-managed-identity"></a>使用系统分配的托管标识对访问进行身份验证

为自动化帐户启用托管标识并向标识授予对目标资源的访问权限后，可以在 runbook 中针对支持托管标识的资源指定该标识。 对于标识支持，请使用 Az cmdlet `Connect-AzAccount` cmdlet。 请参阅 PowerShell 参考中的 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount)。

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
```

> [!NOTE]
> 如果你的组织仍在使用已弃用的 AzureRM cmdlet，则可以使用 `Connect-AzureRMAccount -Identity`。

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>在不使用 Azure cmdlet 的情况下生成访问令牌

对于 HTTP 终结点，请确保以下事项。

- 元数据标头必须存在并且应设置为“true”。
- 资源必须作为 GET 请求的查询参数和 POST 请求的表单数据，与请求一起传递。
- X-IDENTITY-HEADER 应设置为混合 Runbook 辅助角色的环境变量 IDENTITY_HEADER 的值。
- Post 请求的内容类型必须为“application/x-www-form-urlencoded”。

### <a name="get-access-token-for-system-assigned-identity-using-http-get"></a>使用 HTTP Get 获取系统分配的标识的访问令牌

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="get-access-token-for-system-assigned-identity-using-http-post"></a>使用 HTTP Post 获取系统分配的标识的访问令牌

```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

### <a name="using-system-assigned-managed-identity-in-azure-powershell"></a>在 Azure PowerShell 中使用系统分配的托管标识

有关详细信息，请参阅 [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret)。

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="using-system-assigned-managed-identity-in-python-runbook"></a>在 Python Runbook 中使用系统分配的托管标识

```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

### <a name="using-system-assigned-managed-identity-to-access-sql-database"></a>使用系统分配的托管标识访问 SQL 数据库

要了解如何预配 Azure SQL 数据库访问权限，请参阅[预配 Azure AD 管理员（SQL 数据库）](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)。

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

## <a name="next-steps"></a>后续步骤

- 如果 runbook 没有成功完成，请查看[排查 Azure 自动化托管标识问题（预览）](troubleshoot/managed-identity.md)。

- 如果需要禁用托管标识，请参阅[禁用 Azure 自动化帐户托管标识（预览版）](disable-managed-identity-for-automation.md)。

- 有关 Azure 自动化帐户安全性的概述，请参阅[自动化帐户身份验证概述](automation-security-overview.md)。