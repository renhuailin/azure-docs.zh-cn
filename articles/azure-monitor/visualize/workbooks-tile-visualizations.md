---
title: Azure Monitor 工作簿磁贴可视化效果
description: 了解所有 Azure Monitor 工作簿磁贴可视化效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 07de9fcd01559e59a1bcd5d8928075aebcf79cc7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100607620"
---
# <a name="tile-visualizations"></a>磁贴可视化

磁贴适用于在工作簿中呈现摘要数据。 下图显示了磁贴的常见用例，应用级别摘要位于详细网格的顶部。

[![磁贴摘要视图的屏幕截图](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

工作簿磁贴支持显示标题、副标题、大文本、图标、基于指标的渐变、缩微折线图/条形图、页脚等。

## <a name="adding-a-tile"></a>添加磁贴

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 依次选择“添加”、“添加查询”，将日志查询控件添加到工作簿。
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析的 KQL。

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. 将“大小”设置为“完整”。
6. 将可视化效果设置为“磁贴”。
7. 选择“磁贴设置”按钮以打开“设置”窗格。
    1. 在“标题”中，设置：
        * 使用列：`name`。
    2. 在“左侧”中，设置：
        * 使用列：`Requests`。
        * 列呈现器：`Big Number`。
        * 调色板：`Green to Red`
        * 最小值：`0`。
    3. 在“底部”中，设置：
        * 使用列：`appName`。
8. 选择窗格底部的“保存并关闭”按钮。

[![屏幕截图，其中的磁贴摘要视图包含上述查询和磁贴设置。](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

读取模式下的磁贴：

[![读取模式下的磁贴摘要视图的屏幕截图。](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>磁贴中的 Spark 行

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 添加一个名为 `TimeRange` 的时间范围参数。
    1. 依次选择“添加”、“添加参数”。
    2. 在参数控件中，选择“添加参数”。
    3. 在“参数名称”字段中输入 `TimeRange`，然后选择 `Time range picker` 作为“参数类型”。
    4. 选择窗格顶部的“保存”，然后在参数控件中选择“完成编辑”。
3. 依次选择“添加”、“添加查询”，以便在参数控件下方添加日志查询控件。
4. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
5. 使用查询编辑器输入用于分析的 KQL。

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. 选择“运行查询”。 （请确保在运行查询之前将 `TimeRange` 设置为所选的值。）
7. 将可视化效果设置为“磁贴”。
8. 将“大小”设置为“完整”。
9. 选择“磁贴设置”。
    1. 在“磁贴”中，设置：
        * 使用列：`name`。
    2. 在“子磁贴”中，设置：
        *  使用列：`appNAme`。
    3. 在“左侧”中，设置：
        *  使用列：`Requests`。
        * 列呈现器：`Big Number`。
        * 调色板：`Green to Red`。
        * 最小值：`0`。
    4. 在“底部”中，设置：
        * 使用列：`Trend`。
        * 列呈现器：`Spark line`。
        * 调色板：`Green to Red`。
        * 最小值：`0`。
10. 选择窗格底部的“保存并关闭”。

![包含 spark 行的磁贴可视化效果的屏幕截图](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>磁贴大小

创建者可以选择在磁贴设置中设置磁贴宽度。

* `fixed`（默认值）

    磁贴的默认行为是相同的固定宽度（大约为 160 像素宽）加上磁贴周围的空间。

    ![显示固定宽度磁贴的屏幕截图](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    每个标题都会根据其内容缩小或增大，但磁贴被限制为磁贴视图（无水平滚动）的宽度。

    ![显示自动宽度磁贴的屏幕截图](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    每个标题将始终为磁贴视图的整个宽度，每行一个标题。

     ![显示完整大小宽度磁贴的屏幕截图](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>后续步骤

* 磁贴还支持复合条呈现器。 若要了解详细信息，请访问[复合条文档](workbooks-composite-bar.md)。
* 若要详细了解 `TimeRange` 等时间参数，请访问[工作簿时间参数文档](workbooks-time.md)。