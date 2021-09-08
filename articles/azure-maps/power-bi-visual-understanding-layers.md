---
title: 了解 Azure Maps Power BI 视觉对象中的图层 | Microsoft Azure Maps
description: 本文介绍适用于 Power BI 的 Microsoft Azure Maps 视觉对象中的不同图层。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6014bbf5ed5f3600bc79d5083f5fcc85d323acd5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123428895"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>了解 Azure Maps Power BI 视觉对象中的图层

Azure Maps 视觉对象有两种类型的图层。 第一种类型侧重于呈现传递到视觉对象的“字段”窗格的数据，包含以下图层，称为数据呈现层。

:::row:::
    :::column span="":::
        **气泡层**

        在地图上以缩放圆的形式呈现这些点。

        ![地图上的气泡层](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **条形图层**

        在地图上将点呈现为三维图条。
        
        ![地图上的条形图层](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

第二种类型的图层将额外的外部数据源连接到地图以提供更多上下文，其包含以下图层。

:::row:::
    :::column span="":::
        **引用层**

        将上传的 GeoJSON 文件覆盖在地图顶层。

        ![地图上的参考层](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **图块层**

        覆盖位于地图顶层的自定义图块层。
        
        ![地图上的图块层](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **流量层**

        覆盖地图上的实时流量信息。
        
        ![地图上的流量层](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

所有数据呈现层以及图块层都有最小和最大缩放级别选项，用于指定这些图层应显示的缩放级别范围。 这样，一种类型的呈现层可以使用一个缩放级别，并过渡到使用另一个缩放级别的另一个呈现层。

这些图层还可以选择相对于地图中的其他图层放置。 使用多个数据呈现层时，如果它们具有相同的“图层位置”值，这些图层添加到地图的顺序将确定其相对分层顺序。

## <a name="general-layer-settings"></a>常规层设置

“格式”窗格的“常规层”部分是常见设置，适用于连接到“字段”窗格中的 Power BI 数据集的图层（气泡层，条形图）。

| 设置     | 说明   |
|-------------|---------------|
| 未选择的透明度 | 选择一个或多个形状时未选择的形状的透明度。  |
| 显示零              | 指定是否应使用最小半径在地图上显示大小值为零的点。 |
| 显示负值          | 指定是否应绘制负数大小值的绝对值。   |
| 最小数据值          | 用作缩放依据的输入数据的最小值。 适用于剪切离群值。  |
| 最大数据值          | 用作缩放依据的输入数据的最大值。 适用于剪切离群值。  |

## <a name="next-steps"></a>后续步骤

更改数据在地图上的显示方式：

> [!div class="nextstepaction"]
> [添加气泡层](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加条形图层](power-bi-visual-add-bar-chart-layer.md)

向地图添加更多上下文：

> [!div class="nextstepaction"]
> [添加引用层](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [添加图块层](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [显示实时流量](power-bi-visual-show-real-time-traffic.md)
