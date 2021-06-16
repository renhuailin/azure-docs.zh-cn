---
title: 可视化时序见解资源管理器中的数据 - Azure 时序见解 Gen2 | Microsoft Docs
description: 了解 Azure 时序见解资源管理器中可用的功能和选项。
author: narmeens
ms.author: narsam
manager: cnovak
ms.reviewer: orspodek
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 08333afd9a752d7ed338ba571d36c823a3ebad09
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112060869"
---
# <a name="azure-time-series-insights-explorer"></a>Azure 时序见解资源管理器

本文介绍 Azure 时序见解 Gen2 [演示环境](https://insights.timeseries.azure.com/preview/demo)中可用的各种功能和选项。

## <a name="prerequisites"></a>先决条件

要开始使用 Azure 时序见解资源管理器，你必须：

* 预配 Azure 时序见解 Gen2 环境。 阅读 [Azure 时序见解 Gen2](./tutorial-set-up-environment.md) 教程，详细了解如何预配实例。
* 提供对为帐户创建的 Azure 时序见解 Gen2 环境的[数据访问](./concepts-access-policies.md)。 可以向其他人以及自己提供访问。
* 向 Azure 时序见解 Gen2 环境添加事件源以将数据推送到该环境：
  * 了解[如何连接到事件中心](./how-to-ingest-data-event-hub.md)
  * 了解[如何连接到 IoT 中心](./how-to-ingest-data-iot-hub.md)

## <a name="explore-the-azure-time-series-insights-explorer"></a>探索 Azure 时序见解资源管理器

Azure 时序见解资源管理器包含以下七个元素：

[![Azure 时序见解资源管理器概述](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [环境面板](#1-environment-panel)：显示所有 Azure 时序见解 Gen2 环境。
1. [导航栏](#2-navigation-bar)：用于在“分析”和“模型”页之间切换。
1. [层次结构树和搜索面板](#3-hierarchy-tree-and-search-panel)：用于选择和搜索要绘制到图表中的特定数据元素。
1. [时序井](#4-time-series-well)：显示所有当前选定的数据元素。
1. [图表面板](#5-chart-panel)：显示当前工作图表。
1. [时间线](#6-time-editor-panel)：使你可以修改工作时间跨度。
1. [应用栏](#7-app-bar)：包含用户管理选项（如当前租户），并使你可以更改主题和语言设置。

## <a name="1-environment-panel"></a>1. 环境面板

环境面板中显示你有权访问的所有 Azure 时序见解 Gen2 环境。 此列表包括 Gen2 环境以及 Gen1 环境。 只需选择要用于立即转到的环境。

1. 选择显示的环境旁边的下拉箭头。

   [![环境面板](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. 然后选择所需的环境。

## <a name="2-navigation-bar"></a>2. 导航栏

  [![导航栏](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

使用导航栏在两个视图之间进行选择：

* 分析：可用于对已建模或未建模时列数据创建图表和执行丰富分析。
* 模型：用于将新的 Azure 时序见解 Gen2 类型、层次结构和实例推送到时序模型。

### <a name="model-authoring"></a>模型创作

Azure 时序见解 Gen2 支持对时序模型执行完整的创建、读取、更新和删除 (CRUD) 操作。

[![模型搜索面板](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* 时序模型类型：可以使用时序模型类型定义用于执行计算的变量或公式。 它们与给定的时序模型实例相关联。 一个类型可以包含一个或多个变量。
* 时序模型层次结构：层次结构是数据的系统组织。 层次结构描述时序模型中不同实例之间的关系。
* 时序模型实例：这些实例是时序本身。 在大多数情况下，它们是 **DeviceID** 或 **AssetID**（环境中的资产的唯一标识符）。

若要详细了解时序模型，请参阅[时序模型](./concepts-model-overview.md)。

## <a name="3-hierarchy-tree-and-search-panel"></a>3. 层次结构树和搜索面板

使用层次结构树和搜索面板可以轻松搜索和导航[时序模型](./concepts-model-overview.md)层次结构，以找到要在图表上显示的特定时序实例。 选择实例时，不仅会将它们添加到当前图表，还会将其添加到数据。

[![层次结构树和搜索面板](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

使用搜索结果窗格，还可以在层次结构视图或列表视图中查看结果，从而方便地查找要显示的实例。

## <a name="4-time-series-well"></a>4. 时序井

井显示与所选时序模型实例关联的实例字段和其他元数据。 选中右侧的复选框可以在当前图表中隐藏或显示特定的实例。

  [![Gen2 井](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

可以通过选择元素左侧的红色“删除”（垃圾桶）控件，从当前数据井中删除特定的数据元素。 井还允许你控制每个元素在图表中的显示方式。 你可以选择添加最小/最大阴影和数据点，及时移动元素，并以步进方式可视化实例。

此外，探索控件使你可以轻松创建时间移位和散点图。

  [![井布局选项](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> 如果显示以下消息，则表示实例在所选时间跨度内没有任何数据。 若要解决该问题，请增大时间跨度或确认实例在推送数据。
>
> ![无数据通知](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. 图表面板

可以通过图表将时序实例显示为线条。 可以通过单击 Web 控件来折叠环境面板、数据模型和时间跨度控件面板，以使图表更大。

  [![Gen2 图表概述](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. 图表类型：控制可用于实现可视化效果的数据元素。

1. 间隔大小：使用间隔大小滑块工具，可以在相同时间范围内放大和缩小间隔。 这能够更精确地控制在大量时间切片之间的移动，显示平滑的趋势直至小到毫秒的切片，从而允许查看数据更精细、分辨率更高的剪切片段。 滑块的默认起点设置为所选的数据的最佳视图；平衡分辨率、查询速度和粒度。

1. 缩放和平移：选择此控件可缩放和平移图表。

1. Y 轴控件：循环显示可用 Y 轴视图选项：

    * `Stacked`：每行都具有单独的 Y 轴。
    * `Overlap`：使用此模式可在同一 Y 轴上堆叠多个线条，Y 轴数据基于所选线条而更改。
    * `Shared`：所有 Y 轴数据一起显示。

1. 标记元素：当前所选数据元素及其关联的详细信息。

可以通过单击左键单击当前图上的数据点同时按住鼠标，然后将所选区域拖动到所选终结点，进一步钻取到特定数据切片。 右键单击蓝色选定区域，然后选择“缩放”，如下所示。 还可以在所选时间跨度中查看和下载遥测事件。

  [![Gen2 图表缩放](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

执行“缩放”操作后，会显示所选的数据集。 选择格式控件以循环访问数据的三个 Y 轴表示。

  [![Gen2 图表 Y 轴](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

下面提供了重叠图表示例：

  [![重叠图表选项](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

“更多操作”按钮将展开以显示“下载为 CSV”、“连接到 Power BI”、“将图表数据显示为表”和“浏览原始事件”选项。

  [![更多操作选项](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

在 [Power BI 连接器](concepts-power-bi.md)中详细了解“连接到 Power BI”选项。

## <a name="6-time-editor-panel"></a>6. 时间编辑器面板

使用 Azure 时序见解 Gen2 时，首先要选择一个时间跨度。 所选时间跨度将控制可使用 Azure 时序见解 Gen2 更新小组件操作的数据集。

  [![时间选项面板](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> 时间线的一部分以琥珀色或橙色突出显示，以指示 warm 存储中可用的数据跨度。

以下 Web 控件在 Azure 时序见解 Gen2 中可用于选择工作时间跨度。

  [![探索井控件](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. 内部日期范围滑块控件：将两个终结点控件拖到所需的时间跨度可以使用这些控件。 此内部日期范围内受外部日期范围滑块控件约束。

1. 增加和减少日期范围按钮：通过选择任一按钮来增加或减少时间跨度，从而获得所需间隔。

1. 时间跨度折叠控件：此 Web 控件使你可以隐藏所有控件（内部日期范围滑块工具除外）。

1. 外部数据范围滑块控件：使用终结点控件选择可用于内部日期范围控件的外部日期范围。

1. 时间范围滑块控件：用于在预设时间跨度选项（如过去 30 分钟、过去 12 小时或自定义范围）之间快速切换。 更改此值也会更改间隔大小滑块工具中所讨论的可用间隔范围。

   [![目标与来源选择面板](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. 应用栏

Azure 时序见解 Gen2 导航面板显示在应用的顶部。 它提供以下功能：

### <a name="current-session-share-link-control"></a>当前会话共享链接控件

  [![“共享”图标](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

选择新的“共享”图标可与团队共享 URL 链接。

  [![共享实例 URL](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>租户分区

  [![租户选择](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* 显示当前 Azure 时序见解 Gen2 登录帐户信息。
* 使用它在可用的主题之间切换。
* 使用它查看 Gen2 [演示环境](https://insights.timeseries.azure.com/preview/demo)。

### <a name="theme-selection"></a>主题选择

若要选择新主题，请选择右上角的个人资料图标。 然后选择“更改主题”。

  [![主题选择](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> 选择个人资料图标还可以设置语言。

Azure 时序见解资源管理器支持两个主题：

* 浅色主题：本文档中显示的默认主题。
* 深色主题：如下所示呈现资源管理器：

  [![所选深色主题](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="gen1-environment-controls"></a>Gen1 环境控件

### <a name="gen2-terms-panel"></a>Gen2 术语面板

此部分仅适用于尝试在更新后的 UI 中使用资源管理器的现有 Gen1 环境。 你可能想要组合使用 Gen1 和 Gen2 产品。 我们将现有 UI 中的一些功能添加到了更新的资源管理器中，但你可以在新 Azure 时序见解资源管理器中获得 Gen1 环境的完整 UI 体验。

将显示 Azure 时序见解 Gen2 术语面板，而不是层次结构。 术语面板允许你在环境中定义查询。 使用该控件还可以基于谓词筛选数据。

  [![Where 查询面板](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Azure 时序见解 Gen2 术语编辑器面板采用以下参数：

Where：使用 where 子句可以使用下表中列出的操作数集快速筛选事件。 如果通过选择操作数来执行搜索，则谓词会基于该搜索自动更新。 支持的操作数类型包括以下各项：

| 操作    | 支持的类型    | 注释 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | 双精度、DateTime、TimeSpan | |
| `=`, `!=`, `<>` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL |
| `IN` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL | 所有操作数应为同一类型或者是 NULL 常数。 |
| `HAS` | 字符串 | 右侧只允许使用常量字符串文本。 不允许空字符串和 NULL。 |

若要详细了解支持的查询操作和数据类型，请参阅[时序表达式 (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax)。

### <a name="examples-of-where-clauses"></a>where 子句示例

  [![Where 子句示例](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

度量值：此下拉列表显示可以用作当前图表的元素的所有数值列（双精度型）。

拆分依据：此下拉列表显示模型中可用作数据分组依据的所有可用分类列（字符串）。 最多可以添加要在同一个 X 轴上查看的五个搜索词。 输入所需的参数，然后选择“添加”以添加新的搜索词。

  [![已查询和筛选视图 1](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

可以通过选择可见图标，在图表面板中显示或隐藏元素，如下图所示。 若要彻底删除查询，请选择红色的“X”。

  [![取消已查询和筛选的选项](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 了解如何将[数据引入](./concepts-ingestion-overview.md)到你的环境。

* 查看有关[存储](concepts-storage.md)的文章。

* 在 Azure 时序见解 Gen2 中阅读有关[数据建模](./concepts-model-overview.md)的内容。

* 了解[如何诊断和排查](./how-to-diagnose-troubleshoot.md)环境问题。
