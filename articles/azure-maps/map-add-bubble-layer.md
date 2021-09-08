---
title: 向地图添加气泡层 | Microsoft Azure Maps
description: 了解如何将地图上的点呈现为固定大小的圆。 请参阅如何使用 Azure Maps Web SDK 添加和自定义气泡图层以实现此目标。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: d39e926a4f8e2b8501f2bb366b6e5953dd5b5a94
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438347"
---
# <a name="add-a-bubble-layer-to-a-map"></a>将气泡层添加地图

本文介绍如何将数据源中的点数据呈现为地图上的气泡层。 气泡层将点呈现为地图上具有固定像素半径的圆。 

> [!TIP]
> 默认情况下，气泡层将呈现数据源中所有几何图形的坐标。 若要限制该层以便仅呈现点几何功能，请将该层的 `filter` 属性设置为 `['==', ['geometry-type'], 'Point']` 或 `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`（如果还想包含 MultiPoint 特征）。

## <a name="add-a-bubble-layer"></a>添加气泡层

下面的代码将一个点数组加载到数据源中。 然后会将数据点连接到[气泡层](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)。 气泡层用五像素呈现每个气泡的半径，并使用白色为填充色。 描边色为蓝色，描边宽度为六像素。 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>显示具有气泡层的标签

此代码演示如何使用气泡图层呈现地图上的点。 以及如何使用符号层呈现标签。 若要隐藏符号层的图标，请将图标选项的 `image` 属性设置为 `'none'`。

<br/>

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

## <a name="customize-a-bubble-layer"></a>自定义气泡层

气泡层只有几个样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='气泡层选项' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>通过 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 查看 Pen <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>气泡层选项</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [示例代码](/samples/browse/?products=azure-maps)