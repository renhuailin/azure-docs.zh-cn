---
title: 向 Android 地图添加热度地图层 | Microsoft Docs
description: 了解如何创建热度地图。 了解如何使用 Azure MapsAndroid SDK 将热度地图层添加到地图。 了解如何自定义热度地图层。
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 5e48be82a2486291d2b7fdecf42d759e2c13eb08
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425187"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>添加热度地图层 (Android SDK)

热度地图也称为点密度地图，是一种数据可视化效果。 它们用于通过一系列颜色表示数据密度，并在地图上显示数据“热点”。 热度地图是呈现包含大量点的数据集的极佳方式。

以符号形式呈现数以万计的点可能会覆盖大部分地图区域。 而这种情况可能会导致许多符号相互叠加， 因而难以更好地了解数据。 但是，将此数据集可视化为热度地图可以轻松观察密度，以及每个数据点的相对密度。

可以在许多不同的方案中使用热度地图，其中包括：

- 温度数据：提供两个数据点之间的温度近似值。
- 噪声传感器数据：不仅显示传感器所在位置的噪声强度，而且还可提供一段距离内的消散情况见解。 任何一个场地的噪声级别可能不高。 如果多个传感器的噪声覆盖区域重叠，则此重叠区域可能会出现更高的噪声级别。 因此，重叠区域将在热度地图中可见。
- GPS 轨迹：将速度包含为加权高度地图，其中每个数据点的强度基于速度。 例如，使用此功能可以查看车辆的加速位置。

> [!TIP]
> 默认情况下，热度地图层将呈现数据源中所有几何图形的坐标。 若要限制层以便仅呈现点几何图形功能，请将层的 `filter` 选项设置为 `eq(geometryType(), "Point")`。 如果还需要包含 MultiPoint 特征，请将层的 `filter` 选项设置为 `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))`。

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>先决条件

请务必完成[快速入门：创建 Android 应用](quick-android-map.md)文档中的步骤。 可以将本文中的代码块插入到地图的 `onReady` 事件处理程序中。

## <a name="add-a-heat-map-layer"></a>添加热度地图层

若要将点数据源呈现为热度地图，请将数据源传递到 `HeatMapLayer` 类的实例中，并将其添加到地图。

以下代码示例加载了过去一周地址的 GeoJSON 源，并以热度地图的形式进行呈现。 每个数据点都以所有缩放范围的 10 像素的半径形式呈现。 为确保提供更好的用户体验，热度地图位于标签层之下，以便标签保持清晰可见。 此示例中的数据摘自 [USGS 地震危害应对计划](https://earthquake.usgs.gov/)。 该示例使用[创建数据源](create-data-source-android-sdk.md)文档中提供的数据导入实用工具代码块来从 Web 加载 GeoJSON 数据。

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson");

//Add data source to the map.
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")

//Add data source to the map.
map.sources.add(source)

//Create a heat map layer.
val layer = HeatMapLayer(
    source,
    heatmapRadius(10f),
    heatmapOpacity(0.8f)
)

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels")
```

::: zone-end

以下屏幕截图显示了使用上述代码加载热度地图的地图。

![包含最近地震的热度地图层的地图](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>自定义热度地图层

前面的示例通过设置半径和不透明度选项来自定义热度地图。 热度地图层提供多个选项用于自定义，其中包括：

- `heatmapRadius`：定义要呈现每个数据点的像素半径。 可将半径设置为固定数字或表达式。 使用表达式可以根据缩放级别来缩放半径，并在地图上表示一个一致的空间区域（例如，5 英里半径）。
- `heatmapColor`：指定如何为热度地图赋色。 颜色渐变是热度地图的常用功能。 可以使用 `interpolate` 表达式来实现该效果。 还可以使用 `step` 表达式为热度地图赋色，直观地将密度分解到类似于等高线或雷达式地图的范围中。 这些调色板定义了从最小到最大密度值的颜色。

  将热度地图的颜色值指定为基于 `heatmapDensity` 值的表达式。 “内插”表达式的索引 0 处定义了没有数据的颜色区域，或“递阶”表达式的默认颜色。 可以使用此值来定义背景色。 通常此值设置为“透明”，或“半透明的黑色”。

  下面是颜色表达式示例：

  | 内插颜色表达式 | 递阶色表达式 |
  |--------------------------------|--------------------------|
  | interpolate(<br/>&nbsp;&nbsp;&nbsp;&nbsp;linear(), <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0, color(Color.TRANSPARENT)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.01, color(Color.MAGENTA)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(parseColor("#fb00fb"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(1, color(parseColor("#00c3ff")))<br/>)` | step(<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;color(Color.TRANSPARENT),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.01, color(parseColor("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.25, color(parseColor("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(Color.GREEN)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(Color.YELLOW)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(1, color(Color.RED))<br/>) |

- `heatmapOpacity`：指定热度图层的不透明或透明程度。
- `heatmapIntensity`：对每个数据点的权重应用乘数，以增加热度地图的整体强度。 这会导致数据点的权重出现差异，使其更易于可视化。
- `heatmapWeight`：默认情况下，所有数据点的权重均为 1，并且是平均加权的。 权重选项充当乘数，可以设置为数字或表达式。 如果将某个数字设置为权重，则这相当于将每个数据点置于地图上两次。 例如，如果权重为 `2`，则密度会加倍。 将权重选项设置为一个数字，以类似于使用强度选项的方式来呈现热度地图。

  但是，如果使用表达式，则每个数据点的权重可以基于每个数据点的属性。 例如，假设每个数据点表示地震。 震级值是每个地震数据点的重要指标。 地震时时刻刻都在发生，但大多数震级都很低，以致于感觉不到。 在表达式中使用震级值可将权重分配到每个数据点。 使用震级值分配权重可以更好地在热度地图中表示地震的严重程度。
- `minZoom` 和 `maxZoom`：应显示层的缩放级别范围。
- `filter`：用于限制从源检索并在层中呈现的筛选器表达式。
- `sourceLayer`如果连接到层的数据源是矢量图块源，那么必须指定矢量图块中的源层。
- `visible`：隐藏或显示层。

下面是热度地图示例，其中线性内插表达式用来创建平滑颜色渐变。 数据中定义的 `mag` 属性与指数内插结合使用来设置每个数据点的权重或相关性。

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
)
```

::: zone-end

以下屏幕截图介绍了以上自定义热度地图层，它们使用与之前热度地图中相同的数据。

![包含最近地震的自定义热度地图层的地图](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>可一致缩放的热度地图

默认情况下，热度地图层中呈现的数据点半径对所有缩放级别采用固定像素的半径。 缩放地图时，数据将会聚合，热度地图层将呈现不同的外观。 以下视频介绍了热度地图的默认行为，即在缩放地图时维护像素半径。

![动画说明通过热度地图层缩放的地图显示的像素一致](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

使用 `zoom` 表达式缩放每个缩放级别的半径，使每个数据点涵盖相同的地图物理区域。 此表达式使热度地图层显得更为静态且一致。 地图的每个缩放级别的垂直和水平像素是前一个缩放级别的两倍。

调整半径使其在每个缩放级别中翻倍，会创建一个在所有缩放级别下均保持一致外观的热度地图。 若要应用这种调整，请将 `zoom` 与以 2 为底的 `exponential interpolation` 表达式配合使用，并为最小缩放级别设置像素半径，为最大缩放级别设置标度半径（计算方式为 `2 * Math.pow(2, minZoom - maxZoom)`，如以下示例中所示）。 缩放地图，观看热度地图如何根据缩放级别而缩放。

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2.0, 19 - 1.0) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
)
```

::: zone-end

以下视频展示了运行以上代码的地图，它能在地图缩放的同时缩放半径，从而创建在缩放级别下保持一致外观的热度地图。

![动画说明通过热度地图层缩放的地图显示的地理空间一致](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

`zoom` 表达式只能在 `step` 和 `interpolate` 表达式中使用。 以下表达式可用于估算以米为单位的半径。 此表达式使用占位符 `radiusMeters`，应将其替换为所需的半径。 此表达式针对缩放级别 0 和 24 计算赤道处缩放级别的近似像素半径，并使用 `exponential interpolation` 表达式在这些值之间进行缩放，工作方式与地图中的平铺系统相同。

::: zone pivot="programming-language-java-android"

```java
interpolate(
    exponential(2),
    zoom(),
    stop(1, product(radiusMeters, 0.000012776039596366526)),
    stop(24, product(radiusMeters, 214.34637593279402))
)
```

> [!TIP]
> 在数据源上启用聚类分析时，相邻的点可以作为一个聚类点组合在一起。 可以使用每个聚类的点计数作为热度地图的权重表达式。 这可以大幅减少要呈现的点数。 聚类点计数存储在点特征的 `point_count` 属性中：
>
> ```java
> HeatMapLayer layer = new HeatMapLayer(dataSource,
>    heatmapWeight(get("point_count"))
> );
> ```
>
> 如果聚类分析半径只有几个像素，那么呈现的视觉差异就很小。 更大的半径可将更多的点分组到每个聚类，从而改善热度地图的性能。

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
interpolate(
    exponential(2),
    zoom(),
    stop(1, product(radiusMeters, 0.000012776039596366526)),
    stop(24, product(radiusMeters, 214.34637593279402))
)
```

> [!TIP]
> 在数据源上启用聚类分析时，相邻的点可以作为一个聚类点组合在一起。 可以使用每个聚类的点计数作为热度地图的权重表达式。 这可以大幅减少要呈现的点数。 聚类点计数存储在点特征的 `point_count` 属性中：
>
> ```kotlin
> var layer = new HeatMapLayer(dataSource,
>    heatmapWeight(get("point_count"))
> )
> ```
>
> 如果聚类分析半径只有几个像素，那么呈现的视觉差异就很小。 更大的半径可将更多的点分组到每个聚类，从而改善热度地图的性能。

::: zone-end

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-android-sdk.md)
