---
title: Azure 服务总线消息序列化和时间戳 | Microsoft Docs
description: 本文介绍如何保留 Azure 服务总线消息的序列化和排序（使用时间戳）。
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 3d5300568232afae1238445113d60eda8cdb2f1b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497091"
---
# <a name="message-sequencing-and-timestamps"></a>消息序列化和时间戳

序列化和时间戳是所有服务总线实体上始终启用的两项功能，通过收到或检索到的消息的 `SequenceNumber` 和 `EnqueuedTimeUtc` 属性体现。

如果消息的绝对顺序至关重要，和/或使用者需要消息的可信唯一标识符，中转站会向消息分发相对于队列或主题的无间隔递增序列号。 对于已分区实体，序列号是相对于分区进行分发。

SequenceNumber 值是在中转站接受并存储消息时分配给消息的唯一 64 位整数，可用作内部标识符。 对于已分区实体，最前面的 16 位数反映的是分区标识符。 在 48/64 位范围用尽后，序列号会回滚到 0。

序列号可作为唯一标识符受到信任，因为它是由中央主管中立机构（而不是客户端）分配。 它还表示真正的到达顺序，比作为顺序条件的时间戳更为精确，因为时间戳在消息传递速度极快时的解析度可能不够高，并且可能会在中转站所有权跨节点转让时受到时钟偏差的影响（不过影响很小）。

绝对到达顺序非常重要。例如，在供货量有限的业务方案中，在供应期内货物供应遵循“先到先得”的原则；音乐会门票销售就是一个例子。

时间戳功能起到中立可信机构的作用，可准确捕获消息的到达时间 (UTC)，通过 EnqueuedTimeUtc 属性体现。 此值非常适用于依赖截止时间、但处理进度远远落后于队列积压工作 (backlog) 的业务方案，如需要确定工作项是否在特定日期的午夜前提交。

## <a name="scheduled-messages"></a>计划的消息

可以将消息提交到队列或主题以供延迟处理；例如，将作业安排为在特定时间可供系统处理。 此功能实现了可靠的分布式时间计划程序。

在定义的排队时间前，计划的消息不会在队列中具体化。 在此之前，可以取消计划的消息。 取消操作会将消息删除。

可以通过两种方式使用我们的任何客户端来计划消息：
- 使用常规发送 API，但在发送之前设置消息上的 `ScheduledEnqueueTimeUtc` 属性。
- 使用计划消息 API，同时传递常规消息和计划时间。 这将返回计划的消息的 SequenceNumber，稍后可用于根据需要取消计划的消息。 

也可以使用[消息浏览](message-browsing.md)，发现计划的消息及其序列号。

只有当计划的消息处于此状态时，消息的 SequenceNumber 才有效。 当消息转换为有效状态时，消息就会被追加到队列中，就像瞬时排入队列一样，包括分配新的 SequenceNumber。

因为此功能与各个消息绑定在一起，并且消息只能排入队列一次，所以服务总线不支持定期安排消息。

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)
