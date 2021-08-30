---
title: Azure Purview 中的数据扫描见解
description: 本操作指南介绍了如何查看和使用数据的 Purview Insights 扫描报表。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 3c5a3b9b312e093d7bee5c0c555e8e2eab0c531b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731140"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Azure Purview 中的数据扫描见解

本操作指南介绍了如何访问、查看和筛选数据的 Azure Purview 扫描见解报表。

在本操作指南中，你将学习如何：

> [!div class="checklist"]
> * 从 Purview 帐户查看见解。
> * 获取扫描的鸟瞰视图。

## <a name="prerequisites"></a>先决条件

在开始利用 Purview 见解之前，请确保已完成以下步骤：

* 设置 Azure 资源，并在该帐户中填充数据。
* 设置并完成数据源扫描。

有关详细信息，请参阅[在 Azure Purview（预览版）中管理数据源](manage-data-sources.md)。

## <a name="use-purview-scan-insights"></a>使用 Purview 扫描见解

在 Azure Purview 中，可以注册和扫描源类型。 你可以在“扫描见解”中查看一段时间内的扫描状态。 见解会告诉你在某个时间段内失败、成功或取消了多少次扫描。

### <a name="view-scan-insights"></a>查看扫描见解

1. 在 Azure 门户中转到“Azure Purview”实例屏幕，并选择 Purview 帐户。

1. 在“概述”页上的“入门”部分中，选择“打开 Purview Studio”磁贴。  

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="从 Azure 门户启动 Purview":::

1. 在 Purview 的“主页”上，选择“查看见解”磁贴来访问“见解”:::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 区域  。

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="在 Azure 门户中查看见解":::

1. 在“见解”:::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 区域中，选择“扫描”以显示 Purview“扫描见解”报表。  

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>查看高级 KPI 以按状态显示扫描计数，并深入了解每个扫描
 
1. 高级 KPI 显示在一段时间内运行的扫描总数。 时间段默认在过去 30 天内。 但是，也可以选择最后七天。 根据时间筛选器，KPI 值会相应地反映扫描计数。


1. 根据所选的时间筛选器值，可在图中看到按周或某一天分布的成功、失败和取消的扫描。

1. 在图的底部，有一个“查看更多”链接供你进一步浏览。 该链接将在“扫描见解”体验中打开“扫描状态”页。 可在此处查看扫描名称，以及过去 30 天内已成功、失败或取消的次数。

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="查看一段时间内的扫描状态":::

4. 单击“扫描名称”，会将你连接到 Azure Purview 的“数据映射”体验内的扫描历史记录，可进一步探索特定扫描。  在运行历史记录页上，可以获取有助于进一步调查失败的运行 ID。

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="查看扫描详细信息":::

5. 最后，可以通过跟踪运行历史记录页左上角的痕迹导航，返回到“扫描见解”的“扫描状态”页。

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="查看扫描历史记录"::: 

## <a name="next-steps"></a>后续步骤

* 了解有关 Azure Purview 的“见解”和[数据见解](./concept-insights.md)的详细信息

* 在[管理数据源](./manage-data-sources.md)中，详细了解 Azure Purview 的“数据映射”体验