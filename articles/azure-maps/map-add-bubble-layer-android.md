---
title: 向 Android 地图添加气泡层 | Microsoft Azure Maps
description: 了解如何将地图上的点呈现为固定大小的圆。 请参阅如何使用 Azure Maps Android SDK 添加和自定义气泡图层以实现此目标。
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: c3f4dc33347a7e4397a2cd53ecc56d87ed155378
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093510"
---
# <a name="add-a-bubble-layer-to-a-map-android-sdk"></a>向地图添加气泡层 (Android SDK)

本文介绍如何将数据源中的点数据呈现为地图上的气泡层。 气泡层将点呈现为地图上具有固定像素半径的圆。

> [!TIP]
> 默认情况下，气泡层将呈现数据源中所有几何图形的坐标。 若要限制层以便仅呈现点几何图形功能，请将层的 `filter` 选项设置为 `eq(geometryType(), "Point")`。 如果还需要包含 MultiPoint 特征，请将层的 `filter` 选项设置为 `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))`。

## <a name="prerequisites"></a>先决条件

请务必完成[快速入门：创建 Android 应用](quick-android-map.md)文档中的步骤。 可以将本文中的代码块插入到地图的 `onReady` 事件处理程序中。

## <a name="add-a-bubble-layer"></a>添加气泡层

下面的代码将一个点数组加载到数据源中。 然后会将数据点连接到气泡层。 气泡层用五像素呈现每个气泡的半径，并使用白色为填充色。 描边色为蓝色，描边宽度为六像素。

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create point locations.
Point[] points = new Point[] {
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
};

//Add multiple points to the data source.
source.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
BubbleLayer layer = new BubbleLayer(source, 
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
);

map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create point locations.
val points: Array<Point> = arrayOf<Point>(
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
)

//Add multiple points to the data source.
source.add(points)

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
val layer = BubbleLayer(
    source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
)

map.layers.add(layer)
```

::: zone-end

下面的屏幕截图展示上述代码在气泡层中呈现的点。

![带有使用气泡层呈现的点的地图](media/map-add-bubble-layer-android/android-bubble-layer.png)

## <a name="show-labels-with-a-bubble-layer"></a>显示具有气泡层的标签

此代码演示如何使用气泡图层呈现地图上的点。 以及如何使用符号层呈现标签。 若要隐藏符号层的图标，请将 `iconImage` 选项设置为 `"none"`。

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641,47.627631));

//Add a bubble layer.
map.layers.add(new BubbleLayer(source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
));

//Add a symbol layer to display text, hide the icon image.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),
    textField("Museum of History & Industry (MOHAI)"),
    textColor("#005995"),
    textOffset(new Float[]{0f, -2.2f})
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641, 47.627631))

//Add a bubble layer.
map.layers.add(
    BubbleLayer(
        source,
        bubbleRadius(5f),
        bubbleColor("white"),
        bubbleStrokeColor("#4288f7"),
        bubbleStrokeWidth(6f)
    )
)

//Add a symbol layer to display text, hide the icon image.
map.layers.add(
    SymbolLayer(
        source,  //Hide the icon image.
        iconImage("none"),
        textField("Museum of History & Industry (MOHAI)"),
        textColor("#005995"),
        textOffset(arrayOf(0f, -2.2f))
    )
)
```

::: zone-end

下面的屏幕截图展示上述代码在气泡层中呈现了一个点，以及使用符号层的点文本标签。

![包含使用气泡层呈现的点和使用符号层的文本标签的地图](media/map-add-bubble-layer-android/android-bubble-symbol-layer.png)

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [聚类点数据](clustering-point-data-android-sdk.md)

> [!div class="nextstepaction"]
> [添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [显示功能信息](display-feature-information-android.md)
