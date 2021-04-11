---
title: Azure Monitor 中的指标 - Azure 事件中心 | Microsoft Docs
description: 本文介绍如何使用 Azure 监视功能来监视 Azure 事件中心
ms.topic: article
ms.date: 02/25/2021
ms.openlocfilehash: 52ab66fe264b85be117eb8e2e21cb89f38d0fcae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715575"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure Monitor 中的 Azure 事件中心指标

事件中心指标可提供 Azure 订阅中的事件中心资源状态。 通过多种指标数据集，可在命名空间和实体级别评估事件中心的总体运行状况。 由于它们能够帮助监视事件中心的状态，因此这些统计信息非常重要。 另外，指标也可帮助解决由根本原因造成的问题，而无需联系 Azure 支持。

Azure Monitor 提供了统一的用户界面，可用于监视各种 Azure 服务。 有关详细信息，请参阅 GitHub 上的[在 Microsoft Azure 中进行监视](../azure-monitor/overview.md)和[通过 .NET 检索 Azure Monitor 指标](https://github.com/Azure-Samples/monitor-dotnet-metrics-api)示例。

## <a name="access-metrics"></a>访问指标

Azure Monitor 提供多种访问指标的方法。 可通过 [Azure 门户](https://portal.azure.com)、Azure Monitor API（REST 和 .Net）与分析解决方案（例如 Log Analytics 和事件中心）访问指标。 有关详细信息，请参阅 [Azure Monitor 收集的监视数据](../azure-monitor/data-platform.md)。

默认情况下，已启用指标，并且可访问最近 30 天的数据。 如需将数据保留更长一段时间，可将指标数据存档到 Azure 存储帐户。 可在 Azure Monitor 的[诊断设置](../azure-monitor/essentials/diagnostic-settings.md)中配置此设置。


## <a name="access-metrics-in-the-portal"></a>在门户中访问指标

可在 [Azure 门户](https://portal.azure.com)中监视一段时间内的指标。 以下示例演示了如何在帐户级别查看成功的请求和传入的请求：

![查看成功的指标][1]

也可以直接通过命名空间来访问指标。 为此，请选择命名空间，然后选择“指标”。 若要显示筛选到事件中心范围的指标，请选择事件中心，然后选择“指标”。

对于支持维度的指标，必须使用所需的维度值进行筛选，如以下示例所示：

![使用维度值进行筛选][2]

## <a name="billing"></a>计费

目前，在 Azure Monitor 中可以免费使用指标。 但是，如果使用引入指标数据的其他解决方案，可能就会按这些解决方案收费。 例如，如果将指标数据存档到 Azure 存储帐户，则 Azure 存储会收费。 如果将指标数据流式传输到 Azure Monitor 日志进行高级分析，Azure 也会向你收费。

以下指标可提供服务运行状况的概述。 

> [!NOTE]
> 我们弃用了多个指标，因为它们已移动到不同的名称下。 这可能会要求你更新引用。 今后将不再支持标有“弃用的”关键字的指标。

所有指标值每隔一分钟发送到 Azure Monitor。 时间粒度定义了提供指标值的时间间隔。 所有事件中心指标所支持的时间间隔为一分钟。

## <a name="azure-event-hubs-metrics"></a>Azure 事件中心指标
有关服务支持的指标列表，请参阅 [Azure 事件中心](../azure-monitor/essentials/metrics-supported.md#microsofteventhubnamespaces)

> [!NOTE]
> 发生用户错误时，Azure 事件中心会更新“用户错误”指标，但不记录任何其他诊断信息。 因此，你需要在应用程序中捕获有关用户错误的详细信息。 或者，还可以转换在将消息发送或接收到 Application Insights 时生成的遥测数据。 有关示例，请参阅[使用 Application Insights 进行跟踪](../service-bus-messaging/service-bus-end-to-end-tracing.md#tracking-with-azure-application-insights)。

## <a name="azure-monitor-integration-with-siem-tools"></a>Azure Monitor 与 SIEM 工具集成
通过使用 Azure Monitor 将监视数据（活动日志、诊断日志等）路由到事件中心，可以轻松地与安全信息和事件管理 (SIEM) 工具集成。 有关详细信息，请参阅以下文章/博客文章：

- [将 Azure 监视数据流式传输到事件中心以便外部工具使用](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)
- [Azure 日志集成简介](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [使用 Azure Monitor 与 SIEM 工具集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

在 SIEM 工具使用事件中心的日志数据的情况下，如果在指标图中看不到传入消息，或者看到传入消息但看不到传出消息，请按照下列步骤操作：

- 如果没有任何传入消息，则表示 Azure Monitor 服务没有将审核/诊断日志移到事件中心。 在此方案中，通过 Azure Monitor 团队建立支持票证。 
- 如果有传入消息，但没有传出消息，则意味着 SIEM 应用程序未读取消息。 请与 SIEM 提供者联系，以确定这些应用程序的事件中心配置是否正确。


## <a name="next-steps"></a>后续步骤

* 请参阅 [Azure 监视概述](../azure-monitor/overview.md)。
* [通过 .NET 检索 Azure Monitor 指标](https://github.com/Azure-Samples/monitor-dotnet-metrics-api)示例。 

有关事件中心的详细信息，请访问以下链接：

- 事件中心入门教程
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [事件中心常见问题解答](event-hubs-faq.md)
* [使用事件中心的示例应用程序](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png
