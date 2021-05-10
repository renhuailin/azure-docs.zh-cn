---
title: 指标和警报 - Azure DNS
description: 借助此学习路径，开始了解 Azure DNS 指标和警报。
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2021
ms.author: rohink
ms.openlocfilehash: f2eaac432673682b075763c6ce9fe9426ed77a70
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108017533"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS 指标和警报

Azure DNS 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 本文介绍了 Azure DNS 服务的指标和警报。

## <a name="azure-dns-metrics"></a>Azure DNS 指标

Azure DNS 提供了相关指标，用于监视 DNS 区域的特定方面。 借助 Azure DNS 中的指标，可以根据符合的条件配置警报。 提供的指标使用 [Azure Monitor 服务](../azure-monitor/index.yml)显示数据。 有关指标资源管理器体验和图表的详细信息，请参阅 [Azure Monitor 指标资源管理器](../azure-monitor/essentials/metrics-charts.md)。 
 
Azure DNS 为 DNS 区域向 Azure Monitor 提供以下指标：

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

有关详细信息，请参阅[指标定义](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkdnszones)。

>[!NOTE]
> 这个时候，这些指标仅可用于 Azure DNS 中托管的公共 DNS 区域。 如果 Azure DNS 托管专用区域，那么这些指标不为这些区域提供数据。 此外，仅 Azure 公有云支持指标和警报功能。 对主权云的支持将会在稍后跟进。 

可以看到指标的最细粒度元素是 DNS 区域。 当前无法查看区域内单个资源记录的指标。

### <a name="query-volume"></a>查询量

“查询量”指标显示 Azure DNS 接收到的 DNS 区域的 DNS 查询量。 度量单位为 `Count`，聚合是一段时间内收到的所有查询的 `Sum`。

若要查看此指标，请从 Azure 门户的“监视”页选择“指标”资源管理器体验 。 将范围缩小到 DNS 区域，然后选择“应用”。 在“指标”下拉列表中，选择 `Query Volume`，然后在“聚合”下拉列表中选择 `Sum` 。

![查询量](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

图：Azure DNS 查询量指标 

### <a name="record-set-count"></a>记录集计数

“记录集计数”指标显示 Azure DNS 中 DNS 区域的记录集数  。 对区域中定义的所有记录集进行计数。 度量单位是 `Count`，聚合是所有记录集的 `Maximum`。

若要查看此指标，请从 Azure 门户的“监视”页选择“指标”资源管理器体验 。 将范围缩小到 DNS 区域，然后选择“应用”。 在“指标”下拉列表中，选择 `Query Volume`，然后在“聚合”下拉列表中选择 `Sum` 。

从“资源”下拉列表选择 DNS 区域，选择“记录集计数”指标，然后选择“最大值”作为“聚合”     。 

![记录集计数](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

图：Azure DNS 记录集计数指标 

### <a name="record-set-capacity-utilization"></a>记录集容量使用率

“记录集容量使用率”指标显示 DNS 区域的记录集容量使用百分比。 每个 Azure DNS 区域都有一个记录集限制，定义了该区域允许的记录集最大数。 有关详细信息，请参阅 [DNS 限制](dns-zones-records.md#limits)部分。 度量单位是 `Percentage`，聚合类型是 `Maximum`。

例如，如果为 DNS 区域配置了 500 个记录集，并且区域的默认记录集限制为 5000。 那么 RecordSetCapacityUtilization 指标将显示值 10%（通过 500 除以 5000 计算得出）。 

若要查看此指标，请从 Azure 门户的“监视”页选择“指标”资源管理器体验 。 将范围缩小到 DNS 区域，然后选择“应用”。 在“指标”下拉列表中选择 `Record Set Capacity Utilization`，然后在“聚合”下拉列表中选择 `Sum` 。 

![显示如何查看指标的示例的屏幕截图。](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

图：Azure DNS 记录集容量使用率指标 

## <a name="alerts-in-azure-dns"></a>Azure DNS 中的警报

Azure Monitor 提供可为每个可用指标值配置的警报功能。 有关详细信息，请参阅 [Azure Monitor 警报](../azure-monitor/alerts/alerts-metric.md)。

1. 若要为 Azure DNS 区域配置警报，请在 Azure 门户中的“监视”页选择“警报”。 然后选择“+ 新建预警规则”。

    :::image type="content" source="./media/dns-alerts-metrics/alert-metrics.png" alt-text="“监视”页上“警报”按钮的屏幕截图。":::


1. 单击“范围”部分的“选择资源”链接，打开“选择资源”页。 按“DNS 区域”筛选，然后选择要用作目标资源的 Azure DNS 区域。 选择区域后，选择“完成”。

    :::image type="content" source="./media/dns-alerts-metrics/select-resource.png" alt-text="配置警报中的“选择资源”页的屏幕截图。":::

1. 接下来，单击“条件”部分中的“添加条件”链接，打开“选择信号”页。 选择要为其配置警报的“指标”信号类型（在三个中选一个）。

    :::image type="content" source="./media/dns-alerts-metrics/select-signal.png" alt-text="“选择信号”页上可用指标的屏幕截图。":::

1. 在“配置信号逻辑”页上，配置所选指标的评估阈值和频率。

    :::image type="content" source="./media/dns-alerts-metrics/configure-signal-logic.png" alt-text="“配置信号逻辑”页的屏幕截图。":::

1. 若要发送通知或调用警报触发的操作，请单击“添加操作组”。 在“添加操作组”页上，选择“+ 创建操作组”。 有关详细信息，请参阅[操作组](../azure-monitor/alerts/action-groups.md)。

1. 输入警报规则名称，然后选择“创建警报规则”以保存配置。

    :::image type="content" source="./media/dns-alerts-metrics/create-alert-rule.png" alt-text="“创建警报规则”页的屏幕截图。":::

有关如何配置 Azure Monitor 指标报警的详细信息，请参阅[使用 Azure Monitor 创建、查看和管理警报](../azure-monitor/alerts/alerts-metric.md)。 

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure DNS](dns-overview.md)。
