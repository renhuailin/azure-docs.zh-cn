---
title: 将来自 Azure 事件网格的事件接收到 HTTP 终结点中
description: 描述如何验证 HTTP 终结点，以及随后如何接收和反序列化来自 Azure 事件网格的事件
ms.topic: conceptual
ms.date: 07/16/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: f655982203c81ef6ff32a01566e37c346cf78138
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730272"
---
# <a name="receive-events-to-an-http-endpoint"></a>将事件接收到 HTTP 终结点

本文介绍如何[验证 HTTP 终结点](webhook-event-delivery.md)以接收来自事件订阅的事件并随后接收和反序列化事件。 本文使用 Azure 函数进行演示，但无论应用程序托管在何处，这些概念都适用。

> [!NOTE]
> 建议在通过事件网格触发 Azure 函数时使用[事件网格触发器](../azure-functions/functions-bindings-event-grid.md)。 它在事件网格和 Azure Functions 之间提供了更简单、更快速的集成。 但是请注意，Azure Functions 的事件网格触发器不支持托管代码需要控制返回给事件网格的 HTTP 状态代码的场景。 鉴于此限制，例如，在 Azure Function 上运行的代码将无法返回 5XX 错误，以通过事件网格启动事件传送重试。

## <a name="prerequisites"></a>先决条件

需要包含 HTTP 触发函数的函数应用。

## <a name="add-dependencies"></a>添加依赖项

若要使用 .NET 进行开发，请向 `Azure.Messaging.EventGrid` [Nuget 包](https://www.nuget.org/packages/Azure.Messaging.EventGrid)的函数[添加依赖项](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies)。

可通过[发布 SDKs](./sdk-overview.md#data-plane-sdks) 引用将 SDK 用于其他语言。 这些包中有用于本机事件类型（如 `EventGridEvent`、`StorageBlobCreatedEventData` 和 `EventHubCaptureFileCreatedEventData`）的模型。

## <a name="endpoint-validation"></a>终结点验证

首先需要处理 `Microsoft.EventGrid.SubscriptionValidationEvent` 事件。 每次有人订阅某个事件时，事件网格都会在数据有效负载中通过 `validationCode` 向终结点发送一个验证事件。 终结点必须回显到响应正文中才能[证明此终结点有效且被你所拥有](webhook-event-delivery.md)。 如果你使用的是[事件网格触发器](../azure-functions/functions-bindings-event-grid.md)（而不是 Webhook 触发函数），系统会为你执行终结点验证。 如果使用第三方 API 服务（例如 [Zapier](https://zapier.com/home) 或 [IFTTT](https://ifttt.com/)），可能无法以编程方式回显验证码。 对于这些服务，可以使用订阅验证事件中发送的验证 URL 手动验证订阅。 在 `validationUrl` 属性中复制该 URL 并通过 REST 客户端或 Web 浏览器发送 GET 请求。

在 C# 中，`ParseMany()` 方法用于将包含一个或多个事件的 `BinaryData` 实例反序列化为 `EventGridEvent` 数组。 如果你提前知道只反序列化单个事件，则可以改用 `Parse` 方法。

若要以编程方式回显验证码，请运行下面的代码。

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>测试验证响应

通过将样本事件粘贴到函数的测试字段，测试验证响应函数：

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

单击“运行”时，输出应为“200 确定”且正文中显示 `{"validationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}`：

:::image type="content" source="./media/receive-events/validation-request.png" alt-text="验证请求":::

:::image type="content" source="./media/receive-events/validation-output.png" alt-text="验证输出":::

## <a name="handle-blob-storage-events"></a>处理 Blob 存储事件

现在，让我们扩展函数来处理 `Microsoft.Storage.BlobCreated` 系统事件：

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                    // Handle the storage blob created event
                    else if (eventData is StorageBlobCreatedEventData storageBlobCreatedEventData)
                    {
                        log.LogInformation($"Got BlobCreated event data, blob URI {storageBlobCreatedEventData.Url}");
                    }
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>测试能否处理“已处理 Blob”事件

通过将 [Blob 存储事件](./event-schema-blob-storage.md#example-event)放到测试字段中并运行以下项来测试函数的新功能：

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

函数日志中应该会显示 Blob URL 输出：

![输出日志](./media/receive-events/blob-event-response.png)

还可以通过以下方式进行测试：创建一个 Blob 存储帐户或通用版 V2 (GPv2) 存储帐户，[添加事件订阅](../storage/blobs/storage-blob-event-quickstart.md)，然后将终结点设置为函数 URL：

![函数 URL](./media/receive-events/function-url.png)

## <a name="handle-custom-events&quot;></a>处理自定义事件

最后，再次扩展函数，使其还能够处理自定义事件。 

添加对事件 `Contoso.Items.ItemReceived` 的检查。 最终代码应如下所示：

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName(&quot;Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                    // Handle the storage blob created event
                    else if (eventData is StorageBlobCreatedEventData storageBlobCreatedEventData)
                    {
                        log.LogInformation($"Got BlobCreated event data, blob URI {storageBlobCreatedEventData.Url}");
                    }
                }
                // Handle the custom contoso event
                else if (eventGridEvent.EventType == "Contoso.Items.ItemReceived")
                {
                    var contosoEventData = eventGridEvent.Data.ToObjectFromJson<ContosoItemReceivedEventData>();
                    log.LogInformation($"Got ContosoItemReceived event data, item SKU {contosoEventData.ItemSku}");
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>测试能否处理自定义事件

最后，测试函数现在能否处理自定义事件类型：

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

还可实时测试此功能，方式是[在门户中通过 CURL 发送自定义事件](./custom-event-quickstart-portal.md)，或者使用任何可通过 POST 发送到终结点（如 [Postman](https://www.getpostman.com/)）的服务或应用程序[发布到自定义主题](./post-to-custom-topic.md)。 使用终结点集作为函数 URL，创建自定义主题和事件订阅。

[!INCLUDE [event-grid-message-headers](./includes/event-grid-message-headers.md)]

## <a name="next-steps"></a>后续步骤

* 浏览 [Azure 事件网格管理和发布 SDK](./sdk-overview.md)
* 了解如何[发布到自定义主题](./post-to-custom-topic.md)
* 请在实践中运用其中一个详尽“事件网格和函数”教程，例如[对上传到 Blob 存储的图像的大小进行调整](resize-images-on-storage-blob-upload-event.md)
