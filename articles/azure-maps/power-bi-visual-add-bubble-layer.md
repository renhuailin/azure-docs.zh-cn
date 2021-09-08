---
title: 将气泡层添加到 Azure Maps Power BI 视觉对象 | Microsoft Azure Maps
description: 在本文中，你将了解如何在适用于 Power BI 的 Microsoft Azure Maps 视觉对象中使用气泡层。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: bc463a69dc0823e14b08e9fd056f3876e1ba9984
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430443"
---
# <a name="add-a-bubble-layer"></a>添加气泡层

气泡层将位置数据渲染为地图上的缩放圆圈。

> [!div class="mx-imgBorder"]
> ![使用气泡层显示点数据的地图](media/power-bi-visual/bubble-layer-with-legend-color.png)

最初，所有气泡均具有相同的填充颜色。 如果将字段传递到“字段”窗格的“图例”Bucket 中，则气泡将基于其分类进行着色。 气泡的轮廓默认为白色，但可以更改为新颜色，也可以通过启用高对比度轮廓选项来更改。 “高对比度轮廓”选项动态分配属于填充颜色的高对比度变体的轮廓颜色。 这有助于确保气泡清晰可见，而不必考虑地图的样式。 下面是“气泡层”部分提供的“格式”窗格中的主要设置。

| 设置               | 说明    |
|-----------------------|----------------|
| 大小                  | 每个气泡的大小。 如果将字段传递到“字段”窗格的“大小”Bucket 中，此选项将被隐藏。 其他选项将按本文后面的 [气泡大小缩放](#bubble-size-scaling)主题中所述显示。 |
| 填充颜色            | 每个气泡的颜色。 如果将字段传递到“字段”窗格的“图例”Bucket 中，此选项将被隐藏，而单独的“数据颜色”部分将显示在“格式”窗格中。 |
| 填充透明度     | 每个气泡的透明度。 |
| 高对比度轮廓 | 通过使用填充颜色的高对比度变体，使轮廓颜色与填充颜色呈对比色来提高可访问性。 |
| 轮廓颜色         | 用于勾勒气泡轮廓的颜色。 启用“高对比度轮廓”选项后，此选项将被隐藏。 |
| 轮廓透明度  | 轮廓的透明度。 |
| 轮廓宽度         | 轮廓的宽度（以像素为单位）。 |
| 模糊                  | 应用于轮廓的模糊程度。 值 1 会使气泡变得模糊，以便只有中心点没有透明度。 值 0 会应用任何模糊效果。 |
| 俯仰角对齐       | 指定在俯仰查看地图时气泡的呈现效果。 <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• 视区 - 气泡显示在地图上相对于视区的边缘上。 （默认值）<br/>&nbsp;&nbsp;&nbsp;&nbsp;• 地图 - 气泡呈现在地图的平面上。 |
| 缩放比例            | 气泡应相对于缩放级别缩放的量。 缩放比例 1 表示无缩放。 缩小时，值越大，气泡越小；放大时，值越大，气泡越大。 这有助于在缩小时减少地图上的混乱，同时在放大时确保点更突出。 值 1 不会应用任何缩放。 |
| 最小缩放级别              | 最小缩放级别图块可用。 |
| 最大缩放级别              | 最大缩放级别图块可用。 |
| 图层位置        | 指定该层相对于其他地图层的位置。 |

## <a name="bubble-size-scaling"></a>气泡大小缩放

如果将字段传递到“字段”窗格的“大小”Bucket 中，则气泡将相对于每个数据点的度量值进行缩放。 如果将字段传递到“大小”Bucket 中，“格式”窗格的“气泡层”部分中的“大小”选项将消失，因为气泡将在最小值和最大值之间进行半径缩放。 如果“大小”Bucket 已指定字段，则以下选项将显示在“格式”窗格的“气泡层”部分中。

| 设置             | 说明  |
|---------------------|--------------|
| 最小大小            | 缩放数据时的最小气泡大小。|
| 最大大小            | 缩放数据时的最大气泡大小。|
| 大小缩放方法 | 用于确定相对气泡大小的缩放算法。<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;•线性 - 以线性方式映射到最小大小和最大大小的输入数据的范围。 （默认值）<br/>&nbsp;&nbsp;&nbsp;&nbsp;•对数 - 以对数形式映射到最小大小和最大大小的输入数据的范围。<br/>&nbsp;&nbsp;&nbsp;&nbsp;• 三次方贝塞尔曲线 - 指定用于创建自定义缩放方法的三次方贝塞尔曲线的 X1、Y1、X2、Y2 值。 |

将“大小缩放方法”设置为“对数”时，可以使用以下选项。

| 设置   | 说明      |
|-----------|------------------|
| 对数刻度 | 计算气泡大小时要应用的对数刻度。 |

将“大小缩放方法”设置为“三次方贝塞尔曲线”时，可以使用以下选项自定义缩放曲线。

| 设置 | 说明                           |
|---------|---------------------------------------|
| X1      | 三次方贝塞尔曲线的 X1 参数。 |
| 是1      | 三次方贝塞尔曲线的 X2 参数。 |
| X2      | 三次方贝塞尔曲线的 Y1 参数。 |
| 是2      | 三次方贝塞尔曲线的 Y2 参数。 |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/) 具有一个可用来为三次方贝塞尔曲线创建参数的易用工具。

## <a name="next-steps"></a>后续步骤

更改数据在地图上的显示方式：

> [!div class="nextstepaction"]
> [添加条形图层](power-bi-visual-add-bar-chart-layer.md)

向地图添加更多上下文：

> [!div class="nextstepaction"]
> [添加引用层](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [添加图块层](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [显示实时流量](power-bi-visual-show-real-time-traffic.md)

自定义视觉对象：

> [!div class="nextstepaction"]
> [Power BI 中颜色格式设置的提示和技巧](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [自定义可视化效果的标题、背景和图例](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)