---
title: 向 Android 地图添加图块层 | Microsoft Docs
description: 了解如何向地图添加图块层。 请参阅示例了解如何使用 Azure Maps Android SDK 向地图添加天气雷达覆盖。
author: anastasia-ms
ms.author: v-stharr
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: d35b1201d0f60c517f419186d01f493efa9fbeba
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435233"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>向地图添加图块层 (Android SDK)

本文介绍如何使用 Azure Maps Android SDK 在地图上呈现图块层。 通过图块层可以在 Azure Maps 基本地图图块顶部附加图像。 可在[缩放级别和图块网格](zoom-levels-and-tile-grid.md)文档中找到有关 Azure Maps 图块系统的详细信息。

图块层将从服务器加载图块。 这些图像可以使用图块层能理解的命名约定（像服务器上的任何其他图像一样）进行预呈现和存储。 或者，可以使用近乎实时生成图像的动态服务来呈现这些图像。 Azure Maps TileLayer 类支持以下三个不同的图块服务命名约定：

* X、Y、缩放表示法 - 基于缩放级别，x 是列，y 是图块网格中图块的行位置。
* Quadkey 表示法 - 将 x、y、缩放信息合并到单个字符串值（即图块的唯一标识符）中。
* 边界框 - 边界框坐标可用于指定格式为 `{west},{south},{east},{north}` 的图像，这通常由 [Web 地图定位服务 (WMS)](https://www.opengeospatial.org/standards/wms) 使用。

> [!TIP]
> TileLayer 是直观显示地图上的大型数据集的好办法。 不仅可以从图像中生成图块层，而且还可以将矢量数据呈现为图块层。 通过将矢量数据呈现为图块层，地图控件只需加载文件大小远小于它们所代表的矢量数据的图块。 此方法可供需要呈现地图上的数百万行数据的用户使用。

传递到图块层中的图块 URL 必须是 TileJSON 资源的 http/https URL 或使用以下参数的图块 URL 模板：

* `{x}` - 图块的 X 位置。 还需要 `{y}` 和 `{z}`。
* `{y}` - 图块的 Y 位置。 还需要 `{x}` 和 `{z}`。
* `{z}` - 图块的缩放级别。 还需要 `{x}` 和 `{y}`。
* `{quadkey}` - 基于必应地图图块系统命名约定的图块 quadkey 标识符。
* `{bbox-epsg-3857}` - EPSG 3857 空间引用系统中格式为 `{west},{south},{east},{north}` 的边界框字符串。
* `{subdomain}` - 子域值占位符（如果指定了子域值）。
* `azmapsdomain.invalid` - 用于将图块请求的域和身份验证与地图所使用的相同值对齐的占位符。 在调用由 Azure Maps 托管的图块服务时使用此参数。

## <a name="prerequisites"></a>先决条件

若要完成本文中的过程，需要安装 [Azure Maps Android SDK](how-to-use-android-map-control-library.md) 来加载地图。

## <a name="add-a-tile-layer-to-the-map"></a>将图块层添加到地图

此示例演示如何创建指向一组图块的图块层。 此示例使用“x、y、缩放”图块系统。 此图块层的源是 [OpenSeaMap 项目](https://openseamap.org/index.php)，其中包含众包航海图。 通常在查看图块层时，最好能清楚地查看地图上的城市标签。 通过在地图标签层下插入图块层，可以实现此行为。

::: zone pivot="programming-language-java-android"

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
)

map.layers.add(layer, "labels")
```

::: zone-end

以下屏幕截图显示上述代码在地图上显示了一个深色灰度类型的航海信息图块层。

![显示图块层的 Android 地图](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="add-an-ogc-web-mapping-service-wms"></a>添加 OGC Web 地图定位服务 (WMS)

Web 地图定位服务 (WMS) 是一项开放地理空间信息联盟 (OGC) 标准，用于提供地图数据的图像。 此格式提供了许多可用于 Azure Maps 的开放数据集。 如果此类型的服务支持 `EPSG:3857` 坐标参考系 (CRS)，该服务就可用于图块层。 使用 WMS 服务时，将宽度和高度参数设置为该服务支持的同一个值，请确保在 `tileSize` 选项中设置这个值。 在带格式的 URL 中，将服务的 `BBOX` 参数设置为 `{bbox-epsg-3857}` 占位符。

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
)

map.layers.add(layer, "labels")
```

::: zone-end

下面的屏幕截图显示的是上面的代码在地图上的标签下覆盖了来自[美国地质调查局 (USGS)](https://mrdata.usgs.gov/) 的地质数据的 Web 地图定位服务后的情况。

![显示 WMS 图块层的 Android 地图](media/how-to-add-tile-layer-android-map/android-tile-layer-wms.jpg)

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>添加 OGC Web 地图定位图块服务 (WMTS)

Web 地图定位图块服务 (WMTS) 是一项开放地理空间信息联盟 (OGC) 标准，用于为地图提供基于图库的重叠。 此格式提供了许多可用于 Azure Maps 的开放数据集。 如果此类型的服务支持 `EPSG:3857` 或 `GoogleMapsCompatible` 坐标参考系 (CRS)，该服务就可用于图块层。 使用 WMTS 服务时，将宽度和高度参数设置为该服务支持的同一个值，请确保在 `tileSize` 选项中设置这个值。 在带格式的 URL 中，相应地替换以下占位符：

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
);

map.layers.add(layer, "transit");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
)

map.layers.add(layer, "transit")
```

::: zone-end

下面的屏幕截图显示的是上面的代码在地图上的道路和标签下覆盖了来自[美国地质调查局 (USGS) 国家地图](https://viewer.nationalmap.gov/services/)的图像的 Web 地图定位图块服务后的情况。

![显示 WMTS 图块层的 Android 地图](media/how-to-add-tile-layer-android-map/android-tile-layer-wmts.jpg)

## <a name="next-steps"></a>后续步骤

请参阅以下文章，详细了解在地图上覆盖图像的方法。

> [!div class="nextstepaction"]
> [图像层](map-add-image-layer-android.md)
