---
title: 向 Android 地图添加图块层 | Microsoft Docs
description: 了解如何向地图添加图块层。 请参阅示例了解如何使用 Azure Maps Android SDK 向地图添加天气雷达覆盖。
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 6a920dc222cae4aedd77b667644de317637bbb69
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047496"
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

## <a name="next-steps"></a>后续步骤

请参阅以下文章，详细了解在地图上覆盖图像的方法。

> [!div class="nextstepaction"]
> [图像层](map-add-image-layer-android.md)
