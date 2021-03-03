---
title: 在 Azure Monitor 中 & 监视的经典警报更新
description: 经典监视服务和功能的停用说明，先前在 Azure 门户警报 (经典) 下显示。
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: a862e54b5e1eddce5fcabf050eba1cfa1f103e30
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734751"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Azure Monitor 中的统一警报和监视替换经典警报和监视

Azure Monitor 是一种统一的监视堆栈，它在 Azure 资源之间支持 "一个指标" 和 "单一警报"。 请参阅此 [博客文章](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/)中的详细信息。 新的 Azure 监视和警报平台构建得更快、更智能、更易于扩展，并与 Microsoft 智能云理念的范围不断增长，同时保持快速增长。

使用新的 Azure 监视和警报平台时，会为公有云用户停用 Azure Monitor 中的经典警报，但在 **31 年 5 2021 月31日** 之前仍处于有限使用状态。 Azure 政府云和 Azure 中国世纪互联的经典警报将于 **2024 年2月29日** 停用。

 ![Azure 门户中的经典警报](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

我们鼓励你开始在新平台中重新创建警报。

> [!IMPORTANT]
> 基于活动日志创建的经典警报规则不会被弃用或迁移。 可以从新的 Azure Monitor -“警报”按现样访问和使用基于活动日志创建的所有经典警报规则。 有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理活动日志警报](./alerts-activity-log.md)。 类似地，可以从新的“服务运行状况”部分按现样访问和使用基于服务运行状况的警报。 有关详细信息，请参阅[基于服务运行状况通知的警报](../../service-health/alerts-activity-log-service-notifications-portal.md)。

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>适用于 Azure 资源的统一指标和警报

2018年3月，我们发布了 Azure 资源的下一代警报。 较新的指标平台和警报的速度更快，并使用维度提供更多的粒度。 维度可用于切片并筛选到特定值组合、条件或操作。 较新的指标警报使用操作组，以允许更多通知和自动化操作。 有关 [使用 Azure Monitor 管理指标警报](./alerts-metric.md)的详细信息，请参阅。

Azure 资源的新指标按以下形式提供：

- **Azure Monitor 标准平台指标** – 提供来自各种 Azure 服务和产品的常用预填充指标。 有关详细信息，请参阅这篇有关 [Azure Monitor 上支持的指标](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported)和 [Azure Monitor 上支持的指标警报](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts)文章。
- **Azure Monitor 自定义指标** – 提供来自用户驱动源（包括 Azure 诊断代理）的指标。 有关详细信息，请参阅这篇有关 [Azure Monitor 中的自定义指标](../essentials/metrics-custom-overview.md)的文章。 使用自定义指标，还可以发布 [Microsoft Azure 诊断代理](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)和 [InfluxData Telegraf 代理](../essentials/collect-custom-metrics-linux-telegraf.md)收集的指标。

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>经典监视和警报平台的停用

如前文所述，为公有云用户停用旧的经典监视和警报。 它包括相关 Api 的闭包、Azure 门户接口和服务，但在 **5 2021 月31日** 之前仍处于有限使用状态。 Azure 政府云和 Azure 中国世纪互联的经典警报将于 **2024 年2月29日** 停用。

具体而言，退休范围适用于当前在 Azure 门户的 " [ (经典) " 部分](./alerts-classic.overview.md) 中提供的经典指标，可作为 " [alertrules/](/rest/api/monitor/alertrules) " 资源进行访问。

这意味着：

- 经典监视和警报服务将停用，并且不再可用于创建新的警报规则。
- 任何在经典) 中继续存在的警报规则都将继续执行并激发通知 (。
- 将迁移大多数经典警报规则。 该过程无缝进行且没有任何停机时间，并且客户在监视覆盖范围内将没有任何损失。
- 触发的通知将包括新的有效负载结构。 目标将需要调整为使用新的结构。
- 某些 [经典警报规则无法自动迁移](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) ，需要用户手动操作才能继续运行。

> [!IMPORTANT]
> Azure Monitor 推出了一种 [工具，可主动](alerts-using-migration-tool.md) 将其经典警报规则迁移到新平台。 服务停用后，将自动迁移剩余规则。 客户需要确保在迁移经典警报规则后，将使用经典警报规则负载的自动化用于处理来自 [其他 Azure 资源的统一指标和警报](#unified-metrics-and-alerts-for-azure-resources)的新负载。 有关详细信息，请参阅 [为经典警报规则迁移做准备](alerts-prepare-migration.md)。

## <a name="pricing-for-migrated-alert-rules"></a>已迁移的警报规则的定价

我们正在推出迁移工具，以帮助你将 Azure Monitor [经典警报](./alerts-classic.overview.md) 迁移到新的警报体验。 已迁移的警报规则和相应的已迁移操作组 (电子邮件、webhook 或逻辑应用) 将保持免费。 使用已迁移的警报规则时，可以使用经典警报的功能，包括编辑阈值、聚合类型和聚合粒度这一功能。 但是，如果您编辑已迁移的警报规则以使用任何新的警报平台功能、通知或操作类型，则将应用相应的费用。 有关警报规则和通知的定价的详细信息，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

下面是你将对警报规则收费的情况的示例：

- 新 Azure Monitor 平台上除免费单位数之外创建的任何新警报（非迁移）规则
- Azure Monitor 内除免费单位数之外引入和保留的任何数据
- Application Insights 执行的任何多测试 Web 测试
- Azure Monitor 内除免费单位数之外存储的任何自定义指标
- 经过编辑的任何已迁移警报规则，以使用较新的指标警报功能，如频率、多个资源/维度、 [动态阈值](../alerts/alerts-dynamic-thresholds.md)、更改资源/信号等等。
- 任何已迁移的操作组，这些操作组将进行编辑以使用较新的通知，或诸如短信、语音呼叫和/或 ITSM 集成等操作类型。

## <a name="next-steps"></a>后续步骤

* 了解[新的统一 Azure Monitor](../overview.md)。
* 了解新的 [Azure 警报](./alerts-overview.md)。