---
title: Azure Monitor 工作簿树可视化效果
description: 了解所有 Azure Monitor 工作簿树可视化效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a815f4dcf7c76d4c11b4f2e227802b15f9c92a54
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100608702"
---
# <a name="tree-visualizations"></a>树可视化效果

工作簿通过树网格支持分层视图。 在树中可以展开某些行进入下一级别，以获得向下钻取体验。

以下示例显示了已可视化为树网格的容器运行状况指标（工作集大小）。 此处的顶级节点是 Azure Kubernetes 服务 (AKS) 节点，下一级别是 Pod，最终级别是容器。 请注意，仍可以像在网格中那样设置列的格式（热度地图、图标、链接）。 在本例中，基础数据源是具有 AKS 日志的 Log Analytics 工作区。

[![磁贴摘要视图的屏幕截图](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>添加树网格
1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 依次选择“添加”、“添加查询”，将日志查询控件添加到工作簿。
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析的 KQL
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. 将可视化效果设置为“网格”
6. 选择“列设置”按钮打开“设置”窗格
7. 在底部的“树/分组依据设置”部分，设置：
    * 树类型：`Parent/Child`
    * ID 字段：`Id`
    * 父 ID 字段：`ParentId`
    * 要显示展开器的列：`Name`
    * 选中“展开树的最高级别”复选框。
8. 在顶部的“列”部分，设置：
    * _ID_ - 列呈现器：`Hidden`
    * _父 ID_ - 列呈现器：`Hidden`
    * _请求_ - 列呈现器：`Bar`，颜色：`Blue`，最小值：`0`
9. 选择窗格底部的“保存并关闭”按钮。

[![屏幕截图，其中的磁贴摘要视图包含上述查询和设置。](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>树设置

| 设置 | 说明 |
|:------------- |:-------------|
| `Id Field` | 网格中每一行的唯一 ID。 |
| `Parent Id Field` | 当前行的父级的 ID。 |
| `Show the expander on` | 要显示树展开器的列。 树网格通常会隐藏其 ID 和父 ID 字段，因为这些数据的可读性不是很好。 相对而言，包含易于阅读的值的字段（例如实体名称）上会显示展开器。 |
| `Expand the top level of the tree` | 如果选中该展开器，树网格会在顶级位置展开。 如果你希望在默认情况下显示更多信息，这会很有用。 |

## <a name="grouping-in-a-grid"></a>在网格中分组

分组使你可以使用简单得多的查询来生成类似于上述视图的分层视图。 在树的内部节点上确实会丢失聚合，但这在某些情况下是可以接受的。 当基础结果集无法转换为适当的自由格式（例如，警报、运行状况和指标数据）时，请使用“分组依据”生成树状视图。

## <a name="adding-a-tree-using-grouping"></a>使用分组添加树

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 依次选择“添加”、“添加查询”，将日志查询控件添加到工作簿。
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析的 KQL
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. 将可视化效果设置为“网格”。
2. 选择“列设置”按钮打开“设置”窗格。
3. 在底部的“树/分组依据设置”部分，设置：
    * 树类型：`Group By`
    * 分组依据：`App`
    * 接下来的分组依据：`None`
    * 选中“展开树的最高级别”复选框。
4. 在顶部的“列”部分，设置：
    * 应用 - 列呈现器：`Hidden`
    * *请求* - 列呈现器：`Bar`，颜色：`Blue`，最小值：`0`
5. 选择窗格底部的“保存并关闭”按钮。

[![屏幕截图，显示在工作簿中创建树可视化效果](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 了解如何创建[工作簿中的图](workbooks-graph-visualizations.md)。
* 了解如何创建[工作簿中的磁贴](workbooks-tile-visualizations.md)。
