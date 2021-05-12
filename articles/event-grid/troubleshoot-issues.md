---
title: 排查事件网格问题
description: 本文提供了各种方式来解决 Azure 事件网格问题
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: d30b8464de90474ad74853cc423de700b41226a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720553"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>解决 Azure 事件网格问题
本文提供的信息有助于解决 Azure 事件网格问题。 

## <a name="diagnostic-logs"></a>诊断日志
为事件网格主题或域启用诊断设置，以捕获和查看发布与传送失败日志。 有关详细信息，请参阅[诊断日志](enable-diagnostic-logs-topic.md)。

## <a name="metrics"></a>指标
你可以查看有关事件网格主题和订阅的指标，并创建关于它们的警报。 有关详细信息，请参阅[事件网格指标](monitor-event-delivery.md)。

## <a name="alerts"></a>警报
创建有关 Azure 事件网格指标和活动日志操作的警报。 有关详细信息，请参阅[有关事件网格指标和活动日志的警报](set-alerts.md)。

## <a name="subscription-validation-issues"></a>订阅验证问题
在创建事件订阅的过程中，你可能会收到一条错误消息，指出所提供的终结点的验证失败。 若要解决订阅验证问题，请参阅[事件网格订阅验证故障排除](troubleshoot-subscription-validation.md)。 

## <a name="network-connectivity-issues"></a>网络连接问题
客户端应用程序无法连接到事件网格主题/域的原因有很多。 你遇到的连接问题可能是永久性的，也可能是暂时性的。 若要了解如何解决这些问题，请参阅[排查连接性问题](troubleshoot-network-connectivity.md)。

## <a name="error-codes"></a>错误代码
如果你收到错误代码为 400、409 和 403 等等的错误消息，请参阅[解决事件网格错误](troubleshoot-errors.md)。 

## <a name="distributed-tracing-net"></a>分布式跟踪 (.NET)
事件网格 .NET 库支持分布式跟踪。 根据有关分布式跟踪的 [CloudEvents 规范指南](https://github.com/cloudevents/spec/blob/master/extensions/distributed-tracing.md)，当分布式跟踪已启用时，该库会在 `CloudEvent` 的 [ExtensionAttributes](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventgrid/Azure.Messaging.EventGrid/src/Customization#L126) 上设置 `traceparent` 和 `tracestate`。 若要详细了解如何在应用程序中启用分布式跟踪，请参阅 Azure SDK [分布式跟踪文档](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing)。

### <a name="sample"></a>示例
请参阅[行计数器示例](/samples/azure/azure-sdk-for-net/line-counter/)。 此示例应用展示了如何将存储、事件中心和事件网格客户端与 ASP.NET Core 集成、分布式跟踪和托管服务配合使用。 它允许用户将文件上传到 blob，这会触发包含文件名的事件中心事件。 事件中心处理器接收事件，然后，应用下载 blob 并对文件中的行数进行计数。 应用将显示一个指向某个页面的链接，其中包含行计数。 单击此链接后，将使用事件网格发布包含该文件名称的 CloudEvent。

## <a name="next-steps"></a>后续步骤
如需更多帮助，请在 [Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-eventgrid)中发布问题，或开具[支持票证](https://azure.microsoft.com/support/options/)。 
