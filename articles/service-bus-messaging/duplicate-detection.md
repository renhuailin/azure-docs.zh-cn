---
title: Azure 服务总线重复消息检测 | Microsoft Docs
description: 本文介绍如何检测 Azure 服务总线消息中的重复项。 可以忽略并丢弃重复消息。
ms.topic: article
ms.date: 01/13/2021
ms.openlocfilehash: 8ff98b3a052be6004a2dc070f10d6f8c9ca0617f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684802"
---
# <a name="duplicate-detection"></a>重复检测

如果应用程序在发送消息后立即由于致命错误而失败，并且重启的应用程序实例错误地认为先前的消息传递未成功，那么后续发送会导致同一消息在系统中出现两次。

在客户端或网络级别上发生错误时，还可能会出现一段较早的错误，并且对于已发送的消息，如果确认未成功返回到客户端，则会将该消息提交到队列中。 在这种情况下，客户端就会对发送操作结果产生怀疑。

重复检测支持发送程序重新发送相同的消息，并让队列或主题放弃任何重复的副本，从而消除了这些情况下的各种怀疑。

> [!NOTE]
> 服务总线的基本层不支持重复检测。 标准层和高级层支持重复检测。 有关这些层之间的差异，请参阅[服务总线定价](https://azure.microsoft.com/pricing/details/service-bus/)。

## <a name="how-it-works"></a>工作原理： 
启用重复检测，有助于跟踪在指定时间范围内发送到队列或主题的所有消息的 MessageId（由应用程序控制）。 如果使用已在相应时间范围内记录的 MessageId 发送任何新消息，则将该消息报告为“已接受”（即发送操作成功），但将立即忽略和删除新发送的消息。 除了 MessageId 之外，不会检查消息的其他任何部分。

应用程序控制的此标识符至关重要，因为只有它才能让应用程序将 MessageId 绑定到业务流程上下文，从中可以在发生故障时预见性地重新构造消息。

如果业务流程是在处理某应用程序上下文的过程中发送多个消息，MessageId 可能会复合应用程序级上下文标识符，如采购订单号和消息主题；例如，12345.2017/payment。

*MessageId* 始终可以是某一 GUID，但将标识符定位到业务流程将产生可预测的可重复性，这是有效使用重复检测功能所需要的。

> [!IMPORTANT]
>- 启用 **分区** 时， `MessageId+PartitionKey` 将使用来确定唯一性。 启用会话后，分区键和会话 ID 必须相同。 
>- 如果 **禁用****分区** (默认) ，则仅 `MessageId` 使用来确定唯一性。
>- 有关 SessionId、PartitionKey 和 MessageId 的信息，请参阅 [分区键的使用](service-bus-partitioning.md#use-of-partition-keys)。
>- [Premier 层](service-bus-premium-messaging.md)不支持分区，因此建议你在应用程序中使用唯一的消息 id，而不是依赖于分区键进行重复检测。 


## <a name="enable-duplicate-detection"></a>启用重复检测

在门户中，此功能在实体创建期间处于启用状态（即“启用重复检测”复选框已选中）。不过，默认情况下，此功能处于禁用状态。 用于新建主题的设置是等效的。

![“创建队列”对话框的屏幕截图，其中“启用重复检测”选项处于选中状态并用红色标出。][1]

> [!IMPORTANT]
> 创建队列后，无法启用/禁用重复检测。 只能在创建队列时执行此操作。 

通过编程方式，可以结合使用 [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) 属性和整个 .NET Framework API，从而设置标志。 借助 Azure 资源管理器 API，可以使用 [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) 属性设置此值。

队列和主题的重复检测时间历史记录默认为 30 秒，最大值不得超过 7 天。 可以在 Azure 门户的队列和主题属性窗口中更改此设置。

![服务总线功能的屏幕截图，其中突出显示了“属性”设置，并且“重复检测历史记录”选项用红色标出。][2]

通过编程方式，可以结合使用 [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) 和整个 .NET Framework API，配置重复检测窗口大小，即消息 ID 的保留时长。 借助 Azure 资源管理器 API，可以使用 [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) 属性设置此值。

启用重复检测和窗口大小直接影响队列（和主题）的吞吐量，因为必须将记录的所有消息 ID 都与新提交的消息标识符进行匹配。

一直保持较小的窗口意味着，必须保留和匹配的消息 ID 变少了，吞吐量受到的影响也会降低。 对于需要启用重复检测的高吞吐量实体，设置的窗口应尽可能小。

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)

如果客户端代码无法使用与以前相同的 *MessageId* 重新提交消息，则必须设计可安全重新处理的消息，这一点很重要。 这篇[关于幂等性的博客文章](https://particular.net/blog/what-does-idempotent-mean)介绍了如何实现这一点的各种技术。

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
