---
title: Azure Monitor 工作簿图形可视化效果
description: 了解所有 Azure Monitor 工作簿图形可视化效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a538eaf06013dcce6529c91816b419159a2600a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100608841"
---
# <a name="graph-visualizations"></a>图形可视化效果

工作簿支持根据日志中的数据可视化任意图形，以显示监视实体之间的关系。

下图显示了通过各种端口从一台计算机流入/流出外部计算机的数据。 这些数据按类型（计算机、端口与外部 IP）以不同的颜色显示，边缘大小对应于不同计算机之间的流动数据量。 基础数据来自针对 VM 连接的 KQL 查询。

[![磁贴摘要视图的屏幕截图](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>添加图形
1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析的 KQL。

    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```

5. 将可视化效果设置为“图形”
6. 选择“图形设置”按钮打开“设置”窗格
7. 在底部的“布局字段”中，设置：
    * 节点 ID：`Id`
    * 源 ID：`SourceId`
    * 目标 ID：`TargetId`
    * 边缘标签：`None`
    * 边缘大小：`Calls`
    * 节点大小：`None`
    * 着色类型：`Categorical`
    * 节点颜色字段：`Kind`
    * 调色板：`Pastel`
8. 在顶部的“节点格式设置”中，设置：
    * _使内容靠上_ - 使用列：`Name`，列呈现器：`Text`
    * _使内容居中_ - 使用列：`Calls`，列呈现器：`Big Number`，调色板：`None`
    * _使内容靠下_ - 使用列：`Kind`，列呈现器：`Text`
9. 选择窗格底部的“保存并关闭”按钮。

[![包含上述查询和设置的磁贴摘要视图的屏幕截图。](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>图形设置

| 设置         | 说明                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | 选择提供图形上节点的唯一 ID 的列。 列的值可以是字符串或数字。 |
| `Source Id`     | 选择为图形上的边缘提供源节点 ID 的列。 值必须映射到节点 ID 列中的值。 |
| `Target Id`     | 选择为图形上的边缘提供目标节点 ID 的列。 值必须映射到节点 ID 列中的值。 |
| `Edge Label`    | 选择在图形上提供边缘标签的列。                                                            |
| `Edge Size`     | 选择提供指标宽度所基于的指标的列。                                |
| `Node Size`     | 选择提供节点区域所基于的指标的列。                                 |
| `Coloring Type` | 用于选择节点着色方案。                                                                            |

## <a name="node-coloring-types"></a>节点着色类型

| 着色类型 | 说明 |
|:------------- |:------------|
| `None`        | 所有节点都具有相同的颜色。 |
| `Categorical` | 根据结果集中某列的值或类别为节点分配颜色。 在上述示例中，着色基于结果集的列“Kind”。 支持的调色板为 `Default`、`Pastel` 和 `Cool tone`。  |
| `Field Based` | 在此类型中，列提供要用于节点的特定 RGB 值。 提供最大灵活性，但通常需要做更多工作才能实现。  |

## <a name="node-format-settings"></a>节点格式设置

图形创建者可以指定节点不同部分（顶部、左侧、中心、右侧和底部）的内容。 图形可以自由使用工作簿支持的任何呈现器（文本、大数字、走势图、图标等）。

## <a name="field-based-node-coloring"></a>基于字段的节点着色

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析的 KQL。

     ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. 将可视化效果设置为“图形”
6. 选择“图形设置”按钮打开“设置”窗格。
7. 在底部的“布局字段”中，设置：
    * 节点 ID：`Id`
    * 源 ID：`SourceId`
    * 目标 ID：`TargetId`
    * 边缘标签：`None`
    * 边缘大小：`Calls`
    * 节点大小：`Node`
    * 着色类型：`Field Based`
    * 节点颜色字段：`Color`
8. 在顶部的“节点格式设置”中，输入以下内容。
    * 在“顶部内容集”中，设置以下项：
        * 使用列：`Name`。
        * 列呈现器：`Text`。
    * 在“中心内容”中，设置以下项：
        * 使用列：`Calls`
        * 列呈现器：`Big Number`
        * 调色板：`None`
    * 在“底部内容”中，设置以下项：
        * 使用列：`Kind`
        * 列呈现器：`Text`。
9. 选择窗格底部的“保存并关闭”按钮。

[![显示使用字段基节点着色创建图形可视化效果的屏幕截图。](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 图形也支持复合条呈现器。 若要了解详细信息，请访问[复合条文档](workbooks-composite-bar.md)。
* 详细了解可在工作簿中使用的[数据源](workbooks-data-sources.md)。
