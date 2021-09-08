---
title: 向 Android 地图添加多边形层 | Microsoft Azure Maps
description: 了解如何向地图添加多边形或圆形。 了解如何使用 Azure Maps Android SDK 自定义几何形状，并使其易于更新和维护。
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 5659e366fd5c949ea374768bf848313d747896d2
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425803"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>向地图添加多边形层 (Android SDK)

本文介绍如何使用多边形层渲染地图上的 `Polygon` 和 `MultiPolygon` 特征几何图形区域。

## <a name="prerequisites"></a>先决条件

请务必完成[快速入门：创建 Android 应用](quick-android-map.md)文档中的步骤。 可以将本文中的代码块插入到地图的 `onReady` 事件处理程序中。

## <a name="use-a-polygon-layer"></a>使用多边形层

将多边形层连接到数据源并加载到地图上时，它将渲染具有 `Polygon` 和 `MultiPolygon` 特征的区域。 要创建多边形，请将其添加到数据源，并使用多边形层通过 `PolygonLayer` 类对其进行渲染。

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source, 
    fillColor("red"),
    fillOpacity(0.7f)
), "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a rectangular polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-73.98235, 40.76799),
                Point.fromLngLat(-73.95785, 40.80044),
                Point.fromLngLat(-73.94928, 40.79680),
                Point.fromLngLat(-73.97317, 40.76437),
                Point.fromLngLat(-73.98235, 40.76799)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillColor("red"),
        fillOpacity(0.7f)
    ), "labels"
)
```

::: zone-end

以下屏幕截图显示上述代码使用多边形层渲染了多边形区域。

![渲染了填充区域的多边形](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>结合使用多边形和线条层

线条层用于渲染多边形的轮廓。 下面的代码示例像上一个示例一样渲染了一个多边形，但添加了一个线条层。 此线条层是连接到数据源的另一个层。

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
    fillColor("rgba(0, 200, 200, 0.5)")
), "labels");

//Create and add a line layer to render the outline of the polygon.
map.layers.add(new LineLayer(source,
    strokeColor("red"),
    strokeWidth(2f)
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a rectangular polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-73.98235, 40.76799),
                Point.fromLngLat(-73.95785, 40.80044),
                Point.fromLngLat(-73.94928, 40.79680),
                Point.fromLngLat(-73.97317, 40.76437),
                Point.fromLngLat(-73.98235, 40.76799)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillColor("rgba(0, 200, 200, 0.5)")
    ), "labels"
)

//Create and add a line layer to render the outline of the polygon.
map.layers.add(
    LineLayer(
        source,
        strokeColor("red"),
        strokeWidth(2f)
    )
)
```

::: zone-end

以下屏幕截图显示上述代码渲染了一个多边形，并使用线条层渲染了多边形的轮廓。

![渲染了填充区域和轮廓的多边形](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> 当使用线条层勾勒多边形轮廓时，请确保闭合多边形中的所有环，使每组点的起点和终点相同。 如果不这样做，线条层可能不会将多边形的最后一个点连接到第一个点。

## <a name="fill-a-polygon-with-a-pattern"></a>使用图案填充多边形

除了为多边形填充颜色之外，还可以使用图像图案来进行填充。 将图像图案加载到地图图像子画面资源中，然后使用多边形层的 `fillPattern` 选项引用该图像。

::: zone pivot="programming-language-java-android"

```java
//Load an image pattern into the map image sprite.
map.images.add("fill-checker-red", R.drawable.fill_checker_red);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-50, -20),
            Point.fromLngLat(0, 40),
            Point.fromLngLat(50, -20),
            Point.fromLngLat(-50, -20)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
        fillPattern("fill-checker-red"),
        fillOpacity(0.5f)
), "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load an image pattern into the map image sprite.
map.images.add("fill-checker-red", R.drawable.fill_checker_red)

//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-50, -20),
                Point.fromLngLat(0, 40),
                Point.fromLngLat(50, -20),
                Point.fromLngLat(-50, -20)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillPattern("fill-checker-red"),
        fillOpacity(0.5f)
    ), "labels"
)
```

::: zone-end

对于本示例，以下图像已加载到应用的可绘制资源文件夹中。

| ![紫色箭头图标图像](media/how-to-add-shapes-to-android-map/fill-checker-red.png)|
|:-----------------------------------------------------------------------:|
| fill_checker_red.png                                                    |

以下屏幕截图显示上述代码在地图上渲染了一个带有填充图案的多边形。

![地图上渲染了填充图案的多边形](media/how-to-add-shapes-to-android-map/android-polygon-pattern.jpg)

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [添加线条层](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形挤压层](map-extruded-polygon-android.md)
