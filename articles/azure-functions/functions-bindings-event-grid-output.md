---
title: 适用于 Azure Functions 的 Azure 事件网格输出绑定
description: 了解如何在 Azure Functions 中发送事件网格事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 9ebf502fc2ae83651e8f69472b3b2042cef723d8
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110537237"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>适用于 Azure Functions 的 Azure 事件网格输出绑定

使用事件网格输出绑定将事件写入到自定义主题。 必须拥有有效的[自定义主题访问密钥](../event-grid/security-authenticate-publishing-clients.md)。

有关设置和配置的详细信息，请参阅[概述](./functions-bindings-event-grid.md)。

> [!NOTE]
> 事件网格输出绑定不支持共享访问签名（SAS 令牌）。 必须使用该主题的访问密钥。

> [!IMPORTANT]
> 事件网格输出绑定仅适用于 Functions 2.x 和更高版本。

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

### <a name="c-2x-and-higher"></a>C#（2.x 及更高版本）

以下示例演示了一个 [C# 函数](functions-dotnet-class-library.md)它使用方法返回值作为输出，将消息写入到事件网格自定义主题：

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

以下示例显示了如何使用 `IAsyncCollector` 接口来发送一批消息。

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

### <a name="version-3x-preview"></a>版本 3.x（预览版）

以下示例演示绑定到 `CloudEvent` 的 Functions 3.x [C# 函数](functions-dotnet-class-library.md)：

```cs
using System.Threading.Tasks;
using Azure.Messaging;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Extensions.Http;

namespace Azure.Extensions.WebJobs.Sample
{
    public static class CloudEventBindingFunction
    {
        [FunctionName("CloudEventBindingFunction")]
        public static async Task<IActionResult> RunAsync(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            [EventGrid(TopicEndpointUri = "EventGridEndpoint", TopicKeySetting = "EventGridKey")] IAsyncCollector<CloudEvent> eventCollector)
        {
            CloudEvent e = new CloudEvent("IncomingRequest", "IncomingRequest", await req.ReadAsStringAsync());
            await eventCollector.AddAsync(e);
            return new OkResult();
        }
    }
}
```

以下示例演示绑定到 `EventGridEvent` 的 Functions 3.x [C# 函数](functions-dotnet-class-library.md)：

```cs
using System.Threading.Tasks;
using Azure.Messaging.EventGrid;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;

namespace Azure.Extensions.WebJobs.Sample
{
    public static class EventGridEventBindingFunction
    {
        [FunctionName("EventGridEventBindingFunction")]
        public static async Task<IActionResult> RunAsync(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            [EventGrid(TopicEndpointUri = "EventGridEndpoint", TopicKeySetting = "EventGridKey")] IAsyncCollector<EventGridEvent> eventCollector)
        {
            EventGridEvent e = new EventGridEvent(await req.ReadAsStringAsync(), "IncomingRequest", "IncomingRequest", "1.0.0");
            await eventCollector.AddAsync(e);
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下示例显示了 *function.json* 文件中的事件网格输出绑定数据。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

以下 C# 脚本代码将创建一个事件：

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

以下 C# 脚本代码将创建多个事件：

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="java"></a>[Java](#tab/java)

事件网格输出绑定对于 Java 不可用。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例显示了 *function.json* 文件中的事件网格输出绑定数据。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

以下 JavaScript 代码将创建单个事件：

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

以下 JavaScript 代码将创建多个事件：

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

下面的示例演示如何配置函数以输出事件网格事件消息。 `type` 设置为 `eventGrid` 的部分配置建立事件网格输出绑定所需的值。

```powershell
{
  "bindings": [
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    },
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
    }
  ]
}
```

在函数中，请使用 `Push-OutputBinding` 通过事件网格输出绑定将事件发送到自定义主题。

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message

Push-OutputBinding -Name outputEvent -Value  @{
    id = "1"
    EventType = "testEvent"
    Subject = "testapp/testPublish"
    EventTime = "2020-08-27T21:03:07+00:00"
    Data = @{
        Message = $message
    }
    DataVersion = "1.0"
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

以下示例演示 function.json 文件中的一个触发器绑定以及使用该绑定的 [Python 函数](functions-reference-python.md)。 然后，它会将事件发送到自定义主题，如 `topicEndpointUri` 所指定。

下面是 function.json 文件中的绑定数据：

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

以下 Python 示例通过设置 `EventGridOutputEvent` 将事件发送到自定义主题：

```python
import logging
import azure.functions as func
import datetime

def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

对于 [C# 类库](functions-dotnet-class-library.md)，使用 [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) 特性。

该特性的构造函数可接受包含自定义主题名称的应用设置的名称，以及包含主题密钥的应用设置的名称。 有关这些设置的详细信息，请参阅[输出 - 配置](#configuration)。 下面是 `EventGrid` 特性的示例：

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

有关完整示例，请参阅[示例](#example)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="java"></a>[Java](#tab/java)

事件网格输出绑定对于 Java 不可用。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell 不支持特性。

# <a name="python"></a>[Python](#tab/python)

事件网格输出绑定对于 Python 不可用。

---

## <a name="configuration"></a>配置

下表解释了在 function.json  文件和 `EventGrid` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为“eventGrid”。 |
|direction | 不适用 | 必须设置为“out”。 在 Azure 门户中创建绑定时，会自动设置该参数。 |
|name | 不适用 | 函数代码中使用的表示事件的变量名称。 |
|**topicEndpointUri** |**TopicEndpointUri** | 包含自定义主题 URI 的应用设置的名称，例如 `MyTopicEndpointUri`。 |
|**topicKeySetting** |**TopicKeySetting** | 包含自定义主题访问密钥的应用设置的名称。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> 确保将 `TopicEndpointUri` 配置属性的值设置为包含自定义主题 URI 的应用设置的名称。 请勿直接在此属性中指定自定义主题的 URI。

## <a name="usage"></a>使用情况

# <a name="c"></a>[C#](#tab/csharp)

可以使用 `out EventGridEvent paramName` 等方法参数发送消息。 若要编写多条消息，可以使用 `ICollector<EventGridEvent>` 或 `IAsyncCollector<EventGridEvent>` 代替 `out EventGridEvent`。

### <a name="additional-types"></a>其他类型 
使用事件网格扩展 3.0.0 或更高版本的应用使用 [Azure.Messaging.EventGrid](/dotnet/api/azure.messaging.eventgrid.eventgridevent) 命名空间中的 `EventGridEvent` 类型。 此外，还可以绑定到 [Azure.Messaging](/dotnet/api/azure.messaging.cloudevent) 命名空间中的 `CloudEvent` 类型。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

可以使用 `out EventGridEvent paramName` 等方法参数发送消息。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 若要编写多条消息，可以使用 `ICollector<EventGridEvent>` 或 `IAsyncCollector<EventGridEvent>` 代替 `out EventGridEvent`。

### <a name="additional-types"></a>其他类型 
使用事件网格扩展 3.0.0 或更高版本的应用使用 [Azure.Messaging.EventGrid](/dotnet/api/azure.messaging.eventgrid.eventgridevent) 命名空间中的 `EventGridEvent` 类型。 此外，还可以绑定到 [Azure.Messaging](/dotnet/api/azure.messaging.cloudevent) 命名空间中的 `CloudEvent` 类型。

# <a name="java"></a>[Java](#tab/java)

事件网格输出绑定对于 Java 不可用。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<name>` 访问输出事件，其中 `<name>` 是在 *function.json* 的 `name` 属性中指定的值。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

通过使用 `Push-OutputBinding` cmdlet 访问输出事件，将事件发送到事件网格输出绑定。

# <a name="python"></a>[Python](#tab/python)

事件网格输出绑定对于 Python 不可用。

---

## <a name="next-steps"></a>后续步骤

* [调度事件网格事件](./functions-bindings-event-grid-trigger.md)
