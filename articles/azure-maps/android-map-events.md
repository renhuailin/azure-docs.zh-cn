---
title: 在 Android 地图中处理地图事件 | Microsoft Azure Maps
description: 了解用户与地图交互时触发的事件。 查看所有受支持地图事件的列表。 了解如何使用 Azure Maps Android SDK 处理事件。
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 49a06fdd2c8025515fd42124aa7d00f27d8c5978
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425709"
---
# <a name="interact-with-the-map-android-sdk"></a>与地图交互 (Android SDK)

本文介绍如何使用地图事件管理器。

## <a name="interact-with-the-map"></a>与地图交互

地图通过其 `events` 属性管理所有事件。 下表列出了所有受支持的地图事件。

| 事件                  | 事件处理程序格式 | 说明 |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>在地图进入“空闲”状态之前呈现的最后一帧之后触发：<ul><li>没有正在进行的照相机转换。</li><li>当前请求的所有图块均已加载。</li><li>所有淡入/过渡动画均已完成。</li></ul></p> |
| `OnCameraMove`         | `()`                 | 因用户交互或方法而在从一个视图到另一个视图的动画过渡期间反复触发。 |
| `OnCameraMoveCanceled` | `()`                 | 取消对相机的移动请求时触发。 |
| `OnCameraMoveStarted`  | `(int reason)`       | 因用户交互或方法而在地图开始从一个视图过渡到另一个视图前触发。 事件侦听器的 `reason` 参数返回一个整数值，该值提供有关如何开始相机移动的详细信息。 以下列表概述了可能的原因：<ul><li>1：手势</li><li>2：开发人员动画</li><li>3：API 动画</li></ul>   |
| `OnClick`              | `(double lat, double lon): boolean` | 在地图上的同一点按下并释放地图时触发。 此事件处理程序将返回一个布尔值，用以指示是否应使用事件或将事件进一步传递给其他事件侦听器。 |
| `OnFeatureClick`       | `(List<Feature>): boolean`    | 在功能上的同一点按下并释放地图时触发。 此事件处理程序将返回一个布尔值，用以指示是否应使用事件或将事件进一步传递给其他事件侦听器。 |
| `OnLayerAdded` | `(Layer layer)` | 在向地图添加层时触发。 |
| `OnLayerRemoved` | `(Layer layer)` | 在从地图移除层时触发。 |
| `OnLoaded` | `()` | 在下载了所有必需的资源并进行了第一次视觉上完整的地图呈现后立即触发。 |
| `OnLongClick`          | `(double lat, double lon): boolean` | 在地图上的同一点按下地图并保持一段时间，然后将其释放时触发。 此事件处理程序将返回一个布尔值，用以指示是否应使用事件或将事件进一步传递给其他事件侦听器。 |
| `OnLongFeatureClick `  | `(List<Feature>): boolean`    | 在功能上的同一点按下地图并保持一段时间，然后将其释放时触发。 此事件处理程序将返回一个布尔值，用以指示是否应使用事件或将事件进一步传递给其他事件侦听器。 |
| `OnReady`              | `(AzureMap map)`     | 最初加载地图时，或应用方向发生更改并加载了所需的最少地图资源，并且地图已经可以通过编程方式进行交互时，便会触发。 |
| `OnSourceAdded` | `(Source source)` | 在向地图添加 `DataSource` 或 `VectorTileSource` 时触发。 |
| `OnSourceRemoved` | `(Source source)` | 在从地图移除 `DataSource` 或 `VectorTileSource` 时触发。 |
| `OnStyleChange` | `()` | 在加载或更改地图的样式时触发。 |

以下代码演示如何将 `OnClick`、`OnFeatureClick` 和 `OnCameraMove` 事件添加到地图。

::: zone pivot="programming-language-java-android"

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.events.add(OnClick { lat: Double, lon: Double -> 
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnCameraMove {
    //Map camera moved.
})
```

::: zone-end

有关详细信息，请参阅[导航地图](how-to-use-android-map-control-library.md#navigating-the-map)文档，了解如何与地图交互和触发事件。

## <a name="scope-feature-events-to-layer"></a>将功能事件范围划分为层

将 `OnFeatureClick` 或 `OnLongFeatureClick` 事件添加到地图时，可以将层实例或层 ID 作为第二个参数传递。 传入层时，事件只有在该层上才会触发。 符号层、气泡层、直线层和多边形层均支持范围为层的事件。

::: zone pivot="programming-language-java-android"

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source.
val source = DataSource()
map.sources.add(source)

//Add data to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a layer and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnFeatureClick { features: List<Feature?>? -> 
        //One or more features clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnLongFeatureClick { features: List<Feature?>? -> 
         //One or more features long clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)
```

::: zone-end

## <a name="next-steps"></a>后续步骤

有关完整代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [在地图中导航](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [添加线条层](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](how-to-add-shapes-to-android-map.md)
