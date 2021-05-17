---
title: 为 Azure 自动化帐户禁用托管标识（预览版）
description: 本文介绍如何为 Azure 自动化帐户设置托管标识。
services: automation
ms.subservice: process-automation
ms.date: 04/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1bdba095c18943be5b367bd605d1a22d6eb6499f
ms.sourcegitcommit: f6b76df4c22f1c605682418f3f2385131512508d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108323658"
---
# <a name="enable-a-managed-identity-for-your-azure-automation-account-preview"></a>为 Azure 自动化帐户禁用托管标识（预览版）

本主题介绍如何为 Azure 自动化帐户创建托管标识，以及如何使用它访问其他资源。 有关托管标识如何与 Azure 自动化配合使用的详细信息，请参阅[托管标识](automation-security-overview.md#managed-identities-preview)。

## <a name="prerequisites"></a>先决条件

- Azure 帐户和订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 托管标识和以及 runbook 使用该标识管理的目标 Azure 资源必须在同一 Azure 订阅中。

- Azure 帐户模块的最新版本。 当前版本为 2.2.8。 （请参阅 [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/)，了解此版本的相关详情。）

- 要从自动化 runbook 访问的 Azure 资源。 此资源需要为托管标识定义角色，这可帮助自动化 runbook 对资源访问进行身份验证。 若要添加角色，需要成为相应 Azure AD 租户中资源的所有者。

- 如果要使用托管标识执行混合作业，请将混合 Runbook 辅助角色更新到最新版本。 所需最低版本为：

   - Windows 混合 Runbook 辅助角色：版本 7.3.1125.0
   - Linux 混合 Runbook 辅助角色：版本 1.7.4.0

## <a name="enable-system-assigned-identity"></a>启用系统分配的标识

>[!IMPORTANT]
>新自动化帐户级别标识会替代以前任何 VM 级别系统分配的标识，这些标识在[将 Runbook 身份验证与托管标识结合使用](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)中进行了介绍。 如果在使用 VM 系统分配的标识访问 runbook 资源的 Azure VM 上运行混合作业，则会将自动化帐户标识用于混合作业。 这意味着，如果在使用自动化帐户的客户管理的密钥 (CMK) 功能，则可能会影响现有作业执行。<br/><br/>如果要继续使用 VM 的托管标识，则不应启用自动化帐户级别标识。 如果已启用该标识，则可以禁用自动化帐户托管标识。 请参阅[禁用 Azure 自动化帐户托管标识](./disable-managed-identity-for-automation.md)。

可以通过以下两种方式之一为 Azure 自动化设置系统分配的标识。 可以使用 Azure 门户或 Azure REST API。

>[!NOTE]
>尚不支持用户分配的标识。

### <a name="enable-system-assigned-identity-in-azure-portal"></a>在 Azure 门户中启用系统分配的标识

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 导航到自动化帐户，然后在“帐户设置”下选择“标识” 。

1. 将“系统分配”选项设置为“开启”，然后按“保存”  。 当系统提示确认时，请选择“是”。

:::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="在 Azure 门户中启用系统分配的标识。":::

自动化帐户现在可以使用系统分配的标识，该标识注册到 Azure Active Directory (Azure AD)，由对象 ID 表示。

:::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="托管标识对象 ID。":::

### <a name="enable-system-assigned-identity-through-the-rest-api"></a>通过 REST API 启用系统分配的标识

可以使用以下 REST API 调用向自动化帐户配置系统分配的托管标识。

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

请求正文
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 .
 .
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
.
.
}
```

|属性 (JSON) | 值 | 说明|
|----------|-----------|------------|
| principalid | \<principal-ID\> | 托管标识的服务主体对象的全局唯一标识符 (GUID)，表示 Azure AD 租户中的自动化帐户。 此 GUID 有时显示为“对象 ID”或 objectID。 |
| tenantid | \<Azure-AD-tenant-ID\> | 全局唯一标识符 (GUID)，表示自动化帐户现在是其中的一名成员的 Azure AD 租户。 在 Azure AD 租户内，服务主体与自动化帐户具有相同名称。 |

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>通过获取令牌来向标识授予对 Azure 资源的访问权限

自动化帐户可以使用其托管标识获取令牌，以访问其他受 Azure AD 保护的资源（如 Azure Key Vault）。 这些令牌不表示应用程序的任何特定用户。 相反，它们表示访问资源的应用程序。 例如，在本例中，令牌表示自动化帐户。

使用系统分配的托管标识进行身份验证之前，请在计划使用该标识的 Azure 资源上为该标识设置访问权限。 若要完成此任务，请在目标 Azure 资源上向该标识分配适当的角色。

此示例使用 Azure PowerShell 演示如何将订阅中的参与者角色分配给目标 Azure 资源。 参与者角色用作示例，在你的情况中可能需要也可能不需要。

```powershell
New-AzRoleAssignment -ObjectId <automation-Identity-object-id> -Scope "/subscriptions/<subscription-id>" -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-managed-identity"></a>使用托管标识对访问权限进行身份验证

为自动化帐户启用托管标识并向标识授予对目标资源的访问权限后，可以在 runbook 中针对支持托管标识的资源指定该标识。 对于标识支持，请使用 Az cmdlet `Connect-AzAccount` cmdlet。 请参阅 PowerShell 参考中的 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount)。

```powershell
Connect-AzAccount -Identity
```

>[!NOTE]
>如果你的组织仍在使用已弃用的 AzureRM cmdlet，则可以使用 `Connect-AzureRMAccount -Identity`。

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>在不使用 Azure cmdlet 的情况下生成访问令牌

对于 HTTP 终结点，请确保以下事项。
- 元数据标头必须存在并且应设置为“true”。
- 资源必须作为 GET 请求的查询参数和 POST 请求的表单数据，与请求一起传递。
- X-IDENTITY-HEADER 应设置为混合 Runbook 辅助角色的环境变量 IDENTITY_HEADER 的值。 
- Post 请求的内容类型必须为“application/x-www-form-urlencoded”。 

### <a name="sample-get-request"></a>示例 GET 请求

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="sample-post-request"></a>示例 POST 请求
```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

## <a name="sample-runbooks-using-managed-identity"></a>使用托管标识的示例 runbook

### <a name="sample-runbook-to-access-a-sql-database-without-using-azure-cmdlets"></a>在不使用 Azure cmdlet 的情况下访问 SQL 数据库的示例 runbook

尝试此脚本之前，请确保已启用标识。 请参阅[启用系统分配的标识](#enable-system-assigned-identity)。

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

### <a name="sample-runbook-to-access-a-key-vault-using-azure-cmdlets"></a>使用 Azure cmdlet 访问密钥保管库的示例 runbook

尝试此脚本之前，请确保已启用标识。 请参阅[启用系统分配的标识](#enable-system-assigned-identity)。

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

### <a name="sample-python-runbook-to-get-a-token"></a>获取令牌的示例 Python runbook

尝试此 runbook 之前，请确保已启用标识。 请参阅[启用系统分配的标识](#enable-system-assigned-identity)。

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

## <a name="next-steps"></a>后续步骤

- 如果 runbook 没有成功完成，请查看[排查 Azure 自动化托管标识问题（预览）](troubleshoot/managed-identity.md)。

- 如果需要禁用托管标识，请参阅[禁用 Azure 自动化帐户托管标识（预览版）](disable-managed-identity-for-automation.md)。

- 有关 Azure 自动化帐户安全性的概述，请参阅[自动化帐户身份验证概述](automation-security-overview.md)。
