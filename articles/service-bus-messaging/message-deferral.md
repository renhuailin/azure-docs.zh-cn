---
title: Azure 服务总线 - 消息延迟
description: 本文介绍如何延迟传送 Azure 服务总线消息。 该消息将保留在队列或订阅中，但会搁置处理。
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 71446f7989a235e79ca3ab208579edd0043dfeb2
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987247"
---
# <a name="message-deferral"></a>消息延迟
当队列或订阅客户端收到一条它愿意处理，但由于出现特殊状况而无法处理的消息时，该客户端可将该消息的检索“延迟”到将来的某个时间点。 该消息将保留在队列或订阅中，但会搁置处理。

> [!NOTE]
> [延迟消息过期后](./service-bus-dead-letter-queues.md#time-to-live)不会自动移动到死信队列。 此行为是设计使然。

## <a name="sample-scenarios"></a>示例方案
延迟是专门针对工作流处理方案设计的功能。 工作流框架可能要求按特定顺序处理某些操作。 它们可能必须推迟处理某些收到的消息，直到完成由其他消息通知的预先指定的工作为止。

订单处理序列就是一个简单的演示示例：来自外部付款提供程序的付款通知先显示在系统中，然后将存储前端中的匹配采购订单传播到履行系统。 在这种情况下，履行系统可能会延迟付款通知的处理，直到出现可与该通知关联的订单。 在会合方案中，来自不同源的消息可推动工作流，实时执行顺序可能确实正确，但反映结果的消息可能不按顺序到达。

最终，延迟有助于将消息的到达顺序重新排列为处理顺序，同时将需要延迟处理的消息安全保留在消息存储中。

如果由于处理消息的特定资源暂时不可用，而无法处理该消息，但同时又不能立即暂停消息处理，则将该消息搁置几分钟的方法是在要延后几分钟的[计划消息](message-sequencing.md)中记住序列号，并在计划的消息到达时重新检索已延迟的消息。 如果消息处理程序依赖于使用某个数据库来执行所有操作，而该数据库暂时不可用，则不应使用延迟，而应全面暂停接收消息，直到数据库再次可用。 

## <a name="retrieving-deferred-messages"></a>检索延迟的消息
延迟的消息连同其他所有活动消息保留在主队列中（与保留在子队列中的死信消息不同），但不再可以使用正则接收操作。 如果应用程序不再能跟踪延迟的消息，可以通过[消息浏览](message-browsing.md)来发现这些消息。

若要检索延迟的消息，消息所有者在延迟它时需负责记住序列号。 任何接收方，只要知道延迟消息序列号，之后都可以通过使用将序列号作为参数的接收方法来接收消息。 有关序列号的详细信息，请参阅[消息序列化和时间戳](message-sequencing.md)。

## <a name="next-steps"></a>后续步骤
尝试采用所选语言的示例，了解 Azure 服务总线功能。 

- [适用于 .NET（最新）的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) - 请参阅“处置消息”示例。 
- [适用于 Java（最新）的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [适用于 Python 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-python/servicebus-samples/) - 请参阅 receive_deferred_message_queue.py 示例。 
- [适用于 JavaScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-javascript/) - 请参阅“高级/deferral.js”示例。 
- [适用于 TypeScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-typescript/) - 请参阅“高级/deferral.ts”示例。 

在下面查找早期 .NET 和 Java 客户端库示例：
- [适用于.NET（旧版）的 Azure 服务总线客户端库示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) - 请参阅“延迟”示例。 
- [适用于 Java（旧版）的 Azure 服务总线客户端库示例](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
