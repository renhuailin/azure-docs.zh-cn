---
title: 向 Android 地图添加控件 | Microsoft Azure Maps
description: 如何向 Microsoft Azure Maps Android SDK 中的地图添加缩放控件、绕 X 轴旋转控件、旋转控件和样式选取器。
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 5599efb61c02c05ea3908d660ca0f1c11bea1ca7
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425295"
---
# <a name="add-controls-to-a-map-android-sdk"></a>向地图添加控件 (Android SDK)

本文展示了如何向地图添加 UI 控件。

## <a name="add-zoom-control"></a>添加缩放控件

缩放控件添加了用于放大和缩小地图的按钮。下面的代码示例创建 `ZoomControl` 类的一个实例并将其添加到地图中。

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

下面的屏幕截图显示了地图上加载的缩放控件。

![已添加到地图的缩放控件](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>添加绕 X 轴旋转控件

绕 X 轴旋转控件添加了用于倾斜地图平面以相对于地平线来绘制地图的按钮。 下面的代码示例创建 `PitchControl` 类的一个实例并将其添加到地图中。

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

下面的屏幕截图显示了地图上加载的绕 X 轴旋转控件。

![已添加到地图的绕 X 轴旋转控件](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>添加指南针控件

指南针控件添加了用于旋转地图的按钮。 下面的代码示例创建 `CompassControl` 类的一个实例并将其添加到地图中。

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

下面的屏幕截图显示了地图上加载的指南针控件。

![已添加到地图的指南针控件](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>添加交通控件

交通控件添加了一个按钮，用于切换地图上交通数据的可见性。 下面的代码示例创建 `TrafficControl` 类的一个实例并将其添加到地图中。

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

下面的屏幕截图显示了地图上加载的交通控件。

![已添加到地图的交通控件](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>包含所有控件的地图

可以将多个控件放入一个数组并同时添加到地图中，然后放置在地图的相同区域，以简化开发。 下面的代码使用这种方法将标准导航控件添加到地图中。

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

下面的屏幕截图显示了地图上加载的所有控件。 它们添加到地图的顺序就是它们的显示顺序。

![已添加到地图的所有控件](media/map-add-controls-android/android-all-controls.jpg)

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
