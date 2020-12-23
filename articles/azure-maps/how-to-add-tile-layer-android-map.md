---
title: 向 Android 地图添加图块层 |Microsoft Azure 映射
description: 了解如何向地图添加图块层。 请参阅使用 Azure Maps Android SDK 将天气雷达图添加到地图的示例。
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8ea6f44c47c5cd4d223b053640f65827f46db482
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679299"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>向地图添加图块层 (Android SDK) 

本文介绍如何使用 Azure Maps Android SDK 在地图上呈现图块层。 通过图块层可以在 Azure Maps 基本地图图块顶部附加图像。 可在[缩放级别和图块网格](zoom-levels-and-tile-grid.md)文档中找到有关 Azure Maps 图块系统的详细信息。

图块层从服务器的磁贴中加载。 可以使用图块层所理解的命名约定，预先呈现这些图像并将其存储为服务器上的任何其他图像。 或者，可以使用动态服务呈现这些图像，这种动态服务会近乎实时生成图像。 Azure Maps TileLayer 类支持三个不同的平铺服务命名约定：

* X、Y、缩放表示法 - 基于缩放级别，x 是列，y 是图块网格中图块的行位置。
* Quadkey 表示法 - 将 x、y、缩放信息合并到单个字符串值（即图块的唯一标识符）中。
* 边界框边界方框坐标可用于指定格式的图像，该图像 `{west},{south},{east},{north}` 通常由 [Web 映射服务 (WMS) ](https://www.opengeospatial.org/standards/wms)使用。

> [!TIP]
> TileLayer 是直观显示地图上的大型数据集的好办法。 不仅可以从图像中生成图块层，而且还可以将矢量数据呈现为图块层。 通过将矢量数据呈现为图块层，地图控件只需加载磁贴，它们的文件大小可能会小得多，而不是它们所表示的矢量数据。 此方法可供需要呈现地图上的数百万行数据的用户使用。

传递到图块层中的图块 URL 必须是 TileJSON 资源的 http/https URL 或使用以下参数的图块 URL 模板： 

* `{x}` - 图块的 X 位置。 还需要 `{y}` 和 `{z}`。
* `{y}` - 图块的 Y 位置。 还需要 `{x}` 和 `{z}`。
* `{z}` - 图块的缩放级别。 还需要 `{x}` 和 `{y}`。
* `{quadkey}` - 基于必应地图图块系统命名约定的图块 quadkey 标识符。
* `{bbox-epsg-3857}` - EPSG 3857 空间引用系统中格式为 `{west},{south},{east},{north}` 的边界框字符串。
* `{subdomain}` -子域值的占位符（如果指定了子域值）。

## <a name="prerequisites"></a>先决条件

若要完成本文中的过程，需要安装 [Azure Maps Android SDK](how-to-use-android-map-control-library.md) 来加载地图。

## <a name="add-a-tile-layer-to-the-map"></a>向地图添加图块层

此示例演示如何创建指向一组图块的图块层。 此示例使用 "x，y，zoom" 平铺系统。 此图块层的源是 [OpenSeaMap 项目](https://openseamap.org/index.php)，其中包含更有源的目标。 通常在查看图块层时，最好能够清楚地查看地图上的城市标签。 可以通过将图块层插入到地图标签层下方来实现此行为。

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

以下屏幕截图显示了上面的代码，该代码在具有深色灰度样式的地图上显示海里信息的平铺层。

![显示图块层的 Android 地图](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解有关如何设置地图样式的详细信息

> [!div class="nextstepaction"]
> [更改地图样式](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [添加热度地图](map-add-heat-map-layer-android.md)
