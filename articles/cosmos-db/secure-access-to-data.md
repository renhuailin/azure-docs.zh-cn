---
title: 了解如何保护对 Azure Cosmos DB 中数据的访问
description: 了解 Azure Cosmos DB 中的访问控制概念，包括主密钥、只读密钥、用户和权限。
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/30/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: a2cf8ef27efb6e368390f23d4011e5dcb7c1e939
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425043"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>保护对 Azure Cosmos DB 中数据的访问
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文概述了 Azure Cosmos DB 中的数据访问控制。

Azure Cosmos DB 提供了三种方法来控制对数据的访问。

| 访问控制类型 | 特征 |
|---|---|
| [主/辅助密钥](#primary-keys) | 允许任何管理或数据操作的共享机密。 它包括读写变量和只读变量。 |
| [基于角色的访问控制](#rbac) | 使用 Azure Active Directory (AAD) 标识进行身份验证的细化、基于角色的权限模型。 |
| [资源令牌](#resource-tokens)| 基于本机 Azure Cosmos DB 用户和权限的细化权限模型。 |

## <a name="primarysecondary-keys"></a><a id="primary-keys"></a> 主/辅助密钥

主/辅助密钥提供对数据库帐户的所有管理资源的访问权限。 每个帐户包括两个密钥：主密钥和辅助密钥。 使用两个密钥的目的是为了能够重新生成或轮换密钥，从而可以持续访问帐户和数据。 若要详细了解主/辅助密钥，请参阅[数据库安全](database-security.md#primary-keys)一文。

### <a name="key-rotation-and-regeneration"></a><a id="key-rotation"></a> 密钥轮换和重新生成

> [!NOTE]
> 以下步骤介绍为 SQL API 轮换和重新生成密钥的步骤。 如果使用的是其他 API，请参阅 [Azure Cosmos DB API for Mongo DB](database-security.md?tabs=mongo-api#key-rotation)、[Cassandra API](database-security.md?tabs=cassandra-api#key-rotation)、[Gremlin API](database-security.md?tabs=gremlin-api#key-rotation)或 [表 API](database-security.md?tabs=table-api#key-rotation) 部分。

密钥轮换和重新生成的过程非常简单。 首先，请确保应用程序始终使用主密钥或辅助密钥来访问你的 Azure Cosmos DB 帐户。 然后，执行以下所述的步骤。

# <a name="if-your-application-is-currently-using-the-primary-key"></a>[如果你的应用程序当前正在使用主密钥](#tab/using-primary-key)

1. 在 Azure 门户中，导航到你的 Azure Cosmos DB 帐户。

1. 从左侧菜单中选择“密钥”，然后从辅助密钥右侧的省略号中选择“重新生成辅助密钥”。

    :::image type="content" source="./media/database-security/regenerate-secondary-key.png" alt-text="显示如何重新生成辅助密钥的 Azure 门户屏幕截图" border="true":::

1. 验证新的辅助密钥是否适用于你的 Azure Cosmos DB 帐户。 密钥重新生成可能需要一分钟，也可能长达数小时，具体取决于 Cosmos DB 帐户的大小。

1. 在应用程序中将主密钥替换为辅助密钥。

1. 返回到 Azure 门户，并触发主密钥的重新生成。

    :::image type="content" source="./media/database-security/regenerate-primary-key.png" alt-text="显示如何重新生成主密钥的 Azure 门户的屏幕截图" border="true":::

# <a name="if-your-application-is-currently-using-the-secondary-key"></a>[如果你的应用程序当前正在使用辅助密钥](#tab/using-secondary-key)

1. 在 Azure 门户中，导航到你的 Azure Cosmos DB 帐户。

1. 从左侧菜单中选择“密钥”，然后从主密钥右侧的省略号中选择“重新生成主密钥”。

    :::image type="content" source="./media/database-security/regenerate-primary-key.png" alt-text="显示如何重新生成主密钥的 Azure 门户的屏幕截图" border="true":::

1. 验证新的主密钥是否适用于你的 Azure Cosmos DB 帐户。 密钥重新生成可能需要一分钟，也可能长达数小时，具体取决于 Cosmos DB 帐户的大小。

1. 在应用程序中将辅助密钥替换为主密钥。

1. 返回到 Azure 门户，并触发辅助密钥的重新生成。

    :::image type="content" source="./media/database-security/regenerate-secondary-key.png" alt-text="显示如何重新生成辅助密钥的 Azure 门户屏幕截图" border="true":::

---

### <a name="code-sample-to-use-a-primary-key"></a>有关如何使用主密钥的代码示例

以下代码示例演示如何使用 Cosmos DB 帐户终结点和主密钥来实例化 CosmosClient：

```csharp
// Read the Azure Cosmos DB endpointUrl and authorization keys from config.
// These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
// Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

## <a name="role-based-access-control"></a><a id="rbac"></a> 基于角色的访问控制

Azure Cosmos DB 公开了一种内置的基于角色的访问控制 (RBAC) 系统，可让你：

- 使用 Azure Active Directory (AAD) 标识来验证数据请求。
- 使用细粒度的、基于角色的权限模型来授权数据请求。

Azure Cosmos DB RBAC 是在以下情况下的理想访问控制方法：

- 你不希望使用共享机密（如主键），但希望依赖于基于令牌的身份验证机制，
- 你希望使用 Azure AD 标识对请求进行身份验证，
- 你需要细化权限模型来严格限制标识允许执行的数据库操作，
- 你希望将访问控制策略具体化为可分配给多个标识的“角色”。

有关 Azure Cosmos DB RBAC 的详细信息，请参阅[为 Azure Cosmos DB 帐户配置基于角色的访问控制](how-to-setup-rbac.md)。

## <a name="resource-tokens"></a><a id="resource-tokens"></a> 资源令牌

资源令牌提供对数据库中应用程序资源的访问权限。 资源令牌：

- 提供对特定容器、分区键、文档、附件、存储过程、触发器和 UDF 的访问权限。
- 向[用户](#users)授予对特定资源的[权限](#permissions)时创建。
- 通过 POST、GET 或 PUT 调用操作权限资源时重新创建。
- 使用专门针对用户、资源和权限构造的哈希资源令牌。
- 生存期受到可自定义的有效期的约束。 默认的有效期限为一小时。 但是，可将令牌生存期显式指定为最长五个小时。
- 可以安全替代主密钥。
- 使客户端能够根据它们的权限读取、写入和删除 Cosmos DB 帐户中的资源。

如果想要为不能通过主密钥得到信任的客户端提供对 Cosmos DB 帐户中资源的访问权限，可以使用资源令牌（通过创建 Cosmos DB 用户和权限来使用）。  

Cosmos DB 资源令牌提供一种安全的替代方案，使客户端能够根据授予的权限读取、写入和删除 Cosmos DB 帐户中的资源，而无需主密钥或只读密钥。

以下是典型的设计模式，通过它可以请求、生成资源令牌并将其提供给客户端：

1. 设置中间层服务，以用于移动应用程序共享用户照片。
2. 中间层服务拥有 Cosmos DB 帐户的主密钥。
3. 照片应用安装在最终用户移动设备上。
4. 登录时，照片应用使用中间层服务建立用户的标识。 这种标识建立机制完全由应用程序决定。
5. 一旦建立标识，中间层服务就会基于标识请求权限。
6. 中间层服务将资源令牌发送回手机应用。
7. 手机应用可以继续使用该资源令牌以该资源令牌定义的权限按照该资源令牌允许的间隔直接访问 Cosmos DB 资源。
8. 资源令牌到期后，后续请求收到 401 未经授权的异常。  此时，手机应用会重新建立标识，并请求新的资源令牌。

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Azure Cosmos DB 资源令牌工作流" border="false":::

资源令牌的生成和管理由本机 Cosmos DB 客户端库处理；但是，如果使用 REST，必须构造请求/身份验证标头。 有关为 REST 创建身份验证标头的详细信息，请参阅 [Cosmos DB 资源的访问控制](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)或我们的 [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/Authorization/AuthorizationHelper.cs) 或 [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts) 的源代码。

有关用于生成或代理资源令牌的中间层服务的示例，请参阅 [ResourceTokenBroker 应用](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)。

### <a name="users"></a>用户<a id="users"></a>

Azure Cosmos DB 用户与 Cosmos 数据库相关联。  每个数据库可以包含零个或更多 Cosmos DB 用户。 以下代码示例展示了如何使用 [Azure Cosmos DB .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement) 创建 Cosmos DB 用户。

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> 每个 Cosmos DB 用户都有一个 ReadAsync() 方法，可以使用此方法检索与用户关联的[权限](#permissions)的列表。

### <a name="permissions"></a>权限<a id="permissions"></a>

权限资源与用户相关联并分配给特定资源。 每个用户可能包含零个或多个权限。 用户在尝试访问某个特定容器或访问特定分区键中的数据时需要一个安全令牌，权限资源提供对该安全令牌的访问权限。 权限资源提供两种可用的访问级别：

- 所有：用户对资源拥有完全权限。
- 读取：用户只能读取资源的内容，但无法对资源执行写入、更新或删除操作。

> [!NOTE]
> 为了运行存储过程，用户必须对将在其中运行存储过程的容器具有全部权限。

如果[对数据平面请求启用诊断日志](cosmosdb-monitor-resource-logs.md)，则会记录与该权限相对应的以下两个属性：

* resourceTokenPermissionId - 此属性指示已指定的资源令牌权限 ID。 

* resourceTokenPermissionMode - 此属性指示在创建资源令牌时设置的权限模式。 权限模式的值可以是“all”或“read”。

#### <a name="code-sample-to-create-permission"></a>有关创建权限的代码示例

以下代码示例演示如何创建权限资源、读取权限资源的资源令牌以及将权限与上面创建的[用户](#users)关联。

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: container,
        resourcePartitionKey: new PartitionKey("012345")));
```

#### <a name="code-sample-to-read-permission-for-user"></a>有关读取用户权限的代码示例

下面的代码片段展示了如何检索与上面创建的用户关联的权限，并代表用户实例化一个新的 CosmosClient，作用域为单个分区键。

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="differences-between-rbac-and-resource-tokens"></a>RBAC 和资源令牌之间的差异

| 使用者 | RBAC | 资源令牌 |
|--|--|--|
| 身份验证  | 与 Azure Active Directory (Azure AD) 集成。 | 基于本机 Azure Cosmos DB 用户<br>将资源令牌与 Azure AD 集成需要额外的工作才能将 Azure AD 标识桥接到 Azure Cosmos DB 用户。 |
| 授权 | 基于角色：角色定义映射允许的操作，并且可以分配给多个标识。 | 基于权限：对于每个 Azure Cosmos DB 用户，需要分配数据访问权限。 |
| 令牌范围 | AAD 令牌带有请求者的标识。 此标识将与所有分配的角色定义匹配以执行授权。 | 资源令牌带有向特定 Azure Cosmos DB 资源上的特定 Azure Cosmos DB 用户授权的权限。 不同资源的授权请求可能需要不同的令牌。 |
| 令牌刷新 | Azure Cosmos DB SDK 在过期时自动刷新 AAD 令牌。 | 不支持资源令牌刷新。 资源令牌过期时，需要发出一个新令牌。 |

## <a name="add-users-and-assign-roles"></a>添加用户和分配角色

若要将 Azure Cosmos DB 帐户读者访问权限添加到用户帐户，请让订阅所有者在 Azure 门户执行以下步骤。

1. 打开 Azure 门户，并选择 Azure Cosmos DB 帐户。
2. 单击“访问控制(IAM)”选项卡，然后单击“+ 添加角色分配”。
3. 在“添加角色分配”窗格中的“角色”框中，选择“Cosmos DB 帐户读者角色”。
4. 在“分配其访问权限”框中，选择“Azure AD 用户、组或应用程序”。
5. 在你想要授予访问权限的目录中选择用户、组或应用程序。  可以通过显示名称、电子邮件地址或对象标识符搜索目录。
    所选用户、组或应用程序会显示在所选成员列表中。
6. 单击“保存” 。

实体现在便可以读取 Azure Cosmos DB 资源。

## <a name="delete-or-export-user-data"></a>删除或导出用户数据

借助 Azure Cosmos DB 数据库服务，你可搜索、选择、修改和删除数据库或容器中的任何数据。 但是，你有责任使用提供的 API 并定义查找和擦除任何个人数据所需的逻辑（如果需要）。 每个多模型 API（SQL、MongoDB、Gremlin、Cassandra、表）都包含不同的语言 SDK，这些 SDK 提供了各种用于搜索和基于自定义谓词删除数据的方法。 还可启用[生存时间 (TTL)](time-to-live.md)功能在指定时间段后自动删除数据，不会产生任何额外费用。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Cosmos 数据库安全性，请参阅 [Cosmos DB 数据库安全性](database-security.md)。
- 若要了解如何构造 Azure Cosmos DB 授权令牌，请参阅 [Azure Cosmos DB 资源的访问控制](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)。
- 有关包含用户和权限的用户管理示例，请参阅 [.NET SDK v3 用户管理示例](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
