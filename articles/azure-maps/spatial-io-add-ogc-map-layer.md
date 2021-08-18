---
title: 添加开放地理空间信息联盟 (OGC) 地图层 | Microsoft Azure Maps
description: 了解如何覆盖地图上的 OGC 地图层，以及如何使用 OgcMapLayer 类中的不同选项。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 387521e07108aeb101361c3720ea6347ded58c75
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735862"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>通过开放地理空间信息联盟 (OGC) 添加地图层

`atlas.layer.OgcMapLayer` 类可以在地图上覆盖 Web 地图服务 (WMS) 图像和 Web 地图图块服务 (WMTS)。 WMS 是由 OGC 开发的一种标准协议，目的是通过 Internet 提供地理参考地图图像。 图像地理参考是指将图像与地理位置相关联的过程。 WMTS 也是 OGC 开发的标准协议。 WMTS 旨在提供预呈现并且有地理参考的地图图块。

以下部分概述了 `OgcMapLayer` 类支持的 Web 地图服务功能。

Web 地图服务 (WMS)

- 支持的版本：`1.0.0`、`1.1.0`、`1.1.1` 和 `1.3.0`
- 该服务必须支持 `EPSG:3857` 投影系统，或者必须能够处理二次投影。
- GetFeatureInfo 要求服务支持 `EPSG:4326` 或能够处理二次投影。 
- 支持的操作：

    | 操作 | 说明 |
    | :-- | :-- |
    | GetCapabilities | 检索有关包含支持功能的服务的元数据 |
    | GetMap | 检索指定区域的地图图像 |
    | GetFeatureInfo | 检索 `feature_info`，其中包含有关该功能的底层数据 |

**Web 地图图块服务 (WMTS)**

- 支持的版本：`1.0.0`
- 图块必须是正方形，比如 `TileWidth == TileHeight`。
- 支持 CRS：`EPSG:3857` 或 `GoogleMapsCompatible` 
- TileMatrix 标识符必须是一个整数值，对应于地图上缩放级别。 在 Azure 地图上，缩放级别是介于 `"0"` 到 `"22"` 之间的值。 因此，支持 `"0"`，但不支持 `"00"`。
- 支持的操作：

    | 操作 | 说明 |
    | :-- | :-- |
    | GetCapabilities | 检索支持的操作和功能 |
    | GetTile | 检索特定图块的图像 |

## <a name="overlay-an-ogc-map-layer"></a>覆盖 OGC 地图层

`url`可以是服务的基 URL，也可以是完整 URL（包含用于获取服务功能的查询）。 根据提供的详细信息，WFS 客户端可能会尝试使用几种标准 URL 格式来确定首次访问服务的方式。

下面的代码演示如何覆盖地图上的 OGC 地图层。

<br/>

<iframe height='700' scrolling='no' title='OGC 地图层示例' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC 地图层示例</a>。
</iframe>

## <a name="ogc-map-layer-options"></a>OGC 地图层选项

下面的示例演示了不同的 OGC 地图层选项。 可以单击右上角的 code pen 按钮来编辑 code pen。

<br/>

<iframe height='700' scrolling='no' title='OGC 地图层选项' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC 地图层选项</a>。
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC Web 地图服务资源管理器

以下工具将来自 Web 地图服务 (WMS) 和 Web 地图图块服务 (WMTS) 的图像叠加为“层”。 可以选择在地图上呈现的服务中的哪些层。 还可以查看这些层的关联图例。

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='OGC Web 地图服务资源管理器' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC Web 地图服务资源管理器</a>。
</iframe>

还可以指定要使用代理服务的地图设置。 通过代理服务，即使某些资源托管在未启用 CORS 的域中，你也可以加载这些资源。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [OgcMapLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

请参阅以下文章，其中包含可以添加到地图的代码示例：

> [!div class="nextstepaction"]
> [连接到 WFS 服务](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心操作](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)