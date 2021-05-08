---
title: 扩展的 GeoJSON 几何图形 | Microsoft Azure Maps
description: 了解 Azure Maps 如何扩展 GeoJSON 规范以包括其他几何形状。 查看示例，了解如何设置在地图中使用的圆和矩形。
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 87a5dec2582796c6703bfb37b76e26e53a1192c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895777"
---
# <a name="extended-geojson-geometries"></a>扩展的 GeoJSON 几何图形

Azure Maps 提供功能强大的 API 列表，用于在地理功能内搜索/基于地理功能搜索。 这些 API 遵循代表地理功能的标准 [GeoJSON 规范][1] 。  

[GeoJSON 规范][1]仅支持以下几何图形：

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* 点
* Polygon

一些Azure Maps API接受不属于 [GeoJSON 规范][1]的几何图形。例如，[几何图形](/rest/api/maps/search/postsearchinsidegeometry) API 内部的搜索接受圆和多边形。

本文提供了有关 Azure Maps 如何扩展 [GeoJSON 规范][1]来表示某些几何图形的详细说明。

## <a name="circle"></a>圆形

[GeoJSON 规范][1]不支持`Circle`几何图形。我们使用 `GeoJSON Point Feature` 对象表示圆形。

使用 `GeoJSON Feature`对象表示的 `Circle` 几何图形必须包含以下各项：

- Center

    圆的中心使用`GeoJSON Point`对象表示。

- 半径

    圆形的 `radius` 使用 `GeoJSON Feature` 的属性表示。 半径值以米为单位，并且其类型必须为 `double`。

- 子类型

    圆形几何图形还必须包含 `subType` 属性。 该属性必须属于 `GeoJSON Feature` 属性的一部分，并且其值应为 _圆_

#### <a name="example"></a>示例

以下是使用 `GeoJSON Feature`对象表示圆的方式 。 让我们将圆圈置于纬度：47.639754 和经度：-122.126986，并为其分配 100 米的半径：

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>矩形

[GeoJSON 规范][1]不支持 `Rectangle` 几何图形。我们使用`GeoJSON Polygon Feature`对象表示矩形。 矩形扩展主要由 Web SDK 的 "绘图工具" 模块使用。

使用 `GeoJSON Polygon Feature`对象 __表示的 `Rectangle` 几何图形必须__ 包含以下各项：

- 角

    使用`GeoJSON Polygon`对象的坐标表示矩形的角  。 应该有五个坐标，每个角一个。 第五个坐标与第一个坐标相同，用于闭合多边形环。 假定这些坐标对齐，并且开发人员可以根据需要对其进行旋转。

- 子类型

    圆形几何图形还必须包含 `subType` 属性。 该属性必须属于 `GeoJSON Feature` 属性的一部分，并且其值应为 _矩形_

### <a name="example"></a>示例

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>后续步骤

了解有关 Azure Maps 中 GeoJSON 数据的更多信息：

> [!div class="nextstepaction"]
> [Geofence GeoJSON 格式](geofence-geojson.md)

请查看与 Azure Maps 和位置智能应用程序相关联的常见技术术语的术语表：

> [!div class="nextstepaction"]
> [Azure Maps 术语表](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946