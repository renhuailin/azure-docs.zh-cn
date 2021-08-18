---
title: 向地图添加图像层 | Microsoft Azure Maps
description: 了解如何向地图添加图像。 了解如何使用 Azure Maps Web SDK 自定义图像层并将图像覆盖在固定坐标集上。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 67a564b813014ffc25049f0694ef05977d65efe6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463046"
---
# <a name="add-an-image-layer-to-a-map"></a>将图像层添加到地图

本文介绍如何将图像覆盖到固定坐标集。 下面是可以在地图上覆盖的不同图像类型的几个示例：

* 从无人机捕获的图像
* 建筑平面图
* 历史或其他专门的地图图像
* 工作地点蓝图
* 天气雷达图像

> [!TIP]
> 借助 [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)，可以轻松将图像覆盖在地图上。 请注意，浏览器可能难以加载大图像。 在这种情况下，请考虑将图像分解为图块并将其作为 [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) 加载到地图中。

图像层支持以下图像格式：

- JPEG
- PNG
- BMP
- GIF（非动画）

## <a name="add-an-image-layer"></a>添加图像层

下面的代码在地图上覆盖 1922 年新泽西州纽瓦克地图的图像。 通过将 URL 传递给图像并以 `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` 格式设置四个角的坐标来创建 [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)。

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

下面是上述代码的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='简单图像层' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/eQodRo/'>简单图像层</a>。
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>导入 KML 文件作为地面覆盖

此示例演示如何将 KML 地面覆盖信息覆盖为地图上的图像层。 KML 地面覆盖提供东、南、西、北坐标和逆时针旋转。 但图像层需要图像每个角的坐标。 此示例中的 KML 地面覆盖是来自[维基媒体](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)的 Chartres 大教堂。

此代码使用 [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) 类中的静态 `getCoordinatesFromEdges` 函数。 它使用 KML 地面覆盖的东、南、西、北和旋转信息来计算图像的四个角。

<br/>

<iframe height='500' scrolling='no' title='KML 地面叠加作为图像层' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML 地面叠加作为图像层</a>。
</iframe>

> [!TIP]
> 使用图像层类的 `getPixels` 和 `getPositions` 函数在定位图像层的地理坐标和本地图像像素坐标之间转换。

## <a name="customize-an-image-layer"></a>自定义图像层

图像层有许多样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='图像层选项' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>图像层选项</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

> [!div class="nextstepaction"]
> [ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加图块层](./map-add-tile-layer.md)
