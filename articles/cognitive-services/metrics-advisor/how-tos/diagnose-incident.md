---
title: 使用指标顾问诊断事件
titleSuffix: Azure Cognitive Services
description: 了解如何使用指标顾问诊断事件，并详细领会数据中的异常。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbullwin
ms.openlocfilehash: dd84293ab627c1e379af1e76d259a566998cb0ea
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2021
ms.locfileid: "112410518"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>操作说明：使用指标顾问诊断事件

指标顾问提供了多种诊断功能、对检测到的事件的深入见解，以及根本原因分析。 在指标上检测到一组异常时，指标顾问会将异常分组为一个层次结构并以此为基础进行分析。

> [!NOTE]
> 当前，指标顾问支持对至少具有一个维度的指标进行事件诊断，并使用数字类型进行衡量。 指标需要具有每个维度的聚合维度值（如 SUM），用于构建诊断层次结构。 指标顾问提供[自动汇总设置](onboard-your-data.md#automatic-roll-up-settings)来帮助生成聚合值。 

单击左侧导航窗口中的“事件中心”，查看给定指标下的所有事件。 在页面顶部，可以选择不同的指标来查看其检测配置、检测结果以及更改时间范围。

> [!TIP]
> 还可以通过以下方式访问事件中心：
> * 在指标的可视化效果中单击数据点，然后使用显示的“添加反馈”窗口底部的链接。
> * 在指标的“事件”选项卡中，单击其中一个异常。 

“概述”部分包含检测结果，包括所选时间范围内的异常和警报计数。

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="事件中心" lightbox="../media/diagnostics/incident-hub-overview.png":::

事件列表中列出了在所选指标和时间范围内检测到的事件。 一些选项可用于筛选事件和对其进行排序。 例如，按严重性。 单击其中某个事件以转到“事件”页面，以进行进一步诊断。

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="事件列表" lightbox="../media/diagnostics/incident-list.png":::

通过“诊断”部分，可对事件进行深入分析，并使用工具来确定根本原因。

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="诊断事件" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>根本原因建议

当指标中检测到一组异常并导致事件时，指标顾问将尝试分析该事件的根本原因。 根本原因建议为事件的可能原因提供自动建议。 此功能仅在维度中存在聚合值时可用。 如果指标没有维度，则根本原因在于指标。 根本原因列在右侧面板中，并且可能列出了多种原因。 如果表中没有数据，则表示维度不满足执行分析的要求。

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="根本原因建议":::


为根本原因指标提供了特定维度时，可以单击“转到指标”，查看该指标的更多详细信息。

## <a name="incident-tree"></a>事件树

指标顾问不仅支持自动分析潜在的根本原因，还支持使用事件树进行手动根本原因分析。 事件页面中有两种事件树：快速诊断树和交互式树 。

快速诊断树用于诊断当前事件，根节点仅限于当前事件根节点。 可以通过单击树节点来将其展开和折叠，其序列将与当前事件序列一起显示在树上方的图表中。

通过交互式树，可诊断当前事件、旧事件以及相关事件。 使用交互式树时，右键单击节点以打开操作菜单，你可以在该菜单中选择要在根节点中向上钻取的维度，以及要为每个节点向下钻取的维度。 通过单击顶部维度列表的“取消”按钮，可以从该维度删除向上钻取或向下钻取。 左键单击某个节点，将其选中，并在图表中显示其序列以及当前事件序列。

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="事件树" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>异常向下钻取

查看事件信息时，可能需要获取更详细的信息，例如有关不同维度和时间戳的信息。 如果数据具有一个或多个维度，则可以使用向下钻取功能获得更详细的视图。 

若要使用向下钻取功能，请单击“事件中心”中的“指标钻取”选项卡 。 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="指标钻取":::

“维度”设置是事件的维度列表，你可以为每个事件选择其他可用的维度值。 维度值更改后。 通过“时间戳”设置即可在不同时间段查看当前事件。

### <a name="select-drilling-options-and-choose-a-dimension"></a>选择钻取选项并选择维度

向下钻取选项包含两种类型：“向下钻取”和“水平比较” 。

> [!Note]
> 1. 对于向下钻取，可以从不同的维度值（当前所选维度除外）浏览数据。 
> 2. 对于水平比较，可以从不同的维度值（所有向上的维度除外）浏览数据。

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

## <a name="view-similar-anomalies-using-time-series-clustering"></a>使用时序聚类分析查看类似异常

查看事件时，可以使用“类似时序聚类分析”选项卡来查看与事件相关的各种序列。 一组中的序列汇总在一起。 从上图可以看出，至少有两个序列组。 此功能仅在满足以下要求时可用：

1. 指标必须具有一个或多个维度/维度值。
2. 一个指标内的序列必须具有相似的趋势。

选项卡顶部列出了可用维度，你可以从中选择一个来指定序列。

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png" alt-text="序列组":::

## <a name="compare-time-series"></a>比较时序

有时，在特定时序上检测到异常时，可将其与单个可视化效果中的多个其他序列进行比较，这十分有帮助。 单击“比较工具”选项卡，再单击蓝色的“+ 添加”按钮 。 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="添加要比较的序列" lightbox="../media/diagnostics/add-series.png":::

从数据馈送中选择一个序列。 可以选择同一种粒度，也可以选择其他粒度。 选择目标维度并加载序列趋势，然后单击“确定”，将其与以前的序列进行比较。 此序列将一起放在一个可视化效果中。 可以继续添加更多序列以进行比较，并获得更多见解。 单击“比较工具”选项卡顶部的下拉菜单，比较一段时间内的时序数据。  

> [!Warning]
> 若要进行比较，时序数据分析可能需要移动数据点，因此数据的粒度必须支持这一点。 例如，如果数据是每周数据，你使用“日同比”进行比较，则不会获得任何结果。 在本例中，你将改为使用“月同比”进行比较。

选择时移比较后，可以选择是要比较数据值、增量值还是百分比增量。

> [!Note]
> * 数据值是原始数据值。
> * 增量值是原始值与比较值之差。
> * 百分比增量值是原始值与比较值之差除以比较值得到的值。

## <a name="related-incidents-across-metrics"></a>指标之间的相关事件

有时可能需要同时检查不同指标的事件或其他指标中的相关事件。 可以在“跨指标分析”部分中找到相关事件的列表。 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="指标之间的相关事件":::

需要在指标之间添加关系，然后才能查看当前指标的相关事件。 单击“指标图设置”添加关系。 只有维度名称相同的指标才能关联。 使用以下参数。

- 当前数据馈送和指标：当前事件的数据馈送和指标
- 方向：两个指标之间的关系方向。 （暂时不影响相关事件列表）
- 其他数据馈送和指标：与当前指标关联的数据馈送和指标


## <a name="next-steps"></a>后续步骤 

- [使用反馈调整异常检测](anomaly-feedback.md)
- [配置指标并微调检测配置](configure-metrics.md)
