---
title: 适用于 Functions 2.x 及更高版本的 Azure Cosmos DB 触发器
description: 了解如何在 Azure Functions 中使用 Azure Cosmos DB 触发器。
author: craigshoemaker
ms.topic: reference
ms.date: 09/01/2021
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 0bccf556f48cea52c4458ec6afc882c3c6035a71
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635178"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x-and-higher"></a>适用于 Azure Functions 2.x 及更高版本的 Azure Cosmos DB 触发器

Azure Cosmos DB 触发器使用 [Azure Cosmos DB 更改源](../cosmos-db/change-feed.md)来侦听跨分区的插入和更新。 更改源发布插入和更新，不发布删除。

有关设置和配置详细信息，请参阅[概述](./functions-bindings-cosmosdb-v2.md)。

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示了一个 [C# 函数](functions-dotnet-class-library.md)。当指定数据库和集合中存在插入或更新操作时，会调用该函数。

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

使用 Cosmos DB [扩展版本 4.x](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) 或更高版本的应用将具有不同的特性属性，如下所示。 此示例引用简单的 `ToDoItem` 类型。

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

```cs
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "databaseName",
            containerName: "containerName",
            Connection = "CosmosDBConnectionSetting",
            LeaseContainerName = "leases",
            CreateLeaseContainerIfNotExists = true)]IReadOnlyList<ToDoItem> input, ILogger log)
        {
            if (input != null && input.Count > 0)
            {
                log.LogInformation("Documents modified " + input.Count);
                log.LogInformation("First document Id " + input[0].Id);
            }
        }
    }
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下示例演示 *function.json* 文件中的一个 Cosmos DB 触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 添加或修改 Cosmos DB 记录时，该函数会写入日志消息。

下面是 function.json 文件中的绑定数据：

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

C# 脚本代码如下所示：

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="java"></a>[Java](#tab/java)

当指定数据库和集合中发生插入或更新操作时，会调用此函数。

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值将来自 Cosmos DB 的参数使用 `@CosmosDBTrigger` 注释。  可以将此注释与本机 Java 类型、POJO 或使用了 `Optional<T>` 的可为 null 的值一起使用。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个 Cosmos DB 触发器绑定以及使用该绑定的 [JavaScript 脚本函数](functions-reference-node.md)。 添加或修改 Cosmos DB 记录时，该函数会写入日志消息。

下面是 function.json 文件中的绑定数据：

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

JavaScript 代码如下所示：

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

下面的示例演示如何在 Cosmos DB 中的数据更改时运行函数。

```json
{
  "type": "cosmosDBTrigger",
  "name": "Documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```

在 run.ps1 文件中，可以通过 `$Documents` 参数访问触发函数的文档。

```powershell
param($Documents, $TriggerMetadata) 

Write-Host "First document Id modified : $($Documents[0].id)" 
```

# <a name="python"></a>[Python](#tab/python)

以下示例演示 *function.json* 文件中的一个 Cosmos DB 触发器绑定以及使用该绑定的 [Python 函数](functions-reference-python.md)。 修改 Cosmos DB 记录时，该函数会写入日志消息。

下面是 function.json 文件中的绑定数据：

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

下面是 Python 代码：

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) 特性。

该特性的构造函数采用数据库名称和集合名称。 有关这些设置以及可以配置的其他属性的信息，请参阅[触发器 - 配置](#configuration)。 下面是某个方法签名中的 `CosmosDBTrigger` 特性示例：

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run([CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
        IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

在[扩展版本 4.x](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) 中，一些设置和属性已被删除或重命名。 有关这些更改的详细信息，请参阅[触发器 - 配置](#configuration)。 下面是某个引用简单 `ToDoItem` 类型的方法签名中的 `CosmosDBTrigger` 特性示例：

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

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run([CosmosDBTrigger("database", "container", Connection = "CosmosDBConnectionSetting")]
        IReadOnlyList<ToDoItem> documents,  
        ILogger log)
    {
        ...
    }
```

有关任一扩展版本的完整示例，请参阅[触发器](#example)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="java"></a>[Java](#tab/java)

在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对从 Cosmos DB 读取数据的参数使用 `@CosmosDBInput` 注释。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

---

## <a name="configuration"></a>配置

下表解释了在 function.json  文件和 `CosmosDBTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为 `cosmosDBTrigger`。 |
|**direction** | 不适用 | 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置该参数。 |
|**name** | 不适用 | 函数代码中使用的变量名称，表示发生更改的文档列表。 |
|**connectionStringSetting** <br> 或 <br> 连接|**ConnectionStringSetting** <br> 或 <br> **Connection**| 应用设置的名称，该应用设置包含用于连接到受监视的 Azure Cosmos DB 帐户的连接字符串。 <br><br> 在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中，此属性称为 `Connection`。 此值是某个应用设置的名称，其中包含用于连接到受监视的 Azure Cosmos DB 帐户的连接字符串，或包含用于定义连接的配置部分或前缀。 请参阅[连接](./functions-reference.md#connections)。 |
|**databaseName**|**DatabaseName**  | 带有受监视的集合的 Azure Cosmos DB 数据库的名称。 |
|**collectionName** <br> 或 <br> **containerName** |**CollectionName** <br> 或 <br> ContainerName | 受监视的集合的名称。 <br><br> 在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中，此属性称为 `ContainerName`。 |
|**leaseConnectionStringSetting** <br> 或 <br> leaseConnection | **LeaseConnectionStringSetting** <br> 或 <br> LeaseConnection | （可选）应用设置的名称，该设置包含保存租用集合的 Azure Cosmos DB 帐户的连接字符串。 未设置时，使用 `connectionStringSetting` 值。 在门户中创建绑定时，将自动设置该参数。 用于租用集合的连接字符串必须具有写入权限。 <br><br> 在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中，此属性称为 `LeaseConnection`。在未设置的情况下，它会使用 `Connection` 值。 此值是某个应用设置的名称，其中包含用于连接到 Azure Cosmos DB 帐户（使用租用容器）的连接字符串，或包含用于定义连接的配置部分或前缀。 请参阅[连接](./functions-reference.md#connections)。|
|**leaseDatabaseName** |**LeaseDatabaseName** | （可选）数据库的名称，该数据库包含用于存储租用的集合。 未设置时，使用 `databaseName` 设置的值。 在门户中创建绑定时，将自动设置该参数。 |
|**leaseCollectionName** <br> 或 <br> leaseContainerName | **LeaseCollectionName** <br> 或 <br> LeaseContainerName | （可选）用于存储租用的集合的名称。 未设置时，使用值 `leases`。 <br><br> 在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中，此属性称为 `LeaseContainerName`。 |
|**createLeaseCollectionIfNotExists** <br> 或 <br> createLeaseContainerIfNotExists | **CreateLeaseCollectionIfNotExists** <br> 或 <br> CreateLeaseContainerIfNotExists | （可选）设置为 `true` 时，如果租用集合并不存在，将自动创建该集合。 默认值是 `false`。 <br><br> 在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中，此属性称为 `CreateLeaseContainerIfNotExists`。 |
|**leasesCollectionThroughput** <br> 或 <br> leasesContainerThroughput| **LeasesCollectionThroughput** <br> 或 <br> LeasesContainerThroughput| （可选）在创建租用集合时，定义要分配的请求单位的数量。 仅当 `createLeaseCollectionIfNotExists` 设置为 `true` 时，才会使用此设置。 使用门户创建绑定时，将自动设置该参数。 <br><br> 在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中，此属性称为 `LeasesContainerThroughput`。 |
|**leaseCollectionPrefix** <br> 或 <br> leaseContainerPrefix| **LeaseCollectionPrefix** <br> 或 <br> leaseContainerPrefix | （可选）设置后，该值将作为前缀添加到在此函数的租约集合中创建的租约。 使用前缀可让两个不同的 Azure 函数通过不同的前缀来共享同一个租约集合。 <br><br> 在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中，此属性称为 `LeaseContainerPrefix`。 |
|**feedPollDelay**| **FeedPollDelay**| （可选）在所有当前更改清空后，每两次在分区中轮询源上的新更改的延迟时间（以毫秒为单位）。 默认值为 5,000 毫秒（5 秒）。
|**leaseAcquireInterval**| **LeaseAcquireInterval**| （可选）设置后，此项以毫秒为单位定义启动一个计算任务的时间间隔（前提是分区在已知的主机实例中均匀分布）。 默认为 13000（13 秒）。
|**leaseExpirationInterval**| **LeaseExpirationInterval**| （可选）设置后，此项以毫秒为单位定义在表示分区的租用上进行租用的时间间隔。 如果在此时间间隔内不续订租用，则该租用会过期，分区的所有权会转移到另一个实例。 默认为 60000（60 秒）。
|**leaseRenewInterval**| **LeaseRenewInterval**| （可选）设置后，此项以毫秒为单位定义当前由实例拥有的分区的所有租用的续订时间间隔。 默认为 17000（17 秒）。
|checkpointInterval| CheckpointInterval| （可选）设置后，此项以毫秒为单位定义租用检查点的时间间隔。 默认为始终在进行每个 Function 调用之后进行检查。 <br><br> 此属性在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中不可用。 |
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| （可选）设置后，此属性会对每次函数调用收到的项目的最大数目进行设置。 如果受监视集合中的操作通过存储过程执行，则在从更改源读取项时，会保留[事务范围](../cosmos-db/stored-procedures-triggers-udfs.md#transactions)。 因此，收到的项数可能高于指定的值，通过同一事务更改的项会通过某个原子批处理操作返回。
|**startFromBeginning**| **StartFromBeginning**| （可选）此选项告知触发器要从集合的更改历史记录开头位置读取更改，而不是从当前时间开始读取。 从开头位置读取仅在触发器首次启动时起作用，因为在后续运行中，已存储检查点。 如果已经创建租约，则将此选项设置为 `true` 将不起作用。 |
|**preferredLocations**| **PreferredLocations**| （可选）为 Azure Cosmos DB 服务中的异地复制数据库帐户定义首选位置（区域）。 值应以逗号分隔。 例如，“美国东部,美国中南部,北欧”。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

触发器需要第二个集合，该集合用于存储各分区的 _租用_。 必须提供受监视的集合和包含租用的集合，触发器才能正常工作。

>[!IMPORTANT]
> 如果多个函数都配置为对同一集合使用 Cosmos DB 触发器，则每个函数都应使用专用租用集合，否则应该为每个函数指定不同的 `LeaseCollectionPrefix`。 否则，将只触发其中一个函数。 有关前缀的信息，请参阅[“配置”部分](#configuration)。

该触发器并不指示是更新还是插入文档，它仅提供文档本身。 如果需要以不同方式处理更新和插入，可通过实现用于插入或更新的时间戳字段来执行该操作。

## <a name="next-steps"></a>后续步骤

- [读取 Azure Cosmos DB 文档（输入绑定）](./functions-bindings-cosmosdb-v2-input.md)
- [保存对 Azure Cosmos DB 文档的更改（输出绑定）](./functions-bindings-cosmosdb-v2-output.md)
