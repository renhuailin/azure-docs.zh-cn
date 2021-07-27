---
title: Azure Functions 的 Azure 服务总线触发器
description: 了解如何在创建 Azure 服务总线消息时运行 Azure Functions。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: d6685d86cb9c807db130b10a9573c3ca44ec911d
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108763787"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure Functions 的 Azure 服务总线触发器

使用服务总线触发器响应来自服务总线队列或主题的消息。 从扩展版本 3.1.0 开始，可以在启用会话的队列或主题上触发。

有关设置和配置详细信息，请参阅[概述](functions-bindings-service-bus.md)。

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示了读取[消息元数据](#message-metadata)并记录服务总线队列消息的 [C# 函数](functions-dotnet-class-library.md)：

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下示例演示 *function.json* 文件中的一个服务总线触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 此函数将读取[消息元数据](#message-metadata)并记录服务总线队列消息。

下面是 function.json 文件中的绑定数据：

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

C# 脚本代码如下所示：

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

# <a name="java"></a>[Java](#tab/java)

以下 Java 函数使用 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中的 `@ServiceBusQueueTrigger` 注释来说明服务总线队列触发器的配置。 此函数获取放置在队列上的消息，然后将其添加到日志。

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

将消息添加到服务总线主题时，也可触发 Java 函数。 以下示例使用 `@ServiceBusTopicTrigger` 注释来说明触发器配置。

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个服务总线触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 此函数将读取[消息元数据](#message-metadata)并记录服务总线队列消息。

下面是 function.json 文件中的绑定数据：

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

JavaScript 脚本代码如下所示：

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

以下示例演示了 function.json 文件中的服务总线触发器绑定以及使用该绑定的 [PowerShell 函数](functions-reference-powershell.md)。 

下面是 function.json 文件中的绑定数据：

```json
{
  "bindings": [
    {
      "name": "mySbMsg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "topicName": "mytopic",
      "subscriptionName": "mysubscription",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

下面是发送服务总线消息时运行的函数。

```powershell
param([string] $mySbMsg, $TriggerMetadata)

Write-Host "PowerShell ServiceBus queue trigger function processed message: $mySbMsg"
```

# <a name="python"></a>[Python](#tab/python)

下面的示例演示如何通过触发器读取服务总线队列消息。

服务总线绑定在 function.json 中定义，其中“类型”设置为 `serviceBusTrigger` 。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

\_init\_.py 中的代码将参数声明为 `func.ServiceBusMessage`，以允许你在函数中读取队列消息。

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
        'metadata' : msg.metadata
    })

    logging.info(result)
```

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，请使用以下属性来配置服务总线触发器：

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  该特性的构造函数采用队列或者主题和订阅的名称。 在 Azure Functions 版本 1.x 中，还可以指定连接的访问权限。 如果未指定访问权限，则默认为 `Manage`。 有关详细信息，请参阅[触发器 - 配置](#configuration)部分。

  下面是一个示例，演示了用于字符串参数的属性：

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  由于未定义 `Connection` 属性，因此 Azure Functions 将查找名为 `AzureWebJobsServiceBus` 的应用设置，这是服务总线连接字符串的默认名称。 还可以设置 `Connection` 属性来指定应用程序设置（其中包含要使用的服务总线连接字符串）的名称，如以下示例中所示：

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  有关完整示例，请参阅[触发器 - 示例](#example)。

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  提供另一种方式来指定要使用的服务总线帐户。 构造函数采用包含服务总线连接字符串的应用设置的名称。 可以在参数、方法或类级别应用该特性。 以下示例演示类级别和方法级别：

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

要使用的服务总线帐户按以下顺序确定：

* `ServiceBusTrigger` 特性的 `Connection` 属性。
* 作为 `ServiceBusTrigger` 特性应用到同一参数的 `ServiceBusAccount` 特性。
* 应用到函数的 `ServiceBusAccount` 特性。
* 应用到类的 `ServiceBusAccount` 特性。
* “AzureWebJobsServiceBus”应用设置。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="java"></a>[Java](#tab/java)

使用 `ServiceBusQueueTrigger` 注释可以创建在创建服务总线队列消息时要运行的函数。 可用的配置选项包括队列名称和连接字符串名称。

使用 `ServiceBusTopicTrigger` 注释可以指定主题和订阅，以便以触发函数的数据为目标。

有关更多详细信息，请参阅触发器[示例](#example)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

---

## <a name="configuration"></a>配置

下表解释了在 function.json  文件和 `ServiceBusTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为“serviceBusTrigger”。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为“in”。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 不适用 | 变量的名称，表示函数代码中的队列或主题消息。 |
|**queueName**|**QueueName**|要监视的队列的名称。  仅在监视队列的情况下设置，不为主题设置。
|**topicName**|**TopicName**|要监视的主题的名称。 仅在监视主题的情况下设置，不为队列设置。|
|**subscriptionName**|**SubscriptionName**|要监视的订阅的名称。 仅在监视主题的情况下设置，不为队列设置。|
|连接|**Connection**|应用设置的名称，包含要用于此绑定的服务总线连接字符串。 如果应用设置名称以“AzureWebJobs”开头，则只能指定该名称的余下部分。 例如，如果将 `connection` 设为“MyServiceBus”，Functions 运行时会查找名为“AzureWebJobsMyServiceBus”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为“AzureWebJobsServiceBus”的应用设置中的默认服务总线连接字符串。<br><br>若要获取连接字符串，请执行[获取管理凭据](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)中显示的步骤。 必须是服务总线命名空间的连接字符串，不限于特定的队列或主题。 <br><br>如果使用 [5.x 版或更高版本的扩展](./functions-bindings-service-bus.md#service-bus-extension-5x-and-higher)，而不是使用连接字符串，则可以提供对用于定义连接的配置节的引用。 请参阅[连接](./functions-reference.md#connections)。|
|**accessRights**|**Access**|连接字符串的访问权限。 可用值为 `manage` 和 `listen`。 默认值是 `manage`，其指示 `connection` 具有“管理”权限。 如果使用不具有“管理”权限的连接字符串，请将 `accessRights` 设置为“listen”。 否则，Functions 运行时可能会在尝试执行需要管理权限的操作时失败。 在 Azure Functions 版本 2.x 及更高版本中，此属性不可用，因为最新版本的服务总线 SDK 不支持管理操作。|
|**isSessionsEnabled**|**IsSessionsEnabled**|如果连接到[会话感知](../service-bus-messaging/message-sessions.md)队列或订阅，则为 `true`。 否则为 `false`（默认值）。|
|**autoComplete**|**AutoComplete**|`true` 是触发器在处理后自动调用 complete，还是函数代码手动调用 complete。<br><br>仅在 C# 中支持将其设置为 `false`。<br><br>如果设置为 `true`，则触发器会在函数执行成功完成时自动完成该消息，否则会放弃该消息。<br><br>设置为 `false` 时，你负责调用 [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) 方法来完成、放弃消息或将消息放入死信队列。 如果引发了异常（并且未调用任何 `MessageReceiver` 方法），则锁仍然存在。 锁到期后，消息会重新排队，同时 `DeliveryCount` 会递增，并且锁会自动续订。<br><br>在非 C# 函数中，函数中的异常会导致运行时在后台调用 `abandonAsync`。 如果未发生异常，则在后台调用 `completeAsync`。 此属性仅在 Azure Functions 2.x 和更高版本中可用。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

# <a name="c"></a>[C#](#tab/csharp)

以下参数类型可用于队列或主题消息：

* `string` - 如果消息是文本。
* `byte[]` - 适用于二进制数据。
* 自定义类型 - 如果消息包含 JSON，Azure Functions 会尝试反序列化 JSON 数据。
* `BrokeredMessage` - 提供带 [BrokeredMessage.GetBody\<T>()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) 方法的反序列化消息。
* [`MessageReceiver`](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) - 用于接收和确认来自消息容器的消息（当 [`autoComplete`](functions-bindings-service-bus.md#hostjson-settings) 设置为 `false` 时为必需）

这些参数类型适用于 Azure Functions 版本 1.x；对于 2.x 及更高版本，请使用 [`Message`](/dotnet/api/microsoft.azure.servicebus.message) 而非 `BrokeredMessage`。

### <a name="additional-types"></a>其他类型 
如果应用使用 5.0.0 或更高版本的服务总线扩展，那么这些应用使用 [Azure.Messaging.ServiceBus](/dotnet/api/azure.messaging.servicebus.servicebusreceivedmessage) 中的 `ServiceBusReceivedMessage` 类型，而不是 [Microsoft.Azure.ServiceBus](/dotnet/api/microsoft.azure.servicebus.message) 命名空间中的对应类型。 此版本为了支持以下类型，删除了对旧的 `Message` 类型的支持：

- [ServiceBusReceivedMessage](/dotnet/api/azure.messaging.servicebus.servicebusreceivedmessage)

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下参数类型可用于队列或主题消息：

* `string` - 如果消息是文本。
* `byte[]` - 适用于二进制数据。
* 自定义类型 - 如果消息包含 JSON，Azure Functions 会尝试反序列化 JSON 数据。
* `BrokeredMessage` - 提供带 [BrokeredMessage.GetBody\<T>()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) 方法的反序列化消息。

这些参数仅适用于 Azure Functions 版本 1.x；对于 2.x 及更高版本，请使用 [`Message`](/dotnet/api/microsoft.azure.servicebus.message) 而非 `BrokeredMessage`。

### <a name="additional-types"></a>其他类型 
如果应用使用 5.0.0 或更高版本的服务总线扩展，那么这些应用使用 [Azure.Messaging.ServiceBus](/dotnet/api/azure.messaging.servicebus.servicebusreceivedmessage) 中的 `ServiceBusReceivedMessage` 类型，而不是 [Microsoft.Azure.ServiceBus](/dotnet/api/microsoft.azure.servicebus.message) 命名空间中的对应类型。 此版本为了支持以下类型，删除了对旧的 `Message` 类型的支持：

- [ServiceBusReceivedMessage](/dotnet/api/azure.messaging.servicebus.servicebusreceivedmessage)

### <a name="additional-types"></a>其他类型 
如果应用使用 5.0.0 或更高版本的服务总线扩展，那么这些应用使用 [Azure.Messaging.ServiceBus](/dotnet/api/azure.messaging.servicebus.servicebusreceivedmessage) 中的 `ServiceBusReceivedMessage` 类型，而不是 [Microsoft.Azure.ServiceBus](/dotnet/api/microsoft.azure.servicebus.message) 命名空间中的对应类型。 此版本为了支持以下类型，删除了对旧的 `Message` 类型的支持：

- [ServiceBusReceivedMessage](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="java"></a>[Java](#tab/java)

可以通过 `ServiceBusQueueMessage` 或 `ServiceBusTopicMessage` 参数获取传入的服务总线消息。

[参阅示例了解详细信息](#example)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<name from function.json>` 访问队列或主题消息。 服务总线消息作为字符串或 JSON 对象传递到函数中。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

可以通过在 function.json 文件的名称属性中配置的参数来使用服务总线实例。

# <a name="python"></a>[Python](#tab/python)

队列消息可通过类型为 `func.ServiceBusMessage` 的参数提供给函数。 服务总线消息作为字符串或 JSON 对象传递到函数中。

---

## <a name="poison-messages"></a>有害消息

无法在 Azure Functions 中控制或配置有害消息处理。 服务总线处理有害消息本身。

## <a name="peeklock-behavior"></a>PeekLock 行为

Functions 运行时以 [PeekLock 模式](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)接收消息。 如果函数成功完成，则对此消息调用 `Complete`；如果函数失败，则调用 `Abandon`。 如果函数的运行时间长于 `PeekLock` 超时时间，则只要该函数正在运行，就会自动续订锁定。

`maxAutoRenewDuration` 可在 *host.json* 中配置，它映射到 [OnMessageOptions.MaxAutoRenewDuration](/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration)。 根据服务总线文档，此设置所允许的最长时间为 5 分钟，而你可以将 Functions 时间限制从默认值 5 分钟增加到 10 分钟。 对于服务总线函数，你不会那么做，因为会超出服务总线续订限制。

## <a name="message-metadata"></a>消息元数据

服务总线触发器提供了几个[元数据属性](./functions-bindings-expressions-patterns.md#trigger-metadata)。 这些属性可在其他绑定中用作绑定表达式的一部分，或者用作代码中的参数。 以下属性是 [Message](/dotnet/api/microsoft.azure.servicebus.message) 类的成员。

|属性|类型|说明|
|--------|----|-----------|
|`ContentType`|`string`|发送方和接收方用于实现应用程序特定逻辑的内容类型标识符。|
|`CorrelationId`|`string`|相关 ID。|
|`DeadLetterSource`|`string`|死信源。|
|`DeliveryCount`|`Int32`|传递次数。|
|`EnqueuedTimeUtc`|`DateTime`|排队时间 (UTC)。|
|`ExpiresAtUtc`|`DateTime`|到期时间 (UTC)。|
|`Label`|`string`|应用程序特定的标签。|
|`MessageId`|`string`|服务总线可用于标识重复消息的用户定义值（如果启用）。|
|`MessageReceiver`|`MessageReceiver`|服务总线消息接收器。 可以用于放弃、完成消息或将消息放入死信队列。|
|`MessageSession`|`MessageSession`|专用于已启用会话的队列和主题的消息接收器。|
|`ReplyTo`|`string`|对队列地址的回复。|
|`SequenceNumber`|`long`|服务总线分配给消息的唯一编号。|
|`To`|`string`|发送到地址。|
|`UserProperties`|`IDictionary<string, object>`|由发送方设置的属性。 （对于扩展版本 5.x 及更高版本，不支持此功能，请使用 `ApplicationProperties`。）|

请参阅在本文的前面部分使用这些属性的[代码示例](#example)。

### <a name="additional-message-metadata"></a>其他消息元数据

使用扩展版本 5.0.0 或更高版本的应用支持以下元数据属性。 这些属性是 [ServiceBusReceivedMessage](/dotnet/api/azure.messaging.servicebus.servicebusreceivedmessage) 类的成员。

|属性|类型|说明|
|--------|----|-----------|
|`ApplicationProperties`|`ApplicationProperties`|由发送方设置的属性。 使用此属性替代 `UserProperties` 元数据属性。|
|`Subject`|`string`|特定于应用程序的标签，可用于替代 `Label` 元数据属性。|
|`MessageActions`|`ServiceBusMessageActions`|可对 `ServiceBusReceivedMessage` 执行的一组操作。 此属性可用于替代 `MessageReceiver` 元数据属性。
|`SessionActions`|`ServiceBusSessionMessageActions`|可对会话和 `ServiceBusReceivedMessage` 执行的一组操作。 此属性可用于替代 `MessageSession` 元数据属性。|

## <a name="next-steps"></a>后续步骤

- [从 Azure Functions 发送 Azure 服务总线消息（输出绑定）](./functions-bindings-service-bus-output.md)
