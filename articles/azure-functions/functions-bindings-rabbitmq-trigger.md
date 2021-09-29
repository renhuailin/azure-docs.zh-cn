---
title: Azure Functions 的 RabbitMQ 触发器
description: 了解如何在创建 RabbitMQ 消息时运行 Azure Function。
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: f0cac775870345ae298672c2af5dad8277c592e6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639779"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>Azure Functions 的 RabbitMQ 触发器概述

> [!NOTE]
> 仅高级和专用计划完全支持 RabbitMQ 绑定。 不支持消耗。

使用 RabbitMQ 触发器响应来自 RabbitMQ 队列的消息。

有关设置和配置详细信息，请参阅[概述](functions-bindings-rabbitmq.md)。

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

以下示例显示了一个 [C# 函数](functions-dotnet-class-library.md)，该函数将 RabbitMQ 消息作为 [RabbitMQ 事件](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html)进行读取和记录：

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

以下示例演示如何将消息作为 POCO 读取。

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }

    public class RabbitMQTriggerCSharp{
        [FunctionName("RabbitMQTriggerCSharp")]
        public static void RabbitMQTrigger_BasicDeliverEventArgs(
            [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass pocObj,
            ILogger logger
            )
        {
            logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {pocObj}");
        }
    }
}
```

与 Json 对象一样，如果未将消息格式正确设置为 C# 对象，则会发生错误。 如果格式设置正确，则将其绑定到变量 pocObj，该变量可用于所需的任何用途。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下示例演示 function.json 文件中的一个 RabbitMQ 触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 此函数读取并记录 RabbitMQ 消息。

下面是 function.json 文件中的绑定数据：

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

C# 脚本代码如下所示：

```C#
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 function.json 文件中的一个 RabbitMQ 触发器绑定以及使用该绑定的 [JavaScript 脚本函数](functions-reference-node.md)。 此函数读取并记录 RabbitMQ 消息。

下面是 function.json 文件中的绑定数据：

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

JavaScript 脚本代码如下所示：

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

以下示例演示如何通过触发器读取 RabbitMQ 队列消息。

RabbitMQ 绑定在 function.json 中定义，其中“类型”设置为 `RabbitMQTrigger`。

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

以下 Java 函数使用 [Java RabbitMQ 类型](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq)中的 `@RabbitMQTrigger` 注释来说明 RabbitMQ 队列触发器的配置。 此函数获取放置在队列上的消息，然后将其添加到日志。

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs) 特性。

下面是某个方法签名中的 `RabbitMQTrigger` 特性：

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
{
    ...
}
```

有关完整示例，请参阅 C# [示例](#example)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

使用 `RabbitMQTrigger` 注释，你’可以创建在创建 RabbitMQ 消息时要运行的函数。 可用的配置选项包括队列名称和连接字符串名称。 有关其他参数详细信息，请访问 [RabbitMQTrigger Java 注释](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java)。

有关更多详细信息，请参阅触发器[示例](#example)。

---

## <a name="configuration"></a>配置

下表解释了在 function.json  文件和 `RabbitMQTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为“RabbitMQTrigger”。|
|**direction** | 不适用 | 必须设置为“in”。|
|name | 不适用 | 表示函数代码中的队列的变量的名称。 |
|**queueName**|**QueueName**| 从中接收消息的队列的名称。 |
|hostName|**HostName**|（如果使用 ConnectStringSetting，则忽略） <br>队列的主机名（例如：10.26.45.210）|
|**userNameSetting**|**UserNameSetting**|（如果使用 ConnectionStringSetting，则忽略） <br>应用设置的名称，该设置包含用于访问队列的用户名。 例如： UserNameSetting: "%< UserNameFromSettings >%"|
|**passwordSetting**|**PasswordSetting**|（如果使用 ConnectionStringSetting，则忽略） <br>应用设置的名称，该设置包含用于访问队列的密码。 例如： PasswordSetting: "%< PasswordFromSettings >%"|
|**connectionStringSetting**|**ConnectionStringSetting**|包含 RabbitMQ 消息队列连接字符串的应用设置的名称。 请注意，如果直接指定连接字符串，而不是通过 local.settings.json 中的应用设置进行指定，则触发器将不起作用。 （例如：在 function.json 中：connectionStringSetting: "rabbitMQConnection" <br> 在 local.settings.json 中："rabbitMQConnection" : "< ActualConnectionstring >"）|
|**port**|端口|（如果使用 ConnectionStringSetting，则忽略）获取或设置所使用的端口。 默认值为 0，该值指向 rabbitmq 客户端的默认端口设置：5672。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

# <a name="c"></a>[C#](#tab/csharp)

默认消息类型为 [RabbitMQ 事件](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html)，RabbitMQ 事件的 `Body` 属性可以读取为下面列出的类型：

* `An object serializable as JSON` - 消息以有效的 JSON 字符串形式传递。
* `string`
* `byte[]`
* `POCO` - 消息的格式设置为 C# 对象。 有关完整示例，请参阅 C# [示例](#example)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

默认消息类型为 [RabbitMQ 事件](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html)，RabbitMQ 事件的 `Body` 属性可以读取为下面列出的类型：

* `An object serializable as JSON` - 消息以有效的 JSON 字符串形式传递。
* `string`
* `byte[]`
* `POCO` - 消息的格式设置为 C# 对象。 有关完整示例，请参阅 C# 脚本[示例](#example)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

队列消息可通过 context.bindings 获得\<NAME\>。 其中 \<NAME\> 与 function.json 中定义的名称相匹配。 如果有效负载为 JSON，该值将反序列化为对象。

# <a name="python"></a>[Python](#tab/python)

请参阅 Python [示例](#example)。

# <a name="java"></a>[Java](#tab/java)

请参阅 Java [特性和注释](#attributes-and-annotations)。

---

## <a name="dead-letter-queues"></a>死信队列
死信队列和交换不能通过 RabbitMQ 触发器进行控制或配置。  为了使用死信队列，请在 RabbitMQ 中预先配置触发器使用的队列。 请参阅 [RabbitMQ 文档](https://www.rabbitmq.com/dlx.html)。

## <a name="hostjson-settings"></a>host.json 设置

本部分介绍版本 2.x 及更高版本中可用于此绑定的全局配置设置。 下面的示例 host.json 文件仅包含此绑定的设置。 有关全局配置设置的详细信息，请参阅 [Azure Functions 版本的 host.json 参考](functions-host-json.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------|
|prefetchCount|30|获取或设置消息接收方可以同时请求并缓存的消息数。|
|queueName|不适用| 从中接收消息的队列的名称。|
|connectionString|不适用|RabbitMQ 消息队列连接字符串。 请注意，连接字符串在此处直接指定，而不是通过应用设置指定。|
|port|0|（如果使用 connectionString，则忽略）获取或设置所使用的端口。 默认值为 0，该值指向 rabbitmq 客户端的默认端口设置：5672。|

## <a name="local-testing"></a>本地测试

> [!NOTE]
> connectionString 优先于“hostName”、“userName”和“password”。 如果这几项全部设置，则 connectionString 将替代另两项。

如果在没有连接字符串的情况下进行本地测试，则应在 host.json 的“rabbitMQ”部分中设置“hostName”设置以及“userName”和“password”（如果适用）：

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "userNameSetting",
            "password": "passwordSetting"
        }
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------|
|hostName|不适用|（如果使用 connectionString，则忽略） <br>队列的主机名（例如：10.26.45.210）|
|userName|不适用|（如果使用 connectionString，则忽略） <br>用于访问队列的名称 |
|password|不适用|（如果使用 connectionString，则忽略） <br>用于访问队列的密码|


## <a name="enable-runtime-scaling"></a>启用运行时缩放

为了使 RabbitMQ 触发器横向扩展到多个实例，必须启用“运行时缩放监视”设置。 

在门户中，可以在函数应用的“配置” > “函数运行时设置”下找到此设置 。

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

在 CLI 中，可以使用以下命令启用“运行时缩放监视”：

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

## <a name="monitoring-rabbitmq-endpoint"></a>监视 RabbitMQ 终结点
若要监视特定 RabbitMQ 终结点的队列和交换，请执行以下操作：

* 启用 [RabbitMQ 管理插件](https://www.rabbitmq.com/management.html)
* 浏览到 http://{node-hostname}:15672，然后用你的用户名和密码登录。

## <a name="next-steps"></a>后续步骤

- [从 Azure Functions 发送 RabbitMQ 消息（输出绑定）](./functions-bindings-rabbitmq-output.md)
