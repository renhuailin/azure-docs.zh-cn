---
title: 监视媒体服务
description: 从此处开始了解如何监视媒体服务
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femilia
ms.topic: how-to
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 03/17/2021
ms.openlocfilehash: 90ca92dc19c588d0b19adf009301cf844e0cdbde
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609033"
---
# <a name="monitor-media-services"></a>监视媒体服务

当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 本文描述了媒体服务生成的监视数据，介绍如何使用 Azure Monitor 的功能对此数据进行分析和发出警报。

## <a name="metrics-are-useful"></a>指标很有用

以下示例说明监视媒体服务指标如何能够帮助你了解应用的执行情况。 使用媒体服务指标可以解决的部分问题包括：

- 如何监视标准流式处理终结点以了解何时超出了限制？
- 如何知道是否有足够的高级流式处理终结点缩放单元？
- 如何设置警报来了解何时应扩展流式处理终结点？
- 如何设置警报来了解何时达到了帐户中配置的最大出口限制？
- 如何查看失败请求的细节以及失败的原因？
- 如何查看正在从打包程序中提取多少个 HLS 或 DASH 请求？
- 如何设置警报以了解何时达到失败请求的阈值？

<!--THIS DOESN'T BELONG HERE Concurrency becomes a concern for the number of Streaming Endpoints used in a single account over time. You need to keep in mind the relationship between the number of concurrent streams with complex publishing parameters like dynamic packaging to multiple protocols, multiple DRM encryptions etc. Each additional published live stream adds to the CPU and output bandwidth on the Streaming Endpoint. With that in mind, you should use Azure Monitor to closely watch the Streaming Endpoint's utilization (CPU and Egress capacity) to make certain that you are scaling it appropriately (or split traffic out between multiple Streaming Endpoints if you are getting into very high concurrency).-->

<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact 
robb@microsoft.com -->

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？

媒体服务使用 [Azure Monitor](../../../azure-monitor/overview.md) 创建监视数据。Azure Monitor 是 Azure 中的一个全堆栈监视服务，提供用于监视 Azure 资源以及其他云中的资源和本地资源的整套功能。

可先阅读[使用 Azure Monitor 监视 Azure 资源](../../../azure-monitor/essentials/monitor-azure-resource.md)一文，其中介绍了以下概念：

- 说明是 Azure Monitor？
- 与监视相关的成本
- 监视 Azure 中收集的数据
- 配置数据收集
- Azure 中用于分析监视数据并就其发出警报的标准工具

## <a name="monitoring-data"></a>监视数据

媒体服务收集与[监视 Azure 资源中的数据](../../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源相同的监视数据。

Azure Monitor 收集的所有数据属于以下两种基本类型之一：指标和日志。 通过这两种类型，可以：

- 使用指标资源管理器来可视化和分析指标数据。
- 监视媒体服务诊断日志，并为其创建警报与通知。
- 通过日志，可以：
  - 将其发送到 Azure 存储
  - 将其流式传输到 Azure 事件中心
  - 将其导出到 Log Analytics
  - 使用第三方服务

若要详细了解媒体服务创建的指标和日志指标，请参阅[监视媒体服务数据引用](monitor-media-services-data-reference.md)一文。

## <a name="collection-and-routing"></a>收集和路由

平台指标和活动日志会自动收集和存储，但可以使用诊断设置将其路由到其他位置 。  

在创建诊断设置并将其路由到一个或多个位置之前，不会收集和存储资源日志。

有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../../../azure-monitor/essentials/diagnostic-settings.md)一文。

创建诊断设置时，请指定要收集的日志类别。 媒体服务的类别在[媒体服务监视数据引用](monitor-media-services-data-reference.md)中列出。

## <a name="analyzing-metrics"></a>分析指标

可以从“Azure Monitor”菜单中打开“指标”，使用指标资源管理器根据来自其他 Azure 服务的指标分析媒体服务的指标 。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../../../azure-monitor/essentials/metrics-getting-started.md)。

有关为媒体服务收集的指标列表，请参阅[监视媒体服务数据引用](monitor-media-services-data-reference.md)。

## <a name="analyzing-logs"></a>分析日志

Azure Monitor 日志中的数据以表形式存储，每个表具有自己独有的属性集。  

Azure Monitor 中的所有资源日志都具有后跟服务特定字段的相同字段。 [Azure Monitor 资源日志架构](../../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)概述了常见架构。

如需了解媒体服务资源日志的架构，请参阅[监视媒体服务数据引用](monitor-media-services-data-reference.md)。

[活动日志](../../../azure-monitor/essentials/activity-log.md)是 Azure 中的一种平台日志，可用于深入了解订阅级别事件。 你可以单独查看它或将它路由到 Azure Monitor 日志，然后便可以在其中使用 Log Analytics 执行复杂得多的查询。

有关为媒体服务收集的资源日志类型列表，请参阅[监视媒体服务数据引用](monitor-media-services-data-reference.md)。

### <a name="why-would-i-want-to-use-diagnostic-logs"></a>为什么需要使用诊断日志？

可以通过诊断日志检查的一些内容包括：

- 按 DRM 类型提供的许可证数量。
- 按策略提供的许可证数量。
- 按 DRM 或策略类型划分的错误。
- 来自客户端的未经授权的许可证请求数。

## <a name="alerts"></a>警报

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在客户注意到你的系统中的问题之前确定和解决它们。 可以在[指标](../../../azure-monitor/alerts/alerts-metric-overview.md)、[日志](../../../azure-monitor/alerts/alerts-unified-log.md)和[活动日志](../../../azure-monitor/alerts/activity-log-alerts.md)上设置警报。

不管值是否变化，媒体服务指标都按固定的时间间隔进行收集。 指标可用于警报，因为可对其频繁采样。 可以使用相对简单的逻辑快速激发警报。

<!--
The following table lists common and recommended alert rules for Media Services.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable
**PLACEHOLDER** table

| Alert type | Condition | Description  |
|:---|:---|:---|
| | | |
| | | |
-->

## <a name="next-steps"></a>后续步骤

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
