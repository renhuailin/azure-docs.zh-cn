---
title: 将热度地图层添加到地图 | Microsoft Azure Maps
description: 了解如何创建热度地图。 了解如何使用 Azure Maps Web SDK 将热度地图层添加到地图。 了解如何自定义热度地图层。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 0145460746665756f007c50845a279743dc60ae1
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666313"
---
# <a name="add-a-heat-map-layer"></a>添加热度地图层

热度地图也称为点密度地图，是一种数据可视化效果。 它们用于通过一系列颜色表示数据密度，并在地图上显示数据“热点”。 热度地图是呈现包含大量点的数据集的极佳方式。

以符号形式呈现数以万计的点可能会覆盖大部分地图区域。 而这种情况可能会导致许多符号相互叠加， 因而难以更好地了解数据。 但是，将此数据集可视化为热度地图可以轻松观察密度，以及每个数据点的相对密度。

可以在许多不同的方案中使用热度地图，其中包括：

- 温度数据：提供两个数据点之间的温度近似值。
- 噪声传感器数据：不仅显示传感器所在位置的噪声强度，而且还可提供一段距离内的消散情况见解。 任何一个场地的噪声级别可能不高。 如果多个传感器的噪声覆盖区域重叠，则此重叠区域可能会出现更高的噪声级别。 因此，重叠区域将在热度地图中可见。
- GPS 轨迹：将速度包含为加权高度地图，其中每个数据点的强度基于速度。 例如，使用此功能可以查看车辆的加速位置。

> [!TIP]
> 默认情况下，热度地图层将呈现数据源中所有几何图形的坐标。 若要限制层以便仅呈现点几何图形特征，请将层的 `filter` 属性设置为 `['==', ['geometry-type'], 'Point']`。 如果还需要包含 MultiPoint 特征，请将层的 `filter` 属性设置为 `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`。

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="add-a-heat-map-layer"></a>添加热度地图层

若要将点数据源呈现为热度地图，请将数据源传递到 `HeatMapLayer` 类的实例中，并将其添加到地图。

在以下代码中，每个热点在所有缩放级别的半径均为 10 像素。 为确保提供更好的用户体验，热度地图位于标签层之下。 标签保持清晰可见。 此示例中的数据摘自 [USGS 地震危害应对计划](https://earthquake.usgs.gov/)。 这些数据适用于过去 30 天发生的重大地震。

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

下面是上述代码的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='简单热度地图层' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>简单热度地图层</a>。
</iframe>

## <a name="customize-the-heat-map-layer"></a>自定义热度地图层

前面的示例通过设置半径和不透明度选项来自定义热度地图。 热度地图层提供多个选项用于自定义，其中包括：

* `radius`：定义要呈现每个数据点的像素半径。 可将半径设置为固定数字或表达式。 使用表达式可以根据缩放级别来缩放半径，并在地图上表示一个一致的空间区域（例如，5 英里半径）。
* `color`：指定如何为热度地图赋色。 颜色渐变是热度地图的常用功能。 可以使用 `interpolate` 表达式来实现该效果。 还可以使用 `step` 表达式为热度地图赋色，直观地将密度分解到类似于等高线或雷达式地图的范围中。 这些调色板定义了从最小到最大密度值的颜色。

  将热度地图的颜色值指定为基于 `heatmap-density` 值的表达式。 “内插”表达式的索引 0 处定义了没有数据的颜色区域，或“递阶”表达式的默认颜色。 可以使用此值来定义背景色。 通常此值设置为“透明”，或“半透明的黑色”。

  下面是颜色表达式示例：

  | 内插颜色表达式 | 递阶色表达式 | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;“内插”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“线性”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“热度地图密度”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0，“透明”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01，“紫色”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5，“#fb00fb”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;1，“#00c3ff”<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;“递阶”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“热度地图密度”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;“透明”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01，“海军蓝”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] |

- `opacity`：指定热度地图层的不透明度或透明度。
- `intensity`：对每个数据点的权重应用乘数，以增加热度地图的整体强度。 这会导致数据点的权重出现差异，使其更易于可视化。
- `weight`：默认情况下，所有数据点的权重均为 1，并且是平均加权的。 权重选项充当乘数，可以设置为数字或表达式。 如果将某个数字设置为权重，则这相当于将每个数据点置于地图上两次。 例如，如果权重为 2，则密度会加倍。 将权重选项设置为一个数字，以类似于使用强度选项的方式来呈现热度地图。

  但是，如果使用表达式，则每个数据点的权重可以基于每个数据点的属性。 例如，假设每个数据点表示地震。 震级值是每个地震数据点的重要指标。 地震时时刻刻都在发生，但大多数震级都很低，以致于感觉不到。 在表达式中使用震级值可将权重分配到每个数据点。 使用震级值分配权重可以更好地在热度地图中表示地震的严重程度。
- `source` 和 `source-layer`：可用于更新数据源。

以下工具可以测试不同热度地图层选项。

<br/>

<iframe height='700' scrolling='no' title='热度地图层选项' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>热度地图层选项</a>。
</iframe>

## <a name="consistent-zoomable-heat-map"></a>可一致缩放的热度地图

默认情况下，热度地图层中呈现的数据点半径对所有缩放级别采用固定像素的半径。 缩放地图时，数据将会聚合，热度地图层将呈现不同的外观。

使用 `zoom` 表达式缩放每个缩放级别的半径，使每个数据点涵盖相同的地图物理区域。 此表达式使热度地图层显得更为静态且一致。 地图的每个缩放级别的垂直和水平像素是前一个缩放级别的两倍。

调整半径使其在每个缩放级别中翻倍，会创建一个在所有缩放级别下均保持一致外观的热度地图。 若要应用这种调整，请将 `zoom` 与以 2 为底的 `exponential interpolation` 表达式配合使用，并为最小缩放级别设置像素半径，为最大缩放级别设置标度半径（计算方式为 `2 * Math.pow(2, minZoom - maxZoom)`，如以下示例中所示）。 缩放地图，观看热度地图如何根据缩放级别而缩放。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="可一致缩放的热度地图" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>可一致缩放的热度地图</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

`zoom` 表达式只能在 `step` 和 `interpolate` 表达式中使用。 以下表达式可用于估算半径（以米为单位）。 此表达式使用占位符 `radiusMeters`，你应将其替换为所需的半径。 此表达式针对缩放级别 0 和 24 计算赤道处缩放级别的近似像素半径，并使用 `exponential interpolation` 表达式在这些值之间进行缩放，工作方式与地图中的平铺系统相同。

```json
[
    `'interpolate', 
    ['exponential', 2],
    ['zoom'],
    0, ['*', radiusMeters, 0.000012776039596366526],
    24, [`'*', radiusMeters, 214.34637593279402]
]
```

> [!TIP]
> 在数据源上启用聚类分析时，相邻的点可以作为一个聚类点组合在一起。 可以使用每个聚类的点计数作为热度地图的权重表达式。 这可以大幅减少要呈现的点数。 聚类点计数存储在点特征的 `point_count` 属性中：
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> 如果聚类分析半径只有几个像素，那么呈现的视觉差异就很小。 更大的半径可将更多的点分组到每个聚类，从而改善热度地图的性能。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [HeatMapLayer](/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)