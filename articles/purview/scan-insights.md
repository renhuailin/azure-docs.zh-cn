---
title: 在 Azure 监控范围中扫描有关数据的见解
description: 本操作方法指南介绍了如何针对数据查看和使用监控范围 Insights 扫描报告。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: ea12bf8a8d93f14c5364864b97d1173fe8602765
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551770"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>在 Azure 监控范围中扫描有关数据的见解

本操作方法指南介绍了如何访问、查看和筛选数据的 Azure 监控范围扫描见解报表。

在本操作方法指南中，你将学习如何执行以下操作：

* 从你的监控范围帐户查看见解。
* 获取扫描的鸟瞰视图。

## <a name="prerequisites"></a>先决条件

在开始利用监控范围 insights 之前，请确保已完成以下步骤：

* 设置你的 Azure 资源，并在该帐户中填充数据。
* 设置并完成数据源扫描。

有关详细信息，请参阅 [在 Azure 监控范围中管理数据源 (预览) ](manage-data-sources.md)。

## <a name="use-purview-scan-insights"></a>使用监控范围 Scan Insights

在 Azure 监控范围中，可以注册和扫描源类型。 你可以在扫描见解中查看一段时间内的扫描状态。 Insights 会告诉你在某个时间段内失败、成功或取消了多少次扫描。

### <a name="view-scan-insights"></a>查看 scan insights

1. 在 Azure 门户中转到 **Azure 监控范围** 实例屏幕，并选择监控范围帐户。

1. 在 " **概述** " 页上的 " **开始** " 部分中，选择 " **打开监控范围 Studio** " 磁贴。

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="从 Azure 门户启动监控范围":::

1. 在监控范围 **主页** 上，选择 " **查看见解** " 磁贴来访问 **insights** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 区域。

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="查看 Azure 门户中的见解":::

1. 在 " **见解**" :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 区域中，选择 " **扫描** " 以显示 "监控范围 **Scan Insights** 报告"。

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status"></a>查看高级 Kpi 以按状态显示扫描计数
 
高级 Kpi 显示在一段时间内运行的扫描总数。 在过去30天，时间段处于默认状态。 不过，您也可以选择最后七天。 根据时间筛选器，KPI 值会相应反映扫描的计数。


根据所选的时间筛选器值，可查看按周或关系图中的某一天分布的成功、失败和取消的扫描。

   :::image type="content" source="./media/scan-insights/scan-insights.png" alt-text="查看 Scan Insights":::

## <a name="next-steps"></a>后续步骤

了解更多有关 Azure 监控范围洞察报表和[资产见解](./asset-insights.md)的信息
