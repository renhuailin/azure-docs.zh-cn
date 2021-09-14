---
title: 在 Azure Monitor 中更新经典警报和监视
description: 关于停用经典监视服务和功能（以前在 Azure 门户中的“警报(经典)”下显示）的说明。
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.openlocfilehash: 64e9c454bfe8df9b65176fe344999e88204720d0
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539112"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Azure Monitor 中的统一警报和监视替换经典警报和监视

Azure Monitor 是一个统一监视堆栈，它支持跨 Azure 资源使用“一个指标”和“一个警报”。 有关详细信息，请参阅此[博客文章](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/)。 全新的 Azure 监视和警报平台已构建完成，它会更快、更智能，并且可扩展 – 紧随云计算领域的发展步伐，并契合“Microsoft 智能云”理念。

随着新的 Azure 监视和警报平台部署到位，已对公有云用户停用了 Azure Monitor 中的经典警报。 适用于 Azure 政府云和 Azure 中国世纪互联的经典警报将于 2024 年 2 月 29 日停用。

 ![Azure 门户中的经典警报](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

我们鼓励你开始在新平台中重新创建警报。

> [!IMPORTANT]
> 基于活动日志创建的经典警报规则不会被弃用或迁移。 可以从新的 Azure Monitor -“警报”按现样访问和使用基于活动日志创建的所有经典警报规则。 有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理活动日志警报](./alerts-activity-log.md)。 类似地，可以从新的“服务运行状况”部分按现样访问和使用基于服务运行状况的警报。 有关详细信息，请参阅[基于服务运行状况通知的警报](../../service-health/alerts-activity-log-service-notifications-portal.md)。

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>Azure 资源的统一指标和警报

在 2018 年 3 月，我们发布了 Azure 资源的下一代警报。 新的指标平台和警报速度更快，并通过使用维度提供了更多的粒度。 利用维度，可以切片并筛选到特定的值组合、条件或操作。 新的指标警报使用操作组，允许更多的通知和自动化操作。 了解有关[使用 Azure Monitor 管理指标警报](./alerts-metric.md)的详细信息。

Azure 资源的新指标按以下形式提供：

- **Azure Monitor 标准平台指标** – 提供来自各种 Azure 服务和产品的常用预填充指标。 有关详细信息，请参阅这篇有关 [Azure Monitor 上支持的指标](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported)和 [Azure Monitor 上支持的指标警报](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts)文章。
- **Azure Monitor 自定义指标** – 提供来自用户驱动源（包括 Azure 诊断代理）的指标。 有关详细信息，请参阅这篇有关 [Azure Monitor 中的自定义指标](../essentials/metrics-custom-overview.md)的文章。 使用自定义指标，还可以发布 [Microsoft Azure 诊断代理](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)和 [InfluxData Telegraf 代理](../essentials/collect-custom-metrics-linux-telegraf.md)收集的指标。

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>经典监视和警报平台的停用

如前文所述，旧的经典监视和警报已对公有云用户停用。 此次停用包括关闭相关的 API、Azure 门户界面和其中的服务，但在 2021 年 5 月 31 日之前仍可有限制地使用这些内容。 适用于 Azure 政府云和 Azure 中国世纪互联的经典警报将于 2024 年 2 月 29 日停用。

具体而言，停用范围是针对 Azure 门户的[“警报(经典)”部分](./alerts-classic.overview.md)中目前可用的以及可作为 [microsoft.insights/alertrules](/rest/api/monitor/alertrules) 资源访问的经典指标。

这意味着：

- 经典监视和警报服务将停用，并且不再可用于创建新的警报规则。
- 继续存在于“警报(经典)”中的任何警报规则都会继续执行并触发通知。
- 大多数经典警报规则将会迁移。 该过程无缝进行且没有任何停机时间，并且客户在监视覆盖范围内将没有任何损失。
- 触发的通知将包括新的有效负载结构。 目标将需要调整为使用新的结构。
- 有些[经典警报规则无法自动迁移](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)，需要用户手动操作才能继续运行。

> [!IMPORTANT]
> Azure Monitor 已经推出了[自愿性迁移工具](alerts-using-migration-tool.md)，可用于将经典警报规则迁移到新平台。 剩余的规则将会在服务停用后自动迁移。 在迁移经典警报规则后，客户将需要确保对使用经典警报规则有效负载的自动化进行调整，以处理来自[其他 Azure 资源的统一指标和警报](#unified-metrics-and-alerts-for-azure-resources)的新有效负载。 有关详细信息，请参阅[准备迁移经典警报规则](alerts-prepare-migration.md)。

## <a name="pricing-for-migrated-alert-rules"></a>已迁移的警报规则的定价

我们正在推出迁移工具，它可以帮助你将 Azure Monitor [经典警报](./alerts-classic.overview.md)迁移到新的警报体验。 已迁移的警报规则和相应的已迁移操作组（电子邮件、Webhook 或 LogicApp）仍将免费。 迁移警报规则以后，你可以继续免费使用以前的经典警报功能，包括编辑阈值、聚合类型和聚合粒度的功能。 但是，如果你编辑已迁移的警报规则，以便使用任何新的警报平台功能、通知或操作类型，则需支付相应的费用。 若要详细了解警报规则和通知的定价，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

下面是警报规则收费案例的示例：

- 新 Azure Monitor 平台上除免费单位数之外创建的任何新警报（非迁移）规则
- Azure Monitor 内除免费单位数之外引入和保留的任何数据
- Application Insights 执行的任何多测试 Web 测试
- Azure Monitor 内除免费单位数之外存储的任何自定义指标
- 经过编辑后可以使用更新的指标警报功能（例如频率、多个资源/维度、[动态阈值](../alerts/alerts-dynamic-thresholds.md)、变化的资源/信号等）的任何已迁移警报规则。
- 任何已迁移的操作组，经过编辑后可以使用新通知或操作类型（例如，短信、语音呼叫和/或 ITSM 集成）。

## <a name="next-steps"></a>后续步骤

* 了解[新的统一 Azure Monitor](../overview.md)。
* 了解新的 [Azure 警报](./alerts-overview.md)。
