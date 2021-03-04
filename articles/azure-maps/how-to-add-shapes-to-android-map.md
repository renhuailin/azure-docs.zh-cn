---
title: 向 Android 地图添加多边形层 |Microsoft Azure 映射
description: 了解如何将多边形或圆添加到地图中。 了解如何使用 Azure Maps Android SDK 自定义几何形状，并使其易于更新和维护。
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 25785ae7a214d6122fb90b80e8f0725a3468c48d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047584"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>向地图添加多边形层 (Android SDK) 

本文介绍如何使用多边形层渲染地图上的 `Polygon` 和 `MultiPolygon` 特征几何图形区域。

## <a name="prerequisites"></a>先决条件

请确保完成 [快速入门：创建 Android 应用](quick-android-map.md) 文档中的步骤。 本文中的代码块可以插入到 maps `onReady` 事件处理程序中。

## <a name="use-a-polygon-layer"></a>使用多边形层

将多边形层连接到数据源并加载到地图上时，它将渲染具有 `Polygon` 和 `MultiPolygon` 特征的区域。 若要创建多边形，请将其添加到数据源，并使用类在多边形层中进行呈现 `PolygonLayer` 。

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

以下屏幕截图显示了使用多边形层呈现多边形区域的上述代码。

![呈现填充区域的多边形](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>结合使用多边形和线条层

线条层用于渲染多边形的轮廓。 下面的代码示例像上一个示例一样渲染了一个多边形，但添加了一个线条层。 此线条层是连接到数据源的另一个层。  

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

下面的屏幕截图显示了上面的代码，该代码使用线条图层渲染了其轮廓。

![呈现填充区域和轮廓的多边形](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> 当使用线条层来显示多边形的轮廓时，请确保在多边形中关闭所有环，使每个点数组具有相同的起点和终点。 如果未执行此操作，则线条层可能不会将多边形的最后一个点连接到第一个点。

## <a name="fill-a-polygon-with-a-pattern"></a>使用图案填充多边形

除了为多边形填充颜色之外，还可以使用图像图案来进行填充。 将图像模式加载到地图图像 sprite 资源，然后使用多边形层的选项引用此图像 `fillPattern` 。

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

对于本示例，以下图像已加载到应用程序的 "图形" 文件夹中。

| ![紫色箭头图标图像](media/how-to-add-shapes-to-android-map/fill-checker-red.png)|
|:-----------------------------------------------------------------------:|
| fill_checker_red.png                                                    |

下面是上面代码的屏幕截图，它在地图上呈现一个带有填充模式的多边形。

![在地图上呈现填充模式的多边形](media/how-to-add-shapes-to-android-map/android-polygon-pattern.jpg)

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
