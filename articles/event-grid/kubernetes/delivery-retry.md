---
title: Kubernetes 上的 Azure 事件网格 - 事件传送和重试
description: 本文介绍具有 Azure Arc 的 Kubernetes 上的事件网格如何传送事件，以及如何处理未送达的消息。
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 6a37945461a61167e2fee7d7d3ef6a8fbccf3372
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122066754"
---
# <a name="event-grid-on-kubernetes---event-delivery-and-retry"></a>Kubernetes 上的事件网格 - 事件传送和重试
对于每个匹配的订阅，具有 Azure Arc 的 Kubernetes 上的事件网格会立即尝试传送每条消息至少一次。 如果未从订阅方获取成功 HTTP 200 响应，或者出现任何故障，Kubernetes 上的事件网格将按照固定的重试计划和重试策略重试传送消息。 

默认情况下，Kubernetes 上的事件网格一次向订阅方传送一个事件。 但是，传送请求的有效负载是包含单个事件的数组。 如果启用输出批处理功能，则它可以一次传送多个事件。 有关此功能的详细信息，请参阅[批量事件传送](batch-event-delivery.md)。

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

> [!NOTE]
> 在预览版中，Kubernetes 上的事件网格功能通过 API 版本 [2020-10-15-Preview](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update) 得到支持。 


## <a name="retry-schedule"></a>重试计划
传送事件后，Kubernetes 上的事件网格最多会等待 60 秒，以获得响应。 如果订阅方的终结点未发送成功响应（HTTP 200 等），则它会重试发送事件。 工作原理如下。 

1. 消息到达 Kubernetes 上的事件网格。 模块尝试立即传递此消息。
1. 如果传送失败，则此消息在 1 分钟队列中排队，并在一分钟后重试。
1. 如果传送继续失败，则此消息在 10 分钟队列中排队，并每 10 分钟重试一次。
1. 模块尝试传递，直至成功或达到重试策略限制为止。
 
## <a name="retry-policy"></a>重试策略
可以通过两种配置来确定重试策略。 它们是：

- 最大尝试次数
- 事件生存时间 (TTL)

如果事件达到任一重试策略限制，则会将其删除。 可以根据订阅来配置这些限制。 以下部分进一步详细介绍了每种限制。

### <a name="configuring-defaults-per-subscriber"></a>为每个订阅方配置默认值
你还可以分别为每个订阅指定重试策略限制。 有关为每个订阅方配置默认值的信息，请参阅 [API 文档](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update)。 订阅级别默认值将替代 Kubernetes 上的事件网格模块级别配置。

以下示例将设置一个 Webhook 订阅，其 `maxNumberOfAttempts` 设置为 3 且 `eventTimeToLiveInMinutes` 设置为 30 分钟。

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": " CloudEventSchemaV1_0"
   }
  },
  "retryPolicy": {
   "eventTimeToLiveInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
```

## <a name="next-steps"></a>后续步骤
若要了解 Azure Arc for Kubernetes 上的事件网格支持的目标和处理程序，请参阅 [Kubernetes 上的事件网格 - 事件处理程序](event-handlers.md)。
