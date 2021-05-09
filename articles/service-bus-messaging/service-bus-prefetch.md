---
title: Azure 服务总线预提取消息 | Microsoft Docs
description: 通过预提取 Azure 服务总线消息提升性能。 在应用程序请求消息之前，消息随时可供本地检索。
ms.topic: article
ms.date: 04/23/2021
ms.openlocfilehash: 992fa9228d3cf8bafb3d2ea9110b515ff4b56498
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989083"
---
# <a name="prefetch-azure-service-bus-messages"></a>预提取 Azure 服务总线消息
为任何官方服务总线客户端启用“预提取”功能后，接收方获得的消息要比应用程序最初请求的消息还要多，该消息数最多将达到指定的预提取计数。 请注意，JavaScript 和 TypeScript 客户端尚不支持此功能。

当消息返回到应用程序时，客户端会在后台获取更多消息，以填充预提取缓冲区。

## <a name="enabling-prefetch"></a>启用预提取
若要启用预提取功能，请将队列或订阅客户端的预提取计数设置为大于零的数字。 将该值设为零可关闭预提取。 

# <a name="net"></a>[.NET](#tab/dotnet)
如果使用最新的 Azure.Messaging.ServiceBus 库，则可以在 [ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusReceiver_PrefetchCount) 和 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusProcessor_PrefetchCount) 对象上设置预提取计数属性。 

如果使用适用于服务总线 (Microsoft.Azure.ServiceBus) 的旧版 .NET 客户端库，则可以在 [MessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount)、[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) 或 [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount) 上设置预提取计数属性。
 
# <a name="java"></a>[Java](#tab/java)
如果使用最新的 azure-messaging-servicebus 库，则可以在 [ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusReceiver_PrefetchCount) 和 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusProcessor_PrefetchCount) 对象上设置预提取计数属性。 

如果使用适用于服务总线（azure-servicebus）旧版 Java 客户端库，则可以在 [MessageReceiver](/java/api/com.microsoft.azure.servicebus.imessagereceiver.setprefetchcount#com_microsoft_azure_servicebus_IMessageReceiver_setPrefetchCount_int_)、[QueueClient](/java/api/com.microsoft.azure.servicebus.queueclient.setprefetchcount#com_microsoft_azure_servicebus_QueueClient_setPrefetchCount_int_) 或 [SubscriptionClient](/java/api/com.microsoft.azure.servicebus.subscriptionclient.setprefetchcount#com_microsoft_azure_servicebus_SubscriptionClient_setPrefetchCount_int_) 上设置预提取计数属性。
 
# <a name="python"></a>[Python](#tab/python)

可以在 [azure.servicebus.ServiceBusReceiver](/python/api/azure-servicebus/azure.servicebus.servicebusreceiver) 或 [azure.servicebus.aio.ServiceBusReceiver](/python/api/azure-servicebus/azure.servicebus.aio.servicebusreceiver) 上设置“预提取计数”。

---

> [!NOTE]
> Java 脚本 SDK 不支持预提取功能。 

尽管消息在预提取缓存区中提供，但后续接收的任何调用均可立即从缓冲区完成。 空间可用时，系统将在后台对缓冲区进行补充。 如果没有消息用于传送，接收操作将清空缓存区，并按预期进行等待或阻止。

## <a name="why-is-prefetch-not-the-default-option"></a>预提取为何不是默认选项？
预提取可加快消息流，方法是在应用程序请求消息前，先准备好用于本地检索的消息。 这种吞吐量提升是应用程序作者不得不明确作出的某种权衡的结果：

在[接收-删除](message-transfers-locks-settlement.md#receiveanddelete)模式下，所有进入预提取缓冲区的消息在队列中都不再可用。 消息仅保留在内存预提取缓冲区中，直到系统将其接收到应用程序中。 如果在应用程序接收到消息前终止应用程序，这些消息将丢失，且不可恢复。

在 [Peek-Lock](message-transfers-locks-settlement.md#peeklock) 接收模式下，提取到预提取缓存区的消息将以锁定状态进入缓存区， 并会将超时时钟用于锁定计时。 如果预提取缓存区很大且所需处理时间过长，以致消息于锁在预提取缓冲区时，甚至于应用程序处理消息时过期，则应用程序可能需要处理出现的一些混乱事件。

应用程序可能收到包含到期或即将到期的锁定的消息。 如有收到，应用程序可能会处理该消息，但随后发现其因锁定到期而无法完成处理。 应用程序可查看 `LockedUntilUtc` 属性（受代理时钟和本地计算机时钟之间的时钟偏差约束）。 如果消息锁定已到期，则应用程序必须忽略该消息，并且不应对该消息或通过该消息调用任何 API。 如果消息未到期但即将到期，则可更新锁定并延长另一个默认的锁定时间。

如果锁定在预提取缓冲区静默地到期，则视为已放弃该消息，且可再次将消息用于从队列进行检索。 这可能导致将消息提取到预提取缓冲区，并置于末端。 如果在消息过期期间，预提取缓冲区通常无法使用，则将导致重复预提取消息，但始终无法将其以可用（有效锁定）状态有效送达，并最终在超出最大传送数后移动到死信队列。

如果消息处理需要高度可靠性，且处理需要花费大量精力和时间，则建议谨慎使用或者避免使用预提取功能。

如果需要较高吞吐量且消息处理通常比较便宜，则预提取会产生显著的吞吐量优势。

需要均衡对队列或订阅配置的最大预提取数和锁定持续时间，以便锁定超时至少超出最大预提取缓存区大小外加一条消息的累积预期消息处理时间。 同时，锁定超时不应过长，以防消息在遭到意外丢弃后超出其最大生存时间，因此需要消息的锁定在重新传送消息前到期。

## <a name="next-steps"></a>后续步骤

尝试采用所选语言的示例，了解 Azure 服务总线功能。 

- [适用于 .NET（最新）的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [适用于 Java（最新）的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [适用于 Python 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [适用于 JavaScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [适用于 TypeScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

在下面查找早期 .NET 和 Java 客户端库示例：
- [适用于.NET（旧版）的 Azure 服务总线客户端库示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)（见预提取示例）。 
- [适用于 Java（旧版）的 Azure 服务总线客户端库示例](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus)（见预提取示例）。 
