---
title: 适用于 Azure Functions 的 Azure 队列存储输出绑定
description: 了解如何在 Azure Functions 中创建 Azure 队列存储消息。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 5d94625e3eb121e556b28038cf59626be1332966
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455799"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>适用于 Azure Functions 的 Azure 队列存储输出绑定

Azure Functions 可以通过设置输出绑定来创建新的 Azure 队列存储消息。

有关设置和配置详细信息，请参阅[概述](./functions-bindings-storage-queue.md)。

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示针对收到的每个 HTTP 请求创建队列消息的[C# 函数](functions-dotnet-class-library.md)。

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下示例演示 function.json 文件中的一个 HTTP 触发器绑定以及使用该绑定的 [C# 脚本 (.csx)](functions-reference-csharp.md) 代码。 该函数针对收到的每个 HTTP 请求创建一个包含 CustomQueueMessage 对象有效负载的队列项。

function.json 文件如下所示：

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

[配置](#configuration)部分解释了这些属性。

下面是可创建一条队列消息的 C# 脚本代码：

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

可以使用 `ICollector` 或 `IAsyncCollector` 参数一次性发送多条消息。 以下 C# 脚本代码发送多条消息，其中一条消息包含 HTTP 请求数据，另一条消息包含硬编码值：

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="java"></a>[Java](#tab/java)

 以下示例演示一个 Java 函数，该函数在受到 HTTP 请求触发时创建一个队列消息。

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值将写入队列存储的参数使用 `@QueueOutput` 注释。  参数类型应为 `OutputBinding<T>`，其中 `T` 是 POJO 的任何本机 Java 类型。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 function.json 文件中的一个 HTTP 触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数针对收到的每个 HTTP 请求创建一个队列项。

function.json 文件如下所示：

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

[配置](#configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

可以通过定义 `myQueueItem` 输出绑定的消息数组，一次性发送多条消息。 以下 JavaScript 代码针对收到的每个 HTTP 请求发送两条包含硬编码值的队列消息。

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

以下代码示例演示了如何从 HTTP 触发的函数输出队列消息。 `type` 是 `queue` 的配置节定义了输出绑定。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "Msg",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

利用此绑定配置，PowerShell 函数可以使用 `Push-OutputBinding` 创建队列消息。 在此示例中，将通过查询字符串或正文参数创建消息。

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

若要同时发送多条消息，请定义消息数组，并使用 `Push-OutputBinding` 向队列输出绑定发送消息。

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = @("message1", "message2")
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

下面的示例演示如何将单个值和多个值输出到存储队列。 无论哪种方式，function.json 所需的配置都是相同的。

存储队列绑定在 function.json 中定义，其中 type 设置为 `queue`。

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
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

若要在队列中设置单个消息，请将单个值传递给 `set` 方法。

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

若要在队列中创建多个消息，请将参数声明为适当的列表类型，并将值的数组（与列表类型匹配）传递给 `set` 方法。

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs)。

该特性将应用到 `out` 参数，或应用到函数的返回值。 该特性的构造函数采用队列的名称，如以下示例中所示：

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

有关完整示例，请参阅[输出示例](#example)。

可以使用 `StorageAccount` 特性在类、方法或参数级别指定存储帐户。 有关详细信息，请参阅“触发器 - 特性”。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="java"></a>[Java](#tab/java)

使用 `QueueOutput` 注释可以将一条消息编写为函数的输出。 以下示例演示一个用于创建队列消息的 HTTP 触发的函数。

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| 属性    | 说明 |
|-------------|-----------------------------|
|`name`       | 在函数签名中声明参数名称。 触发函数时，此参数的值包含队列消息的内容。 |
|`queueName`  | 在存储帐户中声明队列名称。 |
|`connection` | 指向存储帐户连接字符串。 |

与 `QueueOutput` 注释关联的参数类型化为 [OutputBinding\<T\>](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) 实例。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

---

## <a name="configuration"></a>配置

下表解释了在 function.json  文件和 `Queue` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为 `queue`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为 `out`。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 不适用 | 表示函数代码中的队列的变量的名称。 设置为 `$return` 可引用函数返回值。|
|**queueName** |**QueueName** | 队列的名称。 |
|连接 | **Connection** |包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。<br><br>例如，如果将 `connection` 设置为“MyStorage”，Functions 运行时将会查找名为“MyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。<br><br>如果使用 [5.x 版或更高版本的扩展](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)，而不是连接字符串，则可以提供对用于定义连接的配置节的引用。 请参阅[连接](./functions-reference.md#connections)。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>默认

使用 `out T paramName` 等方法参数写入一条队列消息。 可以使用方法返回类型而不使用 `out` 参数，`T` 可为以下任何类型：

* 可序列化为 JSON 的对象
* `string`
* `byte[]`
* [CloudQueueMessage] 

如果在尝试绑定到 `CloudQueueMessage` 时出现错误消息，请确保引用[正确的存储 SDK 版本](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)。

在 C# 和 C# 脚本中，可使用以下类型之一编写多条队列消息： 

* `ICollector<T>` 或 `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>其他类型

应用如果使用 [5.0.0 版或更高版本的存储扩展](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)，还可以使用[用于 .NET 的 Azure SDK](/dotnet/api/overview/azure/storage.queues-readme) 中的类型。 此版本为了支持以下类型，删除了对旧的 `CloudQueue` 和 `CloudQueueMessage` 类型的支持：

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- 用于编写多个队列消息的 [QueueClient](/dotnet/api/azure.storage.queues.queueclient)

有关使用这些类型的示例，请参阅[扩展的 GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

### <a name="default"></a>默认

使用 `out T paramName` 等方法参数写入一条队列消息。 `paramName` 是在 *function.json* 的 `name` 属性中指定的值。 可以使用方法返回类型而不使用 `out` 参数，`T` 可为以下任何类型：

* 可序列化为 JSON 的对象
* `string`
* `byte[]`
* [CloudQueueMessage] 

如果在尝试绑定到 `CloudQueueMessage` 时出现错误消息，请确保引用[正确的存储 SDK 版本](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)。

在 C# 和 C# 脚本中，可使用以下类型之一编写多条队列消息： 

* `ICollector<T>` 或 `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>其他类型

应用如果使用 [5.0.0 版或更高版本的存储扩展](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)，还可以使用[用于 .NET 的 Azure SDK](/dotnet/api/overview/azure/storage.queues-readme) 中的类型。 此版本为了支持以下类型，删除了对旧的 `CloudQueue` 和 `CloudQueueMessage` 类型的支持：

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- 用于编写多个队列消息的 [QueueClient](/dotnet/api/azure.storage.queues.queueclient)

有关使用这些类型的示例，请参阅[扩展的 GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples)。

# <a name="java"></a>[Java](#tab/java)

可通过两个选项使用 [QueueOutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput) 注释从函数输出队列消息：

- **返回值**：通过将注释应用于函数本身，函数的返回值将持久保存为队列消息。

- **命令性**：若要显式设置消息值，请将注释应用于 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 类型的特定参数，其中 `T` 是 POJO 或任何本机 Java 类型。 使用此配置时，向 `setValue` 方法传递某值会将该值持久保存为队列消息。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

可通过 `context.bindings.<NAME>` 使用输出队列项，其中，`<NAME>` 与 *function.json* 中定义的名称相匹配。 可对队列项有效负载使用字符串或 JSON 可序列化对象。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

通过 `Push-OutputBinding` 可以输出到队列消息，你在其中传递的参数与 function.json 文件中绑定的 `name` 参数指定的名称匹配。

# <a name="python"></a>[Python](#tab/python)

有两个选项可用于从函数输出队列消息：

- **返回值**：将 function.json 中的 `name` 属性  设置为 `$return`。 使用此配置时，函数的返回值将作为队列存储消息保留。

- **命令性**：将值传递给声明为 [Out](/python/api/azure-functions/azure.functions.out) 类型的参数的 [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) 方法。 传递给 `set` 的值将作为队列存储消息保留。

---

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 |  参考 |
|---|---|
| 队列 | [队列错误代码](/rest/api/storageservices/queue-service-error-codes) |
| Blob、表、队列 | [存储错误代码](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob、表、队列 |  [故障排除](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>后续步骤

- [在队列存储数据更改时运行函数（触发器）](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
