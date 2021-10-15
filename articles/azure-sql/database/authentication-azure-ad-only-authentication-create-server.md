---
title: 创建启用了“仅限 Azure Active Directory 身份验证”的服务器
description: 本文将引导你创建启用了“仅限 Azure Active Directory (Azure AD) 身份验证”的 Azure SQL 逻辑服务器或托管实例，这会禁用使用 SQL 身份验证的连接
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/04/2021
ms.openlocfilehash: 959175611f42c8c75da465044c7962c585d3728f
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458657"
---
# <a name="create-server-with-azure-ad-only-authentication-enabled-in-azure-sql"></a>在 Azure SQL 中创建启用了“仅限 Azure AD 身份验证”的服务器

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 本文所述的“仅限 Azure AD 身份验证”功能以公共预览版提供 。 有关此功能的详细信息，请参阅 [Azure SQL 中的“仅限 Azure AD 身份验证”](authentication-azure-ad-only-authentication.md)。 “仅限 Azure AD 身份验证”目前不适用于 Azure Synapse Analytics。

本操作指南概述了在预配期间创建启用了[仅限 Azure AD 身份验证](authentication-azure-ad-only-authentication.md)的 Azure SQL 数据库的[逻辑服务器](logical-servers.md)或 [Azure SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)的步骤。 “仅限 Azure AD 身份验证”功能可阻止用户使用 SQL 身份验证连接到服务器或托管实例，仅允许使用 Azure AD 身份验证进行连接。

## <a name="prerequisites"></a>先决条件

- 使用 Azure CLI 时，需要 2.26.1 或更高版本。 若要详细了解安装和最新版本，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。
- 使用 PowerShell 时需要 [Az 6.1.0](https://www.powershellgallery.com/packages/Az/6.1.0) 模块或更高版本。
- 如果要使用 Azure CLI、PowerShell 或 REST API 预配托管实例，需要在开始之前创建虚拟网络和子网。 有关详细信息，请参阅[为 Azure SQL 托管实例创建虚拟网络](../managed-instance/virtual-network-subnet-create-arm-template.md)。

## <a name="permissions"></a>权限

若要预配逻辑服务器或托管实例，需要具有相应权限才能创建这些资源。 具有更高权限的 Azure 用户，例如订阅[所有者](../../role-based-access-control/built-in-roles.md#owner)、[参与者](../../role-based-access-control/built-in-roles.md#contributor)、[服务管理员](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)和[共同管理员](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)，拥有创建 SQL Server 或托管实例的特权。 若要使用特权最低的 Azure RBAC 角色创建这些资源，请对 SQL 数据库使用 [SQL Server 参与者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)角色，对托管实例使用 [SQL 托管实例参与者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)角色。

[SQL 安全管理器](../../role-based-access-control/built-in-roles.md#sql-security-manager) Azure RBAC 角色没有足够的权限来创建启用了“仅限 Azure AD 身份验证”的服务器或实例。 创建服务器或实例后，将需要 [SQL 安全管理器](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色来管理“仅限 Azure AD 身份验证”功能。

## <a name="provision-with-azure-ad-only-authentication-enabled"></a>在启用了“仅限 Azure AD 身份验证”的情况下预配

以下部分提供了一些示例和脚本，介绍如何创建逻辑服务器或托管实例，为服务器或实例设置 Azure AD 管理员，以及如何在服务器创建期间启用“仅限 Azure AD 身份验证”。 有关该功能的详细信息，请参阅[仅限 Azure AD 身份验证](authentication-azure-ad-only-authentication.md)。

在我们的示例中，我们将使用系统分配的服务器管理员和密码，在服务器或托管实例创建期间启用“仅限 Azure AD 身份验证”。 启用“仅限 Azure AD 身份验证”后，该功能将阻止服务器管理员访问，仅允许 Azure AD 管理员访问该资源。 可以选择向 API 添加参数，以在服务器创建过程中包含自己的服务器管理员和密码。 但是，在禁用“仅限 Azure AD 身份验证”之前，无法重置密码。

若要在创建服务器或托管实例后更改现有属性，应使用其他现有 API。 有关详细信息，请参阅[使用 API 管理仅 Azure AD 身份验证](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)和[使用 Azure SQL 配置和管理 Azure AD 身份验证](authentication-aad-configure.md)。

> [!NOTE]
> 如果“仅限 Azure AD 身份验证”设置为 false（默认情况下为 false），则需要在服务器或托管实例创建过程中将服务器管理员和密码包含在所有 API 中。

## <a name="azure-sql-database"></a>Azure SQL 数据库

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 浏览到 Azure 门户中的[选择 SQL 部署](https://portal.azure.com/#create/Microsoft.AzureSQL)选项页。

1. 如果你尚未登录到 Azure 门户，请按提示登录。

1. 在“SQL 数据库”下将“资源类型”设置保留为“单一数据库”，然后选择“创建”   。

1. 在“创建 SQL 数据库”窗体的“基本信息”选项卡上的“项目详细信息”下，选择所需的 Azure订阅   。

1. 对于“资源组”，请选择“新建”，输入资源组的名称，然后选择“确定”  。

1. 在“数据库名称”处，输入数据库的名称。

1. 对于“服务器”，选择“新建”，并使用以下值填写“新服务器”窗体 ：

   - **服务器名称**：输入唯一的服务器名称。 对于 Azure 中的所有服务器，服务器名称必须全局唯一，而不只是在订阅中唯一。 输入一个值，Azure 门户将告知你是否可用。
   - **位置**：从下拉列表中选择一个位置
   - **身份验证方法**：选择“仅使用 Azure Active Directory (Azure AD) 身份验证”。
   - 选择“设置管理员”，这将打开一个菜单，在其中选择一个Azure AD 主体作为逻辑服务器 Azure AD 管理员。 完成后，使用“选择”按钮设置管理员。

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-portal-create-server.png" alt-text="创建启用了仅 Azure AD 身份验证的服务器的屏幕截图":::
    
1. 在完成时选择“下一步:网络”。

1. 在“网络”选项卡上，对于“连接方法”，选择“公共终结点”  。

1. 对于“防火墙规则”，将“添加当前客户端 IP 地址”设置为“是”  。 将“允许 Azure 服务和资源访问此服务器”设置保留为“否” 。 

1. 将“连接策略”和“最低 TLS 版本”设置保留为其默认值 。

1. 在页面底部选择“下一步: 安全”。 为环境配置 Azure Defender for SQL、账本、标识和透明数据加密的任何设置   。 也可以跳过这些设置。

   > [!NOTE]
   > 仅 Azure AD 身份验证中不支持使用用户分配的托管标识 (UMI)。 请勿将“标识”部分的服务器标识设置为 UMI。

1. 在页面底部选择“查看 + 创建”。

1. 在“查看 + 创建”页上，查看后选择“创建”。 

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI命令 `az sql server create` 用于预配新的逻辑服务器。 以下命令将预配启用了“仅限 Azure AD 的身份验证”的新服务器。

服务器 SQL 管理员登录名将自动创建，密码将设置为随机密码。 由于在服务器创建过程中禁用了 SQL 身份验证连接，因此不会使用 SQL 管理员登录。

服务器 Azure AD 管理员将是你为 `<AzureADAccount>` 设置的帐户，可用于管理服务器。

替换示例中的以下值：

- `<AzureADAccount>`：可以是 Azure AD 用户或组。 例如： `DummyLogin`
- `<AzureADAccountSID>`：用户的 Azure AD 对象 ID
- `<ResourceGroupName>`：逻辑服务器的资源组名称
- `<ServerName>`：使用唯一的逻辑服务器名称

```azurecli
az sql server create --enable-ad-only-auth --external-admin-principal-type User --external-admin-name <AzureADAccount> --external-admin-sid <AzureADAccountSID> -g <ResourceGroupName> -n <ServerName>
```

有关详细信息，请参阅 [az sql server create](/cli/azure/sql/server#az_sql_server_create)。

要在创建后检查服务器状态，请参阅以下命令：

```azurecli
az sql server show --name <ServerName> --resource-group <ResourceGroupName> --expand-ad-admin
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 命令 `New-AzSqlServer` 用于预配新的 Azure SQL 逻辑服务器。 以下命令将预配启用了“仅限 Azure AD 的身份验证”的新服务器。 

服务器 SQL 管理员登录名将自动创建，密码将设置为随机密码。 由于在服务器创建过程中禁用了 SQL 身份验证连接，因此不会使用 SQL 管理员登录。

服务器 Azure AD 管理员将是你为 `<AzureADAccount>` 设置的帐户，可用于管理服务器。

替换示例中的以下值：

- `<ResourceGroupName>`：逻辑服务器的资源组名称
- `<Location>`：服务器的位置，例如 `West US` 或 `Central US`
- `<ServerName>`：使用唯一的逻辑服务器名称
- `<AzureADAccount>`：可以是 Azure AD 用户或组。 例如： `DummyLogin`

```powershell
New-AzSqlServer -ResourceGroupName "<ResourceGroupName>" -Location "<Location>" -ServerName "<ServerName>" -ServerVersion "12.0" -ExternalAdminName "<AzureADAccount>" -EnableActiveDirectoryOnlyAuthentication
```

有关详细信息，请参阅 [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)。

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

[服务器 - 创建或更新](/rest/api/sql/2020-11-01-preview/servers/create-or-update) REST API 可用于在预配期间创建启用了仅 Azure AD 身份验证的逻辑服务器。 

以下脚本将预配逻辑服务器，将 Azure AD 管理员设置为 `<AzureADAccount>`，并启用“仅限 Azure AD 身份验证”。 服务器 SQL 管理员登录名也将自动创建，而且密码将设置为随机密码。 由于在预配过程中禁用了 SQL 身份验证连接，因此不会使用 SQL 管理员登录。

预配完成后，可使用 Azure AD 管理员 `<AzureADAccount>` 管理服务器。

替换示例中的以下值：

- `<tenantId>`：可以通过转到 [Azure 门户](https://portal.azure.com)，然后转到 Azure Active Directory 资源来找到此值。 在“概述”窗格中，应会看到“租户 ID” 
- `<subscriptionId>`：可以在 Azure 门户中找到订阅 ID
- `<ServerName>`：使用唯一的逻辑服务器名称
- `<ResourceGroupName>`：逻辑服务器的资源组名称
- `<AzureADAccount>`：可以是 Azure AD 用户或组。 例如： `DummyLogin`
- `<Location>`：服务器的位置，例如 `westus2` 或 `centralus`
- `<objectId>`：可以通过转到 [Azure 门户](https://portal.azure.com)，然后转到 Azure Active Directory 资源来找到此值。 在“用户”窗格中，搜索 Azure AD 用户并查找其“对象 ID” 

```rest
Import-Module Azure
Import-Module MSAL.PS

$tenantId = '<tenantId>'
$clientId = '1950a258-227b-4e31-a9cf-717495945fc2' # Static Microsoft client ID used for getting a token
$subscriptionId = '<subscriptionId>'
$uri = "urn:ietf:wg:oauth:2.0:oob" 
$authUrl = "https://login.windows.net/$tenantId"
$serverName = "<ServerName>"
$resourceGroupName = "<ResourceGroupName>"

Login-AzAccount -tenantId $tenantId

# login as a user with SQL Server Contributor role or higher 

# Get a token

$result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -TenantId $tenantId -Scopes "https://management.core.windows.net/.default"

#Authetication header
$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

# Enable Azure AD-only auth 
# No server admin is specified, and only Azure AD admin and Azure AD-only authentication is set to true
# Server admin (login and password) is generated by the system

# Authentication body
# The sid is the Azure AD Object ID for the user 

$body = '{ 
"location": "<Location>",
"properties": { "administrators":{ "login":"<AzureADAccount>", "sid":"<objectId>", "tenantId":"<tenantId>", "principalType":"User", "azureADOnlyAuthentication":true }
  }
}'

# Provision the server

Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/?api-version=2020-11-01-preview -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

若要检查服务器状态，可以使用以下脚本：

```rest
$uri = 'https://management.azure.com/subscriptions/'+$subscriptionId+'/resourceGroups/'+$resourceGroupName+'/providers/Microsoft.Sql/servers/'+$serverName+'?api-version=2020-11-01-preview&$expand=administrators/activedirectory'

$responce=Invoke-WebRequest -Uri $uri -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"

$responce.statuscode

$responce.content
```

# <a name="arm-template"></a>[ARM 模板](#tab/arm-template)

有关 ARM 模板的详细信息，请参阅 [Azure SQL 数据库和 SQL 托管实例的 Azure 资源管理器模板](arm-templates-content-guide.md)。

要使用 ARM 模板预配逻辑服务器，同时为服务器设置 Azure AD 管理员并且启用了“仅限 Azure AD 身份验证”，请参阅我们的[启用“仅限 Azure AD 身份验证”的 Azure SQL 逻辑服务器](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-logical-server-aad-only-auth)快速入门模板。

还可使用以下模板。 使用 [Azure 门户中的自定义部署](https://portal.azure.com/#create/Microsoft.Template)，在编辑器中生成自己的模板。 接下来，粘贴到示例中后保存配置。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "server": {
            "type": "string",
            "defaultValue": "[uniqueString('sql', resourceGroup().id)]",
            "metadata": {
                "description": "The name of the logical server."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "aad_admin_name": {
            "type": "String",
            "metadata": {
                "description": "The name of the Azure AD admin for the SQL server."
            }
        },
        "aad_admin_objectid": {
            "type": "String",
            "metadata": {
                "description": "The Object ID of the Azure AD admin."
            }
        },
        "aad_admin_tenantid": {
            "type": "String",
            "defaultValue": "[subscription().tenantId]",
            "metadata": {
                "description": "The Tenant ID of the Azure Active Directory"
            }
        },
        "aad_admin_type": {
            "defaultValue": "User",
            "allowedValues": [
                "User",
                "Group",
                "Application"
            ],
            "type": "String"
        },
        "aad_only_auth": {
            "defaultValue": true,
            "type": "Bool"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('server')]",
            "location": "[parameters('location')]",
            "properties": {
                "administrators": {
                    "login": "[parameters('aad_admin_name')]",
                    "sid": "[parameters('aad_admin_objectid')]",
                    "tenantId": "[parameters('aad_admin_tenantid')]",
                    "principalType": "[parameters('aad_admin_type')]",
                    "azureADOnlyAuthentication": "[parameters('aad_only_auth')]"
                }
            }
        }
    ]
}
```

---

## <a name="azure-sql-managed-instance"></a>Azure SQL 托管实例

# <a name="portal"></a>[门户](#tab/azure-portal)

当前不支持通过 Azure 门户使用仅 Azure AD 身份验证来管理或部署托管实例。 可以通过 Azure CLI、PowerShell、Rest API 或 ARM 模板使用仅 Azure AD 身份验证来部署托管实例。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 命令 `az sql mi create` 用于预配新的 Azure SQL 托管实例。 以下命令将预配启用了“仅限 Azure AD 身份验证”的新托管实例。

> [!NOTE]
> 作为先决条件，该脚本要求创建虚拟网络和子网。

托管实例 SQL 管理员登录名将自动创建，密码将设置为随机密码。 由于在预配过程中禁用了 SQL 身份验证连接，因此不会使用 SQL 管理员登录名。

Azure AD 管理员将是为 `<AzureADAccount>` 设置的帐户，可用于在预配完成时管理实例。

替换示例中的以下值：

- `<AzureADAccount>`：可以是 Azure AD 用户或组。 例如： `DummyLogin`
- `<AzureADAccountSID>`：用户的 Azure AD 对象 ID
- `<managedinstancename>`：命名要创建的托管实例
- `<ResourceGroupName>`：托管实例的资源组的名称。 资源组还应包括创建的虚拟网络和子网
- 需要使用 `<Subscription ID>`、`<ResourceGroupName>`、`<VNetName>` 和 `<SubnetName>` 更新 `subnet` 参数。 可以在 Azure 门户中找到订阅 ID

```azurecli
az sql mi create --enable-ad-only-auth --external-admin-principal-type User --external-admin-name <AzureADAccount> --external-admin-sid <AzureADAccountSID> -g <ResourceGroupName> -n <managedinstancename> --subnet /subscriptions/<Subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>
```

有关详细信息，请参阅 [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 命令 `New-AzSqlInstance` 用于预配新的 Azure SQL 托管实例。 以下命令将预配启用了“仅限 Azure AD 身份验证”的新托管实例。

> [!NOTE]
> 作为先决条件，该脚本要求创建虚拟网络和子网。

托管实例 SQL 管理员登录名将自动创建，密码将设置为随机密码。 由于在预配过程中禁用了 SQL 身份验证连接，因此不会使用 SQL 管理员登录名。

Azure AD 管理员将是为 `<AzureADAccount>` 设置的帐户，可用于在预配完成时管理实例。

替换示例中的以下值：

- `<managedinstancename>`：命名要创建的托管实例
- `<ResourceGroupName>`：托管实例的资源组的名称。 资源组还应包括创建的虚拟网络和子网
- `<Location>`：服务器的位置，例如 `West US` 或 `Central US`
- `<AzureADAccount>`：可以是 Azure AD 用户或组。 例如： `DummyLogin`
- 需要使用 `<Subscription ID>`、`<ResourceGroupName>`、`<VNetName>` 和 `<SubnetName>` 更新 `SubnetId` 参数。 可以在 Azure 门户中找到订阅 ID


```powershell
New-AzSqlInstance -Name "<managedinstancename>" -ResourceGroupName "<ResourceGroupName>" -ExternalAdminName "<AzureADAccount>" -EnableActiveDirectoryOnlyAuthentication -Location "<Location>" -SubnetId "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4
```

有关详细信息，请参阅 [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)。

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

[托管实例 - 创建或更新](/rest/api/sql/2020-11-01-preview/managed-instances/create-or-update) REST API 可用于在预配期间创建启用了“仅限 Azure AD 身份验证”的托管实例。

> [!NOTE]
> 作为先决条件，该脚本要求创建虚拟网络和子网。

以下脚本将预配托管实例，将 Azure AD 管理员设置为 `<AzureADAccount>`，并启用“仅限 Azure AD 身份验证”。 实例 SQL 管理员登录名也将自动创建，而且密码将设置为随机密码。 由于在预配过程中禁用了 SQL 身份验证连接，因此不会使用 SQL 管理员登录。

Azure AD 管理员 `<AzureADAccount>` 可用于在预配完成时管理实例。

替换示例中的以下值：

- `<tenantId>`：可以通过转到 [Azure 门户](https://portal.azure.com)，然后转到 Azure Active Directory 资源来找到此值。 在“概述”窗格中，应会看到“租户 ID” 
- `<subscriptionId>`：可以在 Azure 门户中找到订阅 ID
- `<instanceName>`：使用唯一的托管实例名称
- `<ResourceGroupName>`：逻辑服务器的资源组名称
- `<AzureADAccount>`：可以是 Azure AD 用户或组。 例如： `DummyLogin`
- `<Location>`：服务器的位置，例如 `westus2` 或 `centralus`
- `<objectId>`：可以通过转到 [Azure 门户](https://portal.azure.com)，然后转到 Azure Active Directory 资源来找到此值。 在“用户”窗格中，搜索 Azure AD 用户并查找其“对象 ID” 
- 需要使用 `<ResourceGroupName>`、`Subscription ID`、`<VNetName>` 和 `<SubnetName>` 更新 `subnetId` 参数


```rest
Import-Module Azure
Import-Module MSAL.PS

$tenantId = '<tenantId>'
$clientId = '1950a258-227b-4e31-a9cf-717495945fc2' # Static Microsoft client ID used for getting a token
$subscriptionId = '<subscriptionId>'
$uri = "urn:ietf:wg:oauth:2.0:oob" 
$instanceName = "<instanceName>"
$resourceGroupName = "<ResourceGroupName>"
$scopes ="https://management.core.windows.net/.default"

Login-AzAccount -tenantId $tenantId

# Login as an Azure AD user with permission to provision a managed instance

$result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -TenantId $tenantId -Scopes $scopes

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

$body = '{
"name": "<instanceName>", "type": "Microsoft.Sql/managedInstances", "identity": { "type": "SystemAssigned"},"location": "<Location>", "sku": {"name": "GP_Gen5", "tier": "GeneralPurpose", "family":"Gen5","capacity": 8},
"properties": {"administrators":{ "login":"<AzureADAccount>", "sid":"<objectId>", "tenantId":"<tenantId>", "principalType":"User", "azureADOnlyAuthentication":true },
"subnetId": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>",
"licenseType": "LicenseIncluded", "vCores": 8, "storageSizeInGB": 2048, "collation": "SQL_Latin1_General_CP1_CI_AS", "proxyOverride": "Proxy", "timezoneId": "UTC", "privateEndpointConnections": [], "storageAccountType": "GRS", "zoneRedundant": false 
  }
}'

# To provision the instance, execute the `PUT` command

Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$instanceName/?api-version=2020-11-01-preview -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"

```

要检查结果，请执行 `GET` 命令：

```rest
Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$instanceName/?api-version=2020-11-01-preview -Method GET -Headers $authHeader  | Format-List
```

# <a name="arm-template"></a>[ARM 模板](#tab/arm-template)

若要预配新的托管实例、虚拟网络和子网，为实例设置 Azure AD 管理员并启用“仅限 Azure AD 身份验证”，请使用以下模板。

使用 [Azure 门户中的自定义部署](https://portal.azure.com/#create/Microsoft.Template)，在编辑器中生成自己的模板。 接下来，粘贴到示例中后保存配置。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "managedInstanceName": {
            "type": "String",
            "metadata": {
                "description": "Enter managed instance name."
            }
        },
        "aad_admin_name": {
            "type": "String",
            "metadata": {
                "description": "The name of the Azure AD admin for the SQL managed instance."
            }
        },
        "aad_admin_objectid": {
            "type": "String",
            "metadata": {
                "description": "The Object ID of the Azure AD admin."
            }
        },
        "aad_admin_tenantid": {
            "type": "String",
            "defaultValue": "[subscription().tenantId]",
            "metadata": {
                "description": "The Tenant ID of the Azure Active Directory"
            }
        },
        "aad_admin_type": {
            "defaultValue": "User",
            "allowedValues": [
                "User",
                "Group",
                "Application"
            ],
            "type": "String"
        },
        "aad_only_auth": {
            "defaultValue": true,
            "type": "Bool"
        },
        "location": {
            "defaultValue": "[resourceGroup().location]",
            "type": "String",
            "metadata": {
                "description": "Enter location. If you leave this field blank resource group location would be used."
            }
        },
        "virtualNetworkName": {
            "type": "String",
            "defaultValue": "SQLMI-VNET",
            "metadata": {
                "description": "Enter virtual network name. If you leave this field blank name will be created by the template."
            }
        },
        "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "String",
            "metadata": {
                "description": "Enter virtual network address prefix."
            }
        },
        "subnetName": {
            "type": "String",
            "defaultValue": "ManagedInstances",
            "metadata": {
                "description": "Enter subnet name. If you leave this field blank name will be created by the template."
            }
        },
        "subnetPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "String",
            "metadata": {
                "description": "Enter subnet address prefix."
            }
        },
        "skuName": {
            "defaultValue": "GP_Gen5",
            "allowedValues": [
                "GP_Gen5",
                "BC_Gen5"
            ],
            "type": "String",
            "metadata": {
                "description": "Enter sku name."
            }
        },
        "vCores": {
            "defaultValue": 16,
            "allowedValues": [
                8,
                16,
                24,
                32,
                40,
                64,
                80
            ],
            "type": "Int",
            "metadata": {
                "description": "Enter number of vCores."
            }
        },
        "storageSizeInGB": {
            "defaultValue": 256,
            "minValue": 32,
            "maxValue": 8192,
            "type": "Int",
            "metadata": {
                "description": "Enter storage size."
            }
        },
        "licenseType": {
            "defaultValue": "LicenseIncluded",
            "allowedValues": [
                "BasePrice",
                "LicenseIncluded"
            ],
            "type": "String",
            "metadata": {
                "description": "Enter license type."
            }
        }
    },
    "variables": {
        "networkSecurityGroupName": "[concat('SQLMI-', parameters('managedInstanceName'), '-NSG')]",
        "routeTableName": "[concat('SQLMI-', parameters('managedInstanceName'), '-Route-Table')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2020-06-01",
            "name": "[variables('networkSecurityGroupName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "allow_tds_inbound",
                        "properties": {
                            "description": "Allow access to data",
                            "protocol": "Tcp",
                            "sourcePortRange": "*",
                            "destinationPortRange": "1433",
                            "sourceAddressPrefix": "VirtualNetwork",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 1000,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "allow_redirect_inbound",
                        "properties": {
                            "description": "Allow inbound redirect traffic to Managed Instance inside the virtual network",
                            "protocol": "Tcp",
                            "sourcePortRange": "*",
                            "destinationPortRange": "11000-11999",
                            "sourceAddressPrefix": "VirtualNetwork",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 1100,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "deny_all_inbound",
                        "properties": {
                            "description": "Deny all other inbound traffic",
                            "protocol": "*",
                            "sourcePortRange": "*",
                            "destinationPortRange": "*",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Deny",
                            "priority": 4096,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "deny_all_outbound",
                        "properties": {
                            "description": "Deny all other outbound traffic",
                            "protocol": "*",
                            "sourcePortRange": "*",
                            "destinationPortRange": "*",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Deny",
                            "priority": 4096,
                            "direction": "Outbound"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/routeTables",
            "apiVersion": "2020-06-01",
            "name": "[variables('routeTableName')]",
            "location": "[parameters('location')]",
            "properties": {
                "disableBgpRoutePropagation": false
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2020-06-01",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('routeTableName')]",
                "[variables('networkSecurityGroupName')]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[parameters('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetPrefix')]",
                            "routeTable": {
                                "id": "[resourceId('Microsoft.Network/routeTables', variables('routeTableName'))]"
                            },
                            "networkSecurityGroup": {
                                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
                            },
                            "delegations": [
                                {
                                    "name": "miDelegation",
                                    "properties": {
                                        "serviceName": "Microsoft.Sql/managedInstances"
                                    }
                                }
                            ]
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Sql/managedInstances",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('managedInstanceName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[parameters('virtualNetworkName')]"
            ],
            "sku": {
                "name": "[parameters('skuName')]"
            },
            "identity": {
                "type": "SystemAssigned"
            },
            "properties": {
                "subnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
                "storageSizeInGB": "[parameters('storageSizeInGB')]",
                "vCores": "[parameters('vCores')]",
                "licenseType": "[parameters('licenseType')]",
                "administrators": {
                    "login": "[parameters('aad_admin_name')]",
                    "sid": "[parameters('aad_admin_objectid')]",
                    "tenantId": "[parameters('aad_admin_tenantid')]",
                    "principalType": "[parameters('aad_admin_type')]",
                    "azureADOnlyAuthentication": "[parameters('aad_only_auth')]"
                }
            }
        }
    ]
}
```

---

### <a name="grant-directory-readers-permissions"></a>授予目录读取者权限

完成托管实例的部署后，用户可能会注意到托管实例需要“读取”权限才能访问 Azure Active Directory。 具有足够特权的人员单击 Azure 门户中显示的消息即可授予读取权限。 有关详细信息，请参阅 [Azure SQL 的 Azure Active Directory 中的目录读取者角色](authentication-aad-directory-readers-role.md)。

:::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-portal-read-permissions.png" alt-text="Azure 门户中显示所需读取权限的 Active Directory 管理员菜单的屏幕截图":::

## <a name="limitations"></a>限制

- 目前不支持在预配期间使用 Azure 门户创建启用了仅 Azure AD 身份验证的托管实例。
- 若要重置服务器管理员密码，必须禁用“仅限 Azure AD 身份验证”。
- 如果“仅限 Azure AD 身份验证”已禁用，则在使用所有 API 时，必须使用服务器管理员和密码创建服务器。

## <a name="next-steps"></a>后续步骤

- 如果已经有 SQL Server 或托管实例，只想启用“仅限 Azure AD 身份验证”，请参阅[教程：使用 Azure SQL 启用仅限 Azure Active Directory 身份验证](authentication-azure-ad-only-authentication-tutorial.md)。
- 有关“仅限 Azure AD 身份验证”功能的详细信息，请参阅 [Azure SQL 中的“仅限 Azure AD 身份验证”](authentication-azure-ad-only-authentication.md)。
- 如果希望在启用仅 Azure AD 身份验证的情况下强制创建服务器，请参阅 [Azure SQL 中仅 Azure Active Directory 身份验证的 Azure Policy](authentication-azure-ad-only-authentication-policy.md)