---
title: Azure Monitor 工作簿复合条呈现器
description: 了解所有 Azure Monitor 工作簿复合条呈现器可视化效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.author: lagayhar
ms.openlocfilehash: 9a02299853174192c6963cbb382ceb1aa06ac088
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728597"
---
# <a name="composite-bar-renderer"></a>复合条呈现器

工作簿允许使用复合条（由多个条形组成的条）呈现数据。

下图显示了数据库状态的复合条，表示有多少服务器处于联机状态、脱机状态和正在恢复的状态。

![数据库状态复合条的屏幕截图。](./media/workbooks-composite-bar/database-status.png)

网格、图块和图形可视化效果均支持复合条呈现器。

## <a name="adding-composite-bar-renderer"></a>添加复合条呈现器

1. 选择“编辑”工具栏项，将工作簿切换到编辑模式。
2. 选择“添加”，然后选择“添加查询” 。
3. 将“数据源”设置为“JSON”，并将“可视化效果”设置为“网格” 。
4. 添加以下 JSON 数据。

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. 运行查询。
6. 选择“列设置”以打开设置。
7. 从“列”中选择“总计”，然后为“列呈现器”选择“复合条” 。
8. 在“复合条设置”下设置以下设置。

| 列名 | 颜色        |
|-------------|--------------|
| 联机      | 绿色        |
| 恢复  | Yellow       |
| offline     | 红色(亮) |

9. 添加标签：`["online"] of ["total"] are healthy`
10. 在“联机”、“脱机”和“正在恢复”列设置中，可以将列呈现器设置为“隐藏”（可选）。
11. 选择顶部的“标签”，然后将总计列的标签更新为“数据库状态”（可选）。
12. 选择“应用”

复合条设置将类似于以下屏幕截图：

![具有上述设置的复合条设置的屏幕截图。](./media/workbooks-composite-bar/composite-bar-settings.png)

具有上述设置的复合条：

![复合条的屏幕截图。](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>复合条设置

选择列名和相应颜色，可将该颜色的相应列呈现为复合条的一部分。 可以插入、删除和上下移动行。

### <a name="label"></a>Label

复合条标签显示在复合条的顶部。 可以搭配使用静态文本、列和参数。  如果“标签”为空，则当前列的值将显示为标签。 在前面的示例中，如果将标签字段保留为黑色，则将显示总计列的值。

请参考具有 `["columnName"]` 的列。

请参考具有 `{paramName}` 的参数。

列名和参数名均区分大小写。 还可以通过选择“将此项设为链接”使标签成为链接，然后添加链接设置。

### <a name="aggregation"></a>聚合

聚合对于“树/分组依据”可视化效果很有用。 组行的列数据由相应列的聚合集决定。 有三种类型的聚合适用于复合条：None、Sum 和 Inherit。

若要添加“分组依据”设置，请执行以下操作：

1. 在列设置中，转到要向其添加设置的列。
2. 在“树类型”下的“树/分组依据设置”中，选择“分组依据” 
3. 选择要按其分组的字段。

![分组依据设置的屏幕截图。](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>无

None 聚合表示对于组行，该列不显示任何结果。

![使用 None 聚合的复合条的屏幕截图。](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>Sum

如果将聚合设置为 Sum，则组行中的列将使用用于呈现它的列的总和来显示复合条。 标签还将使用其中引用的列的总和。

在下面的示例中，所有“联机”、“脱机”和“正在恢复”列均将最大聚合设置为 sum，并且总计列的聚合为 sum。

![使用 sum 聚合的复合条的屏幕截图。](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>继承

如果将聚合设置为 inherit，则组行中的列将使用用于呈现它的列的用户设置的聚合来显示复合条。 标签中使用的列也使用用户设置的聚合。 如果当前的列呈现器是复合条且在标签中被引用（如上面示例中的“总计”），则 sum 将用作相应列的聚合。

在下面的示例中，所有“联机”、“脱机”和“正在恢复”列均将最大聚合设置为自身，并且总计列的聚合是 inherit。

![使用 inherit 聚合的复合条的屏幕截图。](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>排序

对于网格可视化效果，使用复合条呈现器对列的行进行排序的依据是一个值，该值是用于动态呈现复合条计算机的列的总和。 在前面的示例中，用于排序的值是该特定行的“联机”、“正在恢复”和“脱机”列的总和。

## <a name="tiles-visualization"></a>图块可视化效果

1. 选择“添加”，然后选择“添加查询”。
2. 将数据源更改为 JSON，然后输入[上一个示例](#adding-composite-bar-renderer)中的数据。
3. 将可视化效果更改为“图块”。
4. 运行查询。
5. 选择“图块设置”。
6. 在“图块”字段中选择“左”。
7. 在“字段设置”下输入以下设置。
    1. 使用列：“服务器”。
    2. 列呈现器：“文本”。
8. 在“图块”字段中选择“底部”。
9. 在“字段设置”下输入以下设置。
    1. 使用列：“总计”。
    2. 列呈现器：“复合条”。
    3. 在“复合条设置”下输入“设置以下设置”。

    | 列名 | 颜色        |
    |-------------|--------------|
    | 联机      | 绿色        |
    | 恢复  | Yellow       |
    | offline     | 红色(亮) |

    4. 添加标签：`["online"] of ["total"] are healthy`。
10. 选择“应用”。

图块的复合条设置：

![具有上述设置的复合条图块设置的屏幕截图。](./media/workbooks-composite-bar/tiles-settings.png)

具有上述设置的“图块”的“复合条”视图将如下所示：

![复合条图块的屏幕截图。](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>图形可视化效果

若要制作用于图形可视化效果（类型为 Hive 群集）的复合条呈现器，请按照以下说明操作。

1. 选择“添加”，然后选择“添加查询”。
2. 将数据源更改为 JSON，然后输入[上一个示例](#adding-composite-bar-renderer)中的数据。
3. 将可视化更改为“图形”。
4. 运行查询。
5. 选择“图形设置”。
6. 在“节点格式设置”中选择“中心内容”。
7. 在“字段设置”下输入以下设置。
    1. 使用列：“总计”。
    2. 列呈现器：“复合条”。
    3. 在“复合条设置”下输入以下设置。

    |列名  |     颜色    |
    |-------------|--------------|
    | 联机      | 绿色        |
    | 恢复  | Yellow       |
    | offline     | 红色(亮) |

   4. 添加标签：`["online"] of ["total"] are healthy`。
9. 在“布局设置”下输入以下设置。
    1. 图形类型：Hive 群集。
    2. 节点 ID 选择：“服务器”。
    3. 分组依据字段：“无”。
    4. 节点大小：100。
    5. 六边形之间的边距：5。
    6. “着色类型”：**无**。
1. 选择“应用”。
    
图形的复合条设置：

![具有上述设置的复合条图形设置的屏幕截图。](./media/workbooks-composite-bar/graphs-settings.png)

具有上述设置的“图形”的“复合条”视图将如下所示：

![具有 hive 群集的复合条图形的屏幕截图。](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>后续步骤

* 使用 Azure 资源管理器[部署](../visualize/workbooks-automate.md)工作簿。
* [控制](./workbooks-access-control.md)和共享对工作簿资源的访问权限。