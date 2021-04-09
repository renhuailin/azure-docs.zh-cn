---
title: 查看和筛选 Azure 资源信息
description: 筛选信息并使用不同的视图来更好地了解你的 Azure 资源。
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: bb48d0b0a7bf6017fbf407a95c33ef17729e34e3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771628"
---
# <a name="view-and-filter-azure-resource-information"></a>查看和筛选 Azure 资源信息

使用 Azure 门户，你可以浏览你的各个 Azure 订阅中的资源的详细信息。 本文介绍了如何筛选信息并使用不同的视图来更好地了解你的资源。

本文重点介绍了以下屏幕截图中所示的“所有资源”屏幕。 各个资源类型（例如虚拟机）的屏幕具有不同的选项，例如启动和停止 VM。

:::image type="content" source="media/manage-filter-resource-views/all-resources.png" alt-text="所有资源的 Azure 门户视图":::

## <a name="filter-resources"></a>筛选资源

通过使用筛选器将焦点放在你的资源的某个子集上，开始探索 **所有资源**。 以下屏幕截图显示了对资源组的筛选，选择了某个订阅的六个资源组中的两个。

:::image type="content" source="media/manage-filter-resource-views/filter-resource-group.png" alt-text="基于资源组的筛选器视图":::

你可以组合筛选器（包括基于文本搜索的筛选器），如以下屏幕截图所示。 在本例中，结果的范围是已选择的两个资源组之一中包含“SimpleWinVM”的资源。

:::image type="content" source="media/manage-filter-resource-views/filter-simplewinvm.png" alt-text="基于文本输入的筛选器视图":::

若要更改视图中包括的列，请选择“管理视图”，然后选择“编辑列”。

:::image type="content" source="media/manage-filter-resource-views/edit-columns.png" alt-text="编辑视图中显示的列":::

## <a name="save-use-and-delete-views"></a>保存、使用和删除视图

你可以保存包含所选筛选器和列的视图。 若要保存并使用某个视图，请执行以下操作：

1. 选择“管理视图”，然后选择“保存视图”。

1. 输入视图的名称，然后选择“确定”。 保存的视图现在显示在“管理视图”菜单中。

    :::image type="content" source="media/manage-filter-resource-views/simple-view.png" alt-text="保存的视图":::

1. 若要使用某个视图，请在“默认”视图和你自己的某个视图之间切换，看看会如何影响所显示资源的列表。

若要删除某个视图，请执行以下操作：

1. 选择“管理视图”，然后选择“浏览所有视图”。

1. 在 **已保存视图** 窗格中，选择该视图，然后选择 **删除** 图标![删除视图图标](media/manage-filter-resource-views/icon-delete.png)。

## <a name="export-information-from-a-view"></a>从视图导出信息

可以从视图中导出资源信息。 若要以 CSV 格式导出信息，请执行以下操作：

1. 选择“导出到 CSV”。

    :::image type="content" source="media/manage-filter-resource-views/export-csv.png" alt-text="导出到 CSV 格式的屏幕截图":::

1. 将该文件保存在本地，然后在 Excel 或其他支持 CSV 格式的应用程序中打开它。 

在门户中浏览时，你会看到可以导出信息的其他区域，如单个的资源组。

## <a name="summarize-resources-with-visuals"></a>汇总具有视觉对象的资源

到目前为止，我们查看过的视图是列表视图，但还有包括视觉对象的汇总视图。 与列表视图一样，你可以保存并使用这些视图。 筛选器在两种视图类型之间保持不变。 汇总视图包含标准视图，例如下面所示的“位置”视图，以及与特定服务相关的视图，例如 Azure 存储的“状态”视图 。

:::image type="content" source="media/manage-filter-resource-views/summary-map.png" alt-text="地图视图中的资源汇总":::

若要保存并使用某个汇总视图，请执行以下操作：

1. 从“视图”菜单中选择“汇总视图”。

    :::image type="content" source="media/manage-filter-resource-views/menu-summary-view.png" alt-text="汇总视图菜单":::

1. 使用汇总视图，你可以按不同的属性（包括“位置”和“类型”）进行汇总。 选择“汇总依据”选项和相应的视觉对象。 以下屏幕截图显示了“类型汇总”和 **条形图** 视觉对象。

    :::image type="content" source="media/manage-filter-resource-views/type-summary-bar-chart.png" alt-text="显示了条形图的类型汇总":::

1. 选择“管理视图”，然后选择“保存”以保存此视图，就像对列表视图的操作一样。

1. 在汇总视图中的“类型汇总”下，选择图表中的一个条形。 选择条形后会出现一个列表，其中的资源已筛选为一种类型。

    :::image type="content" source="media/manage-filter-resource-views/all-resources-filtered-type.png" alt-text="按类型筛选的所有资源":::

## <a name="run-queries-in-azure-resource-graph"></a>在 Azure Resource Graph 中运行查询

Azure Resource Graph 提供高效且高性能的资源探索，能够跨一组订阅进行大规模查询。 Azure 门户中的“所有资源”屏幕包含一个链接，该链接用于打开作用域为当前的已筛选视图的一个 Resource Graph 查询。

若要运行 Resource Graph 查询，请执行以下操作：

1. 选择“打开查询”。

    :::image type="content" source="media/manage-filter-resource-views/open-query.png" alt-text="打开 Azure Resource Graph 查询":::

1. 在 **Azure Resource Graph Explorer** 中，选择“运行查询”以查看结果。

    :::image type="content" source="media/manage-filter-resource-views/run-query.png" alt-text="运行 Azure Resource Graph 查询":::

    有关详细信息，请参阅[使用 Azure Resource Graph Explorer 运行你的第一个 Resource Graph 查询](../governance/resource-graph/first-query-portal.md)。

## <a name="next-steps"></a>后续步骤

[Azure 门户概述](azure-portal-overview.md)

[在 Azure 门户中创建和共享仪表板](azure-portal-dashboards.md)
