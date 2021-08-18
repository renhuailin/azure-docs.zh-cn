---
title: 将图块层添加到 Azure Maps Power BI 视觉对象 | Microsoft Azure Maps
description: 在本文中，你将了解如何在适用于 Power BI 的 Microsoft Azure Maps 视觉对象中使用图块层。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 1aee3c8213221775e3fde21003055fd9cc973455
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114669344"
---
# <a name="add-a-tile-layer"></a>添加图块层

像参考图层功能一样，图块层功能允许在地图上叠加额外的数据以提供更多上下文。 通过图块层可以在 Azure Maps 基本地图图块顶部附加图像。 这是覆盖大型或复杂数据集（例如无人机的影像或数百万行数据）的一种绝佳方法。

> [!div class="mx-imgBorder"]
> ![在图块层上方显示气泡层的地图，气泡上显示来自 Azure Maps 的当前红外天气数据](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

图块层将从服务器加载图块。 这些图像可以预呈现，也可以是动态呈现。 使用图块层能理解的命名约定（像服务器上的任何其他图像一样）对预呈现图像进行存储。 动态呈现的图像使用一项服务接近实时地加载图像。 图块层是在地图上可视化大型数据集的一种绝佳方法。 不仅可以从图像中生成图块层，而且还可以将矢量数据呈现为图块层。

提供图块服务的边界框和缩放范围可以作为设置传递，以限制请求图块的位置。 这属于视觉对象和图块服务的性能增强。 下面概述了“图块层”部分提供的“格式”窗格中的所有可用设置。 

| 设置        | 说明   |
|----------------|---------------|
| Url            | 指向图块服务的格式化 URL  |
| 图块大小      | 一个整数值，用于指定图块的宽度和高度维度。   |
| 朝北    | 提供图块的边界框的北纬。 |
| 朝南    | 提供图块的边界框的南纬。 |
| 朝东     | 提供图块的边界框的东经。  |
| 朝西     | 提供图块的边界框的西经。   |
| 透明度   | 图块层的透明度。   |
| 是 TMS         | 图块映射服务，它是一种将此图块层的 Y 坐标轴反转的规范。 |
| 最小缩放级别       | 最小缩放级别图块可用。 |
| 最大缩放级别       | 最大缩放级别图块可用。  |
| 图层位置 | 指定该层相对于其他地图层的位置。 |

## <a name="tile-url-formatting"></a>图块 URL 格式

Azure Maps 视觉对象支持以下三个不同的图块服务命名约定；

-   X、Y、缩放表示法 - X 是列，Y 是图块网格中图块的行位置，缩放表示法是一个基于缩放级别的值。
-   Quadkey 表示法 - 将 x、y、缩放信息合并到单个字符串值中。 此字符串值将成为单个图块的唯一标识符。
-   边界框 - 以边界框坐标格式指定图像：`{west},{south},{east},{north}`。 [Web 映射服务 (WMS)](https://www.opengeospatial.org/standards/wms) 通常使用此格式。

图块 URL 是指向使用以下参数的图块 URL 模板的 https URL：

-   `{x}` - 图块的 X 位置。 还需要 `{y}` 和 `{z}`。
-   `{y}` - 图块的 Y 位置。 还需要 `{x}` 和 `{z}`。
-   `{z}` - 图块的缩放级别。 还需要 `{x}` 和 `{y}`。
-   `{quadkey}` - 基于必应地图图块系统命名约定的图块 `quadkey` 标识符。
-   `{bbox-epsg-3857}` - EPSG 3857 空间引用系统中格式为 `{west},{south},{east},{north}` 的边界框字符串。

例如，下面是 Azure Maps 中的[天气雷达图块服务](/rest/api/maps/render-v2/get-map-tile)的格式化图块 URL。 请注意，`[subscription-key]` 是 Azure Maps 订阅密钥的占位符。

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

有关 Azure Maps 图块系统的详细信息，请参阅[缩放级别和图块网格](zoom-levels-and-tile-grid.md)。

## <a name="next-steps"></a>后续步骤

向地图添加更多上下文：

> [!div class="nextstepaction"]
> [显示实时流量](power-bi-visual-show-real-time-traffic.md)