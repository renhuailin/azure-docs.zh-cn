---
title: 传递和重试 - Azure 事件网格 IoT Edge |Microsoft Docs
description: IoT Edge 事件网格中的传递和重试。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: aa0b3a05fb26f6be951b697145d7b22e03b7792d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171612"
---
# <a name="delivery-and-retry"></a>传递和重试

事件网格提供持久传送。 它会尝试为每个匹配的订阅至少立即传递每个消息一次。 如果订阅方的终结点没有确认收到事件或有故障发生，事件网格会根据固定的“重试计划”和“重试策略”重试传递 。  默认情况下，事件网格模块一次向一个订阅方传递一个事件。 但有效负载是一个包含单个事件的数组。 通过启用输出批处理功能，可以让此模块一次传递多个事件。 有关此功能的详细信息，请参阅[输出批处理](delivery-output-batching.md)。  

> [!IMPORTANT]
>此模块不为事件数据提供暂留支持。 也就是说，如果重新部署或重新启动事件网格模块，你会丢失尚未传递的所有事件。

## <a name="retry-schedule"></a>重试计划

传递消息后，事件网格最多会等待 60 秒，以获得响应。 如果订阅方的终结点未确认响应，则该消息将在某一个回退队列中排队，以便随后重试。

有两个预配置的回退队列，它们将确定重试尝试基于哪个计划执行。 它们分别是：

| 计划 | 说明 |
| ---------| ------------ |
| 1 分钟 | 最终进入此队列的消息每分钟尝试传递一次。
| 10 分钟 | 最终进入此队列的消息每 10 分钟尝试传递一次。

### <a name="how-it-works"></a>工作原理

1. 消息到达事件网格模块。 模块尝试立即传递此消息。
1. 如果传递失败，则此消息在 1 分钟队列中排队，并在一分钟后重试。
1. 如果传递继续失败，则此消息在 10 分钟队列中排队，并每 10 分钟重试一次。
1. 模块尝试传递，直至成功或达到重试策略限制为止。

## <a name="retry-policy-limits"></a>重试策略限制

可以通过两种配置来确定重试策略。 它们分别是：

* 最大尝试次数
* 事件生存时间 (TTL)

如果事件达到任一重试策略限制，模块会将其删除。 “重试计划”部分介绍了重试计划本身。 可以为所有订阅方配置这些限制，也可以分别为每个订阅进行配置。 以下部分进一步详细介绍了每种限制。

## <a name="configuring-defaults-for-all-subscribers"></a>为所有订阅方配置默认值

在部署事件网格时，可以配置 `brokers__defaultMaxDeliveryAttempts` 和 `broker__defaultEventTimeToLiveInSeconds` 两种属性，来控制所有订阅方的重试策略默认值。

| 属性名称 | 说明 |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 尝试传递事件的最大次数。 默认值：30。
| `broker__defaultEventTimeToLiveInSeconds` | 事件 TTL（以秒为单位），在这段时间之后，如果事件未传递，则将被删除。 默认值：7200 秒

## <a name="configuring-defaults-per-subscriber"></a>为每个订阅方配置默认值

你还可以分别为每个订阅指定重试策略限制。
有关如何为每个订阅方配置默认值的信息，请参阅 [API 文档](api.md)。 订阅级别默认值会重写模块级别配置。

## <a name="examples"></a>示例

下面的示例将事件网格模块中的重试策略设置为 maxNumberOfAttempts = 3，事件 TTL 为 30 分钟

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

下面的示例将 Webhook 订阅设置为 maxNumberOfAttempts = 3，事件 TTL 为 30 分钟

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
