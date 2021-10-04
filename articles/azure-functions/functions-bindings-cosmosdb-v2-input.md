---
title: 适用于 Functions 2.x 及更高版本的 Azure Cosmos DB 输入绑定
description: 了解如何在 Azure Functions 中使用 Azure Cosmos DB 输入绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 09/01/2021
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 50a4e4ecbfb646603411944a2e65944b536aeb43
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615959"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x-and-higher"></a>适用于 Azure Functions 2.x 及更高版本的 Azure Cosmos DB 输入绑定

Azure Cosmos DB 输入绑定会使用 SQL API 检索一个或多个 Azure Cosmos DB 文档，并将其传递给函数的输入参数。 可根据调用函数的触发器确定文档 ID 或查询参数。

有关设置和配置详细信息，请参阅[概述](./functions-bindings-cosmosdb-v2.md)。

> [!NOTE]
> 如果集合已[分区](../cosmos-db/partitioning-overview.md#logical-partitions)，则查找操作还需指定分区键值。
>

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

本部分包含以下示例：

* [队列触发器，从 JSON 查找 ID](#queue-trigger-look-up-id-from-json-c)
* [HTTP 触发器，从查询字符串查找 ID](#http-trigger-look-up-id-from-query-string-c)
* [HTTP 触发器，从路由数据查找 ID](#http-trigger-look-up-id-from-route-data-c)
* [HTTP 触发器，使用 SqlQuery 从路由数据查找 ID](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP 触发器，使用 SqlQuery 获取多个文档](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP 触发器，使用 DocumentClient 获取多个文档](#http-trigger-get-multiple-docs-using-documentclient-c)
* [HTTP 触发器，使用 CosmosClient（v4 扩展）获取多个文档](#http-trigger-get-multiple-docs-using-cosmosclient-c)

这些示例引用简单的 `ToDoItem` 类型：

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        [JsonProperty("id")]
        public string Id { get; set; }

        [JsonProperty("partitionKey")]
        public string PartitionKey { get; set; }

        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>队列触发器，从 JSON 查找 ID

以下示例演示检索单个文档的 [C# 函数](functions-dotnet-class-library.md)。 该函数由包含 JSON 对象的队列消息触发。 队列触发器将 JSON 分析为 `ToDoItemLookup` 类型的对象，其中包含要查找的 ID 和分区键值。 该 ID 和分区键值用于从指定的数据库和集合中检索 `ToDoItem` 文档。

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP 触发器，从查询字符串查找 ID

以下示例演示检索单个文档的 [C# 函数](functions-dotnet-class-library.md)。 此函数由 HTTP 请求触发，该请求使用的查询字符串用于指定要查找的 ID 和分区键值。 该 ID 和分区键值用于从指定的数据库和集合中检索 `ToDoItem` 文档。

> [!NOTE]
> HTTP 查询字符串参数区分大小写。
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP 触发器，从路由数据查找 ID

以下示例演示检索单个文档的 [C# 函数](functions-dotnet-class-library.md)。 此函数由 HTTP 请求触发，该请求使用的路由数据用于指定要查找的 ID 和分区键值。 该 ID 和分区键值用于从指定的数据库和集合中检索 `ToDoItem` 文档。

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP 触发器，使用 SqlQuery 从路由数据查找 ID

以下示例演示检索单个文档的 [C# 函数](functions-dotnet-class-library.md)。 此函数由 HTTP 请求触发，该请求使用的路由数据用于指定要查找的 ID。 该 ID 用于从指定的数据库和集合检索 `ToDoItem` 文档。

以下示例演示如何在 `SqlQuery` 参数中使用绑定表达式。 可以将路由数据传递至所示的 `SqlQuery` 参数，但目前[无法传递查询字符串值](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583)。

> [!NOTE]
> 如果需要只按 ID 进行查询，则建议使用查找（如[以前的示例](#http-trigger-look-up-id-from-query-string-c)所示），因为该查找消耗较少的[请求单位](../cosmos-db/request-units.md)。 点读取操作 (GET) 比按 ID 进行的查询[更高效](../cosmos-db/optimize-cost-reads-writes.md)。
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP 触发器，使用 SqlQuery 获取多个文档

以下示例演示检索文档列表的 [C# 函数](functions-dotnet-class-library.md)。 此函数由 HTTP 请求触发。 此查询在 `SqlQuery` 特性属性中指定。

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP 触发器，使用 DocumentClient 获取多个文档

以下示例演示检索文档列表的 [C# 函数](functions-dotnet-class-library.md)。 此函数由 HTTP 请求触发。 此代码使用 Azure Cosmos DB 绑定提供的 `DocumentClient` 实例来读取文档列表。 `DocumentClient` 实例也可用于写入操作。

> [!NOTE]
> 还可以使用 [IDocumentClient](/dotnet/api/microsoft.azure.documents.idocumentclient) 接口来简化测试。

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-cosmosclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-cosmosclient"></a>HTTP 触发器，使用 CosmosClient 获取多个文档

以下示例演示检索文档列表的 [C# 函数](functions-dotnet-class-library.md)。 此函数由 HTTP 请求触发。 此代码使用 Azure Cosmos DB 绑定（在[扩展版本 4.x](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) 中提供）提供的 `CosmosClient` 实例来读取文档列表。 `CosmosClient` 实例也可用于写入操作。

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingCosmosClient
    {  
        [FunctionName("DocsByUsingCosmosClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                containerName: "Items",
                Connection = "CosmosDBConnection")] CosmosClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"].ToString();
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Container container = client.GetDatabase("ToDoItems").GetContainer("Items");

            log.LogInformation($"Searching for: {searchterm}");

            QueryDefinition queryDefinition = new QueryDefinition(
                "SELECT * FROM items i WHERE CONTAINS(i.Description, @searchterm)")
                .WithParameter("@searchterm", searchterm);
            using (FeedIterator<ToDoItem> resultSet = container.GetItemQueryIterator<ToDoItem>(queryDefinition))
            {
                while (resultSet.HasMoreResults)
                {
                    FeedResponse<ToDoItem> response = await resultSet.ReadNextAsync();
                    ToDoItem item = response.First();
                    log.LogInformation(item.Description);
                }
            }

            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

本部分包含以下示例：

* [队列触发器，从字符串查找 ID](#queue-trigger-look-up-id-from-string-c-script)
* [队列触发器，使用 SqlQuery 获取多个文档](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP 触发器，从查询字符串查找 ID](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP 触发器，从路由数据查找 ID](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP 触发器，使用 SqlQuery 获取多个文档](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP 触发器，使用 DocumentClient 获取多个文档](#http-trigger-get-multiple-docs-using-documentclient-c-script)

HTTP 触发器示例引用简单的 `ToDoItem` 类型：

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>队列触发器，从字符串查找 ID

以下示例演示 *function.json* 文件中的一个 Cosmos DB 输入绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数读取单个文档，并更新文档的文本值。

下面是 function.json 文件中的绑定数据：

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

[配置](#configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>队列触发器，使用 SqlQuery 获取多个文档

以下示例演示 *function.json* 文件中的一个 Azure Cosmos DB 输入绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数使用队列触发器自定义查询参数检索 SQL 查询指定的多个文档。

队列触发器提供参数 `departmentId`。 `{ "departmentId" : "Finance" }` 的队列消息将返回财务部的所有记录。

下面是 function.json 文件中的绑定数据：

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

[配置](#configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP 触发器，从查询字符串查找 ID

以下示例演示检索单个文档的 [C# 脚本函数](functions-reference-csharp.md)。 此函数由 HTTP 请求触发，该请求使用的查询字符串用于指定要查找的 ID 和分区键值。 该 ID 和分区键值用于从指定的数据库和集合中检索 `ToDoItem` 文档。

function.json 文件如下所示：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

C# 脚本代码如下所示：

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP 触发器，从路由数据查找 ID

以下示例演示检索单个文档的 [C# 脚本函数](functions-reference-csharp.md)。 此函数由 HTTP 请求触发，该请求使用的路由数据用于指定要查找的 ID 和分区键值。 该 ID 和分区键值用于从指定的数据库和集合中检索 `ToDoItem` 文档。

function.json 文件如下所示：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "id": "{id}",
      "partitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

C# 脚本代码如下所示：

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP 触发器，使用 SqlQuery 获取多个文档

以下示例演示检索文档列表的 [C# 脚本函数](functions-reference-csharp.md)。 此函数由 HTTP 请求触发。 此查询在 `SqlQuery` 特性属性中指定。

function.json 文件如下所示：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

C# 脚本代码如下所示：

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP 触发器，使用 DocumentClient 获取多个文档

以下示例演示检索文档列表的 [C# 脚本函数](functions-reference-csharp.md)。 此函数由 HTTP 请求触发。 此代码使用 Azure Cosmos DB 绑定提供的 `DocumentClient` 实例来读取文档列表。 `DocumentClient` 实例也可用于写入操作。

function.json 文件如下所示：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

C# 脚本代码如下所示：

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="java"></a>[Java](#tab/java)

本部分包含以下示例：

* [HTTP 触发器，从查询字符串查找 ID - 字符串参数](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP 触发器，从查询字符串查找 ID - POJO 参数](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP 触发器，从路由数据查找 ID](#http-trigger-look-up-id-from-route-data-java)
* [HTTP 触发器，使用 SqlQuery 从路由数据查找 ID](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP 触发器，使用 SqlQuery 从路由数据获取多个文档（C# 脚本）](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

这些示例引用简单的 `ToDoItem` 类型：

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>HTTP 触发器，从查询字符串查找 ID - 字符串参数

以下示例展示了检索单个文档的 Java 函数。 此函数由 HTTP 请求触发，该请求使用的查询字符串用于指定要查找的 ID 和分区键值。 该 ID 和分区键值用于以字符串形式从指定的数据库和集合中检索文档。

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
```

在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值将来自 Cosmos DB 的函数参数使用 `@CosmosDBInput` 注释。  可以将此注释与本机 Java 类型、POJO 或使用了 `Optional<T>` 的可为 null 的值一起使用。

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>HTTP 触发器，从查询字符串查找 ID - POJO 参数

以下示例展示了检索单个文档的 Java 函数。 此函数由 HTTP 请求触发，该请求使用的查询字符串用于指定要查找的 ID 和分区键值。 该 ID 和分区键值用于从指定的数据库和集合中检索文档。 然后将该文档转换为先前创建的 `ToDoItem` POJO 实例，并作为参数传递给该函数。

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP 触发器，从路由数据查找 ID

以下示例展示了检索单个文档的 Java 函数。 此函数由 HTTP 请求触发，该请求使用的路由参数用于指定要查找的 ID 和分区键值。 该 ID 和分区键值用于从指定的数据库和集合中检索文档，将它作为 `Optional<String>` 返回。

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP 触发器，使用 SqlQuery 从路由数据查找 ID

以下示例展示了检索单个文档的 Java 函数。 此函数由 HTTP 请求触发，该请求使用路由参数指定要查找的 ID。 该 ID 用于从指定的数据库和集合中检索文档，将结果集转换为 `ToDoItem[]`，因为可能会返回许多文档，具体取决于查询条件。

> [!NOTE]
> 如果需要只按 ID 进行查询，则建议使用查找（如[以前的示例](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)所示），因为该查找消耗较少的[请求单位](../cosmos-db/request-units.md)。 点读取操作 (GET) 比按 ID 进行的查询[更高效](../cosmos-db/optimize-cost-reads-writes.md)。
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>HTTP 触发器，使用 SqlQuery 从路由数据获取多个文档

以下示例演示了检索多个文档的 Java 函数。 该函数由 HTTP 请求触发，该请求使用路由参数 `desc` 指定要在 `description` 字段中搜索的字符串。 搜索项用于从指定的数据库和集合中检索文档集合，将结果集转换为 `ToDoItem[]` 并将其作为参数传递给函数。

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

此部分包含的以下示例可以通过指定各种源提供的 ID 值来读取单个文档：

* [队列触发器，从 JSON 查找 ID](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP 触发器，从查询字符串查找 ID](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP 触发器，从路由数据查找 ID](#http-trigger-look-up-id-from-route-data-javascript)
* [队列触发器，使用 SqlQuery 获取多个文档](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>队列触发器，从 JSON 查找 ID

以下示例演示 *function.json* 文件中的一个 Cosmos DB 输入绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数读取单个文档，并更新文档的文本值。

下面是 function.json 文件中的绑定数据：

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[配置](#configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP 触发器，从查询字符串查找 ID

以下示例演示检索单个文档的 [JavaScript 函数](functions-reference-node.md)。 此函数由 HTTP 请求触发，该请求使用的查询字符串用于指定要查找的 ID 和分区键值。 该 ID 和分区键值用于从指定的数据库和集合中检索 `ToDoItem` 文档。

function.json 文件如下所示：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP 触发器，从路由数据查找 ID

以下示例演示检索单个文档的 [JavaScript 函数](functions-reference-node.md)。 此函数由 HTTP 请求触发，该请求使用的路由数据用于指定要查找的 ID 和分区键值。 该 ID 和分区键值用于从指定的数据库和集合中检索 `ToDoItem` 文档。

function.json 文件如下所示：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>队列触发器，使用 SqlQuery 获取多个文档

以下示例演示 *function.json* 文件中的一个 Azure Cosmos DB 输入绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数使用队列触发器自定义查询参数检索 SQL 查询指定的多个文档。

队列触发器提供参数 `departmentId`。 `{ "departmentId" : "Finance" }` 的队列消息将返回财务部的所有记录。

下面是 function.json 文件中的绑定数据：

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

[配置](#configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function (context, input) {
  var documents = context.bindings.documents;
  for (var i = 0; i < documents.length; i++) {
    var document = documents[i];
    // operate on each document
  }
  context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* [队列触发器，从 JSON 查找 ID](#queue-trigger-look-up-id-from-json-ps)
* [HTTP 触发器，从查询字符串查找 ID](#http-trigger-id-query-string-ps)
* [HTTP 触发器，从路由数据查找 ID](#http-trigger-id-route-data-ps)
* [队列触发器，使用 SqlQuery 获取多个文档](#queue-trigger-multiple-docs-sqlquery-ps)

### <a name="queue-trigger-look-up-id-from-json"></a>队列触发器，从 JSON 查找 ID

下面的示例演示如何读取和更新单个 Cosmos DB 文档。 文档的唯一标识符通过队列消息中的 JSON 值提供。

Cosmos DB 输入绑定在函数配置文件 (_function.json_) 中提供的绑定列表中首先列出。

<a name="queue-trigger-look-up-id-from-json-ps"></a>

```json
{
  "name": "InputDocumentIn",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id": "{queueTrigger_payload_property}",
  "partitionKey": "{queueTrigger_payload_property}",
  "connectionStringSetting": "CosmosDBConnection",
  "direction": "in"
},
{
  "name": "InputDocumentOut",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": false,
  "partitionKey": "{queueTrigger_payload_property}",
  "connectionStringSetting": "CosmosDBConnection",
  "direction": "out"
}
```

_run.ps1_ 文件包含 PowerShell 代码，该代码读取传入的文档并输出更改。

```powershell
param($QueueItem, $InputDocumentIn, $TriggerMetadata)

$Document = $InputDocumentIn 
$Document.text = 'This was updated!'

Push-OutputBinding -Name InputDocumentOut -Value $Document  
```

<a name="http-trigger-id-query-string-ps"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP 触发器，从查询字符串查找 ID

下面的示例演示如何通过 Web API 读取和更新单个 Cosmos DB 文档。 文档的唯一标识符通过 HTTP 请求中的 querystring 参数提供，如绑定的 `"Id": "{Query.Id}"` 属性所定义。

Cosmos DB 输入绑定在函数配置文件 (_function.json_) 中提供的绑定列表中首先列出。

```json
{ 
  "bindings": [ 
    { 
      "type": "cosmosDB", 
      "name": "ToDoItem", 
      "databaseName": "ToDoItems", 
      "collectionName": "Items", 
      "connectionStringSetting": "CosmosDBConnection", 
      "direction": "in", 
      "Id": "{Query.id}", 
      "PartitionKey": "{Query.partitionKeyValue}" 
    },
    { 
      "authLevel": "anonymous", 
      "name": "Request", 
      "type": "httpTrigger", 
      "direction": "in", 
      "methods": [ 
        "get", 
        "post" 
      ] 
    }, 
    { 
      "name": "Response", 
      "type": "http", 
      "direction": "out" 
    },
  ], 
  "disabled": false 
} 
```

_run.ps1_ 文件包含 PowerShell 代码，该代码读取传入的文档并输出更改。

```powershell
using namespace System.Net

param($Request, $ToDoItem, $TriggerMetadata)

Write-Host 'PowerShell HTTP trigger function processed a request'

if (-not $ToDoItem) { 
    Write-Host 'ToDo item not found'

    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::NotFound 
        Body = $ToDoItem.Description 
    })

} else {

    Write-Host "Found ToDo item, Description=$($ToDoItem.Description)"

    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::OK 
        Body = $ToDoItem.Description 
    }) 
}
```

<a name="http-trigger-id-route-data-ps"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP 触发器，从路由数据查找 ID

下面的示例演示如何通过 Web API 读取和更新单个 Cosmos DB 文档。 文档的唯一标识符通过 route 参数提供。 route 参数在 HTTP 请求绑定的 `route` 属性中定义，并在 Cosmos DB `"Id": "{Id}"` 绑定属性中引用。

Cosmos DB 输入绑定在函数配置文件 (_function.json_) 中提供的绑定列表中首先列出。

```json
{ 
  "bindings": [ 
    { 
      "type": "cosmosDB", 
      "name": "ToDoItem", 
      "databaseName": "ToDoItems", 
      "collectionName": "Items", 
      "connectionStringSetting": "CosmosDBConnection", 
      "direction": "in", 
      "Id": "{id}", 
      "PartitionKey": "{partitionKeyValue}" 
    },
    { 
      "authLevel": "anonymous", 
      "name": "Request", 
      "type": "httpTrigger", 
      "direction": "in", 
      "methods": [ 
        "get", 
        "post" 
      ], 
      "route": "todoitems/{partitionKeyValue}/{id}" 
    }, 
    { 
      "name": "Response", 
      "type": "http", 
      "direction": "out" 
    }
  ], 
  "disabled": false 
} 
```

_run.ps1_ 文件包含 PowerShell 代码，该代码读取传入的文档并输出更改。

```powershell
using namespace System.Net

param($Request, $ToDoItem, $TriggerMetadata)

Write-Host 'PowerShell HTTP trigger function processed a request'

if (-not $ToDoItem) { 
    Write-Host 'ToDo item not found'

    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::NotFound 
        Body = $ToDoItem.Description 
    })

} else { 
    Write-Host "Found ToDo item, Description=$($ToDoItem.Description)"

    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::OK 
        Body = $ToDoItem.Description 
    }) 
} 
```

<a name="queue-trigger-multiple-docs-sqlquery-ps"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>队列触发器，使用 SqlQuery 获取多个文档

下面的示例演示如何读取多个 Cosmos DB 文档。 函数的配置文件 (_function.json_) 定义了包含 `sqlQuery` 的绑定属性。 提供给 `sqlQuery` 属性的 SQL 语句选择提供给函数的文档集。

```json
{ 
  "name": "Documents", 
  "type": "cosmosDB", 
  "direction": "in", 
  "databaseName": "MyDb", 
  "collectionName": "MyCollection", 
  "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}", 
  "connectionStringSetting": "CosmosDBConnection" 
} 
```

_run1.ps_ 文件包含 PowerShell 代码，该代码读取传入的文档。

```powershell
param($QueueItem, $Documents, $TriggerMetadata)

foreach ($Document in $Documents) { 
    # operate on each document 
} 
```

# <a name="python"></a>[Python](#tab/python)

此部分包含的以下示例可以通过指定各种源提供的 ID 值来读取单个文档：

* [队列触发器，从 JSON 查找 ID](#queue-trigger-look-up-id-from-json-python)
* [HTTP 触发器，从查询字符串查找 ID](#http-trigger-look-up-id-from-query-string-python)
* [HTTP 触发器，从路由数据查找 ID](#http-trigger-look-up-id-from-route-data-python)
* [队列触发器，使用 SqlQuery 获取多个文档](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>队列触发器，从 JSON 查找 ID

以下示例演示 *function.json* 文件中的一个 Cosmos DB 输入绑定以及使用该绑定的 [Python 函数](functions-reference-python.md)。 该函数读取单个文档，并更新文档的文本值。

下面是 function.json 文件中的绑定数据：

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[配置](#configuration)部分解释了这些属性。

下面是 Python 代码：

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP 触发器，从查询字符串查找 ID

以下示例展示了检索单个文档的 [Python 函数](functions-reference-python.md)。 此函数由 HTTP 请求触发，该请求使用的查询字符串用于指定要查找的 ID 和分区键值。 该 ID 和分区键值用于从指定的数据库和集合中检索 `ToDoItem` 文档。

function.json 文件如下所示：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "scriptFile": "__init__.py"
}
```

下面是 Python 代码：

```python
import logging
import azure.functions as func

def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP 触发器，从路由数据查找 ID

以下示例展示了检索单个文档的 [Python 函数](functions-reference-python.md)。 此函数由 HTTP 请求触发，该请求使用的路由数据用于指定要查找的 ID 和分区键值。 该 ID 和分区键值用于从指定的数据库和集合中检索 `ToDoItem` 文档。

function.json 文件如下所示：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

下面是 Python 代码：

```python
import logging
import azure.functions as func

def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>队列触发器，使用 SqlQuery 获取多个文档

以下示例演示 *function.json* 文件中的一个 Azure Cosmos DB 输入绑定以及使用该绑定的 [Python 函数](functions-reference-python.md)。 该函数使用队列触发器自定义查询参数检索 SQL 查询指定的多个文档。

队列触发器提供参数 `departmentId`。 `{ "departmentId" : "Finance" }` 的队列消息将返回财务部的所有记录。

下面是 function.json 文件中的绑定数据：

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

[配置](#configuration)部分解释了这些属性。

下面是 Python 代码：

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

 ---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) 特性。

该特性的构造函数采用数据库名称和集合名称。 在[扩展版本 4.x](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) 中，一些设置和属性已被删除或重命名。 若要了解可以为所有版本配置的设置和其他属性，请参阅[下面的“配置”部分](#configuration)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="java"></a>[Java](#tab/java)

在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对写入 Cosmos DB 的参数使用 `@CosmosDBOutput` 注释。 注释参数类型应当为 `OutputBinding<T>`，其中 `T` 是本机 Java 类型或 POJO。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

---

## <a name="configuration"></a>配置

下表解释了在 function.json  文件和 `CosmosDB` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type     | 不适用 | 必须设置为 `cosmosDB`。        |
|**direction**     | 不适用 | 必须设置为 `in`。         |
|**name**     | 不适用 | 表示函数中的文档的绑定参数的名称。  |
|**databaseName** |**DatabaseName** |包含文档的数据库。        |
|**collectionName** <br> 或 <br> **containerName**|**CollectionName** <br> 或 <br> ContainerName| 包含文档的集合的名称。 <br><br> 在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中，此属性称为 `ContainerName`。 |
|**id**    | Id | 要检索的文档的 ID。 此属性支持[绑定表达式](./functions-bindings-expressions-patterns.md)。 不要同时设置 `id` 和 **sqlQuery** 属性。 如果上述两个属性都未设置，则会检索整个集合。 |
|**sqlQuery**  |**SqlQuery**  | 用于检索多个文档的 Azure Cosmos DB SQL 查询。 该属性支持运行时绑定，如以下示例中所示：`SELECT * FROM c where c.departmentId = {departmentId}`。 不要同时设置 `id` 和 `sqlQuery` 属性。 如果上述两个属性都未设置，则会检索整个集合。|
|**connectionStringSetting** <br> 或 <br> 连接  |**ConnectionStringSetting** <br> 或 <br> **Connection**|内含 Azure Cosmos DB 连接字符串的应用设置的名称。 <br><br> 在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中，此属性称为 `Connection`。 此值是某个应用设置的名称，其中包含连接字符串或包含用于定义连接的配置部分或前缀。 请参阅[连接](./functions-reference.md#connections)。 |
|**partitionKey**|**PartitionKey**|指定用于查找分区键值。 可以包含绑定参数。 它是在[分区的](../cosmos-db/partitioning-overview.md#logical-partitions)集合中进行查询所需的。|
|**preferredLocations**| **PreferredLocations**| （可选）为 Azure Cosmos DB 服务中的异地复制数据库帐户定义首选位置（区域）。 值应以逗号分隔。 例如，“美国东部,美国中南部,北欧”。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

# <a name="c"></a>[C#](#tab/csharp)

函数成功退出时，通过命名输入参数对输入文档所做的任何更改都会自动保存。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

函数成功退出时，通过命名输入参数对输入文档所做的任何更改都会自动保存。

# <a name="java"></a>[Java](#tab/java)

在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，[@CosmosDBInput](/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) 注释向函数公开 Cosmos DB 数据。 可以将此注释与本机 Java 类型、POJO 或使用了 `Optional<T>` 的可为 null 的值一起使用。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

函数退出时不会自动进行更新。 请改用 `context.bindings.<documentName>In` 和 `context.bindings.<documentName>Out` 进行更新。 有关更多详细信息，请参阅 [JavaScript 示例](#example)。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

函数退出时不会自动对文档进行更新。 若要在函数中更新文档，请使用[输出绑定](./functions-bindings-cosmosdb-v2-input.md)。 有关更多详细信息，请参阅 [PowerShell 示例](#example)。

# <a name="python"></a>[Python](#tab/python)

数据通过 `DocumentList` 参数提供给函数。 不会自动保留对文档所做的更改。

---

## <a name="next-steps"></a>后续步骤

- [创建或修改 Azure Cosmos DB 文档时运行函数（触发器）](./functions-bindings-cosmosdb-v2-trigger.md)
- [保存对 Azure Cosmos DB 文档的更改（输出绑定）](./functions-bindings-cosmosdb-v2-output.md)
