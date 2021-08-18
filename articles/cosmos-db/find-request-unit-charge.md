---
title: 在 Azure Cosmos DB 中找到 SQL 查询的请求单位 (RU) 费用
description: 了解如何查找针对 Azure Cosmos 容器执行的 SQL 查询所产生的请求单位 (RU) 费用。 可以使用 Azure 门户、.NET、Java、Python 和 Node.js 语言来查找 RU 费用。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 6c11e6804b507fcaa838a83cf9eba102cbca381f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727908"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>查找 Azure Cosmos DB SQL API 中执行的操作的请求单位费用
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 支持多种 API，例如 SQL、MongoDB、Cassandra、Gremlin 和表。 每个 API 具有自身的数据库操作集。 这些操作包括简单的点读取和写入，以及复杂的查询等等。 每个数据库操作根据其复杂性消耗系统资源。

所有数据库操作的成本将由 Azure Cosmos DB 规范化，并以“请求单位”（简称 RU）表示。 请求费用是所有数据库操作使用的请求单位。 你可以将 RU 视为性能货币，它抽象化了执行 Azure Cosmos DB 支持的数据库操作所需的系统资源，例如 CPU、IOPS 和内存。 不管使用哪个 API 来与 Azure Cosmos 容器和数据库操作交互，都始终以 RU 来计量成本。 无论数据库操作是写入、点读取还是查询，都始终以 RU 来计量成本。 若要了解详细信息，请参阅[请求单位及其注意事项](request-units.md)一文。

本文介绍如何通过不同方式查找针对 Azure Cosmos DB SQL API 中的容器执行的任何操作所消耗的[请求单位](request-units.md) (RU)。 如果你使用的是其他 API，请参阅 [API for MongoDB](mongodb/find-request-unit-charge-mongodb.md)、[Cassandra API](cassandra/find-request-unit-charge-cassandra.md)、[Gremlin API](find-request-unit-charge-gremlin.md) 和[表 API](table/find-request-unit-charge.md) 这几篇文章来查找 RU/秒费用。

目前，若要度量这种消耗，只能使用 Azure 门户，或者通过某个 SDK 检查 Azure Cosmos DB 发回的响应。 如果使用 SQL API，则可以使用多个选项来查找针对 Azure Cosmos 容器执行的操作所消耗的 RU。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-sql-api-dotnet.md#create-account)并在其中植入数据，或选择一个已包含数据的现有 Azure Cosmos 帐户。

1. 转到“数据资源管理器”窗格，然后选择要处理的容器。

1. 选择“新建 SQL 查询”。

1. 输入有效的查询，然后选择“执行查询”。

1. 选择“查询统计信息”，以显示执行的请求的实际请求费用。

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Azure 门户中的 SQL 查询请求费用屏幕截图":::

## <a name="use-the-net-sdk"></a>使用 .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

从 [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 返回的对象公开 `RequestCharge` 属性：

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

从 [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) 返回的对象公开 `RequestCharge` 属性：

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

有关详细信息，请参阅[快速入门：在 Azure Cosmos DB 中使用 SQL API 帐户生成 .NET Web 应用](create-sql-api-dotnet.md)。

---

## <a name="use-the-java-sdk"></a>使用 Java SDK

从 [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) 返回的对象公开 `getRequestCharge()` 方法：

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

有关详细信息，请参阅[快速入门：使用 Azure Cosmos DB SQL API 帐户生成 Java 应用程序](create-sql-api-java.md)。

## <a name="use-the-nodejs-sdk"></a>使用 Node.js SDK

从 [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) 返回的对象公开 `headers` 子对象，该对象可映射底层 HTTP API 返回的所有标头。 请求费用显示在 `x-ms-request-charge` 键下：

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

有关详细信息，请参阅[快速入门：使用 Azure Cosmos DB SQL API 帐户生成 Node.js 应用](create-sql-api-nodejs.md)。 

## <a name="use-the-python-sdk"></a>使用 Python SDK

从 [Python SDK](https://pypi.org/project/azure-cosmos/) 返回的 `CosmosClient` 对象公开 `last_response_headers` 字典，该字典可映射底层 HTTP API 针对上次执行的操作返回的所有标头。 请求费用显示在 `x-ms-request-charge` 键下：

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

有关详细信息，请参阅[快速入门：使用 Azure Cosmos DB SQL API 帐户生成 Python 应用](create-sql-api-python.md)。 

## <a name="next-steps"></a>后续步骤

若要了解如何优化 RU 消耗量，请参阅以下文章：

* [Azure Cosmos DB 中的请求单位和吞吐量](request-units.md)
* [在 Azure Cosmos DB 中优化预配的吞吐量成本](optimize-cost-throughput.md)
* [优化 Azure Cosmos DB 中的查询成本](./optimize-cost-reads-writes.md)
* [全局缩放预配的吞吐量](./request-units.md)
* [在容器和数据库上预配吞吐量](set-throughput.md)
* [为容器预配吞吐量](how-to-provision-container-throughput.md)
* [使用 Azure Cosmos DB 中的指标进行监视和调试](use-metrics.md)
