---
title: 在 CloudEvents 架构中将 Azure 事件网格与事件配合使用
description: 说明如何对 Azure 事件网格中的事件使用 CloudEvents 架构。 该服务在 CloudEvents 的 JSON 实现中支持事件。
ms.topic: conceptual
ms.date: 07/22/2021
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 0fcf4a5cd629401c44a208d6d697a04855b4a902
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469478"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>将 CloudEvents v1.0 架构与事件网格配合使用
除了采用[默认事件架构](event-schema.md)的事件，Azure 事件网格本身还支持采用 [CloudEvents v1.0 的 JSON 架构](https://github.com/cloudevents/spec/blob/v1.0/json-format.md)和 [HTTP 协议绑定](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)的事件。 [CloudEvents](https://cloudevents.io/) 是一种用于描述事件数据的[开放规范](https://github.com/cloudevents/spec/blob/v1.0/spec.md)。

CloudEvents 提供的常用事件架构适合发布和使用基于云的事件，因此可简化互操作性。 可以通过此架构使用统一的工具、以标准方式路由和处理事件，以及以通用方式反序列化外部事件架构。 使用通用架构可以更轻松地跨平台集成工作。

CloudEvents 是由包括 Microsoft 在内的多个[协作者](https://github.com/cloudevents/spec/blob/master/community/contributors.md)通过 [Cloud Native Computing Foundation](https://www.cncf.io/) 构建的。 它目前的发布版本为 1.0。

本文介绍如何将 CloudEvents 架构与事件网格配合使用。

## <a name="cloudevent-schema"></a>CloudEvent 架构

下面是一个采用 CloudEvents 格式的 Azure Blob 存储事件示例：

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

有关可用字段及其类型和定义的详细说明，请参阅 [CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)。

在 CloudEvents 架构和事件网格架构中传递的事件的标头值是相同的，但 `content-type` 除外。 对于 CloudEvents 架构，该标头值为 `"content-type":"application/cloudevents+json; charset=utf-8"`。 对于事件网格架构，该标头值为 `"content-type":"application/json; charset=utf-8"`。

## <a name="configure-event-grid-for-cloudevents"></a>为 CloudEvents 配置事件网格

可以将事件网格用于 CloudEvents 架构中的事件的输入和输出。 下表说明了可能的转换：

 事件网格资源 | 输入架构       | 传递架构
|---------------------|-------------------|---------------------
| 系统主题       | 事件网格架构 | 事件网格架构或 CloudEvents 架构
| 自定义主题/域 | 事件网格架构 | 事件网格架构或 CloudEvents 架构
| 自定义主题/域 | CloudEvents 架构 | CloudEvents 架构
| 自定义主题/域 | 自定义架构     | 自定义架构、事件网格架构或 CloudEvents 架构
| 合作伙伴主题       | CloudEvents 架构 | CloudEvents 架构

对于所有事件架构，事件网格都要求在发布到事件网格主题时以及在创建事件订阅时进行验证。

有关详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。

### <a name="input-schema"></a>输入架构

在创建自定义主题时为自定义主题设置输入架构。

对于 Azure CLI，请使用：

```azurecli-interactive
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

对于 PowerShell，请使用：

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>输出架构

在创建事件订阅时设置输出架构。

对于 Azure CLI，请使用：

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

对于 PowerShell，请使用：
```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 目前，在以 CloudEvents 架构传递事件时，无法为 Azure Functions 应用使用事件网格触发器。 使用 HTTP 触发器。 有关实现在 CloudEvents 架构中接收事件的 HTTP 触发器的示例，请参阅[将 CloudEvents 与 Azure Functions 配合使用](#azure-functions)。

## <a name="endpoint-validation-with-cloudevents-v10"></a>使用 CloudEvents v1.0 验证终结点

如果熟悉事件网格，你可能会了解用于防止滥用的终结点验证握手。 CloudEvents v1.0 使用 HTTP OPTIONS 方法来实现自己的[滥用防护语义](webhook-event-delivery.md)。 若要了解有关详细信息，请参阅[适用于事件传递的 HTTP 1.1 Webhook - 版本 1.0](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)。 使用 CloudEvents 架构进行输出时，事件网格将使用 CloudEvents v1.0 滥用防护取代事件网格验证事件机制。

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>与 Azure Functions 配合使用

[Azure Functions 事件网格绑定](../azure-functions/functions-bindings-event-grid.md)本身不支持 CloudEvents，因此，HTTP 触发的函数被用来读取 CloudEvents 消息。 使用 HTTP 触发器来读取 CloudEvents 时，必须编写代码来指定事件网格触发器自动执行的操作：

* 将验证响应发送到[订阅验证请求](../event-grid/webhook-event-delivery.md)
* 针对请求正文中包含的事件数组的每个元素调用该函数一次

有关用于在本地调用函数或者在 Azure 中运行函数的 URL 的信息，请参阅 [HTTP 触发器绑定参考文档](../azure-functions/functions-bindings-http-webhook.md)。

以下 HTTP 触发器的示例 C# 代码可模拟事件网格触发器的行为。 将此示例用于以 CloudEvents 架构传递的事件。

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == HttpMethod.Options)
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Headers.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request isn't for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

以下 HTTP 触发器的示例 JavaScript 代码可模拟事件网格触发器的行为。 将此示例用于以 CloudEvents 架构传递的事件。

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = {
            status: 200,
            headers: {
                'Webhook-Allowed-Origin': 'eventgrid.azure.net',
            },
         };
    }
    else
    {
        var message = req.body;
        
        // The request isn't for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>后续步骤

* 有关监视事件传送的信息，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 我们鼓励你测试、评论并[参与编写 CloudEvents](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md)。
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
