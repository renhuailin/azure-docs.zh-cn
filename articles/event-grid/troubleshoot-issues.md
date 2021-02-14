---
title: 事件网格问题疑难解答
description: 本文提供了不同的方式来解决 Azure 事件网格问题
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 9c52ba8561c10dd94ec6ef51c78b8534c6c58e96
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417083"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>排查 Azure 事件网格问题
本文提供的信息有助于排查 Azure 事件网格问题。 

## <a name="diagnostic-logs"></a>诊断日志
启用事件网格主题或域的诊断设置，以捕获和查看发布和传递失败日志。 有关详细信息，请参阅 [诊断日志](enable-diagnostic-logs-topic.md)。

## <a name="metrics"></a>指标
你可以查看事件网格主题和订阅的指标，并对其创建警报。 有关详细信息，请参阅 [事件网格指标](monitor-event-delivery.md)。

## <a name="alerts"></a>警报
针对 Azure 事件网格指标和活动日志操作创建警报。 有关详细信息，请参阅 [事件网格指标和活动日志警报](set-alerts.md)。

## <a name="subscription-validation-issues"></a>订阅验证问题
在创建事件订阅的过程中，可能会收到一条错误消息，指出所提供终结点的验证失败。 有关订阅验证问题的疑难解答，请参阅 [事件网格订阅验证故障排除](troubleshoot-subscription-validation.md)。 

## <a name="network-connectivity-issues"></a>网络连接问题
客户端应用程序无法连接到事件网格主题/域的原因有多种。 你遇到的连接问题可能是永久性的，也可能是暂时性的。 若要了解如何解决这些问题，请参阅 [排查连接问题](troubleshoot-network-connectivity.md)。

## <a name="error-codes"></a>错误代码
如果收到包含错误代码（如400、409和403）的错误消息，请参阅 [排查事件网格错误](troubleshoot-errors.md)。 

## <a name="distributed-tracing-net"></a>分布式跟踪 ( .NET) 
事件网格 .NET 库支持分布跟踪。 为了遵守[CloudEvents 规范](https://github.com/cloudevents/spec/blob/master/extensions/distributed-tracing.md)有关分发跟踪的指导，库在 `traceparent` `tracestate` 启用分布式跟踪时，将和[](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid/src/Customization/CloudEvent.cs#L126)设置为 ExtensionAttributes `CloudEvent` 。 若要了解有关如何在应用程序中启用分布式跟踪的详细信息，请参阅 Azure SDK [分布式跟踪文档](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing)。

### <a name="sample"></a>示例
请参阅 [行计数器示例](/samples/azure/azure-sdk-for-net/line-counter/)。 此示例应用阐释了如何使用存储、事件中心和事件网格客户端以及 ASP.NET Core 集成、分布式跟踪和托管服务。 它允许用户将文件上传到 blob，这会触发包含文件名的事件中心事件。 事件中心处理器接收事件，然后应用下载 blob 并对文件中的行数进行计数。 应用程序将显示一个指向包含行计数的页的链接。 单击此链接后，将使用事件网格发布包含该文件名称的 CloudEvent。

## <a name="next-steps"></a>后续步骤
如需更多帮助，请在 [Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-eventgrid)中发布问题，或开具[支持票证](https://azure.microsoft.com/support/options/)。 
