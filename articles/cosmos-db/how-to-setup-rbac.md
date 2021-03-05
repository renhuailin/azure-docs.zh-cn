---
title: 使用 Azure AD 为 Azure Cosmos DB 帐户配置基于角色的访问控制
description: 了解如何使用 Azure Active Directory 为 Azure Cosmos DB 帐户配置基于角色的访问控制
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/03/2021
ms.author: thweiss
ms.openlocfilehash: 7c5497615ce71d0be713ef9ae28ab1e0f85b7ddb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177226"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>使用 Azure Cosmos DB 帐户 (预览的 Azure Active Directory 配置基于角色的访问控制) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB 基于角色的访问控制当前为预览版。 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

> [!NOTE]
> 本文介绍了有关 Azure Cosmos DB 中的数据平面操作的基于角色的访问控制。 如果使用管理平面操作，请参阅应用于管理平面操作 [一文的基于角色的访问控制](role-based-access-control.md) 。

Azure Cosmos DB 公开了一种内置的基于角色的访问控制 (RBAC) 系统，可让你：

- 使用 Azure Active Directory (Azure AD) 标识来验证数据请求。
- 使用细化的基于角色的权限模型来授权你的数据请求。

## <a name="concepts"></a>概念

Azure Cosmos DB 数据平面 RBAC 建立在其他 RBAC 系统（如 [AZURE RBAC](../role-based-access-control/overview.md)）中常见的概念之上：

- [权限模型](#permission-model)由一组 **操作** 组成;其中每个操作都映射到一个或多个数据库操作。 操作的一些示例包括读取项、写入项或执行查询。
- Azure Cosmos DB 用户创建包含允许操作列表的 **[角色定义](#role-definitions)** 。
- 角色定义通过 **[角色分配](#role-assignments)** 分配给特定 Azure AD 标识。 角色分配还定义了角色定义适用的作用域;目前有三个作用域：
    - Azure Cosmos DB 帐户，
    - 一个 Azure Cosmos DB 数据库，
    - 一个 Azure Cosmos DB 容器。

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="RBAC 概念":::

> [!NOTE]
> Azure Cosmos DB RBAC 当前不公开任何内置角色定义。

## <a name="permission-model"></a><a id="permission-model"></a> 权限模型

下表列出了权限模型公开的所有操作。

| 名称 | 对应的数据库操作 (s)  |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | 读取帐户元数据。 有关详细信息，请参阅 [元数据请求](#metadata-requests) 。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | 创建新项。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | 按 (点读取) 读取单个项的 ID 和分区键。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | 替换现有项。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | "Upsert" 项，这意味着，如果它不存在，则将其创建，如果存在，则替换它。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | 删除项。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | 执行 [SQL 查询](sql-query-getting-started.md)。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | 从容器的 [更改源](read-change-feed.md)读取数据。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | 执行 [存储过程](stored-procedures-triggers-udfs.md)。 |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | 管理多写入区域帐户的 [冲突](conflict-resolution-policies.md) (即，列出并删除冲突源) 中的项。 |

*容器* 和 *项* 级别支持通配符：

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

> [!IMPORTANT]
> 此权限模型仅涵盖可用于读取和写入数据的数据库操作。 它 **不** 包含任何类型的管理操作，例如创建容器或更改其吞吐量。 若要使用 AAD 标识对管理操作进行身份验证，请改用 [AZURE RBAC](role-based-access-control.md) 。

### <a name="metadata-requests"></a><a id="metadata-requests"></a> 元数据请求

使用 Azure Cosmos DB Sdk 时，这些 Sdk 会在初始化期间发出只读元数据请求并为特定的数据请求提供服务。 这些元数据请求提取各种配置详细信息，例如： 

- 帐户的全局配置，其中包括可用的帐户所在的 Azure 区域。
- 容器或其索引策略的分区键。
- 构成容器及其地址的物理分区的列表。

它们 *不* 会提取你在帐户中存储的任何数据。

为了确保最大程度地提高权限模型的透明度，这些元数据请求由操作明确覆盖 `Microsoft.DocumentDB/databaseAccounts/readMetadata` 。 每种情况下都应该允许此操作，在这种情况下，可以通过 Azure Cosmos DB 的 Sdk 之一访问 Azure Cosmos DB 帐户。 可以通过角色分配) 在 Azure Cosmos DB 层次结构中的任何级别上 (分配该角色， (为、帐户、数据库或容器) 。

操作允许的实际元数据请求 `Microsoft.DocumentDB/databaseAccounts/readMetadata` 取决于操作分配到的范围：

| 范围 | 操作允许的请求 |
|---|---|
| 帐户 | -列出帐户下的数据库<br>-对于帐户下的每个数据库，允许在数据库范围内执行的操作 |
| 数据库 | -读取数据库元数据<br>-列出数据库下的容器<br>-对于数据库下的每个容器，容器范围内允许的操作 |
| 容器 | -读取容器元数据<br>-列出容器下的物理分区<br>-解析每个物理分区的地址 |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> 创建角色定义

创建角色定义时，需要提供：

- Azure Cosmos DB 帐户的名称。
- 包含帐户的资源组。
- 角色定义的类型; `CustomRole` 目前仅支持。
- 角色定义的名称。
- 希望角色允许的 [操作](#permission-model) 的列表。
- 一个或多个作用域 (s) 可在其中分配角色定义;支持的作用域包括：
    - `/` (帐户级) ，
    - `/dbs/<database-name>` (数据库级) ，
    - `/dbs/<database-name>/colls/<container-name>` (容器级) 。

> [!NOTE]
> 下面所述的操作当前在中可用：
> - Azure PowerShell： [CosmosDB 2.0.1 版-预览版](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI： ["cosmosdb" 扩展版本 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

创建一个名为 *MyReadOnlyRole* 的角色，该角色只包含读取操作：

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

创建一个名为 *MyReadWriteRole* 的角色，其中包含所有操作：

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

列出已创建的用于提取其 Id 的角色定义：

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

创建一个名为 *MyReadOnlyRole* 的角色，该角色只包含读取操作：

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

创建一个名为 *MyReadWriteRole* 的角色，其中包含所有操作：

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

列出已创建的用于提取其 Id 的角色定义：

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

## <a name="create-role-assignments"></a><a id="role-assignments"></a> 创建角色分配

创建角色定义后，可以将其与 AAD 标识相关联。 创建角色分配时，需要提供：

- Azure Cosmos DB 帐户的名称。
- 包含帐户的资源组。
- 要分配的角色定义的 ID。
- 应为角色定义分配的标识的主体 ID。
- 角色分配的范围;支持的作用域包括：
    - `/` (帐户级别的) 
    - `/dbs/<database-name>` (数据库级别的) 
    - `/dbs/<database-name>/colls/<container-name>` (容器级别的) 

  作用域必须与角色定义的可分配作用域之一的子作用域匹配。

> [!NOTE]
> 若要创建服务主体的角色分配，请确保使用 **Azure Active Directory** 门户边栏选项卡的 "**企业应用程序**" 部分中的 "**对象 ID** "。

> [!NOTE]
> 下面所述的操作当前在中可用：
> - Azure PowerShell： [CosmosDB 2.0.1 版-预览版](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI： ["cosmosdb" 扩展版本 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

为标识分配角色：

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" // as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope $accountName `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>使用 Azure CLI

为标识分配角色：

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' // as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>用 Azure AD 初始化 SDK

若要在应用程序中使用 Azure Cosmos DB RBAC，必须更新初始化 Azure Cosmos DB SDK 的方式。 必须传递类的实例，而不是传递帐户的主键 `TokenCredential` 。 此实例向 Azure Cosmos DB SDK 提供了在代表要使用的标识获取 AAD 令牌时所需的上下文。

创建实例的方式 `TokenCredential` 超出了本文的范围。 有多种方法可以创建此类实例，具体取决于要使用的 AAD 标识的类型 (用户主体、服务主体、组等 ) 。 最重要的是，你 `TokenCredential` 的实例必须解析为你已向其分配了角色的 (主体 ID) 标识。 你可以找到创建类的示例 `TokenCredential` ：

- [.NET 中](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme#credential-classes)
- [Java 中的](https://docs.microsoft.com/java/api/overview/azure/identity-readme#credential-classes)

下面的示例将服务主体与实例一起使用 `ClientSecretCredential` 。

### <a name="in-net"></a>在 .NET 中

> [!NOTE]
> 必须使用 `preview` 版本的 Azure Cosmos DB .NET SDK 才能访问此功能。

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>在 Java 中

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

## <a name="auditing-data-requests"></a>审核数据请求

使用 Azure Cosmos DB RBAC 时， [诊断日志](cosmosdb-monitor-resource-logs.md) 会扩充每个数据操作的标识和授权信息。 这使你可以执行详细审核并检索用于发送到 Azure Cosmos DB 帐户的每个数据请求的 AAD 标识。

此附加信息在 **DataPlaneRequests** 日志类别中流动，并包含另外两列：

- `aadPrincipalId_g` 显示用于对请求进行身份验证的 AAD 标识的主体 ID。
- `aadAppliedRoleAssignmentId_g` 显示在授权请求时接受的 [角色分配](#role-assignments) 。

## <a name="limits"></a>限制

- 对于每个 Azure Cosmos DB 帐户，最多可以创建100个角色定义和2000个角色分配。
- 对于属于超过200个组的标识，目前不支持 Azure AD 组解析。
- Azure AD 令牌当前以标头的形式传递，每个请求发送到 Azure Cosmos DB 服务，从而增加总体负载大小。
- 尚不支持通过 [Azure Cosmos DB 资源管理器](data-explorer.md) Azure AD 访问数据。 使用 Azure Cosmos DB 资源管理器仍要求用户有权访问帐户的主密钥。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>RBAC 支持哪些 Azure Cosmos DB Api？

目前仅支持 SQL API。

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>是否可以从 Azure 门户管理角色定义和角色分配？

尚未提供对角色管理的 Azure 门户支持。

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Azure Cosmos DB SQL API 中的哪些 Sdk 支持 RBAC？

目前支持 [.Net V3](sql-api-sdk-dotnet-standard.md) 和 [Java V4](sql-api-sdk-java-v4.md) sdk。

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Azure Cosmos DB Sdk 在过期时是否自动刷新 Azure AD 令牌？

是的。

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>使用 RBAC 时，是否可以禁用帐户主键的使用？

目前不能禁用帐户主键。

## <a name="next-steps"></a>后续步骤

- 大概了解如何[安全访问 Cosmos DB 中的数据](secure-access-to-data.md)。
- 详细了解 [Azure Cosmos DB 管理的 RBAC](role-based-access-control.md)。
