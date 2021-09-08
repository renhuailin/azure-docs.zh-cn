---
title: 向 Azure Maps Power BI 视觉对象中添加条形图层 | Microsoft Azure Maps
description: 在本文中，你将了解如何在适用于 Power BI 的 Microsoft Azure Maps 视觉对象中使用条形图层。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 4cb7fdf7f32070de66e81736d7572e0b48aad896
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429489"
---
# <a name="add-a-bar-chart-layer"></a>添加条形图层

条形图层支持在地图上以 3D 条形或圆柱体形式直观显示位置数据，有利于将数据提取到下一个维度。 同气泡层类似，条形图稍后也可以使用颜色和相对高度，同时轻松地直观显示两个指标。 为了使条形具有高度，需要向“字段”窗格的“大小”Bucket 中添加一个度量值。 如果未提供度量值，则没有高度的条形将显示为平面正方形或圆，具体取决于“条形形状”选项。

> [!div class="mx-imgBorder"]
> ![使用条形图层显示点数据的地图](media/power-bi-visual/bar-chart-layer-styled.png)

用户可以倾斜和旋转地图，从不同角度查看数据。 可以使用以下方法之一倾斜或俯仰地图。

-   打开“格式”窗格“地图设置”中的“导航控制”选项  。 这样将会添加一个用来倾斜地图的按钮。
-   按下鼠标右键，然后向上或向下拖动鼠标。
-   在触摸屏上使用双指触摸地图，并同时向上或向下拖动双指。
-   聚焦到地图后，按住 Shift 键，再按向上或向下箭头键。 

可以使用以下方法之一旋转地图。

-   打开“格式”窗格“地图设置”中的“导航控制”选项  。 这样将会添加一个用来旋转地图的按钮。
-   按下鼠标右键，然后向左或向右拖动鼠标。
-   在触摸屏上使用双指触摸地图并旋转。
-   聚焦到地图后，按住 Shift 键，再按向左或向右箭头键。  

下面是“条形图层”部分可用的“格式”窗格中的所有设置。

| 设置              | 说明      |
|----------------------|------------------|
| 条形形状            | 3D 条形的形状。<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;•框 – 以矩形框形式呈现的条形。<br/>&nbsp;&nbsp;&nbsp;&nbsp;•圆柱图 – 以圆柱图形式呈现的条形。 |
| 高度               | 每个条形的高度。 如果有字段传递到“字段”窗格的“大小”Bucket，则会相对于此高度值缩放条形。 |
| 缩放时缩放高度 | 指定是否应相对于缩放级别来缩放条形高度。 |
| 宽度                | 每个条形的宽度。  |
| 缩放时缩放宽度  | 指定是否应相对于缩放级别来缩放条形宽度。  |
| 填充颜色           | 每个条形的颜色。 当有字段传递到“字段”窗格的“图例”Bucket 时，此选项将被隐藏，“格式”窗格中将显示一个单独的“数据颜色” 部分。 |
| 透明度         | 每个条形的透明度。 |
| 最小缩放级别             | 最小缩放级别图块可用。 |
| 最大缩放级别             | 最大缩放级别图块可用。 |
| 图层位置       | 指定该层相对于其他地图层的位置。 |

> [!NOTE]
> 如果条形的宽度值较小并且“缩放时缩放宽度”选项被禁用，则在大幅放大时，它们可能会消失，因为它们呈现的宽度大小可能小于一像素。 但如果启用了“缩放时缩放宽度”选项，则会在缩放级别发生更改时执行其他计算，这可能会影响大型数据集的性能。

## <a name="next-steps"></a>后续步骤

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