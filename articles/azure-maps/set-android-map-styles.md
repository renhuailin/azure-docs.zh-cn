---
title: 设置 Android maps 中的地图样式 |Microsoft Azure 映射
description: 了解设置地图样式的两种方法。 请参阅如何使用布局文件或活动类中的 Azure Maps Android SDK 来调整样式。
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: aef8fbacf8302fb5dd4b5fe28afc615c6bf56090
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100978"
---
# <a name="set-map-style-android-sdk"></a> (Android SDK 设置地图样式) 

本文介绍两种使用 Azure Maps Android SDK 设置地图样式的方法。 Azure Maps 有六个不同的地图样式可供选择。 有关支持的地图样式的详细信息，请参阅 [Azure Maps 中支持的地图样式](supported-map-styles.md)。

## <a name="prerequisites"></a>先决条件

请确保完成 [快速入门：创建 Android 应用](quick-android-map.md) 文档中的步骤。

## <a name="set-map-style-in-the-layout"></a>在布局中设置地图样式

添加地图控件时，可以在活动类的布局文件中设置地图样式。 下面的代码设置中心位置、缩放级别和地图样式。

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

以下屏幕截图显示了上面的代码，其中显示了具有灰度深色样式的道路地图。

![具有灰度深色道路地图样式的地图](media/set-android-map-styles/android-grayscale-dark.png)

## <a name="set-map-style-in-code"></a>在代码中设置地图样式

可以使用映射的方法在代码中以编程方式设置地图样式 `setStyle` 。 下面的代码使用 maps `setCamera` 方法和地图样式来设置中心位置和缩放级别 `SATELLITE_ROAD_LABELS` 。

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

以下屏幕截图显示了上面的代码，其中显示了具有卫星道路标签样式的地图。

![具有卫星道路标签样式的地图](media/set-android-map-styles/android-satellite-road-labels.png)

## <a name="setting-the-map-camera"></a>设置地图相机

地图相机控制地图中显示的地图部分。 照相机可以在代码中以编程方式进行布局。 在代码中进行设置时，可以通过两种主要方法来设置地图的位置;使用 "居中" 和 "缩放" 或 "传入" 边界框。 下面的代码演示了如何在使用和时设置所有可选 `center` 的相机选项 `zoom` 。

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

通常需要将地图集中于一组数据。 可以使用方法从特征计算边界框 `MapMath.fromData` ，并可以将其传递到 `bounds` 地图相机的选项中。 如果基于边界框设置地图视图，则指定一个 `padding` 值以考虑呈现为气泡或符号的点的像素大小通常是很有用的。 下面的代码演示了如何在使用边界框设置照相机位置时设置所有可选的相机选项。

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

请注意，边界框的纵横比可能与地图的纵横比不相同，因为这种地图通常会显示完整的边界框区域，但通常仅垂直或水平。

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer-android.md)
