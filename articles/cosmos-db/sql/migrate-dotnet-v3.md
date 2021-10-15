---
title: 迁移应用程序以使用 Azure Cosmos DB .NET SDK 3.0 (Microsoft.Azure.Cosmos)
description: 了解如何将现有 .NET 应用程序从 v2 SDK 升级到适用于 Core (SQL) API 的较新 .NET SDK v3（Microsoft.Azure.Cosmos 包）。
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/04/2021
ms.openlocfilehash: 58ea7624b32b7730863fe3d29f6d9245c4199d25
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535293"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v3"></a>迁移应用程序以使用 Azure Cosmos DB .NET SDK v3
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> 若要了解 Azure Cosmos DB .NET SDK v3，请参阅[发行说明](sql-api-sdk-dotnet-standard.md)、[.NET GitHub 存储库](https://github.com/Azure/azure-cosmos-dotnet-v3)、.NET SDK v3 [性能提示](performance-tips-dotnet-sdk-v3-sql.md)和[故障排除指南](troubleshoot-dot-net-sdk.md)。
>

本文重点介绍将现有 .NET 应用程序升级到适用于 Core (SQL) API 的较新 Azure Cosmos DB .NET SDK v3 时的一些注意事项。 Azure Cosmos DB .NET SDK v3 对应于 Microsoft.Azure.Cosmos 命名空间。 如果要从以下任何 Azure Cosmos DB .NET SDK 迁移应用程序，则可以使用此文档中提供的信息：

* 适用于 SQL API 的 Azure Cosmos DB .NET Framework SDK v2
* 适用于 SQL API 的 Azure Cosmos DB .NET Core SDK v2

本文中的说明还有助于迁移以下外部库，这些库现在是适用于 Core (SQL) API 的 Azure Cosmos DB .NET SDK v3 的一部分：

* .NET 更改源处理器库 2.0
* .NET 批量执行工具库 1.1 或更高版本

## <a name="whats-new-in-the-net-v3-sdk"></a>.NET V3 SDK 的新增功能

v3 SDK 包含许多可用性和性能改进，包括：

* 直观编程模型命名
* .NET Standard 2.0 **
* 通过流 API 支持提高了性能
* 取代 URI 工厂需求的 fluent 层次结构
* 对更改源处理器库的内置支持
* 对批量操作的内置支持
* 用于简化单元测试的 Mockable API
* 事务性批处理和 Blazor 支持
* 可插入序列化程序
* 缩放非分区和自动缩放容器

**该 SDK 面向 .NET Standard 2.0，.NET Standard 2.0 将现有 Azure Cosmos DB .NET Framework 和 .NET Core SDK 统一到单个 .NET SDK 中。 可以在任何实现 .NET Standard 2.0 的平台（包括 .NET Framework 4.6.1+ 和 .NET Core 2.0+ 应用程序）中使用 .NET SDK。

大多数网络、重试逻辑和较低级别的 SDK 在很大程度上保持不变。

**Azure Cosmos DB .NET SDK v3 现在为开源。** 欢迎提出任何拉取请求，我们将在 [GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/) 上记录问题并跟踪反馈。 我们致力于提供任何能够改善客户体验的功能。

## <a name="why-migrate-to-the-net-v3-sdk"></a>为何要迁移到 .NET v3 SDK

除了大量的可用性和性能改进之外，最新 SDK 中的新功能投资将不会反向移植到较旧版本。
v2 SDK 目前处于维护模式。 如需获得最佳的开发体验，我们建议务必从最新的受支持的 SDK 版本入手。

## <a name="major-name-changes-from-v2-sdk-to-v3-sdk"></a>主名称从 v2 SDK 更改为 v3 SDK

在整个 .NET 3.0 SDK 中应用了以下名称更改，与 Core (SQL) API 的 API 命名约定保持一致：

* `DocumentClient` 已重命名为 `CosmosClient`
* `Collection` 已重命名为 `Container`
* `Document` 已重命名为 `Item`

所有资源对象都是用附加属性重命名的，为清楚起见，其中包含资源名称。

下面是一些主要的类名更改：

| .NET v2 SDK | .NET v3 SDK |
|-------------|-------------|
|`Microsoft.Azure.Documents.Client.DocumentClient`|`Microsoft.Azure.CosmosClient`|
|`Microsoft.Azure.Documents.Client.ConnectionPolicy`|`Microsoft.Azure.Cosmos.CosmosClientOptions`|
|`Microsoft.Azure.Documents.Client.DocumentClientException` |`Microsoft.Azure.Cosmos.CosmosException`|
|`Microsoft.Azure.Documents.Client.Database`|`Microsoft.Azure.Cosmos.DatabaseProperties`|
|`Microsoft.Azure.Documents.Client.DocumentCollection`|`Microsoft.Azure.Cosmos.ContainerProperties`|
|`Microsoft.Azure.Documents.Client.RequestOptions`|`Microsoft.Azure.Cosmos.ItemRequestOptions`|
|`Microsoft.Azure.Documents.Client.FeedOptions`|`Microsoft.Azure.Cosmos.QueryRequestOptions`|
|`Microsoft.Azure.Documents.Client.StoredProcedure`|`Microsoft.Azure.Cosmos.StoredProcedureProperties`|
|`Microsoft.Azure.Documents.Client.Trigger`|`Microsoft.Azure.Cosmos.TriggerProperties`|

### <a name="classes-replaced-on-net-v3-sdk"></a>.NET v3 SDK 上被替换的类

在 3.0 SDK 上，以下类已被替换:

* `Microsoft.Azure.Documents.UriFactory`

* `Microsoft.Azure.Documents.Document`

* `Microsoft.Azure.Documents.Resource`

Microsoft.Azure.Documents.UriFactory 类已被替换为 fluent 设计。 Fluent 设计在内部生成 URL，并支持传递单个 `Container` 对象，而不是 `DocumentClient`、`DatabaseName` 和 `DocumentCollection`。

### <a name="changes-to-item-id-generation"></a>对项 ID 生成的更改

.NET v3 SDK 中不再自动填充项 ID。 因此，项 ID 必须具体包括生成的 ID。 查看以下示例：

```csharp
[JsonProperty(PropertyName = "id")]
public Guid Id { get; set; }
```

### <a name="changed-default-behavior-for-connection-mode"></a>更改了连接模式的默认行为

SDK v3 现在默认为直接 + TCP 连接模式，而以前的 v2 SDK 默认为网关 + HTTPS 连接模式。 此更改增强了性能和可伸缩性。

### <a name="changes-to-feedoptions-queryrequestoptions-in-v30-sdk"></a>对 FeedOptions 的更改（v3.0 SDK 中的 QueryRequestOptions）

SDK v2 中的 `FeedOptions` 类现已在 SDK v3 中被重命名为 `QueryRequestOptions`，在此类中，多个属性的名称和/或默认值已更改，或者已完全删除。  

`FeedOptions.MaxDegreeOfParallelism` 已重命名为 `QueryRequestOptions.MaxConcurrency`，默认值和关联行为保持不变，并行查询执行期间运行客户端的操作将以非并行方式串行执行。

`FeedOptions.EnableCrossPartitionQuery` 已删除，SDK 3.0 中的默认行为是将执行跨分区查询，而不需要专门启用属性。

默认情况下启用 `FeedOptions.PopulateQueryMetrics`，结果显示在响应的诊断属性中。

`FeedOptions.RequestContinuation` 现已提升为查询方法本身。

已删除以下属性：

* `FeedOptions.DisableRUPerMinuteUsage`

* `FeedOptions.EnableCrossPartitionQuery`

* `FeedOptions.JsonSerializerSettings`

* `FeedOptions.PartitionKeyRangeId`

* `FeedOptions.PopulateQueryMetrics`

### <a name="constructing-a-client"></a>构造客户端

.NET SDK v3 提供取代 SDK v2 URI 工厂需求的 fluent `CosmosClientBuilder` 类。

下面的示例创建一个新 `CosmosClientBuilder`，其中包含强 ConsistencyLevel 和首选位置列表：

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder(
    accountEndpoint: "https://testcosmos.documents.azure.com:443/",
    authKeyOrResourceToken: "SuperSecretKey")
.WithConsistencyLevel(ConsistencyLevel.Strong)
.WithApplicationRegion(Regions.EastUS);
CosmosClient client = cosmosClientBuilder.Build();
```

### <a name="exceptions"></a>例外

v2 SDK 在操作过程中使用 `DocumentClientException` 标记错误，v3 SDK 使用 `CosmosClientException`，后者公开了 `StatusCode`、`Diagnostics` 和其他与响应相关的信息。 使用 `ToString()` 时，所有完整信息都会被序列化：

```csharp
catch (CosmosClientException ex)
{
    HttpStatusCode statusCode = ex.StatusCode;
    CosmosDiagnostics diagnostics = ex.Diagnostics;
    // store diagnostics optionally with diagnostics.ToString();
    // or log the entire error details with ex.ToString();
}
```

### <a name="diagnostics"></a>诊断

v2 SDK 通过 `ResponseDiagnosticsString` 属性提供“仅直接”诊断，而 v3 SDK 则使用所有响应和异常中皆可用的 `Diagnostics`，后者更加丰富且不局限于直接模式。 它们不仅包括操作在 SDK 上花费的时间，还包括操作接触的区域：

```csharp
try
{
    ItemResponse<MyItem> response = await container.ReadItemAsync<MyItem>(
                    partitionKey: new PartitionKey("MyPartitionKey"),
                    id: "MyId");
    
    TimeSpan elapsedTime = response.Diagnostics.GetElapsedTime();
    if (elapsedTime > somePreDefinedThreshold)
    {
        // log response.Diagnostics.ToString();
        IReadOnlyList<(string region, Uri uri)> regions = response.Diagnostics.GetContactedRegions();
    }
}
catch (CosmosException cosmosException) {
    string diagnostics = cosmosException.Diagnostics.ToString();
    
    TimeSpan elapsedTime = cosmosException.Diagnostics.GetElapsedTime();
    
    IReadOnlyList<(string region, Uri uri)> regions = cosmosException.Diagnostics.GetContactedRegions();
    
    // log cosmosException.ToString()
}
```

### <a name="connectionpolicy"></a>ConnectionPolicy

`ConnectionPolicy` 中的某些设置已重命名或替换：

| .NET v2 SDK | .NET v3 SDK |
|-------------|-------------|
|`EnableEndpointRediscovery`|`LimitToEndpoint` - 该值现在已反转。如果 `EnableEndpointRediscovery` 设置为 `true`，则 `LimitToEndpoint` 应设置为 `false`。 在使用此设置之前，你需要了解[它对客户端有何影响](troubleshoot-sdk-availability.md)。|
|`ConnectionProtocol`|删除。 协议与模式相关联，可以是网关 (HTTPS) 或直接 (TCP)。 V3 SDK 不再支持使用 HTTPS 协议的直接模式，建议使用 TCP 协议。 |
|`MediaRequestTimeout`|删除。 附件不再受支持。|

### <a name="session-token"></a>会话令牌

在需要捕获会话令牌的情况下，由于会话令牌是一个标头，v2 SDK 会将响应的会话令牌作为 `ResourceResponse.SessionToken` 公开，而 v3 SDK 会将该值公开在任何响应的 `Headers.Session` 属性中。

### <a name="timestamp"></a>时间戳

v2 SDK 通过 `Timestamp` 属性公开文档的时间戳，因为 `Document` 不再可用，用户可以将 `_ts` [系统属性](../account-databases-containers-items.md#properties-of-an-item)映射到其模型中的属性。

### <a name="openasync"></a>OpenAsync

对于使用 `OpenAsync()` 来预热 v2 SDK 客户端的用例，可使用 `CreateAndInitializeAsync` 来[创建并预热](https://devblogs.microsoft.com/cosmosdb/improve-net-sdk-initialization/) v3 SDK 客户端。

### <a name="using-the-change-feed-processor-apis-directly-from-the-v3-sdk"></a>直接从 v3 SDK 使用更改源处理器 API

v3 SDK 具有对更改源处理器 API 的内置支持，支持使用同一 SDK 来生成应用程序和更改源处理器实现。 以前，必须使用单独的更改源处理器库。

有关详细信息，请参阅[如何从更改源处理器库迁移到 Azure Cosmos DB .NET v3 SDK](how-to-migrate-from-change-feed-library.md)

### <a name="using-the-bulk-executor-library-directly-from-the-v3-sdk"></a>直接从 V3 SDK 使用批量执行工具库

v3 SDK 具有对批量执行工具库的内置支持，支持使用同一 SDK 来生成应用程序和执行批量操作。 以前，需要使用单独的批量执行工具库。

有关详细信息，请参阅[如何从批量执行工具库迁移到 Azure Cosmos DB .NET V3 SDK 中的批量操作支持](how-to-migrate-from-bulk-executor-library.md)

## <a name="code-snippet-comparisons"></a>代码片段比较

以下代码片段显示了 .NET v2 和 v3 SDK 在资源创建方式上的差异：

## <a name="database-operations"></a>数据库操作

### <a name="create-a-database"></a>创建数据库

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Create database with no shared provisioned throughput
DatabaseResponse databaseResponse = await client.CreateDatabaseIfNotExistsAsync(DatabaseName);
Database database = databaseResponse;
DatabaseProperties databaseProperties = databaseResponse;

// Create a database with a shared manual provisioned throughput
string databaseIdManual = new string(DatabaseName + "_SharedManualThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdManual, ThroughputProperties.CreateManualThroughput(400));

// Create a database with shared autoscale provisioned throughput
string databaseIdAutoscale = new string(DatabaseName + "_SharedAutoscaleThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdAutoscale, ThroughputProperties.CreateAutoscaleThroughput(4000));
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create database
ResourceResponse<Database> databaseResponse = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = DatabaseName });
Database database = databaseResponse;

// Create a database with shared standard provisioned throughput
database = await client.CreateDatabaseIfNotExistsAsync(new Database{ Id = databaseIdStandard }, new RequestOptions { OfferThroughput = 400 });

// Creating a database with shared autoscale provisioned throughput from v2 SDK is not supported use v3 SDK
```
---

### <a name="read-a-database-by-id"></a>按 ID 读取数据库

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Read a database
Console.WriteLine($"{Environment.NewLine} Read database resource: {DatabaseName}");
database = client.GetDatabase(DatabaseName);
Console.WriteLine($"{Environment.NewLine} database { database.Id.ToString()}");

// Read all databases
string findQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(findQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id.ToString()}");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Read a database
database = await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine("\n database {0}", database.Id.ToString());

// Read all databases
Console.WriteLine("\n1.1 Reading all databases resources");
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    Console.WriteLine("\n database {0} \n {1}", _database.Id.ToString(), _database.ToString());
}
```
---

### <a name="delete-a-database"></a>删除数据库

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Delete a database
await client.GetDatabase(DatabaseName).DeleteAsync();
Console.WriteLine($"{ Environment.NewLine} database {DatabaseName} deleted.");

// Delete all databases in an account
string deleteQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(deleteQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            await client.GetDatabase(_database.Id).DeleteAsync();
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id} deleted");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Delete a database
database = await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine(" database {0} deleted.", DatabaseName);

// Delete all databases in an account
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(_database.Id));
    Console.WriteLine("\n database {0} deleted", _database.Id);
}
```
---

## <a name="container-operations"></a>容器操作

### <a name="create-a-container-autoscale--time-to-live-with-expiration"></a>创建容器（自动缩放 + 生存时间（包含到期时间））

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateManualThroughputContainer(Database database)
{
    // Set throughput to the minimum value of 400 RU/s manually configured throughput
    string containerIdManual = ContainerName + "_Manual";
    ContainerResponse container = await database.CreateContainerIfNotExistsAsync(
        id: containerIdManual,
        partitionKeyPath: partitionKeyPath,
        throughput: 400);
}

// Create container with autoscale
private static async Task CreateAutoscaleThroughputContainer(Database database)
{
    string autoscaleContainerId = ContainerName + "_Autoscale";
    ContainerProperties containerProperties = new ContainerProperties(autoscaleContainerId, partitionKeyPath);

    Container container = await database.CreateContainerIfNotExistsAsync(
        containerProperties: containerProperties,
        throughputProperties: ThroughputProperties.CreateAutoscaleThroughput(autoscaleMaxThroughput: 4000);
}

// Create a container with TTL Expiration
private static async Task CreateContainerWithTtlExpiration(Database database)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    ContainerProperties properties = new ContainerProperties
        (id: containerIdManualwithTTL,
        partitionKeyPath: partitionKeyPath);

    properties.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day

    ContainerResponse containerResponse = await database.CreateContainerIfNotExistsAsync(containerProperties: properties);
    ContainerProperties returnedProperties = containerResponse;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create a collection
private static async Task CreateManualThroughputContainer(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";

    // Set throughput to the minimum value of 400 RU/s manually configured throughput

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManual;
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });
}

private static async Task CreateAutoscaleThroughputContainer(DocumentClient client)
{
        // .NET v2 SDK does not support the creation of provisioned autoscale throughput containers
}

 private static async Task CreateContainerWithTtlExpiration(DocumentClient client)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManualwithTTL;
    collectionDefinition.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });

}
```
---

### <a name="read-container-properties"></a>读取容器属性

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadContainerProperties(Database database)
{
    string containerIdManual = ContainerName + "_Manual";
    Container container = database.GetContainer(containerIdManual);
    ContainerProperties containerProperties = await container.ReadContainerAsync();
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadContainerProperties(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";
    DocumentCollection collection = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));
}
```
---

### <a name="delete-a-container"></a>删除容器

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteContainers(Database database)
{
    string containerIdManual = ContainerName + "_Manual";

    // Delete a container
    await database.GetContainer(containerIdManual).DeleteContainerAsync();

    // Delete all CosmosContainer resources for a database
    using (FeedIterator<ContainerProperties> feedIterator = database.GetContainerQueryIterator<ContainerProperties>())
    {
        while (feedIterator.HasMoreResults)
        {
            foreach (ContainerProperties _container in await feedIterator.ReadNextAsync())
            {
                await database.GetContainer(_container.Id).DeleteContainerAsync();
                Console.WriteLine($"{Environment.NewLine}  deleted container {_container.Id}");
            }
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteContainers(DocumentClient client)
{
    // Delete a collection
    string containerIdManual = ContainerName + "_Manual";
    await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));

    // Delete all containers for a database
    foreach (var collection in await client.ReadDocumentCollectionFeedAsync(UriFactory.CreateDatabaseUri(DatabaseName)))
    {
        await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, collection.Id));
    }
}
```
---

## <a name="item-and-query-operations"></a>项和查询操作

### <a name="create-an-item"></a>创建项

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateItemAsync(Container container)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    ItemResponse<SalesOrder> response = await container.CreateItemAsync(salesOrder1,
        new PartitionKey(salesOrder1.AccountNumber));
}

private static async Task RunBasicOperationsOnDynamicObjects(Container container)
{
    // Dynamic Object
    dynamic salesOrder = new
    {
        id = "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    Console.WriteLine("\nCreating item");
    ItemResponse<dynamic> response = await container.CreateItemAsync<dynamic>(
        salesOrder, new PartitionKey(salesOrder.AccountNumber));
    dynamic createdSalesOrder = response.Resource;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task CreateItemAsync(DocumentClient client)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder1,
        new RequestOptions { PartitionKey = new PartitionKey("Account1")});
}

private static async Task RunBasicOperationsOnDynamicObjects(DocumentClient client)
{
    // Create a dynamic object
    dynamic salesOrder = new
    {
        id= "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    ResourceResponse<Document> response = await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder,
        new RequestOptions { PartitionKey = new PartitionKey(salesOrder.AccountNumber)});

    dynamic createdSalesOrder = response.Resource;
    }
```
---

### <a name="read-all-the-items-in-a-container"></a>读取容器中的所有项

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadAllItems(Container container)
{
    // Read all items in a container
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition: null,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 5
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder salesOrder = response.First();
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadAllItems(DocumentClient client)
{
    // Read all items in a collection
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    string continuationToken = null;
    do
    {
        var feed = await client.ReadDocumentFeedAsync(
            UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
            new FeedOptions { MaxItemCount = 5, RequestContinuation = continuationToken });
        continuationToken = feed.ResponseContinuation;
        foreach (Document document in feed)
        {
            SalesOrder salesOrder = (SalesOrder)(dynamic)document;
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.Add(salesOrder);

        }
    } while (continuationToken != null);
}
```
---

### <a name="query-items"></a>查询项

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task QueryItems(Container container)
{
    // Query for items by a property other than Id
    QueryDefinition queryDefinition = new QueryDefinition(
        "select * from sales s where s.AccountNumber = @AccountInput")
        .WithParameter("@AccountInput", "Account1");

    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();
    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 1
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder sale = response.First();
            Console.WriteLine($"\n Account Number: {sale.AccountNumber}; Id: {sale.Id};");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task QueryItems(DocumentClient client)
{
    // Query for items by a property other than Id
    SqlQuerySpec querySpec = new SqlQuerySpec()
    {
        QueryText = "select * from sales s where s.AccountNumber = @AccountInput",
        Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@AccountInput", "Account1")
            }
    };
    var query = client.CreateDocumentQuery<SalesOrder>(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        querySpec,
        new FeedOptions {EnableCrossPartitionQuery = true});

    var allSalesForAccount1 = query.ToList();

    Console.WriteLine($"\n1.4.2 Query found {allSalesForAccount1.Count} items.");
}
```
---

### <a name="delete-an-item"></a>删除项

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteItemAsync(Container container)
{
    ItemResponse<SalesOrder> response = await container.DeleteItemAsync<SalesOrder>(
        partitionKey: new PartitionKey("Account1"), id: "SalesOrder3");
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteItemAsync(DocumentClient client)
{
    ResourceResponse<Document> response = await client.DeleteDocumentAsync(
        UriFactory.CreateDocumentUri(DatabaseName, ContainerName, "SalesOrder3"),
        new RequestOptions { PartitionKey = new PartitionKey("Account1") });
}
```
---

## <a name="next-steps"></a>后续步骤

* 使用 v3 SDK [生成控制台应用](sql-api-get-started.md)以管理 Azure Cosmos DB SQL API 数据
* 详细了解 [v3 SDK 的用途](sql-api-dotnet-v3sdk-samples.md)
* 尝试为迁移到 Azure Cosmos DB 进行容量计划？
    * 若只知道现有数据库群集中的 vcore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
    * 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)
