---
title: 向地图添加线条层 | Microsoft Azure Maps
description: 了解如何向地图添加线条。 请参阅示例，了解如何使用 Azure Maps Web SDK 向地图添加线条层并使用符号和颜色渐变自定义线条。
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 8c2a0bd9a6923d0ad03a64c90db6832d4ea34145
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437069"
---
# <a name="add-a-line-layer-to-the-map"></a>向地图添加线条层

线条层可用于将 `LineString` 和 `MultiLineString` 特征呈现为地图上的路径或路由。 线条层还可用于呈现 `Polygon` 和 `MultiPolygon` 特征的轮廓。 数据源连接到线条层以向它提供要呈现的数据。 

> [!TIP]
> 默认情况下，线条层将呈现数据源中的多边形和线条的坐标。 若要限制该层以便仅呈现 LineString 特征，请将该层的 `filter` 属性设置为 `['==', ['geometry-type'], 'LineString']` 或 `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]`（如果还想要包含 MultiLineString 特征）。

以下代码演示如何创建线条。 将线条添加到数据源，然后使用 [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) 类通过线条层呈现它。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='向地图添加线条' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a>（向地图添加线条）。
</iframe>

可以使用 [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) 和[使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)来设置线条层的样式。

## <a name="add-symbols-along-a-line"></a>沿线条添加符号

此示例演示如何沿地图上的线条添加箭头图标。 使用符号层时，将“placement”选项设置为“line”。 此选项会沿线条呈现符号并旋转图标（0 度 = 右）。

<br/>

<iframe height="500" scrolling="no" title="沿线条显示箭头" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/drBJwX/'>沿线条显示箭头</a>。
</iframe>

> [!TIP]
> Azure Maps Web SDK 提供了多个可与符号层一起使用的可自定义的图像模板。 有关详细信息，请参阅[如何使用图像模板](how-to-use-image-templates-web-sdk.md)文档。

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>向线条添加笔划渐变

可以将一种笔划颜色应用于线条。 还可以使用颜色渐变填充线条，以显示从一个线段到下一个线段的转换。 例如，线条渐变可用于表示随时间和距离推移的变化，或是一连串连接的对象间的不同温度。 若要将此特征应用于线条，数据源必须将 `lineMetrics` 选项设置为 `true`，随后可以将颜色渐变表达式传递到线条的 `strokeColor` 选项。 笔划渐变表达式必须引用将计算线条指标公开给表达式的 `['line-progress']` 数据表达式。

<br/>

<iframe height="500" scrolling="no" title="具有笔划渐变的线条" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>具有笔划渐变的线条</a>。
</iframe>

## <a name="customize-a-line-layer"></a>自定义线条层

线条层有多个样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='线条层选项' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>线条层选项</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [添加弹出项](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [如何使用图像模板](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)