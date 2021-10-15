---
title: 仅限 Azure Active Directory 的身份验证
description: 本文介绍 Azure SQL 数据库和 Azure SQL 托管实例中的“仅限 Azure Active Directory (Azure AD) 的身份验证”功能
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/31/2021
ms.openlocfilehash: 95a3d04ce8af0e83072e214e2b3fac72c78b28c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669581"
---
# <a name="azure-ad-only-authentication-with-azure-sql"></a>Azure SQL 中的“仅限 Azure AD 身份验证”

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 本文所述的“仅限 Azure AD 身份验证”功能以公共预览版提供 。 

“仅限 Azure AD 身份验证”是 [Azure SQL](../azure-sql-iaas-vs-paas-what-is-overview.md) 中可使服务仅支持 Azure AD 身份验证的一项功能，它受 [Azure SQL 数据库](sql-database-paas-overview.md)和 [Azure SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)的支持。 在 Azure SQL 环境中启用“仅限 Azure AD 身份验证”时，将禁用 SQL 身份验证，包括禁用来自 SQL 服务器管理员、登录名和用户的连接。 只有使用 [Azure AD 身份验证](authentication-aad-overview.md)的用户才有权连接到服务器或数据库。

可以使用 Azure 门户、Azure CLI、PowerShell 或 REST API 启用或禁用“仅限 Azure AD 身份验证”。 也可以在创建服务器期间使用 ARM 模板配置“仅限 Azure AD 身份验证”。

有关 Azure SQL 身份验证的详细信息，请参阅[身份验证和授权](logins-create-manage.md#authentication-and-authorization)。

> [!IMPORTANT]
> 目前，无法在 Azure 门户中管理 Azure SQL 托管实例的“仅限 Azure AD 身份验证”。 有关启用“仅限 Azure AD 身份验证”的不同方法的教程，请参阅[教程：启用 Azure SQL 中的“仅限 Azure Active Directory 身份验证”](authentication-azure-ad-only-authentication-tutorial.md)。

## <a name="feature-description"></a>功能描述

启用“仅限 Azure AD 身份验证”时，将在服务器级别禁用 [SQL 身份验证](logins-create-manage.md#authentication-and-authorization)，并阻止基于任何 SQL 身份验证凭据的任何身份验证。 SQL 身份验证用户将无法连接到 Azure SQL 数据库（包括其所有数据库）的[逻辑服务器](logical-servers.md)。 即使禁用了 SQL 身份验证，拥有适当权限的 Azure AD 帐户也仍可以创建新的 SQL 身份验证登录名和用户。 不允许新建的 SQL 身份验证帐户连接到服务器。 启用“仅限 Azure AD 身份验证”不会删除现有的 SQL 身份验证登录名和用户帐户。 该功能只会阻止这些帐户连接到服务器以及为此服务器创建的任何数据库。

还可以使用 Azure Policy 强制创建启用了“仅限 Azure AD 的身份验证”功能的服务器。 有关详细信息，请参阅[支持“仅限 Azure AD 的身份验证”的 Azure Policy](authentication-azure-ad-only-authentication-policy.md)。

## <a name="permissions"></a>权限

作为高特权 [Azure AD 内置角色](../../role-based-access-control/built-in-roles.md)（例如 Azure 订阅[所有者](../../role-based-access-control/built-in-roles.md#owner)、[参与者](../../role-based-access-control/built-in-roles.md#contributor)和[全局管理员](../../active-directory/roles/permissions-reference.md#global-administrator)）成员的 Azure AD 用户可以启用或禁用“仅限 Azure AD 身份验证”。 此外，[SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色也可以启用或禁用“仅限 Azure AD 身份验证”功能。

[SQL Server 参与者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)和 [SQL 托管实例参与者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)角色无权启用或禁用“仅限 Azure AD 身份验证”功能。 这种限制与[职责分离](security-best-practice.md#implement-separation-of-duties)方案保持一致，在此方案中，可以创建 Azure SQL 服务器或创建 Azure AD 管理员的用户无法启用或禁用安全功能。

### <a name="actions-required"></a>必需操作

以下操作已添加到 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色，使其能够管理“仅限 Azure AD 身份验证”功能。

- Microsoft.Sql/servers/azureADOnlyAuthentications/*
- Microsoft.Sql/servers/administrators/read - 只有访问 Azure 门户“Azure Active Directory”菜单的用户才需要此操作
- Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*
- Microsoft.Sql/managedInstances/read

还可将上述操作添加到自定义角色，以管理“仅限 Azure AD 身份验证”。 有关详细信息，请参阅[在 Azure Active Directory 中创建和分配自定义角色](../../active-directory/roles/custom-create.md)。

## <a name="managing-azure-ad-only-authentication-using-apis"></a>使用 API 管理“仅限 Azure AD 身份验证”

> [!IMPORTANT]
> 在启用“仅限 Azure AD 身份验证”之前必须设置 Azure AD 管理员。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

必须使用 Azure CLI 2.14.2 或更高版本。

`name` 对应于服务器或实例名称的前缀（例如 `myserver`），`resource-group` 对应于服务器所属的资源（例如 `myresource`）。

## <a name="azure-sql-database"></a>Azure SQL Database

有关详细信息，请参阅 [az sql server ad-only-auth](/cli/azure/sql/server/ad-only-auth)。

### <a name="enable-or-disable-in-sql-database"></a>在 SQL 数据库中启用或禁用

**启用**

```azurecli
az sql server ad-only-auth enable --resource-group myresource --name myserver
```

**Disable**

```azurecli
az sql server ad-only-auth disable --resource-group myresource --name myserver
```

### <a name="check-the-status-in-sql-database"></a>检查 SQL 数据库中的状态

```azurecli
az sql server ad-only-auth get --resource-group myresource --name myserver
```

## <a name="azure-sql-managed-instance"></a>Azure SQL 托管实例

有关详细信息，请参阅 [az sql mi ad-only-auth](/cli/azure/sql/mi/ad-only-auth)。

**启用**

```azurecli
az sql mi ad-only-auth enable --resource-group myresource --name myserver
```

**Disable**

```azurecli
az sql mi ad-only-auth disable --resource-group myresource --name myserver
```

### <a name="check-the-status-in-sql-managed-instance"></a>检查 SQL 托管实例中的状态

```azurecli
az sql mi ad-only-auth get --resource-group myresource --name myserver
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

需要 [Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) 模块或更高版本。

`ServerName` 或 `InstanceName` 对应于服务器名称的前缀（例如 `myserver` 或 `myinstance`），`ResourceGroupName` 对应于服务器所属的资源（例如 `myresource`）。

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="enable-or-disable-in-sql-database"></a>在 SQL 数据库中启用或禁用

**启用**

有关详细信息，请参阅 [Enable-AzSqlServerActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlserveractivedirectoryonlyauthentication)。 还可以运行 `get-help Enable-AzSqlServerActiveDirectoryOnlyAuthentication -full`。

```powershell
Enable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName myserver -ResourceGroupName myresource
```

还可使用以下命令：

```powershell
Get-AzSqlServer -ServerName myserver | Enable-AzSqlServerActiveDirectoryOnlyAuthentication
```

禁用

有关详细信息，请参阅 [Disable-AzSqlServerActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/disable-azsqlserveractivedirectoryonlyauthentication)。

```powershell
Disable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName myserver -ResourceGroupName myresource
```

### <a name="check-the-status-in-sql-database"></a>检查 SQL 数据库中的状态

```powershell
Get-AzSqlServerActiveDirectoryOnlyAuthentication  -ServerName myserver -ResourceGroupName myresource
```

还可使用以下命令：

```powershell
Get-AzSqlServer -ServerName myserver | Get-AzSqlServerActiveDirectoryOnlyAuthentication
```

## <a name="azure-sql-managed-instance"></a>Azure SQL 托管实例

### <a name="enable-or-disable-in-sql-managed-instance"></a>在 Azure SQL 托管实例中启用或禁用

**启用**

有关详细信息，请参阅 [Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlinstanceactivedirectoryonlyauthentication)。

```powershell
Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

还可使用以下命令：

```powershell
Get-AzSqlInstance -InstanceName myinstance | Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication
```

如需这些 PowerShell 命令的详细信息，请运行 `get-help  Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -full`。

禁用

有关详细信息，请参阅 [Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/disable-azsqlinstanceactivedirectoryonlyauthentication)。

```powershell
Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

### <a name="check-the-status-in-sql-managed-instance"></a>检查 SQL 托管实例中的状态

```powershell
Get-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

还可使用以下命令：

```powershell
Get-AzSqlInstance -InstanceName myinstance | Get-AzSqlInstanceActiveDirectoryOnlyAuthentication
```

# <a name="rest-api"></a>[REST API](#tab/rest-api)

需要定义以下参数：

- 在 Azure 门户中导航到“订阅”可以找到 `<subscriptionId>`。
- `<myserver>` 对应于服务器或实例名称的前缀（例如 `myserver`）。
- `<myresource>` 对应于服务器所属的资源（例如 `myresource`）

若要使用最新的 MSAL，请从 https://www.powershellgallery.com/packages/MSAL.PS 下载。

```rest
$subscriptionId = '<subscriptionId>'
$serverName = "<myserver>"
$resourceGroupName = "<myresource>"
```

## <a name="azure-sql-database"></a>Azure SQL Database

有关详细信息，请参阅[服务器 - 仅限 Azure AD 身份验证](/rest/api/sql/2021-02-01-preview/serverazureadonlyauthentications) REST API 文档。

### <a name="enable-or-disable-in-sql-database"></a>在 SQL 数据库中启用或禁用

**启用**

```rest
$body = @{ properties = @{ azureADOnlyAuthentication = 1 } } | ConvertTo-Json
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

**Disable**

```rest
$body = @{ properties = @{ azureADOnlyAuthentication = 0 } } | ConvertTo-Json
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

### <a name="check-the-status-in-sql-database"></a>检查 SQL 数据库中的状态

```rest
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method GET -Headers $authHeader  | Format-List
```

## <a name="azure-sql-managed-instance"></a>Azure SQL 托管实例

有关详细信息，请参阅 [托管实例 - 仅限 Azure AD 身份验证](/rest/api/sql/2021-02-01-preview/managedinstanceazureadonlyauthentications) REST API 文档。

### <a name="enable-or-disable-in-sql-managed-instance"></a>在 Azure SQL 托管实例中启用或禁用

**启用**

```rest
$body = @{   properties = @{  azureADOnlyAuthentication = 1 }  } | ConvertTo-Json 
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

**Disable**

```rest
$body = @{   properties = @{  azureADOnlyAuthentication = 0 }  } | ConvertTo-Json 
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

### <a name="check-the-status-in-sql-managed-instance"></a>检查 SQL 托管实例中的状态

```rest
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method GET -Headers $authHeader  | Format-List
```

# <a name="arm-template"></a>[ARM 模板](#tab/arm)

- 输入部署的 Azure AD 管理员。 转到 [Azure 门户](https://portal.azure.com)并导航到你的 Azure Active Directory 资源可以找到“用户对象 ID”。 在“管理”下，选择“用户” 。 搜索要设置为 Azure SQL 服务器 Azure AD 管理员的用户。 选择该用户，在其“配置文件”页下可以看到“对象 ID” 。
- 可以在 Azure Active Directory 资源的“概述”页中找到“租户 ID” 。

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="enable"></a>启用

以下 ARM 模板在 Azure SQL 数据库中启用“仅限 Azure AD 身份验证”。 若要禁用“仅限 Azure AD 身份验证”，请将 `azureADOnlyAuthentication` 属性设置为 `false`。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "sqlServer_name": {
            "type": "String"
        },
        "aad_admin_name": {
            "type": "String"
        },
        "aad_admin_objectid": {
            "type": "String"
        },
        "aad_admin_tenantid": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/administrators",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('sqlServer_name'), '/ActiveDirectory')]",
            "properties": {
                "administratorType": "ActiveDirectory",
                "login": "[parameters('aad_admin_name')]",
                "sid": "[parameters('aad_admin_objectid')]",
                "tenantId": "[parameters('aad_admin_tenantId')]"
            }
        },        
        {
            "type": "Microsoft.Sql/servers/azureADOnlyAuthentications",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('sqlServer_name'), '/Default')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/administrators', parameters('sqlServer_name'), 'ActiveDirectory')]"
            ],
            "properties": {
                "azureADOnlyAuthentication": true
            }
        }
    ]
}
```

有关详细信息，请参阅 [Microsoft.Sql servers/azureADOnlyAuthentications](/azure/templates/microsoft.sql/servers/azureadonlyauthentications)。

## <a name="azure-sql-managed-instance"></a>Azure SQL 托管实例

### <a name="enable"></a>启用

以下 ARM 模板在 Azure SQL 托管实例中启用“仅限 Azure AD 身份验证”。 若要禁用“仅限 Azure AD 身份验证”，请将 `azureADOnlyAuthentication` 属性设置为 `false`。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "String"
        },
        "aad_admin_name": {
            "type": "String"
        },
        "aad_admin_objectid": {
            "type": "String"
        },
        "aad_admin_tenantid": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/managedInstances/administrators",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('instance'), '/ActiveDirectory')]",
            "properties": {
                "administratorType": "ActiveDirectory",
                "login": "[parameters('aad_admin_name')]",
                "sid": "[parameters('aad_admin_objectid')]",
                "tenantId": "[parameters('aad_admin_tenantId')]"
            }
        },
        {
            "type": "Microsoft.Sql/managedInstances/azureADOnlyAuthentications",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('instance'), '/Default')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/managedInstances/administrators', parameters('instance'), 'ActiveDirectory')]"
            ],
            "properties": {
                "azureADOnlyAuthentication": true
            }
        }
    ]
}

```

有关详细信息，请参阅 [Microsoft.Sql managedInstances/azureADOnlyAuthentications](/azure/templates/microsoft.sql/managedinstances/azureadonlyauthentications)。

---

### <a name="checking-azure-ad-only-authentication-using-t-sql"></a>使用 T-SQL 检查“仅限 Azure AD 身份验证”

已添加 [SEVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql) `IsExternalAuthenticationOnly` 来检查是否为服务器或托管实例启用了“仅限 Azure AD 身份验证”。 `1` 表示已启用该功能，`0` 表示已禁用该功能。

```sql
SELECT SERVERPROPERTY('IsExternalAuthenticationOnly') 
```

## <a name="remarks"></a>备注

- [SQL Server 参与者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)可以设置或删除 Azure AD 管理员，但不能指定“仅限 Azure Active Directory 身份验证”设置。 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)不能设置或删除 Azure AD 管理员，但可以指定“仅限 Azure Active Directory 身份验证”设置。 只有具有更高 Azure RBAC 角色或包含这两种权限的自定义角色的帐户才能设置或删除 Azure AD 管理员，以及指定“仅限 Azure Active Directory 身份验证”设置。 [参与者](../../role-based-access-control/built-in-roles.md#contributor)角色就是这样的一种角色。
- 在 Azure 门户中启用或禁用“仅限 Azure Active Directory 身份验证”后，可以在“SQL 服务器”菜单中看到“活动日志”条目  。
    :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-portal-sql-server-activity-log.png" alt-text="Azure 门户中的“活动日志”条目":::
- 如果指定了 Azure Active Directory 管理员，则只有拥有适当权限的用户才能启用或禁用“仅限 Azure Active Directory 身份验证”设置 。 如果未设置 Azure AD 管理员，则“仅限 Azure Active Directory 身份验证”设置将保持非活动状态，且无法启用或禁用。 如果未设置 Azure AD 管理员，则使用 API 启用“仅限 Azure AD 身份验证”也会失败。
- 在已启用“仅限 Azure AD 身份验证”的情况下，支持拥有适当权限的用户更改 Azure AD 管理员。
- 只允许拥有适当权限的用户在 Azure 门户中更改 Azure AD 管理员以及启用或禁用“仅限 Azure AD 身份验证”。 在 Azure 门户中执行一次“保存”即可完成这两项操作。 必须设置 Azure AD 管理员才能启用“仅限 Azure AD 身份验证”。
- 在已启用“仅限 Azure AD 身份验证”功能的情况下，不支持删除 Azure AD 管理员。 如果已启用“仅限 Azure AD 身份验证”，则使用 API 删除 Azure AD 管理员将会失败。
    - 如果已启用“仅限 Azure Active Directory 身份验证”设置，则 Azure 门户中的“删除管理员”按钮将处于非活动状态 。
- 允许删除 Azure AD 管理员和禁用“仅限 Azure Active Directory 身份验证”设置，但需要适当的用户权限才能完成操作。 在 Azure 门户中执行一次“保存”即可完成这两项操作。
- 拥有适当权限的 Azure AD 用户可以模拟现有的 SQL 用户。
    - 即使启用了“仅限 Azure AD 身份验证”功能，也仍可以在 SQL 身份验证用户之间继续进行模拟。

## <a name="known-issues"></a>已知问题

- 启用“仅限 Azure AD 身份验证”后，无法重置服务器管理员密码。 目前，密码重置操作在门户中会成功，但在 SQL 引擎中会失败。 服务器活动日志中会指示这种失败。 若要重置服务器管理员密码，必须禁用“仅限 Azure AD 身份验证”功能。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：启用 Azure SQL 中的仅限 Azure Active Directory 身份验证](authentication-azure-ad-only-authentication-tutorial.md)

> [!div class="nextstepaction"]
> [在 Azure SQL 中创建启用了仅限 Azure AD 的身份验证的服务器](authentication-azure-ad-only-authentication-create-server.md)