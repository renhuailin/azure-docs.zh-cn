---
title: 向 Android 地图添加线条层 |Microsoft Azure 映射
description: 了解如何向 maps 添加行。 请参阅使用 Azure Maps Android SDK 将线条图层添加到地图以及使用符号和颜色渐变自定义线条的示例。
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 3e68be79a4405af103512a9009187857a0d9af39
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681447"
---
# <a name="add-a-line-layer-to-the-map-android-sdk"></a>向地图添加线条层 (Android SDK) 

线条层可用于将 `LineString` 和 `MultiLineString` 特征呈现为地图上的路径或路由。 线条层还可用于呈现 `Polygon` 和 `MultiPolygon` 特征的轮廓。 数据源连接到线条层以向它提供要呈现的数据。

> [!TIP]
> 默认情况下，线条层将呈现数据源中的多边形和线条的坐标。 若要将该层限制为仅呈现 LineString 几何特征，请将 `filter` 层的选项设置为 `eq(geometryType(), "LineString")` 。 如果还想要包含 MultiLineString 功能，请将层的 `filter` 选项设置为 `any(eq(geometryType(), "LineString"), eq(geometryType(), "MultiLineString"))` 。

## <a name="prerequisites"></a>先决条件

请确保完成 [快速入门：创建 Android 应用](quick-android-map.md) 文档中的步骤。 本文中的代码块可以插入到 maps `onReady` 事件处理程序中。

## <a name="add-a-line-layer"></a>添加线条层

以下代码演示如何创建线条。 将行添加到数据源，然后使用类以线条层呈现 `LineLayer` 。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a list of points.
List<Point> points = Arrays.asList(
    Point.fromLngLat(-73.972340, 40.743270),
    Point.fromLngLat(-74.004420, 40.756800));

//Create a LineString geometry and add it to the data source.
source.add(LineString.fromLngLats(points));

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor("blue"),
    strokeWidth(5f)
);

map.layers.add(layer);
```

以下屏幕截图显示了在线条层中呈现线条的上述代码。

![使用线条层绘制的线条的地图](media/android-map-add-line-layer/android-line-layer.png)

## <a name="data-drive-line-style"></a>数据驱动器线条样式

下面的代码创建两个行功能，并将速度限制值作为属性添加到每行。 线条层根据速度限制值使用数据驱动器样式表达式颜色。 由于直线数据覆盖沿公路，下面的代码会将线条层添加到标签层下方，以便仍可以清楚地阅读道路标签。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a line feature.
Feature feature = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.131821, 47.704033),
        Point.fromLngLat(-122.099919, 47.703678))));

//Add a property to the feature.
feature.addNumberProperty("speedLimitMph", 35);

//Add the feature to the data source.
source.add(feature);

//Create a second line feature.
Feature feature2 = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.126662, 47.708265),
        Point.fromLngLat(-122.126877, 47.703980))));

//Add a property to the second feature.
feature2.addNumberProperty("speedLimitMph", 15);

//Add the second feature to the data source.
source.add(feature2);

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor(
        interpolate(
            linear(),
            get("speedLimitMph"),
            stop(0, color(Color.GREEN)),
            stop(30, color(Color.YELLOW)),
            stop(60, color(Color.RED))
        )
    ),
    strokeWidth(5f)
);

map.layers.add(layer, "labels");
```

以下屏幕截图显示了上面的代码，该代码在线条层中呈现两行颜色，并根据行功能中的属性从数据驱动样式表达式中检索其颜色。

![使用在线条层中呈现的数据驱动器样式线条映射](media/android-map-add-line-layer/android-line-layer-data-drive-style.png)

## <a name="add-symbols-along-a-line"></a>沿线条添加符号

此示例演示如何沿地图上的线条添加箭头图标。 使用符号层时，请将 `symbolPlacement` 选项设置为 `SymbolPlacement.LINE` 。 此选项会沿线条呈现符号并旋转图标（0 度 = 右）。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add("arrow-icon", R.drawable.purple-arrow-right);

//Create and add a line to the data source.
source.add(LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.18822, 47.63208),
        Point.fromLngLat(-122.18204, 47.63196),
        Point.fromLngLat(-122.17243, 47.62976),
        Point.fromLngLat(-122.16419, 47.63023),
        Point.fromLngLat(-122.15852, 47.62942),
        Point.fromLngLat(-122.15183, 47.62988),
        Point.fromLngLat(-122.14256, 47.63451),
        Point.fromLngLat(-122.13483, 47.64041),
        Point.fromLngLat(-122.13466, 47.64422),
        Point.fromLngLat(-122.13844, 47.65440),
        Point.fromLngLat(-122.13277, 47.66515),
        Point.fromLngLat(-122.12779, 47.66712),
        Point.fromLngLat(-122.11595, 47.66712),
        Point.fromLngLat(-122.11063, 47.66735),
        Point.fromLngLat(-122.10668, 47.67035),
        Point.fromLngLat(-122.10565, 47.67498))));

//Create a line layer and add it to the map.
map.layers.add(new LineLayer(source,
    strokeColor("DarkOrchid"),
    strokeWidth(5f)
));

//Create a symbol layer and add it to the map.
map.layers.add(new SymbolLayer(source,
    //Space symbols out along line.
    symbolPlacement(SymbolPlacement.LINE),

    //Spread the symbols out 100 pixels apart.
    symbolSpacing(100f),
    
    //Use the arrow icon as the symbol.
    iconImage("arrow-icon"),

    //Allow icons to overlap so that they aren't hidden if they collide with other map elements.
    iconAllowOverlap(true),

    //Center the symbol icon.
    iconAnchor(AnchorType.CENTER),

    //Scale the icon size.
    iconSize(0.8f)
));
```

对于本示例，以下图像已加载到应用程序的 "图形" 文件夹中。

| ![紫色箭头图标图像](media/android-map-add-line-layer/purple-arrow-right.png)|
|:-----------------------------------------------------------------------:|
|                                                  |

下面的屏幕截图显示了上面的代码，其中显示了一个带有箭头图标的行。

![使用数据驱动器样式线条映射，线条图层中呈现箭头](media/android-map-add-line-layer/android-symbols-along-line-path.png)

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [添加多边形层](how-to-add-shapes-to-android-map.md)
