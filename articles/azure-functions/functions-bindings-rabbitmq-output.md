---
title: Azure Functions 的 RabbitMQ 输出绑定
description: 了解如何从 Azure Functions 发送 RabbitMQ 消息。
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/16/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 8ea4b0c7c8df1ff7fe0e3ecb253b23d188681e80
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629674"
---
# <a name="rabbitmq-output-binding-for-azure-functions-overview"></a>Azure Functions 概述的 RabbitMQ 输出绑定

> [!NOTE]
> 只有 **Windows 高级版和专用** 计划才完全支持 RabbitMQ 绑定。 当前不支持使用和 Linux。

使用 RabbitMQ 输出绑定将消息发送到 RabbitMQ 队列。

有关设置和配置详细信息，请参阅[概述](functions-bindings-rabbitmq-output.md)。

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

下面的示例演示了一个 [c # 函数](functions-dotnet-class-library.md) ，该函数在 TimerTrigger 每5分钟触发一次后，使用方法返回值作为输出来发送 RabbitMQ 消息：

```cs
[FunctionName("RabbitMQOutput")]
[return: RabbitMQ("outputQueue", ConnectionStringSetting = "ConnectionStringSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

下面的示例演示如何使用 IAsyncCollector 接口发送消息。

```cs
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("destinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    // processing:
    var myProcessedEvent = DoSomething(rabbitMQEvent);
    
     // send the message
    await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
}
```

下面的示例演示如何将消息作为 Poco 发送。

```cs
public class TestClass
{
    public string x { get; set; }
}

[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "TriggerConnectionString")] TestClass rabbitMQEvent,
[RabbitMQ("destinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<TestClass> outputPocObj,
ILogger log)
{
    // send the message
    await outputPocObj.Add(rabbitMQEvent);
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

下面的示例演示了文件 *function.js* 中的 RabbitMQ 输出绑定，以及使用绑定的 [c # 脚本函数](functions-reference-csharp.md) 。 函数从 HTTP 触发器读取消息，并将其输出到 RabbitMQ 队列。

下面是 function.json 文件中的绑定数据：

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "connectionStringAppSetting",
            "direction": "out"
        }
    ]
}
```

C# 脚本代码如下所示：

```csx
using System;
using Microsoft.Extensions.Logging;

public static void Run(string input, out string outputMessage, ILogger log)
{
    log.LogInformation(input);
    outputMessage = input;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下面的示例演示了文件 *function.js* 中的 RabbitMQ 输出绑定，以及使用绑定的 [JavaScript 函数](functions-reference-node.md) 。 函数从 HTTP 触发器读取消息，并将其输出到 RabbitMQ 队列。

下面是 function.json 文件中的绑定数据：

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "connectionStringAppSetting",
            "direction": "out"
        }
    ]
}
```

下面是 JavaScript 代码：

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下面的示例演示了文件 *function.js* 中的 RabbitMQ 输出绑定，以及使用绑定的 Python 函数。 函数从 HTTP 触发器读取消息，并将其输出到 RabbitMQ 队列。

下面是 function.json 文件中的绑定数据：

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
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "connectionStringAppSetting",
            "direction": "out"
        }
    ]
}
```

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:
    input_msg = req.params.get('message')
    msg.set(input_msg)
    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

下面的示例演示一个 Java 函数，该函数在 TimerTrigger 每5分钟触发一次时将消息发送到 RabbitMQ 队列。

```java
@FunctionName("RabbitMQOutputExample")
public void run(
@TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
@RabbitMQOutput(connectionStringSetting = "rabbitMQ", queueName = "hello") OutputBinding<String> output,
final ExecutionContext context) {
    output.setValue("Some string");
}
```

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

在 [c # 类库](functions-dotnet-class-library.md)中，使用 [RabbitMQAttribute](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/RabbitMQAttribute.cs)。

下面是 `RabbitMQAttribute` 方法签名中的特性：

```csharp
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("SourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("DestinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    ...
}
```

有关完整示例，请参阅 c # [示例](#example)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

使用 `RabbitMQOutput` 批注，可以创建在发送 RabbitMQ 消息时运行的函数。 可用的配置选项包括队列名称和连接字符串名称。 有关其他参数的详细信息，请访问 [RabbitMQOutput Java 注释](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQOutput.java)。

有关更多详细信息，请参阅输出绑定 [示例](#example) 。

---

## <a name="configuration"></a>配置

下表解释了在 function.json  文件和 `RabbitMQ` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**type** | 不适用 | 必须设置为 "RabbitMQ"。|
|**direction** | 不适用 | 必须设置为“out”。 |
|**name** | 不适用 | 表示函数代码中的队列的变量的名称。 |
|**queueName**|**QueueName**| 要向其发送消息的队列的名称。 |
|**段**|**HostName**|如果使用 ConnectStringSetting，则 (忽略)  <br>队列的主机名 (Ex： 10.26.45.210) |
|**userName**|**UserName**|如果使用 ConnectionStringSetting，则 (忽略)  <br>应用设置的名称，该设置包含用于访问队列的用户名。 例如： UserNameSetting： "< UserNameFromSettings >"|
|**password**|**密码**|如果使用 ConnectionStringSetting，则 (忽略)  <br>应用设置的名称，该设置包含用于访问队列的密码。 例如： UserNameSetting： "< UserNameFromSettings >"|
|**connectionStringSetting**|**ConnectionStringSetting**|包含 RabbitMQ 消息队列连接字符串的应用设置的名称。 请注意，如果直接指定连接字符串而不是通过 local.settings.json 中的应用设置，则触发器将不起作用。  (Ex： In *function.json*： connectionStringSetting： "rabbitMQConnection" <br> 在 *local.settings.js*： "rabbitMQConnection"： "< ActualConnectionstring >" ) |
|**port**|**端口**|如果使用 ConnectionStringSetting，则 (忽略) 获取或设置所使用的端口。 默认值为 0。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

# <a name="c"></a>[C#](#tab/csharp)

对输出绑定使用以下参数类型：

* `byte[]` - 如果函数退出时参数值为 null，Functions 不创建消息。
* `string` - 如果函数退出时参数值为 null，Functions 不创建消息。
* `POCO` -如果参数值未格式化为 c # 对象，则会收到错误。 有关完整示例，请参阅 c # [示例](#example)。

使用 C# 函数时：

* 异步函数需要返回值或 `IAsyncCollector` 而不是 `out` 参数。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

对输出绑定使用以下参数类型：

* `byte[]` - 如果函数退出时参数值为 null，Functions 不创建消息。
* `string` - 如果函数退出时参数值为 null，Functions 不创建消息。
* `POCO` -如果参数值未格式化为 c # 对象，则会收到错误。 有关完整示例，请参阅 c # 脚本 [示例](#example)。

使用 c # 脚本函数时：

* 异步函数需要返回值或 `IAsyncCollector` 而不是 `out` 参数。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

队列消息可通过上下文绑定获得。<NAME> 其中 <NAME> 与 function.js上的中定义的名称匹配。 如果有效负载为 JSON，该值将反序列化为对象。

# <a name="python"></a>[Python](#tab/python)

请参阅 Python [示例](#example)。

# <a name="java"></a>[Java](#tab/java)

对输出绑定使用以下参数类型：

* `byte[]` - 如果函数退出时参数值为 null，Functions 不创建消息。
* `string` - 如果函数退出时参数值为 null，Functions 不创建消息。
* `POJO` -如果参数值未设置为 Java 对象的格式，则会收到错误。

---

## <a name="next-steps"></a>后续步骤

- [ (触发器创建 RabbitMQ 消息时运行函数) ](./functions-bindings-rabbitmq-trigger.md)
