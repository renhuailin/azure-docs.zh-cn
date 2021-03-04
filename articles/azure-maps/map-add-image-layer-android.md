---
title: 向 Android 地图添加图像层 |Microsoft Azure 映射
description: 了解如何将图像添加到地图中。 请参阅如何使用 Azure Maps Android SDK 自定义图像层并覆盖固定坐标集上的图像。
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e1d99297c0357039606149bdf7e5a526258fc7c5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054458"
---
# <a name="add-an-image-layer-to-a-map-android-sdk"></a>向地图添加图像层 (Android SDK) 

本文介绍如何将图像叠加到一组固定的坐标。 下面是可以在地图上重叠的不同图像类型的几个示例：

* 从无人机捕获的映像
* 构建 floorplans
* 历史或其他专用地图图像
* 作业站点的蓝图

> [!TIP]
> 使用图像层可以轻松地在地图上覆盖图像。 请注意，大图像可能会占用大量内存，并可能会导致性能问题。 在这种情况下，请考虑将图像分解为磁贴，并将其作为图块层加载到地图中。

## <a name="add-an-image-layer"></a>添加图像层

下面的代码在地图上覆盖 [1922 的纽瓦克、New Jersey 的图](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) 的图像。 此图像将添加到 `drawable` 项目的文件夹中。 图像层是通过设置格式中四个角的图像和坐标来创建的 `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` 。 通常需要在层下方添加图像层 `label` 。

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setImage(R.drawable.newark_nj_1922)
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216)   //Bottom Left Corner
        )
    ),
    setImage(R.drawable.newark_nj_1922)
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

此外，还可以指定联机承载的图像的 URL。 但是，如果你的方案允许，请将映像添加到你 `drawable` 的项目文件夹，这将更快加载，因为映像将在本地可用且无需下载。

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216) //Bottom Left Corner
        )
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

以下屏幕截图显示了使用图像层从1922重叠的纽瓦克、新 Jersey 的地图。

![使用图像层从1922重叠的纽瓦克、New Jersey 的映射](media/map-add-image-layer-android/android-image-layer.gif)

## <a name="import-a-kml-file-as-ground-overlay"></a>导入 KML 文件作为地面覆盖

此示例演示如何将 KML 地面叠加信息添加为地图上的图像层。 KML 地面叠加提供北方、南部、东和西坐标以及逆时针旋转。 但图像层需要图像每个角的坐标。 本示例中的 KML 地面覆盖适用于 Chartres cathedral，其来源为 [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom">
<GroundOverlay>
    <name>Map of Chartres cathedral</name>
    <Icon>
        <href>https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png</href>
        <viewBoundScale>0.75</viewBoundScale>
    </Icon>
    <LatLonBox>
        <north>48.44820923628113</north>
        <south>48.44737203258976</south>
        <east>1.488833825534365</east>
        <west>1.486788581643038</west>
        <rotation>46.44067597839695</rotation>
    </LatLonBox>
</GroundOverlay>
</kml>
```

此代码使用 `getCoordinatesFromEdges` 类中的静态方法 `ImageLayer` 。 此方法使用 KML 地面叠加的北部、南部、东、西和旋转信息来计算图像的四个角。

::: zone pivot="programming-language-java-android"

```java
//Calculate the corner coordinates of the ground overlay.
Position[] corners = ImageLayer.getCoordinatesFromEdges(
    //North, south, east, west
    48.44820923628113, 48.44737203258976, 1.488833825534365, 1.486788581643038,

    //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
    360 - 46.44067597839695
);

//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Calculate the corner coordinates of the ground overlay.
val corners: Array<Position> =
    ImageLayer.getCoordinatesFromEdges( //North, south, east, west
        48.44820923628113,
        48.44737203258976,
        1.488833825534365,
        1.486788581643038,  //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
        360 - 46.44067597839695
    )

//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

以下屏幕截图显示了一个地图，其中包含使用图像层重叠的 KML 地面重叠。

![使用图像层 KML 地面覆盖的地图](media/map-add-image-layer-android/android-ground-overlay.jpg)

> [!TIP]
> 使用 `getPixels` `getPositions` 图像层类的和方法来转换定位图像层和本地图像像素坐标的地理坐标。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解有关在地图上覆盖图像的方法的详细信息。

> [!div class="nextstepaction"]
> [添加图块层](how-to-add-tile-layer-android-map.md)