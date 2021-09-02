---
title: 服务总线死信队列 | Microsoft 文档
description: 介绍 Azure 服务总线中的死信队列。 服务总线队列和主题订阅提供一个名为死信队列的辅助子队列。
ms.topic: article
ms.date: 08/30/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: b64500b6fefd8da7fe2b6dda4719e7ed6ae81b94
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258342"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>服务总线死信队列概述

Azure 服务总线队列和主题订阅提供一个名为“死信队列”(DLQ) 的辅助子队列。 死信队列不需要显式创建，并且不能删除或独立于主实体进行管理。

本文介绍服务总线中的死信队列。 GitHub 上的[死信队列示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue)对很多讨论进行了说明。
 
## <a name="the-dead-letter-queue"></a>死信队列

死信队列的用途是存放无法传递给任何接收方的消息或无法处理的消息。 然后，可从 DLQ 中删除和检查这些消息。 应用程序可能会在操作员的帮助下，更正问题并重新提交消息，记录出错的实际情况和执行更正操作。 

从 API 和协议的角度看，DLQ 与任何其他队列都极其相似，不同的是，消息只能通过父实体的死信操作提交。 此外，无法查看生存时间，而且不能将 DLQ 中的消息设为死信。 死信队列完全支持扫视锁定传递和事务性操作。

DLQ 不会自动执行清理操作。 消息将保留在 DLQ 中，直到从 DLQ 中显式检索它们并完成死信消息为止。


## <a name="dlq-message-count"></a>DLQ 消息计数
无法在主题级别获取死信队列中的消息计数。 这是因为消息不会驻留在主题级别，除非服务总线引发内部错误。 相反，当发送方将消息发送给主题时，消息会在毫秒内转发给主题的订阅，因此消息不再驻留于主题级别。 所以，你可以在与主题的订阅相关联的 DLQ 中查看消息。 在以下示例中，Service Bus Explorer 显示订阅“test1”的 DLQ 中目前有 62 条消息。 

![DLQ 消息计数](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

还可以通过使用 Azure CLI 命令：[`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) 来获取 DLQ 消息的计数。 

## <a name="moving-messages-to-the-dlq"></a>将消息移到 DLQ
服务总线中有几个活动会导致从消息引擎本身将消息推送到 DLQ。 应用程序也可以显式将消息移到 DLQ。 以下两个属性（死信原因和死信说明）将添加到死信消息中。 应用程序可以为死信原因属性定义其自己的代码，但系统会设置以下值。

| 死信原因 | 死信错误说明 |
| --- | --- |
|HeaderSizeExceeded |已超过此流的大小配额。 |
|TTLExpiredException |消息过期并已设为死信。 有关详细信息，请参阅[生存时间](#time-to-live)部分。 |
|会话 ID 为 null。 |启用会话的实体不允许使用会话标识符为 null 的消息。 |
|MaxTransferHopCountExceeded | 在队列之间转发时允许的最大跃点数。 值设置为 4。 |
| MaxDeliveryCountExceededExceptionMessage | 在最大传送尝试次数以后，仍然无法使用消息。 有关详细信息，请参阅[最大传送数](#maximum-delivery-count)部分。 |

## <a name="maximum-delivery-count"></a>最大传送数
为服务总线队列和订阅传送消息的尝试次数是有限制的。 默认值为 10。 只要消息在 peek-lock 下传递，但已显式放弃或锁已过期，则消息的传送数就会递增。 如果传送数超出限制，会将消息移至 DLQ。 DLQ 中消息的死信原因设置为：MaxDeliveryCountExceeded。 无法禁止此行为，但可将最大传送数设置为较大的数。

## <a name="time-to-live"></a>生存时间
在队列或订阅上启用死信时，所有过期的消息都将移动到 DLQ。 死信原因代码设置为：TTLExpiredException。

延迟消息在过期后也不会被清除和移动到死信队列。 此行为是设计使然。

## <a name="errors-while-processing-subscription-rules"></a>处理订阅规则时的错误
如果对筛选器评估异常启用死信，会在 DLQ 中捕获执行订阅的 SQL 筛选器规则时出现的任何错误以及有问题的消息。 请勿在部分消息类型没有订阅服务器的生产环境中使用此选项。

## <a name="application-level-dead-lettering"></a>应用程序级死信
除了系统提供的死信功能外，应用程序也可以使用 DLQ 显式拒绝无法接受的消息。 这可能包括由于任何类型的系统问题而导致无法正确处理的消息、存储格式错误的有效负载的消息，或在使用某种消息级安全方案时未通过身份验证的消息。

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>ForwardTo 或 SendVia 方案中的死信
在下列情况下，消息将发送到传输死信队列：

- 消息传递 4 个以上[链接在一起](service-bus-auto-forwarding.md)的队列或主题。
- 目标队列或主题被禁用或删除。
- 目标队列或主题超出最大实体大小。

## <a name="path-to-the-dead-letter-queue"></a>死信队列的路径
可以通过使用以下语法来访问死信队列：

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>后续步骤
请参阅[为队列或订阅启用死信](enable-dead-letter.md)，了解配置邮件到期时的死信设置的不同方式。
