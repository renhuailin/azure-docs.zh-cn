---
title: 进一步分析事件并评估影响
titleSuffix: Azure Cognitive Services
description: 了解如何利用分析工具进一步分析事件。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbullwin
ms.openlocfilehash: 0aefc4207f064550b41f9341e946cae2f1611961
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341404"
---
# <a name="further-analyze-an-incident-and-evaluate-impact"></a>进一步分析事件并评估影响

## <a name="metrics-drill-down-by-dimensions"></a>按维度向下钻取指标

查看事件信息时，可能需要获取更详细的信息，例如有关不同维度和时间戳的信息。 如果数据具有一个或多个维度，则可以使用向下钻取功能获得更详细的视图。 

若要使用向下钻取功能，请单击“事件中心”中的“指标钻取”选项卡 。 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="指标钻取":::

“维度”设置是事件的维度列表，你可以为每个事件选择其他可用的维度值。 维度值更改后。 通过“时间戳”设置即可在不同时间段查看当前事件。

### <a name="select-drilling-options-and-choose-a-dimension"></a>选择钻取选项并选择维度

向下钻取选项包含两种类型：“向下钻取”和“水平比较” 。

> [!Note]
> - 对于向下钻取，可以从不同的维度值（当前所选维度除外）浏览数据。 
> - 对于水平比较，可以从不同的维度值（所有向上的维度除外）浏览数据。

:::image type="content" source="../media/diagnostics/drill-down-dimension.png" lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="向下钻取维度":::

### <a name="value-comparison-for-different-dimension-values"></a>不同维度值的值比较

“向下钻取”选项卡的第二部分是一个表格，其中包含不同维度值的比较情况。 其中包括值、基线值、差值、增量值以及这是否为异常。
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="向下钻取比较" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>不同维度值的值和期望值比较

“向下钻取”选项卡的第三部分是一个直方图，其中包含不同维度值的值和期望值。 直方图按值与期望值之差排序。 可以轻松找到影响最大的意外值。 例如，在上图中，我们可以发现，除了所有向上的值之外，US7 对异常的影响最大。

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="向下钻取表" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>原始值可视化效果
“向下钻取”选项卡的最后一部分是原始值的折线图。 提供此图表后，无需导航到指标页面即可查看详细信息。

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="向下钻取折线图" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="compare-time-series"></a>比较时序

有时，在特定时序上检测到异常时，可将其与单个可视化效果中的多个其他序列进行比较，这十分有帮助。 单击“比较工具”选项卡，再单击蓝色的“+ 添加”按钮 。 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="添加要比较的序列" lightbox="../media/diagnostics/add-series.png":::

从数据馈送中选择一个序列。 可以选择同一种粒度，也可以选择其他粒度。 选择目标维度并加载序列趋势，然后单击“确定”，将其与以前的序列进行比较。 此序列将一起放在一个可视化效果中。 可以继续添加更多序列以进行比较，并获得更多见解。 单击“比较工具”选项卡顶部的下拉菜单，比较一段时间内的时序数据。  

> [!Warning]
> 若要进行比较，时序数据分析可能需要移动数据点，因此数据的粒度必须支持这一点。 例如，如果数据是每周数据，你使用“日同比”进行比较，则不会获得任何结果。 在本例中，你将改为使用“月同比”进行比较。

选择时移比较后，可以选择是要比较数据值、增量值还是百分比增量。

## <a name="view-similar-anomalies-using-time-series-clustering"></a>使用时序聚类分析查看类似异常

查看事件时，可以使用“类似时序聚类分析”选项卡来查看与事件相关的各种序列。 一组中的序列汇总在一起。 从上图可以看出，至少有两个序列组。 此功能仅在满足以下要求时可用：

- 指标必须具有一个或多个维度/维度值。
- 一个指标内的序列必须具有相似的趋势。

选项卡顶部列出了可用维度，你可以从中选择一个来指定序列。

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png" alt-text="序列组":::