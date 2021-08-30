---
title: Azure Monitor 工作簿概述
description: 了解工作簿如何提供灵活的画布来分析数据以及在 Azure 门户中创建丰富的视觉报告。
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: c9a535e06278146f89f2c8f6a1844813c2417a1a
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114706921"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor 工作簿

工作簿提供了一块灵活的画布，以用于分析数据以及在 Azure 门户中创建丰富的视觉报告。 使用工作簿可以在整个 Azure 中接入多个数据源，并将其组合成统一的交互式体验。

下面是有关如何创建工作簿的视频演练。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

> [!NOTE]
> 旧工作簿和专用工作簿已删除。 使用[工作簿检索工具](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/LegacyAI/DeprecatedWorkbookRetrievalTool.md)检索旧工作簿的内容。

## <a name="data-sources"></a>数据源

工作簿可以查询 Azure 中多个源的数据。 工作簿的作者可以转换此数据，以提供对底层组件的可用性、性能、使用情况和总体运行状况的见解。 例如，分析虚拟机的性能日志可以识别 CPU 使用率较高或内存使用量较低的实例，并在交互式报告中以网格形式显示结果。
  
但是，工作簿的真正强大之处是能够将来自不同源的数据组合到一份报告中。 这样，便可以创建复合资源视图或者在不同的资源之间进行联接，以提供更丰富的数据和见解，除此之外别无他法。

工作簿目前与以下数据源兼容：

* [日志](../visualize/workbooks-data-sources.md#logs)
* [度量值](../visualize/workbooks-data-sources.md#metrics)
* [Azure Resource Graph](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [警报（预览版）](../visualize/workbooks-data-sources.md#alerts-preview)
* [工作负载运行状况](../visualize/workbooks-data-sources.md#workload-health)
* [Azure 资源运行状况](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure 数据资源管理器](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>可视化效果

工作簿提供了一组丰富的功能以用于将数据可视化。 有关每种可视化效果类型的详细示例，可以参考下面的链接：

* [文本](../visualize/workbooks-text-visualizations.md)
* [图表](../visualize/workbooks-chart-visualizations.md)
* [网格](../visualize/workbooks-grid-visualizations.md)
* [平铺](../visualize/workbooks-tile-visualizations.md)
* [树](../visualize/workbooks-tree-visualizations.md)
* [关系图](../visualize/workbooks-graph-visualizations.md)
* [复合条](../visualize/workbooks-composite-bar.md)
* [蜂巢](workbooks-honey-comb.md)
* [Map](workbooks-map-visualizations.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="工作簿可视化效果的示例。" border="false" lightbox="./media/workbooks-overview/visualizations.png":::

### <a name="pinning-visualizations"></a>固定可视化效果

当工作簿处于固定模式，或者工作簿作者已启用显示图钉图标的设置时，可以使用工作簿中的文本、查询和指标步骤上的图钉按钮，来固定这些项。

要访问 pin 模式，请选择“编辑”来进入编辑模式，然后选择顶部栏中的蓝色图钉图标。 然后，单个图钉图标将显示在屏幕右侧的每个对应工作簿部分的“编辑”框的上方。

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="固定体验的屏幕截图。" border="false":::

> [!NOTE]
> 固定时会保存工作簿的状态。如果修改基础工作簿，仪表板上固定的工作簿不会更新。 若要更新固定的工作簿部分，需要删除该部分，然后将其重新固定。

## <a name="getting-started"></a>入门

若要探索工作簿体验，请先导航到 Azure Monitor 服务。 为此，可以在 Azure 门户的搜索框中键入 **Monitor**。

然后选择“工作簿”。

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="在红色框中突出显示的工作簿按钮的屏幕截图。" border="false":::

### <a name="gallery"></a>库

通过库，可以方便地对所有类型的工作簿进行组织、排序和管理。

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="“全部”选项卡上的库的屏幕截图。" lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>库选项卡

库中有四个帮助整理工作簿类型的选项卡。

| 选项卡              | 说明                                       |
|------------------|---------------------------------------------------|
| 全部 | 显示每个类型（工作簿、公共模板和我的模板）的前四项。 工作簿按修改日期排序，你会看到八个最近修改的工作簿。|
| 工作簿 | 显示你创建的或与你共享的所有可用工作簿的列表。 |
| 公共模板 | 显示 Microsoft 发布的所有可供使用的入门功能工作簿模板的列表。 按类别分组。 |
| 我的模板 | 显示你创建的或与你共享的所有可用的已部署工作簿模板的列表。 按类别分组。 |

#### <a name="features"></a>功能

* 每个选项卡中都有一个网格，其中包含有关工作簿的信息。 包括说明、上次修改日期、标记、订阅、资源组、区域和共享状态。 你还可以通过此信息对工作簿进行排序。
* 按资源组、订阅、工作簿/模板名称或模板类别进行筛选。
* 选择多个要删除或批量删除的工作簿。
* 每个工作簿都有一个上下文菜单（末尾的省略号/三个点），选择它将打开快速操作的列表。
    * 查看资源 - 访问工作簿资源选项卡，以查看工作簿的资源 ID、添加标记、管理锁等。
    * 删除或重命名工作簿。
    * 将工作簿固定到仪表板。

### <a name="workbooks-versus-workbook-templates"></a>工作簿与工作簿模板

可以看到一个绿色的工作簿，以及多个紫色的工作簿模板。  模板充当特选的报告，可供多个用户和团队灵活重复使用。 打开某个模板会创建一个临时工作簿，其中填充了该模板的内容。

可以调整基于模板的工作簿的参数并执行分析，而无需担心将来会影响同事的报告体验。 如果打开某个模板，进行一些调整，然后选择“保存”图标，此时会将该模板保存为工作簿，该工作簿将以绿色显示，而原始模板保持不变。

在幕后，模板也与保存的工作簿不同。 保存工作簿会创建关联的 Azure 资源管理器资源，而在打开模板时创建的临时工作簿则没有关联的唯一资源。 若要详细了解如何在工作簿中管理访问控制，请参阅[工作簿访问控制](../visualize/workbooks-access-control.md)一文。

### <a name="exploring-a-workbook-template"></a>探索工作簿模板

选择“应用程序故障分析”以查看默认应用程序工作簿模板之一。

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="应用程序故障分析模板的屏幕截图。" border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

如前文所述，打开模板会创建一个可让你与之交互的临时工作簿。 默认情况下，该工作簿以读取模式打开，在此模式下，只会显示原始模板作者按其目标所创建的分析体验的信息。

对于此特定工作簿，体验是交互式的。 可以调整订阅、目标应用，以及要显示的数据的时间范围。 做出这些选择后，“HTTP 请求”的网格也是交互式的，选择单个行会更改要在报告底部的两个图表中呈现的数据。

### <a name="editing-mode"></a>编辑模式

若要了解此工作簿模板的构成，需要选择“编辑”切换到编辑模式。

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="工作簿中“编辑”按钮的屏幕截图。" border="false" :::

切换到编辑模式后，你会发现右侧显示了一些“编辑”框，它们对应于工作簿的每个方面。

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="“编辑”按钮的屏幕截图。" border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

如果选择紧靠在请求数据网格下面的编辑按钮，可以看到此工作簿部分包含一个 Kusto 查询，该查询针对的是 Application Insights 资源中的数据。

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="基础 Kusto 查询的屏幕截图。" border="false" lightbox="./media/workbooks-overview/kusto.png":::

选择右侧的其他“编辑”按钮会显示一些构成了工作簿的核心组件，例如基于 Markdown 的[文本框](../visualize/workbooks-text-visualizations.md)、[参数选择](../visualize/workbooks-parameters.md) UI 元素，以及其他[图表/可视化类型](#visualizations)。

在编辑模式下探索预生成的模板，然后根据需要对其进行修改并保存自己的自定义工作簿，是开始了解 Azure Monitor 工作簿功能的极佳方式。

## <a name="dashboard-time-ranges"></a>仪表板时间范围

如果固定的项配置为使用“时间范围”参数，则固定的工作簿查询部分将遵循仪表板的时间范围。 仪表板的时间范围值将用作时间范围参数的值，对仪表板时间范围进行任何更改会导致固定的项更新。 如果某个固定的部分正在使用仪表板的时间范围，则每当仪表板的时间范围发生更改时，你都会看到该固定部分的副标题更新为显示该时间范围。

此外，使用时间范围参数的固定工作簿部分将按照仪表板时间范围确定的频率自动刷新。 上次运行查询的时间将显示在固定部分的副标题中。

如果某个固定步骤具有显式设置的时间范围（不使用时间范围参数），则无论仪表板的设置如何，都始终会将该时间范围用于仪表板。 固定部分的副标题不会显示仪表板的时间范围，并且查询不会在仪表板上自动刷新。 副标题将显示上次执行查询的时间。

> [!NOTE]
> 目前，使用“合并”数据源的查询在固定到仪表板时不受支持。

## <a name="sharing-workbook-templates"></a>共享工作簿模板

开始创建自己的工作簿模板后，你可能希望将其与更广泛的社区成员共享。 若要了解详细信息，或要探索其他未包含在默认 Azure Monitor 库视图中的模板，请访问我们的 [GitHub 存储库](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)。 若要浏览现有的工作簿，请访问 GitHub 上的[工作簿库](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks)。


## <a name="next-step"></a>后续步骤

* [开始](#visualizations)详细了解工作簿的丰富可视化选项。
* [控制](../visualize/workbooks-access-control.md)并共享对工作簿资源的访问权限。