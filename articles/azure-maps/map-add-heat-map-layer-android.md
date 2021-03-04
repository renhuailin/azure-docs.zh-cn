---
title: 向 Android 地图添加热度地图层 |Microsoft Azure 映射
description: 了解如何创建热度地图。 请参阅如何使用 Azure MapsAndroid SDK 将热度地图层添加到地图。 了解如何自定义热度地图层。
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: fce2c2d007f92c43e763826f9345f773324e885e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100179"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>Android SDK 添加热度地图层 () 

热度地图也称为点密度地图，是一种数据可视化效果。 它们用于用一系列颜色来表示数据的密度，并在地图上显示数据 "热点"。 热度地图是使用大量点呈现数据集的好方法。 

将成千上万个点渲染为符号可以涵盖大多数地图区域。 这种情况可能会导致许多符号重叠。 很难更好地了解数据。 但是，将此数据集可视化为热度地图可以轻松地查看每个数据点的密度和相对密度。

可以在许多不同的方案中使用热度地图，其中包括：

- **温度数据**：提供两个数据点之间温度的近似值。
- **噪音传感器的数据**：不仅显示传感器的噪音强度，而且还可以提供距离内的散热。 任何一个站点的噪音级别可能不是高。 如果多个传感器的噪音范围区域重叠，则此重叠区域可能会出现更高的干扰级别。 因此，重叠区域将显示在热度地图中。
- **GPS 跟踪**：将速度作为加权高度地图，其中每个数据点的强度基于速度。 例如，使用此功能可以查看车辆的加速位置。

> [!TIP]
> 默认情况下，热度地图层会呈现数据源中所有几何的坐标。 若要将该层限制为仅呈现点几何特征，请将 `filter` 层的选项设置为 `eq(geometryType(), "Point")` 。 如果还想要包含 MultiPoint 功能，请将层的 `filter` 选项设置为 `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` 。

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>先决条件

请确保完成 [快速入门：创建 Android 应用](quick-android-map.md) 文档中的步骤。 本文中的代码块可以插入到 maps `onReady` 事件处理程序中。

## <a name="add-a-heat-map-layer"></a>添加热度地图层

若要将点的数据源呈现为热度地图，请将数据源传递到类的实例 `HeatMapLayer` 中，并将其添加到地图中。

下面的代码示例从过去一周加载地震的 GeoJSON 源，并将其呈现为热度地图。 呈现每个数据点时，在所有缩放级别都有10个像素的半径。 为了确保更好的用户体验，热度地图位于标签层下方，因此标签保持清晰可见。 本示例中的数据来自 [USGS 地震危险计划](https://earthquake.usgs.gov/)。 此示例使用 [创建数据源](create-data-source-android-sdk.md) 文档中提供的数据导入实用工具代码块从 Web 加载 GeoJSON 数据。

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a heat map layer.
val layer = HeatMapLayer(
    source,
    heatmapRadius(10f),
    heatmapOpacity(0.8f)
)

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels")

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this
) { result: String? ->
    //Parse the data as a GeoJSON Feature Collection.
    val fc = FeatureCollection.fromJson(result!!)

    //Add the feature collection to the data source.
    source.add(fc)

    //Optionally, update the maps camera to focus in on the data.
    //Calculate the bounding box of all the data in the Feature Collection.
    val bbox = MapMath.fromData(fc)

    //Update the maps camera so it is focused on the data.
    map.setCamera(
        bounds(bbox),
        padding(20)
    )
}
```

::: zone-end

以下屏幕截图显示了使用上面的代码加载热度地图的地图。

![带最近地震的热度地图层的地图](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>自定义热度地图层

前面的示例通过设置半径和不透明度选项来自定义热度地图。 热度地图层提供了用于自定义的几个选项，包括：

- `heatmapRadius`：定义要在其中呈现每个数据点的像素半径。 可以将 radius 设置为固定数字或表达式。 通过使用表达式，可以根据缩放级别缩放半径，并在地图上表示一致的空间区域 (例如，5英里半径) 。
- `heatmapColor`：指定热度地图的着色方式。 颜色渐变是热度地图的常见功能。 您可以使用表达式来实现效果 `interpolate` 。 还可以使用 `step` 表达式着色热度地图，并将密度直观地分解为类似于等高线或雷达图样式图的范围。 这些调色板定义了从最小到最大密度值的颜色。

  将热度地图的颜色值指定为值的表达式 `heatmapDensity` 。 在 "内插" 表达式的索引0处定义了没有数据的区域的颜色，或者定义了 "阶数" 表达式的默认颜色。 您可以使用此值来定义背景色。 通常，此值设置为透明或半透明黑色。 

  下面是颜色表达式的示例：

  | 内插颜色表达式 | 阶颜色表达式 |
  |--------------------------------|--------------------------|
  | 内插 (<br/>&nbsp;&nbsp;&nbsp;&nbsp;线性 () ， <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity () ，<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0，color (color。透明) ) ，<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0.01，color (color。洋红色) ) ，<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0.5、color (parseColor ( "#fb00fb" ) ) ) <br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (1、color (parseColor ( "#00c3ff" ) ) ) <br/>)` | 步骤 (<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity () ，<br/>&nbsp;&nbsp;&nbsp;&nbsp;颜色 (颜色。透明) ，<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0.01、color (parseColor ( "#000080" ) ) ) <br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0.25、color (parseColor ( "#000080" ) ) ) <br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0.5，color (color。绿色) ) ，<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0.5，color (color。黄色) ) ，<br/>&nbsp;&nbsp;&nbsp;&nbsp;停止 (1，颜色 (颜色。红色) ) <br/>) |

- `heatmapOpacity`：指定热度地图层的不透明或透明程度。
- `heatmapIntensity`：对每个数据点的权重应用乘数，以增加热度地图的总体亮度。 这会导致数据点的权重差别，使其更易于可视化。
- `heatmapWeight`：默认情况下，所有数据点的权重都为1，并且具有相同的加权。 权重选项用作乘数，你可以将其设置为数字或表达式。 如果将数字设置为权重，则这是将每个数据点置于地图上两次的等效性。 例如，如果权重为 `2` ，则该密度会加倍。 将权重选项设置为一个数字，以类似于使用强度选项的方式来呈现热度地图。

  但是，如果使用表达式，则每个数据点的权重可以基于每个数据点的属性。 例如，假设每个数据点都表示地震。 数量级值是每个地震数据点的重要指标。 地震始终发生，但大多数情况下都是很低的，而且没有注意到。 使用表达式中的 "量值" 将权重分配给每个数据点。 通过使用数量级值分配权重，可以更好地表示热图中地震的重要性。
- `minZoom` 和 `maxZoom` ：应显示层的缩放级别范围。
- `filter`：一个筛选器表达式，用于限制从源检索并在层中呈现的。
- `sourceLayer`：如果连接到层的数据源是矢量图块源，则必须指定矢量图块内的源层。
- `visible`：隐藏或显示层。

下面是一个热度地图的示例，其中的 "内插" 表达式用于创建平滑颜色渐变。 `mag`在数据中定义的属性与指数内插一起使用，以设置每个数据点的权重或相关性。

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

以下屏幕截图显示了使用上一热图示例中相同数据的上述自定义热度地图层。

![具有最近地震的自定义热度地图层的地图](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>一致的 zoomable 热度地图

默认情况下，热度地图层中呈现的数据点的半径对于所有缩放级别都具有固定像素半径。 缩放地图时，数据聚合在一起，热度地图层看起来不同。 以下视频显示了热度地图的默认行为，在缩放地图时它会维持像素半径。

![显示地图缩放的动画显示大小一致的热度地图层](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

使用 `zoom` 表达式可缩放每个缩放级别的半径，使每个数据点都涵盖地图的同一物理区域。 此表达式使热度地图层的外观更具静态和一致性。 地图的每个缩放级别都具有两倍于与上一个缩放级别垂直和水平的像素。

缩放半径，使每个缩放级别翻倍，从而创建一个在所有缩放级别上都保持一致的热度地图。 若要应用此缩放，请将 `zoom` 与基2表达式结合使用 `exponential interpolation` ，并为最小缩放级别设置像素半径，并为计算的最大缩放级别设置缩放半径， `2 * Math.pow(2, minZoom - maxZoom)` 如以下示例中所示。 缩放地图，查看热度地图如何随缩放级别一起缩放。

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

下图显示了一个运行上述代码的地图，该地图在放大地图时缩放半径，以创建跨缩放级别的一致热度地图。

![动画显示地图缩放，显示一致的地理空间大小的热度地图层](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-android-sdk.md)
