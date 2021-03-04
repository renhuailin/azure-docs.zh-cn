---
title: 向 Android 地图添加多边形延伸层 |Microsoft Azure 映射
description: 如何将多边形延伸层添加到 Microsoft Azure Map Android SDK。
author: rbrundritt
ms.author: richbrun
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b62c2540dc8cf2c7d4f67d465b2d464cbc0c3091
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054530"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map-android-sdk"></a>向地图添加多边形延伸层 (Android SDK) 

本文介绍如何使用多边形延伸层将 `Polygon` 和 `MultiPolygon` 特征几何作为延伸形状。

## <a name="use-a-polygon-extrusion-layer"></a>使用多边形延伸层

将多边形延伸层连接到数据源。 然后，将其加载到地图中。 多边形延伸层会将和功能的区域 `Polygon` 呈现 `MultiPolygon` 为延伸形状。 `height` `base` 多边形延伸层的和属性定义了与延伸形状的地面和高度的基准距离（以米为 **单位**）。 下面的代码演示如何创建一个多边形，如何将其添加到数据源中，以及如何使用多边形延伸层类进行呈现。

> [!Note]
> `base`多边形延伸层中定义的值应小于或等于的值 `height` 。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.95838379859924, 40.80027995478159),
            Point.fromLngLat(-73.98154735565186, 40.76845986171129),
            Point.fromLngLat(-73.98124694824219, 40.767761062136955),
            Point.fromLngLat(-73.97361874580382, 40.76461637311633),
            Point.fromLngLat(-73.97306084632874, 40.76512830937617),
            Point.fromLngLat(-73.97259950637817, 40.76490890860481),
            Point.fromLngLat(-73.9494466781616, 40.79658450499243),
            Point.fromLngLat(-73.94966125488281, 40.79708807289436),
            Point.fromLngLat(-73.95781517028809, 40.80052360358227),
            Point.fromLngLat(-73.95838379859924, 40.80027995478159)
        )
    )
));

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillColor("#fc0303"),
    fillOpacity(0.7f),
    height(500f)
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

下面的屏幕截图显示了上面的代码，呈现多边形使用多边形拉伸层垂直拉伸。

![使用多边形延伸层垂直拉伸的多边形](media/map-extruded-polygon-android/polygon-extrusion-layer.jpg)

## <a name="add-data-driven-polygons"></a>添加数据驱动多边形

可以使用多边形延伸层来呈现等值线图地图。 将 `height` `fillColor` 延伸层的和属性设置为 `Polygon` 和特征几何中统计变量的度量值 `MultiPolygon` 。 下面的代码示例根据状态的人口密度度量值显示美国的拉伸等值线图地图。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the geojson data and add it to the data source.
Utils.importData("https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/US_States_Population_Density.json", this,  (String result) -> {
    //Parse the data as a GeoJSON Feature Collection.
    FeatureCollection fc = FeatureCollection.fromJson(result);

    //Add the feature collection to the data source.
    source.add(fc);
});

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillOpacity(0.7f),
    fillColor(
        step(
            get("density"),
            literal("rgba(0, 255, 128, 1)"),
            stop(10, "rgba(9, 224, 118, 1)"),
            stop(20, "rgba(11, 191, 103, 1)"),
            stop(50, "rgba(247, 227, 5, 1)"),
            stop(100, "rgba(247, 199, 7, 1)"),
            stop(200, "rgba(247, 130, 5, 1)"),
            stop(500, "rgba(247, 94, 5, 1)"),
            stop(1000, "rgba(247, 37, 5, 1)")
        )
    ),
    height(
        interpolate(
            linear(),
            get("density"),
            stop(0, 100),
            stop(1200, 960000)
        )
    )
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

下面的屏幕截图显示了美国等值线图地图，并将其垂直拉伸为基于人口密度的拉伸多边形。

![一种等值线图的状态地图，以垂直方式拉伸为基于人口密度的拉伸多边形](media/map-extruded-polygon-android/android-extruded-choropleth.jpg)

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [添加多边形层](how-to-add-shapes-to-android-map.md)
