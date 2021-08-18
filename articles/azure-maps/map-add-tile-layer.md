---
title: 向地图添加图块层 | Microsoft Azure Maps
description: 了解如何在地图上叠加图像。 请参阅示例，了解如何使用 Azure Maps Web SDK 向地图添加包含天气雷达覆盖的图块层。
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: c28eb1bdbb0069a831f397150d6479380df8b3a3
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114665151"
---
# <a name="add-a-tile-layer-to-a-map"></a>将图块层添加到地图

本文介绍如何在地图上覆盖图块层。 通过图块层可以在 Azure Maps 基本地图图块顶部附加图像。 有关 Azure Maps 图块系统的详细信息，请参阅[缩放级别和图块网格](zoom-levels-and-tile-grid.md)。

图块层将从服务器加载图块。 这些图像可以预呈现，也可以是动态呈现。 使用图块层能理解的命名约定（像服务器上的任何其他图像一样）对预呈现图像进行存储。 动态呈现的图像使用一项服务接近实时地加载图像。 Azure Maps [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) 类支持以下三个不同的图块服务命名约定：

* X、Y、缩放表示法 - X 是列，Y 是图块网格中图块的行位置，缩放表示法是一个基于缩放级别的值。
* Quadkey 表示法 - 将 x、y、缩放信息合并到单个字符串值中。 此字符串值将成为单个图块的唯一标识符。
* 边界框 - 以边界框坐标格式指定图像：`{west},{south},{east},{north}`。 [Web 映射服务 (WMS)](https://www.opengeospatial.org/standards/wms) 通常使用此格式。

> [!TIP]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) 是直观显示地图上的大型数据集的好办法。 不仅可以从图像中生成图块层，而且还可以将矢量数据呈现为图块层。 通过将矢量数据呈现为图块层，地图控件只需加载文件大小小于它们所代表的矢量数据的图块。 此方法通常用于呈现地图上的数百万行数据。

传递到图块层中的图块 URL 必须是 TileJSON 资源的 http 或 https URL 或使用以下参数的图块 URL 模板：

* `{x}` - 图块的 X 位置。 还需要 `{y}` 和 `{z}`。
* `{y}` - 图块的 Y 位置。 还需要 `{x}` 和 `{z}`。
* `{z}` - 图块的缩放级别。 还需要 `{x}` 和 `{y}`。
* `{quadkey}` - 基于必应地图图块系统命名约定的图块 quadkey 标识符。
* `{bbox-epsg-3857}` - EPSG 3857 空间引用系统中格式为 `{west},{south},{east},{north}` 的边界框字符串。
* `{subdomain}` - 将添加子域值（如果已指定）的占位符 `subdomain`。
* `{azMapsDomain}` - 用于将图块请求的域和身份验证与地图所使用的相同值对齐的占位符。

## <a name="add-a-tile-layer"></a>添加图块层

 此示例演示如何创建指向一组图块的图块层。 此示例使用 x、y、缩放图块系统。 此图块层的源是 [OpenSeaMap 项目](https://openseamap.org/index.php)，其中包含众包航海图。 查看雷达图数据时，用户最好能在导航地图时清楚地查看城市的标签。 通过在 `labels` 层下插入图块层，可以实现此行为。

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256,
    minSourceZoom: 7,
    maxSourceZoom: 17
}), 'labels');
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='使用 X、Y 和 Z 的图块层' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>使用 X、Y 和 Z 的图块层</a>。
</iframe>

## <a name="add-an-ogc-web-mapping-service-wms"></a>添加 OGC Web 地图定位服务 (WMS)

Web 地图定位服务 (WMS) 是一项开放地理空间信息联盟 (OGC) 标准，用于提供地图数据的图像。 此格式提供了许多可用于 Azure Maps 的开放数据集。 如果此类型的服务支持 `EPSG:3857` 坐标参考系 (CRS)，该服务就可用于图块层。 使用 WMS 服务时，将宽度和高度参数设置为该服务支持的同一个值，请确保在 `tileSize` 选项中设置这个值。 在带格式的 URL 中，将服务的 `BBOX` 参数设置为 `{bbox-epsg-3857}` 占位符。

下面的屏幕截图显示的是上面的代码在地图上的标签下覆盖了来自[美国地质调查局 (USGS)](https://mrdata.usgs.gov/) 的地质数据的 Web 地图定位服务后的情况。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="WMS 图块层" src="https://codepen.io/azuremaps/embed/BapjZqr?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/BapjZqr'>WMS 图块层</a>。
</iframe>

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>添加 OGC Web 地图定位图块服务 (WMTS)

Web 地图定位图块服务 (WMTS) 是一项开放地理空间信息联盟 (OGC) 标准，用于为地图提供基于图库的重叠。 此格式提供了许多可用于 Azure Maps 的开放数据集。 如果此类型的服务支持 `EPSG:3857` 或 `GoogleMapsCompatible` 坐标参考系 (CRS)，该服务就可用于图块层。 使用 WMTS 服务时，将宽度和高度参数设置为该服务支持的同一个值，请确保在 `tileSize` 选项中设置这个值。 在带格式的 URL 中，相应地替换以下占位符：

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

下面的屏幕截图显示的是上面的代码在地图上的道路和标签下覆盖了来自[美国地质调查局 (USGS) 国家地图](https://viewer.nationalmap.gov/services/)的图像的 Web 地图定位图块服务后的情况。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="WMTS 图块层" src="https://codepen.io/azuremaps/embed/BapjZVY?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/BapjZVY'>WMTS 图块层</a>。
</iframe>

## <a name="customize-a-tile-layer"></a>自定义图块层

图块层类有许多样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='图块层选项' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>图块层选项</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加图像层](./map-add-image-layer.md)
