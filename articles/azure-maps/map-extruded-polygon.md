---
title: 向地图添加多边形挤压层 | Microsoft Azure Maps
description: 如何将多边形挤压层添加到 Microsoft Azure Map Web SDK。
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: a3e357395cf75456545dfa77414c901aaebc06c0
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432623"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>向地图中添加多边形挤压层

本文介绍如何使用多边形拉伸层将 `Polygon` 和 `MultiPolygon` 特征几何体的面积渲染为挤压形状。 Azure Maps Web SDK 支持按[扩展 GeoJSON 架构](extend-geojson.md#circle)中定义的方式渲染 Circle 几何图形。 在地图上渲染时，可以将这些圆将转换为多边形。 使用 [atlas.Shape](/javascript/api/azure-maps-control/atlas.shape) 类进行包装时，可轻松更新所有特征几何图形。

## <a name="use-a-polygon-extrusion-layer"></a>使用多边形挤压层

将[多边形挤压层](/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer)连接到数据源。 然后，将其加载到地图中。 多边形挤压层会将 `Polygon` 和 `MultiPolygon` 特征的区域呈现为挤压形状。 多边形挤压层的 `height` 和 `base` 属性定义了与挤压形状的地面和高度的基准距离（以米为单位）。 下面的代码演示如何创建一个多边形，如何将其添加到数据源中，以及如何使用多边形挤压层类进行呈现。

> [!Note]
> 多边形挤压层中定义的 `base` 值应小于或等于 `height` 的值。

<br/>

<iframe height="500" scrolling="no" title="挤压多边形" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>挤压多边形</a>。</iframe>

## <a name="add-data-driven-polygons"></a>添加数据驱动多边形

可以使用多边形挤压层来呈现分级统计图。 将挤压层的 `height` 和 `fillColor` 属性设置为 `Polygon` 和 `MultiPolygon` 特征几何形状中统计变量的度量。 下面的代码示例根据状态的人口密度度量值显示美国的挤压等值线图地图。

<br/>

<iframe height="500" scrolling="no" title="挤压的等值线图映射" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅在 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/eYYYNox'>挤压等值线图地图</a>。
</iframe>

## <a name="add-a-circle-to-the-map"></a>将圆添加到地图

Azure Maps 使用扩展版本的 GeoJSON 架构提供圆的定义，如[此处](./extend-geojson.md#circle)所述。 可以通过创建一个 `point`（`subType` 属性为 `Circle`）和一个编号的 `Radius` 属性（以米为单位表示半径），在地图上呈现挤压圆。 例如：

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Maps Web SDK 在后台将这些 `Point` 特征转换为 `Polygon` 特征。 然后，使用多边形挤压层在地图上渲染这些 `Point` 特征，如下面的代码示例中所示。

<br/>

<iframe height="500" scrolling="no" title="无人机空域多边形" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>无人机空域多边形</a>。
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>自定义多边形挤压层

多边形挤压层有多个样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [多边形](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [多边形挤压层](/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer)

其他资源：

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON 规范扩展](extend-geojson.md#circle)