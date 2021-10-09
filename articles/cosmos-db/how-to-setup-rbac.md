---
title: 使用 Azure AD 为 Azure Cosmos DB 帐户配置基于角色的访问控制
description: 了解如何使用 Azure Active Directory 为 Azure Cosmos DB 帐户配置基于角色的访问控制
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/21/2021
ms.author: thweiss
ms.openlocfilehash: 29aeee156ee87c055a3581e9dc2fd0bd86a2064d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128551038"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>使用 Azure Active Directory 为 Azure Cosmos DB 帐户配置基于角色的访问控制
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!NOTE]
> 本文介绍了有关 Azure Cosmos DB 中数据平面操作的基于角色的访问控制。 如果使用管理平面操作，请参阅适用于管理平面操作的[基于角色的访问控制](role-based-access-control.md)一文。

Azure Cosmos DB 公开了一种内置的基于角色的访问控制 (RBAC) 系统，可让你：

- 使用 Azure Active Directory (Azure AD) 标识来验证数据请求。
- 使用细粒度的、基于角色的权限模型来授权数据请求。

## <a name="concepts"></a>概念

Azure Cosmos DB 数据平面 RBAC 建立在其他 RBAC 系统（如 [Azure RBAC](../role-based-access-control/overview.md)）中常见的概念之上：

- [权限模型](#permission-model)由一组操作组成；其中每个操作都映射到一个或多个数据库操作。 操作的一些示例包括读取项、写入项或执行查询。
- Azure Cosmos DB 用户创建包含允许操作列表的[角色定义](#role-definitions)。
- 角色定义通过[角色分配](#role-assignments)分配给特定 Azure AD 标识。 角色分配还定义了角色定义适用的范围；目前有三个范围：
    - 一个 Azure Cosmos DB 帐户，
    - 一个 Azure Cosmos DB 数据库，
    - 一个 Azure Cosmos DB 容器。

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="RBAC 概念":::

## <a name="permission-model"></a><a id="permission-model"></a> 权限模型

> [!IMPORTANT]
> 此权限模型仅涵盖涉及读取和写入数据的数据库操作。 它不涵盖对管理资源的任何类型的管理操作，例如：
> - 创建/替换/删除数据库
> - 创建/替换/删除容器
> - 替换容器吞吐量
> - 创建/替换/删除/读取存储过程
> - 创建/替换/删除/读取触发器
> - 创建/替换/删除/读取用户定义的函数
>
> *不能通过任何 Azure Cosmos DB 数据平面 SDK* 使用 Azure AD 标识对管理操作进行身份验证。 而是，必须通过以下选项之一使用 [Azure RBAC](role-based-access-control.md)：
> - [Azure 资源管理器模板（ARM 模板）](./sql/manage-with-templates.md)
> - [Azure PowerShell 脚本](./sql/manage-with-powershell.md)
> - [Azure CLI 脚本](./sql/manage-with-cli.md)
> - 以下版本的 Azure 管理库：
>   - [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.CosmosDB/)
>   - [Java](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-cosmos)
>   - [Python](https://pypi.org/project/azure-mgmt-cosmosdb/)
>   
> 读取数据库和读取容器被视为[元数据请求](#metadata-requests)。 可以按以下部分所述授予对这些操作的访问权限。

下表列出了权限模型公开的所有操作。

| 名称 | 对应的数据库操作 |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | 读取帐户元数据。 有关详细信息，请参阅[元数据请求](#metadata-requests)。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | 创建新项。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | 通过 ID 和分区键读取单个项（点读）。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | 替换现有项。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | “Upsert”一个项，意味着如果项不存在就创建项，如果存在就替换它。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | 删除项。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | 执行 [SQL 查询](sql-query-getting-started.md)。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | 从容器的[更改源](read-change-feed.md)读取。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | 执行[存储过程](stored-procedures-triggers-udfs.md)。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | 管理多写入区域帐户的[冲突](conflict-resolution-policies.md)（即，列出并删除冲突源中的项）。 |

容器和项级别均支持通配符：

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

### <a name="metadata-requests"></a><a id="metadata-requests"></a> 元数据请求

使用 Azure Cosmos DB SDK 时，这些 SDK 会在初始化期间发出只读元数据请求并为特定数据请求提供服务。 这些元数据请求提取各种配置详细信息，例如： 

- 帐户的全局配置，其中包括帐户可用的 Azure 区域。
- 容器或其索引策略的分区键。
- 构成容器及其地址的物理分区列表。

它们不会提取你在帐户中存储的任何数据。

为了确保最大程度地提高权限模型的透明度，这些元数据请求由 `Microsoft.DocumentDB/databaseAccounts/readMetadata` 操作显式覆盖。 在任何通过某个 Azure Cosmos DB SDK 访问 Azure Cosmos DB 帐户的情况下，都应允许此操作。 它可以在 Azure Cosmos DB 层次结构的任何级别（即帐户、数据库或容器）中分配（通过角色分配）。

`Microsoft.DocumentDB/databaseAccounts/readMetadata` 操作允许的实际元数据请求取决于分配操作的范围：

| 范围 | 操作允许的请求 |
|---|---|
| 帐户 | - 列出帐户下的数据库<br>- 对于帐户下的每个数据库，数据库范围内允许的操作 |
| 数据库 | - 读取数据库元数据<br>- 列出数据库下的容器<br>- 对于数据库下的每个容器，容器范围内允许的操作 |
| 容器 | - 读取容器元数据<br>- 列出容器下的物理分区<br>- 解析每个物理分区的地址 |

## <a name="built-in-role-definitions"></a>内置角色定义

Azure Cosmos DB 公开 2 个内置角色定义：

| ID | 名称 | 包含的操作 |
|---|---|---|
| 00000000-0000-0000-0000-000000000001 | Cosmos DB 内置数据读取者 | `Microsoft.DocumentDB/databaseAccounts/readMetadata`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` |
| 00000000-0000-0000-0000-000000000002 | Cosmos DB 内置数据参与者 | `Microsoft.DocumentDB/databaseAccounts/readMetadata`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*` |

## <a name="create-custom-role-definitions"></a><a id="role-definitions"></a> 创建自定义角色定义

创建自定义角色定义时，需要提供：

- Azure Cosmos DB 帐户的名称。
- 包含帐户的资源组。
- 角色定义的类型：`CustomRole`。
- 角色定义的名称。
- 希望角色允许的[操作](#permission-model)列表。
- 可分配角色定义的一个或多个范围；支持的范围包括：
    - `/`（帐户级别）、
    - `/dbs/<database-name>`（数据库级别）、
    - `/dbs/<database-name>/colls/<container-name>`（容器级别）。

> [!NOTE]
> 下面所述的操作在以下环境中可用：
> - Azure PowerShell：[Az.CosmosDB 版本 1.2.0](https://www.powershellgallery.com/packages/Az.CosmosDB/1.2.0) 或更高版本
> - [Azure CLI](/cli/azure/install-azure-cli)：版本 2.24.0 或更高版本

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

创建一个名为 MyReadOnlyRole 的角色，该角色只包含读取操作：

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

创建一个名为 MyReadWriteRole 的角色，其中包含所有操作：

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

列出已创建的用于提取其 ID 的角色定义：

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>使用 Azure CLI

创建一个名为 MyReadOnlyRole 的角色，该角色只包含读取操作：

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

创建一个名为 MyReadWriteRole 的角色，其中包含所有操作：

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

列出已创建的用于提取其 ID 的角色定义：

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

有关使用 Azure 资源管理器模板创建角色定义的参考和示例，请参阅[此页](/rest/api/cosmos-db-resource-provider/2021-04-15/sqlresources2/create-update-sql-role-definition)。

## <a name="create-role-assignments"></a><a id="role-assignments"></a> 创建角色分配

可以将内置或自定义角色定义与 Azure AD 标识相关联。 创建角色分配时，需要提供：

- Azure Cosmos DB 帐户的名称。
- 包含帐户的资源组。
- 要分配的角色定义的 ID。
- 应为角色定义分配的标识的主体 ID。
- 角色分配的范围；支持的范围包括：
    - `/`（帐户级别）
    - `/dbs/<database-name>`（数据库级别）
    - `/dbs/<database-name>/colls/<container-name>`（容器级别）

  范围必须与角色定义的某个可分配范围匹配，或者是它的子范围。

> [!NOTE]
> 若要创建服务主体的角色分配，请确保使用“Azure Active Directory”门户边栏选项卡的“企业应用程序”部分中找到的“对象 ID”。

> [!NOTE]
> 下面所述的操作在以下环境中可用：
> - Azure PowerShell：[Az.CosmosDB 版本 1.2.0](https://www.powershellgallery.com/packages/Az.CosmosDB/1.2.0) 或更高版本
> - [Azure CLI](/cli/azure/install-azure-cli)：版本 2.24.0 或更高版本

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

为标识分配角色：

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" # as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope "/" `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>使用 Azure CLI

为标识分配角色：

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' # as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

有关使用 Azure 资源管理器模板创建角色分配的参考和示例，请参阅[此页](/rest/api/cosmos-db-resource-provider/2021-04-15/sqlresources2/create-update-sql-role-assignment)。

## <a name="initialize-the-sdk-with-azure-ad"></a>用 Azure AD 初始化 SDK

若要在应用程序中使用 Azure Cosmos DB RBAC，必须更新初始化 Azure Cosmos DB SDK 的方式。 必须传递 `TokenCredential` 类的实例，而不是传递帐户的主键。 此实例为 Azure Cosmos DB SDK 提供了在代表要使用的标识获取 AAD 令牌时所需的上下文。

创建 `TokenCredential` 实例的方式不在本文讨论范围。 有多种方法可以创建此类实例，具体取决于要使用的 AAD 标识类型（用户主体、服务主体、组等）。 最重要的是，`TokenCredential` 实例必须解析为已向其分配角色的标识（主体 ID）。 你可以找到创建 `TokenCredential` 类的示例：

- [在 .NET 中](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [在 Java 中](/java/api/overview/azure/identity-readme#credential-classes)
- [在 JavaScript 中](/javascript/api/overview/azure/identity-readme#credential-classes)

下面的示例使用带有 `ClientSecretCredential` 实例的服务主体。

### <a name="in-net"></a>在 .NET 中

[.NET SDK V3](sql-api-sdk-dotnet-standard.md) 中当前支持 Azure Cosmos DB RBAC。

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>在 Java 中

[Java SDK V4](sql-api-sdk-java-v4.md) 当前支持 Azure Cosmos DB RBAC。

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

### <a name="in-javascript"></a>在 JavaScript 中

[JavaScript SDK V3](sql-api-sdk-node.md) 当前支持 Azure Cosmos DB RBAC。

```javascript
const servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
const client = new CosmosClient({
    "<account-endpoint>",
    aadCredentials: servicePrincipal
});
```

## <a name="authenticate-requests-on-the-rest-api"></a>对 REST API 上的请求进行身份验证

在构造 [REST API 授权标头](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)时，请将 type 参数设置为 aad，并将哈希签名 (sig) 设置为 oauth 令牌，如以下示例所示   ：

`type=aad&ver=1.0&sig=<token-from-oauth>`

## <a name="use-data-explorer"></a>使用数据资源管理器

> [!NOTE]
> Azure 门户中公开的数据资源管理器尚不支持 Azure Cosmos DB RBAC。 若要在浏览数据时使用 Azure AD 标识，必须改用 [Azure Cosmos DB 资源管理器](https://cosmos.azure.com/?feature.enableAadDataPlane=true)。

使用特定 `?feature.enableAadDataPlane=true` 查询函数访问 [Azure Cosmos DB 资源管理器](https://cosmos.azure.com/?feature.enableAadDataPlane=true)并登录时，系统会按照以下逻辑访问数据：

1. 代表已登录的标识尝试获取帐户主密钥的请求。 如果此请求成功，则系统会使用主密钥访问帐户的数据。
1. 如果不允许登录的标识提取帐户的主密钥，则此标识将直接用于对数据访问进行身份验证。 在此模式下，必须为标识[分配适当的角色定义](#role-assignments)，以确保数据访问。

## <a name="audit-data-requests"></a>审核数据请求

使用 Azure Cosmos DB RBAC 时，[诊断日志](cosmosdb-monitor-resource-logs.md)会扩充每个数据操作的标识和授权信息。 这使你可以执行详细审核，并检索用于发送到 Azure Cosmos DB 帐户的每个数据请求的 AAD 标识。

此附加信息存在于 DataPlaneRequests 日志类别中，并包含两个额外的列：

- `aadPrincipalId_g` 显示用于对请求进行身份验证的 AAD 标识的主体 ID。
- `aadAppliedRoleAssignmentId_g` 显示在授权请求时接受的[角色分配](#role-assignments)。

## <a name="enforcing-rbac-as-the-only-authentication-method"></a><a id="disable-local-auth"></a> 强制将 RBAC 用作唯一的身份验证方法

在需要强制客户端以独占方式通过 RBAC 连接到 Azure Cosmos DB 的情况下，可以选择禁用该帐户的主/辅助密钥。 执行此操作时，系统会主动拒绝任何使用主/辅助密钥或资源令牌的传入请求。

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

在使用 Azure 资源管理器模板创建或更新 Azure Cosmos DB 帐户时，请将 `disableLocalAuth` 属性设置为 `true`：

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "properties": {
            "disableLocalAuth": true,
            // ...
        },
        // ...
    },
    // ...
 ]
```

## <a name="limits"></a>限制

- 对于每个 Azure Cosmos DB 帐户，最多可以创建 100 个角色定义和 2,000 个角色分配。
- 只能将角色定义分配给与 Azure Cosmos DB 帐户属于同一 Azure AD 租户的 Azure AD 标识。
- 对于属于超过 200 个组的标识，目前不支持 Azure AD 组解析。
- Azure AD 令牌当前以标头形式传递，每个请求发送到 Azure Cosmos DB 服务，从而增加总体有效负载大小。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>RBAC 支持哪些 Azure Cosmos DB API？

目前仅支持 SQL API。

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>是否可以从 Azure 门户管理角色定义和角色分配？

尚未提供对角色管理的 Azure 门户支持。

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Azure Cosmos DB SQL API 中的哪些 SDK 支持 RBAC？

目前支持 [.NET V3](sql-api-sdk-dotnet-standard.md)、[Java V4](sql-api-sdk-java-v4.md) 和 [JavaScript V3](sql-api-sdk-node.md) SDK。

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Azure AD 令牌过期时，Azure Cosmos DB SDK 是否会自动刷新令牌？

是。

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primarysecondary-keys-when-using-rbac"></a>使用 RBAC 时，是否可以禁用帐户主/辅助密钥的使用？

可以，详见[强制将 RBAC 用作唯一的身份验证方法](#disable-local-auth)。

## <a name="next-steps"></a>后续步骤

- 大概了解如何[安全访问 Cosmos DB 中的数据](secure-access-to-data.md)。
- 详细了解 [Azure Cosmos DB 管理的 RBAC](role-based-access-control.md)。
