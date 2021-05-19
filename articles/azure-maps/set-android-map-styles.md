---
title: 设置 Android 地图中的地图样式 | Microsoft Azure Maps
description: 了解设置地图样式的两种方法。 了解如何在布局文件或活动类中使用 Azure Maps Android SDK 来调整样式。
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: aef8fbacf8302fb5dd4b5fe28afc615c6bf56090
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100978"
---
# <a name="set-map-style-android-sdk"></a>设置地图样式 (Android SDK)

本文介绍使用 Azure Maps Android SDK 设置地图样式的两种方法。 Azure Maps 有六种不同的地图样式可供选择。 有关受支持的地图样式的详细信息，请参阅 [Azure Maps 中受支持的地图样式](supported-map-styles.md)。

## <a name="prerequisites"></a>先决条件

请务必完成[快速入门：创建 Android 应用](quick-android-map.md)文档中的步骤。

## <a name="set-map-style-in-the-layout"></a>在布局中设置地图样式

在添加地图控件时，你可以在活动类的布局文件中设置地图样式。 以下代码可设置中心位置、缩放级别和地图样式。

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/mapcontrol"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_centerLat="47.602806"
    app:mapcontrol_centerLng="-122.329330"
    app:mapcontrol_zoom="12"
    app:mapcontrol_style="grayscale_dark"
    />
```

以下屏幕截图表明，上述代码可显示具有灰度深色样式的道路地图。

![具有灰度深色道路地图样式的地图](media/set-android-map-styles/android-grayscale-dark.png)

## <a name="set-map-style-in-code"></a>在代码中设置地图样式

使用地图的 `setStyle` 方法可以在代码中以编程方式设置地图样式。 以下代码使用地图的 `setCamera` 方法设置中心位置和缩放级别并且将地图样式设置为 `SATELLITE_ROAD_LABELS`。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14))

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS))
}
```

::: zone-end

以下屏幕截图表明，上述代码可显示具有卫星道路标签样式的地图。

![具有卫星道路标签样式的地图](media/set-android-map-styles/android-satellite-road-labels.png)

## <a name="setting-the-map-camera"></a>设置地图相机

地图相机控制地图中显示的地图部分。 可以在代码中以编程方式将相机放在布局中。 在代码中设置地图时，有两种主要方法可用于设置地图的位置：使用居中和缩放，或在边界框传递。 以下代码演示在使用 `center` 和 `zoom` 时如何设置所有可选相机选项。

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

通常，需要通过一组数据将地图聚焦。 可以使用 `MapMath.fromData` 方法根据地物计算边界框，然后可以将其传递到地图相机的 `bounds` 选项。 在根据边界框设置地图视图时，指定一个 `padding` 值以考虑呈现为气泡或符号的点的像素大小通常是很有用的。 下面的代码演示了如何在使用边界框设置相机位置时设置所有可选的相机选项。

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

请注意，边界框的纵横比可能与地图的纵横比不同，因此地图通常会显示完整的边界框区域，但通常仅在垂直或水平方向紧缩。

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer-android.md)
