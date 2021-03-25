---
title: Azure Monitor 工作簿蜂巢可视化效果
description: 了解 Azure Monitor 工作簿蜂巢可视化效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: lagayhar
ms.openlocfilehash: c91912f711b912c54c1673f2f92e998b4d9ea9db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736927"
---
# <a name="honey-comb-visualizations"></a>蜂巢可视化效果

蜂巢支持可选择性地分组为群集的指标或类别的高密度视图。 它们在直观识别热点和进一步钻取时非常有用。

下图显示了两个订阅中虚拟机的 CPU 利用率。 每个巢室表示一个虚拟机，颜色/标签表示其平均 CPU 利用率（红色表示高利用率计算机）。 虚拟机按订阅聚集。

[![屏幕截图显示两个订阅中虚拟机的 CPU 利用率](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>添加蜂巢

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用底部的“添加”，然后使用“添加查询”将日志查询控件添加到工作簿。
3. 对于“数据源”，选择“日志”；对于“资源类型”，选择“Log Analytics”；对于“资源”，指向包含虚拟机性能日志的工作区  。
4. 使用查询编辑器输入用于分析的 KQL。

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. 运行查询。
6. 将“可视化效果”设置为“图形”。
7. 选择“图形设置”。
    1. 在底部的“布局字段”中，设置：
        1. 图形类型：Hive 群集。
        2. 节点 ID：`Id`。
        3. 分组依据：`None`。
        4. 节点大小：100。
        5. 六边形之间的边距：`5`。
        6. 着色类型：热度地图。
        7. 节点颜色字段：`CouterValue`。
        8. 调色板：`Red to Green`。
        9. 最小值：`100`。
        10. 最大值：`2000`。
    2. 在顶部的“节点格式设置”中，设置：
        1. 顶部内容：
            1. 使用列：`Computer`。
            2. 列呈现器：`Text`。
        9. 中央内容：
            1. 使用列：`CounterValue`。
            2. 列呈现器：`Big Number`。
            3. 调色板：`None`。
            4. 选中“自定义数字格式”框。
            5. 单位：`Megabytes`。
            6. 最大小数位数：`1`。
8. 选择窗格底部的“保存并关闭”按钮。

[![查询控件、图形设置和蜂巢的屏幕截图，其中显示了上述查询和设置](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>蜂巢布局设置

| 设置 | 说明 |
|:------------- |:-------------|
| `Node Id` | 选择提供节点的唯一 ID 的列。 列的值可以是字符串或数字。 |
| `Group By Field` | 选择要针对其聚集节点的列。 |
| `Node Size` | 设置六边形巢室的大小。 与 `Margin between hexagons` 属性结合使用，以自定义蜂巢图的外观。 |
| `Margin between hexagons` | 设置六边形巢室之间的间隔。 与 `Node size` 属性结合使用，以自定义蜂巢图的外观。 |
| `Coloring type` | 选择要用于为节点着色的方案。 |
| `Node Color Field` | 选择提供节点区域所基于的指标的列。 |

## <a name="node-coloring-types"></a>节点着色类型

| 着色类型 | 说明 |
|:------------- |:-------------|
| `None` | 所有节点都具有相同的颜色。 |
| `Categorical` | 根据结果集中某列的值或类别为节点分配颜色。 在上述示例中，着色基于结果集的列“Kind”。 支持的调色板为 `Default`、`Pastel` 和 `Cool tone`。  |
| `Heatmap` | 在此类型中，巢室基于指标列和调色板进行着色。 这提供了一种简单方式来突出显示指标在巢室之间的分布。 |
| `Thresholds` | 在此类型中，巢室颜色由阈值规则设置（例如 CPU > 90% => 红色，60% > CPU > 90% => 黄色，CPU < 60% => 绿色） |
| `Field Based` | 在此类型中，列提供要用于节点的特定 RGB 值。 提供最大灵活性，但通常需要做更多工作才能实现。  |
      
## <a name="node-format-settings"></a>节点格式设置

蜂巢创建者可以指定节点不同部分（顶部、左侧、中心、右侧和底部）的内容。 创建者可以自由使用工作簿支持的任何呈现器（文本、大数字、走势图、图标等）。

## <a name="next-steps"></a>后续步骤

- 了解如何[在工作簿中创建复合条呈现器](workbooks-composite-bar.md)。
- 了解如何[将 Azure Monitor 日志数据导入到 Power BI 中](./powerbi.md)。