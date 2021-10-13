---
title: 适用于 Functions 2.x 及更高版本的 Azure Cosmos DB 输出绑定
description: 了解如何在 Azure Functions 中使用 Azure Cosmos DB 输出绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 09/01/2021
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: d43407221d62992a54f1e5efdeb23afd3dde92ef
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661172"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x-and-higher"></a>适用于 Azure Functions 2.x 及更高版本的 Azure Cosmos DB 输出绑定

Azure Cosmos DB 输出绑定允许使用 SQL API 将新文档写入 Azure Cosmos DB 数据库。

有关设置和配置详细信息，请参阅[概述](./functions-bindings-cosmosdb-v2.md)。

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

本部分包含以下示例：

* [队列触发器，写入一个文档](#queue-trigger-write-one-doc-c)
* [队列触发器，写入一个文档（v4 扩展）](#queue-trigger-write-one-doc-v4-c)
* [队列触发器，使用 IAsyncCollector 写入文档](#queue-trigger-write-docs-using-iasynccollector-c)

这些示例引用简单的 `ToDoItem` 类型：

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

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>队列触发器，写入一个文档

以下示例演示一个使用队列存储消息中提供的数据，将文档添加到数据库的 [C# 函数](functions-dotnet-class-library.md)。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-one-doc-v4-c"></a>

### <a name="queue-trigger-write-one-doc-v4-extension"></a>队列触发器，写入一个文档（v4 扩展）

使用 Cosmos DB [扩展版本 4.x](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) 或更高版本的应用将具有不同的特性属性，如下所示。 以下示例演示一个使用队列存储消息中提供的数据，将文档添加到数据库的 [C# 函数](functions-dotnet-class-library.md)。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                containerName: "Items",
                Connection = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>队列触发器，使用 IAsyncCollector 来写入文档

以下示例演示了一个 [C# 函数](functions-dotnet-class-library.md)，该函数使用以队列消息 JSON 格式提供的数据将文档集添加到数据库。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

本部分包含以下示例：

* [队列触发器，写入一个文档](#queue-trigger-write-one-doc-c-script)
* [队列触发器，使用 IAsyncCollector 写入文档](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>队列触发器，写入一个文档

以下示例演示 *function.json* 文件中的一个 Azure Cosmos DB 输出绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数使用一个用于某个队列的队列输入绑定，该队列以下列格式接收 JSON：

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

该函数按下列格式为每个记录创建 Azure Cosmos DB 文档：

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

下面是 function.json 文件中的绑定数据：

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[配置](#configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>队列触发器，使用 IAsyncCollector 来写入文档

若要创建多个文档，可以绑定到 `ICollector<T>` 或 `IAsyncCollector<T>`，其中 `T` 是受支持的类型之一。

此示例引用简单的 `ToDoItem` 类型：

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

function.json 文件如下所示：

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# 脚本代码如下所示：

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

* [队列触发器，通过返回值将消息保存到数据库](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP 触发器，通过返回值将文件保存到数据库](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP 触发器，通过 OutputBinding 将一个文档保存到数据库](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP 触发器，通过 OutputBinding 将多个文档保存到数据库](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>队列触发器，通过返回值将消息保存到数据库

以下示例展示了一个 Java 函数，它向数据库中添加一个文档，该文档包含来自队列存储中消息的数据。

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>HTTP 触发器，通过返回值将文件保存到数据库

以下示例显示了 Java 函数，其签名使用 ```@CosmosDBOutput``` 注释，并且返回值类型为 ```String```。 该函数返回的 JSON 文档将自动写入相应的 CosmosDB 集合。

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>HTTP 触发器，通过 OutputBinding 将一个文档保存到数据库

以下示例显示了 Java 函数，该函数通过 ```OutputBinding<T>``` 输出参数将文档写入 CosmosDB。 在此示例中，需要使用 ```@CosmosDBOutput``` 为 ```outputItem``` 参数提供注释，而不要使用函数签名。 使用 ```OutputBinding<T>```，让函数可以利用绑定将文档写入 CosmosDB，同时还可向函数调用者返回不同的值，例如 JSON 或 XML 文档。

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>HTTP 触发器，通过 OutputBinding 将多个文档保存到数据库

以下示例显示了 Java 函数，该函数通过 ```OutputBinding<T>``` 输出参数将多个文档写入 CosmosDB。 在此示例中，需使用 ```@CosmosDBOutput``` 为 ```outputItem``` 参数提供注释，而不要使用函数签名。 输出参数 ```outputItem``` 有一个 ```ToDoItem``` 对象列表作为其模板参数类型。 使用 ```OutputBinding<T>```，让函数可以利用绑定将文档写入 CosmosDB，同时还可向函数调用者返回不同的值，例如 JSON 或 XML 文档。

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值将写入到 Cosmos DB 的参数使用 `@CosmosDBOutput` 注释。  注释参数类型应当为 ```OutputBinding<T>```，其中 T 是本机 Java 类型或 POJO。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个 Azure Cosmos DB 输出绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数使用一个用于某个队列的队列输入绑定，该队列以下列格式接收 JSON：

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

该函数按下列格式为每个记录创建 Azure Cosmos DB 文档：

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

下面是 function.json 文件中的绑定数据：

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[配置](#configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

对于批量插入，请先构建对象，然后运行 stringify 函数。 JavaScript 代码如下所示：

```javascript
    module.exports = function (context) {
    
        context.bindings.employeeDocument = JSON.stringify([
        {
            "id": "John Henry-123456",
            "name": "John Henry",
            "employeeId": "123456",
            "address": "A town nearby"
        },
        {
            "id": "John Doe-123457",
            "name": "John Doe",
            "employeeId": "123457",
            "address": "A town far away"
        }]);
    
      context.done();
    };
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

下面的示例演示如何使用输出绑定将数据写入 Cosmos DB。 绑定在函数的配置文件 (functions.json) 中声明，并从队列消息中获取数据，然后写出到 Cosmos DB 文档中。

```json
{ 
  "name": "EmployeeDocument",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "direction": "out" 
} 
```

在 run.ps1 文件中，从函数返回的对象将映射到 `EmployeeDocument` 对象，该对象将持久保存在数据库中。

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name EmployeeDocument -Value @{ 
    id = $QueueItem.name + '-' + $QueueItem.employeeId 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

下面的示例演示如何将文档作为函数的输出写入 Azure CosmosDB 数据库。

绑定定义在 function.json 中定义，其中 type 设置为 `cosmosDB`。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

若要写入数据库，请将文档对象传递给数据库参数的 `set` 方法。

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/dev/test/WebJobs.Extensions.CosmosDB.Tests) 特性。

该特性的构造函数采用数据库名称和集合名称。 有关这些设置以及可以配置的其他属性的信息，请参阅[输出 - 配置](#configuration)。 下面是某个方法签名中的 `CosmosDB` 特性示例：

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

在[扩展版本 4.x](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) 中，一些设置和属性已被删除或重命名。 有关这些更改的详细信息，请参阅[输出 - 配置](#configuration)。 下面是某个方法签名中的 `CosmosDB` 特性示例：

```csharp
    [FunctionName("QueueToCosmosDB")]
    public static void Run(
      [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
      [CosmosDB("database", "container", Connection = "CosmosDBConnectionSetting")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="java"></a>[Java](#tab/java)

`CosmosDBOutput` 注释可用于将数据写入 Cosmos DB。 可将注释应用到函数或单个函数参数。 在函数方法中使用时，函数的返回值就是写入 Cosmos DB 的值。 如果对参数使用注释，则必须将参数的类型声明为 `OutputBinding<T>`，其中 `T` 是本机 Java 类型或 POJO。

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
|**direction**     | 不适用 | 必须设置为 `out`。         |
|**name**     | 不适用 | 表示函数中的文档的绑定参数的名称。  |
|**databaseName** | **DatabaseName**|包含在其中创建文档的集合的数据库。     |
|**collectionName** <br> or <br> **containerName** |**CollectionName** <br> or <br> ContainerName | 包含在其中创建文档的集合的名称。 <br><br> 在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中，此属性称为 `ContainerName`。 |
|**createIfNotExists**  |**CreateIfNotExists**    | 一个用于指示是否创建集合（如果不存在）的布尔值。 默认值为 *false*，因为新集合是使用保留的吞吐量创建的，具有成本方面的隐含意义。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。  |
|**partitionKey**|**PartitionKey** |当 `CreateIfNotExists` 为 true 时，将定义所创建集合的分区键路径。|
|**collectionThroughput** <br> or <br> containerThroughput|**CollectionThroughput** <br> or <br> ContainerThroughput| 当 `CreateIfNotExists` 为 true 时，将定义所创建集合的[吞吐量](../cosmos-db/set-throughput.md)。 <br><br> 在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中，此属性称为 `ContainerThroughput`。 |
|**connectionStringSetting** <br> or <br> 连接   |**ConnectionStringSetting** <br> or <br> **Connection**|内含 Azure Cosmos DB 连接字符串的应用设置的名称。  <br><br> 在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中，此属性称为 `Connection`。 此值是某个应用设置的名称，其中包含连接字符串或包含用于定义连接的配置部分或前缀。 请参阅[连接](./functions-reference.md#connections)。 |
|**preferredLocations**| **PreferredLocations**| （可选）为 Azure Cosmos DB 服务中的异地复制数据库帐户定义首选位置（区域）。 值应以逗号分隔。 例如，“美国东部,美国中南部,北欧”。 |
|**useMultipleWriteLocations**| **UseMultipleWriteLocations**| （可选）与 `PreferredLocations` 一起设置为 `true` 时，它可以利用 Azure Cosmos DB 服务中的[多区域写入](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions)。 <br><br> 此属性在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中不可用。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

默认情况下，当写入函数中的输出参数时，将在数据库中创建一个文档。 本文档将自动生成的 GUID 作为文档 ID。 可以通过在传递给输出参数的 JSON 对象中指定 `id` 属性来指定输出文档的文档 ID。

> [!Note]
> 如果指定现有文档的 ID，它会被新的输出文档覆盖。

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 | 参考 |
|---|---|
| CosmosDB | [CosmosDB 错误代码](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.json 设置

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|属性  |默认 |说明 |
|----------|--------|------------|
|GatewayMode|网关|连接到 Azure Cosmos DB 服务时该函数使用的连接模式。 选项为 `Direct` 和 `Gateway`|
|协议|Https|连接到 Azure Cosmos DB 服务时该函数使用的连接协议。 请参阅[此文](../cosmos-db/performance-tips.md#networking)，了解两种模式的说明。 <br><br> 此设置在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中不可用。 |
|leasePrefix|不适用|应用中所有函数要使用的租用前缀。 <br><br> 此设置在 [4.x 版扩展](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher)中不可用。|

## <a name="next-steps"></a>后续步骤

- [创建或修改 Azure Cosmos DB 文档时运行函数（触发器）](./functions-bindings-cosmosdb-v2-trigger.md)
- [读取 Azure Cosmos DB 文档（输入绑定）](./functions-bindings-cosmosdb-v2-input.md)
