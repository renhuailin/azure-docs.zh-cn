---
title: 向 Android 地图添加图像层 | Microsoft Azure Maps
description: 了解如何向地图添加图像。 了解如何使用 Azure Maps Android SDK 自定义图像层并将图像覆盖在固定坐标集上。
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 4c11a1275b94eebba33819ae6c9ffeb313a3ce0b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463097"
---
# <a name="add-an-image-layer-to-a-map-android-sdk"></a>向地图添加图像层 (Android SDK)

本文介绍如何将图像覆盖到固定坐标集。 下面是可以在地图上覆盖的不同图像类型的几个示例：

* 从无人机捕获的图像
* 建筑平面图
* 历史或其他专门的地图图像
* 工作地点蓝图

> [!TIP]
> 借助图像层，可以轻松将图像覆盖在地图上。 请注意，大幅图像可能会占用大量内存，并可能导致性能问题。 在这种情况下，请考虑将图像分解为图块并将其作为图块层加载到地图中。

## <a name="add-an-image-layer"></a>添加图像层

下面的代码在地图上覆盖 1922 年新泽西州纽瓦克地图的图像。 此图像将添加到项目的 `drawable` 文件夹中。 通过设置图像并以 `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` 格式设置四个角的坐标来创建图像层。 通常需要在 `label` 层下方添加图像层。

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

或者，可以指定联机托管图像的 URL。 但是，如果方案允许，将图像添加到项目的 `drawable` 文件夹，这样加载速度会更快，因为图像将在本地提供，不需要下载。

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

以下屏幕截图显示了使用图像层覆盖的 1922 年新泽西州纽瓦克的地图。

![使用图像层覆盖的 1922 年新泽西州纽瓦克的地图](media/map-add-image-layer-android/android-image-layer.gif)

## <a name="import-a-kml-file-as-ground-overlay"></a>导入 KML 文件作为地面覆盖

此示例演示如何将 KML 地面覆盖信息覆盖为地图上的图像层。 KML 地面覆盖提供东、南、西、北坐标和逆时针旋转。 但图像层需要图像每个角的坐标。 此示例中的 KML 地面覆盖是来自[维基媒体](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)的 Chartres 大教堂。

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

此代码使用 `ImageLayer` 类中的静态 `getCoordinatesFromEdges` 方法。 此方法使用 KML 地面覆盖的东、南、西、北和旋转信息来计算图像的四个角。

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

以下屏幕截图显示了使用图像层覆盖的 KML 地面覆盖的地图。

![使用图像层覆盖的 KML 地面覆盖的地图](media/map-add-image-layer-android/android-ground-overlay.jpg)

> [!TIP]
> 使用图像层类的 `getPixels` 和 `getPositions` 方法在定位图像层的地理坐标和本地图像像素坐标之间转换。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，详细了解在地图上覆盖图像的方法。

> [!div class="nextstepaction"]
> [添加图块层](how-to-add-tile-layer-android-map.md)
