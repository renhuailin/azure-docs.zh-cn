---
title: 在 Android maps 中处理地图事件 |Microsoft Azure 映射
description: 了解用户与地图进行交互时要触发的事件。 查看所有受支持的地图事件的列表。 请参阅如何使用 Azure Maps Android SDK 来处理事件。
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 818d33947fa079a130c3009a34dcb9b72ad52f91
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681433"
---
# <a name="interact-with-the-map-android-sdk"></a>与地图 (Android SDK 交互) 

本文介绍如何使用 maps 事件管理器。

## <a name="interact-with-the-map"></a>与地图交互

地图通过其属性管理所有事件 `events` 。 下表列出了所有支持的映射事件。

| 事件                  | 事件处理程序格式 | 描述 |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>在地图进入“空闲”状态之前呈现的最后一帧之后触发：<ul><li>没有正在进行的照相机转换。</li><li>当前请求的所有图块均已加载。</li><li>所有淡入/过渡动画均已完成。</li></ul></p> |
| `OnCameraMove`         | `()`                 | 因用户交互或方法而在从一个视图到另一个视图的动画过渡期间反复触发。 |
| `OnCameraMoveCanceled` | `()`                 | 在取消对照相机的移动请求时触发。 |
| `OnCameraMoveStarted`  | `(int reason)`       | 因用户交互或方法而在地图开始从一个视图过渡到另一个视图前触发。 `reason`事件侦听器的自变量返回一个整数值，它提供了有关如何启动照相机移动的详细信息。 下面列出了可能的原因：<ul><li>1：手势</li><li>2：开发人员动画</li><li>3： API 动画</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | 在地图上按下并释放地图时激发。 |
| `OnFeatureClick`       | `(List<Feature>)`    | 在功能上按下并释放地图时激发。  |
| `OnLongClick`          | `(double lat, double lon)` | 在按下地图时触发，并在地图上的同一点释放。 |
| `OnLongFeatureClick `  | `(List<Feature>)`    | 在按下地图时触发，并在某一特征的同一点释放。 |
| `OnReady`              | `(AzureMap map)`     | 当最初加载映射时，或在应用程序方向发生变化并且加载了所需的最小映射资源并且映射可以以编程方式与进行交互时触发。 |

下面的代码演示如何将 `OnClick` 、 `OnFeatureClick` 和 `OnCameraMove` 事件添加到映射。

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

有关详细信息，请参阅有关如何与 map 和 trigger 事件交互的 [导航图](how-to-use-android-map-control-library.md#navigating-the-map) 文档。

## <a name="scope-feature-events-to-layer"></a>将功能事件作用域分层

`OnFeatureClick`向映射添加或 `OnLongFeatureClick` 事件时，可以将层 ID 作为第二个参数传入。 当传入层 ID 时，只有当该事件发生在该层上时，才会触发事件。 作用域为层的事件受符号、气泡、线条和多边形层的支持。

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
}, layer.getId());

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer.getId());
```

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
