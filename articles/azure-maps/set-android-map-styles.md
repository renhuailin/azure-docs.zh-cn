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
ms.openlocfilehash: 84e87593a67bfda512619c5637ffc13c07fa8111
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113091261"
---
# <a name="set-map-style-android-sdk"></a>设置地图样式 (Android SDK)

本文介绍使用 Azure Maps Android SDK 设置地图样式的两种方法。 Azure Maps 有六种不同的地图样式可供选择。 有关受支持的地图样式的详细信息，请参阅 [Azure Maps 中受支持的地图样式](supported-map-styles.md)。

## <a name="prerequisites"></a>先决条件

请务必完成[快速入门：创建 Android 应用](quick-android-map.md)文档中的步骤。

>[!important]
>本部分的过程需要 Gen 1 或 Gen 2 定价层中的 Azure Maps 帐户。 有关定价层的详细信息，请查看[在 Azure Maps 中选择正确的定价层](choose-pricing-tier.md)。


## <a name="set-map-style-in-the-layout"></a>在布局中设置地图样式

在添加地图控件时，你可以在活动类的布局文件中设置地图样式。 以下代码可设置中心位置、缩放级别和地图样式。

```XML
<com.azure.android.maps.control.MapControl
    android:id="@+id/mapcontrol"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:azure_maps_centerLat="47.602806"
    app:azure_maps_centerLng="-122.329330"
    app:azure_maps_zoom="12"
    app:azure_maps_style="grayscale_dark"
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

地图相机可控制在地图视区中显示世界的哪一部分。 可以在代码中以编程方式将相机放在布局中。 在代码中设置地图时，有两种主要方法可用于设置地图的位置：使用居中和缩放，或在边界框传递。 以下代码演示在使用 `center` 和 `zoom` 时如何设置所有可选相机选项。

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
    
    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
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
    
    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
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

    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
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

    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

边界框的纵横比可能与地图的纵横比不同，因此地图通常会显示完整的边界框区域，但通常仅在垂直或水平方向紧缩。

### <a name="animate-map-view"></a>将地图视图制成动画

设置地图的相机选项时，动画选项还可用于创建当前地图视图和下一个地图视图之间的过渡效果。 这些选项指定动画的类型和移动照相机所需的持续时间。

| 选项 | 说明 |
|--------|-------------|
| `animationDuration(Integer durationMs)` | 指定相机在视图之间进行动画处理的时间（以毫秒 (ms) 为单位）。 |
| `animationType(AnimationType animationType)` | 指定要执行的动画过渡效果类型。<br/><br/> - `JUMP` - 即时更改。<br/> - `EASE` - 逐渐更改相机的设置。<br/> - `FLY` - 在类似飞行的弧线之后逐渐改变相机的设置。 |

下面的代码演示如何在三秒钟内使用 `FLY` 动画对地图视图进行动画处理。

::: zone pivot="programming-language-java-android"

``` java
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.6)),
    zoom(12),
    animationType(AnimationType.FLY), 
    animationDuration(3000)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.6)),
    zoom(12.0),
    AnimationOptions.animationType(AnimationType.FLY),
    AnimationOptions.animationDuration(3000)
)
```

::: zone-end

下面演示了以上代码对从纽约到西雅图的地图视图产生的动画效果。

![相机从纽约变换到西雅图的地图动画效果](media/set-android-map-styles/android-animate-camera.gif)

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer-android.md)
