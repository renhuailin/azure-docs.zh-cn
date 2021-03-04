---
title: 向 Android 地图添加控件 |Microsoft Azure 映射
description: 如何将缩放控件、螺距控件、旋转控件和样式选取器添加到 Microsoft Azure map Android SDK 中的地图。
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 90d037fc02bdc1c4d6fe682386790561c890c1e6
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100213"
---
# <a name="add-controls-to-a-map-android-sdk"></a>向地图添加控件 (Android SDK) 

本文说明如何将 UI 控件添加到地图。

## <a name="add-zoom-control"></a>添加缩放控件

缩放控件添加了用于放大和缩小地图的按钮。下面的代码示例创建类的实例 `ZoomControl` 并将其添加到映射。

::: zone pivot="programming-language-java-android"

```java
//Construct a zoom control and add it to the map.
map.controls.add(new ZoomControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a zoom control and add it to the map.
map.controls.add(ZoomControl())
```

::: zone-end

下面的屏幕截图是在地图上加载的缩放控件。

![添加到地图的缩放控件](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>添加绕 X 轴旋转控件

螺距控件添加倾斜的按钮，以相对于水平位置进行地图。 下面的代码示例创建类的实例 `PitchControl` 并将其添加到映射。

::: zone pivot="programming-language-java-android"

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a pitch control and add it to the map.
map.controls.add(PitchControl())
```

::: zone-end

下面的屏幕截图是在地图上加载的螺距控件。

![添加到地图的螺距控件](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>添加指南针控件

指南针控件添加了用于旋转地图的按钮。 下面的代码示例创建类的实例 `CompassControl` 并将其添加到映射。

::: zone pivot="programming-language-java-android"

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a compass control and add it to the map.
map.controls.add(CompassControl())
```

::: zone-end

下面的屏幕截图是在地图上加载的罗盘控件。

![添加到地图的罗盘控件](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>添加流量控制

流量控件添加一个按钮，用于切换地图上流量数据的可见性。 下面的代码示例创建类的实例 `TrafficControl` 并将其添加到映射。

::: zone pivot="programming-language-java-android"

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a traffic control and add it to the map.
map.controls.add(TrafficControl())
```

::: zone-end

下面的屏幕截图是在映射上加载的流量控件。

![添加到地图的流量控件](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>包含所有控件的地图

可以将多个控件置于数组中，并将其同时添加到地图的同一区域，以简化开发。 下面的方法使用此方法将标准导航控件添加到地图中。

::: zone pivot="programming-language-java-android"

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.controls.add(
    arrayOf<Control>(
        ZoomControl(),
        CompassControl(),
        PitchControl(),
        TrafficControl()
    )
)
```

::: zone-end

下面的屏幕截图显示了地图上加载的所有控件。 请注意，将它们添加到地图的顺序就是它们的显示顺序。

![添加到地图的所有控件](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [添加线条层](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](how-to-add-shapes-to-android-map.md)
