---
title: Azure 服务总线重复消息检测 | Microsoft Docs
description: 本文介绍如何检测 Azure 服务总线消息中的重复项。 可以忽略并丢弃重复消息。
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: 9e1837d0e8859791e3834519af4a9966d2435b60
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108759070"
---
# <a name="duplicate-detection"></a>重复检测

如果应用程序在发送消息后立即由于致命错误而失败，并且重启的应用程序实例错误地认为先前的消息传递未成功，那么后续发送会导致同一消息在系统中出现两次。

也有可能更早在客户端或网络一级出错，导致已发送的消息被提交到队列，可以确认的是消息没有成功返回到客户端。 在这种情况下，客户端就会对发送操作结果产生怀疑。

重复检测支持发送程序重新发送相同的消息，并让队列或主题放弃任何重复的副本，从而消除了这些情况下的各种怀疑。

> [!NOTE]
> 基本层的服务总线不支持重复检测。 标准层和高级层支持重复检测。 有关这些层之间的差异，请参阅[服务总线定价](https://azure.microsoft.com/pricing/details/service-bus/)。

## <a name="how-it-works"></a>工作原理
启用重复检测，有助于跟踪在指定时间范围内发送到队列或主题的所有消息的 MessageId（由应用程序控制）。 如果使用已在相应时间范围内记录的 MessageId 发送任何新消息，则将该消息报告为“已接受”（即发送操作成功），但将立即忽略和删除新发送的消息。 除了 MessageId 之外，不会检查消息的其他任何部分。

应用程序控制的此标识符至关重要，因为只有它才能让应用程序将 MessageId 绑定到业务流程上下文，从中可以在发生故障时预见性地重新构造消息。

如果业务流程是在处理某应用程序上下文的过程中发送多个消息，MessageId 可能会复合应用程序级上下文标识符，如采购订单号和消息主题；例如，12345.2017/payment。

虽然 MessageId 可以始终是某 GUID，但将标识符绑定到业务流程可以预测重复消息，这更有利于有效使用重复检测功能。

> [!IMPORTANT]
>- 启用分区时，使用 `MessageId+PartitionKey` 来确定唯一性 。 启用会话时，分区键和会话 ID 必须相同。 
>- 禁用分区（默认设置）时，仅使用 `MessageId` 来确定唯一性 。
>- 有关 SessionId、PartitionKey 和 MessageId 的信息，请参阅[使用分区键](service-bus-partitioning.md#use-of-partition-keys)。
>- [高级层](service-bus-premium-messaging.md)不支持分区，因此建议在应用程序中使用唯一的消息 ID，而不依赖分区键进行重复检测。 


## <a name="duplicate-detection-window-size"></a>重复检测时间段长度

除了启用重复检测外，还可以配置保留消息 ID 的重复检测历史记录时间段长度。
对于队列和主题，此值默认为 10 分钟，最小值为 20 秒，最大值为 7 天。

启用重复检测和窗口大小直接影响队列（和主题）的吞吐量，因为必须将记录的所有消息 ID 都与新提交的消息标识符进行匹配。

一直保持较小的窗口意味着，必须保留和匹配的消息 ID 变少了，吞吐量受到的影响也会降低。 对于需要启用重复检测的高吞吐量实体，设置的窗口应尽可能小。

## <a name="next-steps"></a>后续步骤
可使用 Azure 门户、PowerShell、CLI、资源管理器模板、.NET、Java、Python 和 JavaScript 来启用重复消息检测。 有关详细信息，请参阅[启用重复消息检测](enable-duplicate-detection.md)。 

在客户端代码无法使用与以前相同的 MessageId 重新提交消息的场景中，设计能够安全地重新进行处理的消息非常重要。 这篇[关于幂等性的博客文章](https://particular.net/blog/what-does-idempotent-mean)介绍了如何实现这一点的各种技术。

尝试采用所选语言的示例，了解 Azure 服务总线功能。 

- [适用于 .NET（最新）的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [适用于 Java（最新）的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [适用于 Python 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [适用于 JavaScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [适用于 TypeScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

在下面查找早期 .NET 和 Java 客户端库示例：
- [适用于 .NET（旧版）的 Azure 服务总线客户端库示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [适用于 Java（旧版）的 Azure 服务总线客户端库示例](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus)

