---
title: 支持的数据格式详细信息 | Microsoft Azure Maps
description: 了解如何在空间 IO 模块中分析带分隔符的空间数据。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 278dae8b3569dd0ff92d3ba12197d1049070167a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744915"
---
# <a name="supported-data-format-details"></a>支持的数据格式详细信息

本文提供有关所有 XML 标记和常见文本几何类型的读取和写入支持的详细信息。 本文还详细介绍如何在空间 IO 模块中分析带分隔符的空间数据。

## <a name="supported-xml-namespaces"></a>支持的 XML 命名空间

空间 IO 模块支持以下命名空间中的 XML 标记。

| 命名空间前缀 | 命名空间 URI   | 备注                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | 在 GeoRSS 文件中具有只读支持。           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | 在 GeoRSS 文件中具有只读支持。                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | 在 GPX 文件中具有只读支持。 分析并使用 DisplayColor。 添加到形状元数据的所有其他属性。 |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | 在 GPX 文件中受支持。 使用线条颜色。 |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | 只读。 GeoRSS 使用 Atom 格式写入。              |

## <a name="supported-xml-elements"></a>支持的 XML 元素

空间 IO 模块支持以下 XML 元素。 任何不受支持的 XML 标记都将转换为 JSON 对象。 然后，每个标记将作为属性添加到父形状或图层的 `properties` 字段中。

### <a name="kml-elements"></a>KML 元素

空间 IO 模块支持以下 KML 元素。

| 元素名称         | 读取    | 写入   | 备注                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | 部分 | 是     | 系统会分析对象，但对象不用于定位形状。                                                                    |
| `AddressDetails`     | 部分 | 否      | 系统会分析对象，但对象不用于定位形状。                                                                    |
| `atom:author`        | 是     | 是     |                                                                                                                            |
| `atom:link`          | 是     | 是     |                                                                                                                            |
| `atom:name`          | 是     | 是     |                                                                                                                            |
| `BalloonStyle`       | 部分 | 部分 | 不支持 `displayMode`。 转换为 `PopupTemplate`。 若要写入，请添加 `popupTemplate` 属性作为要写入此元素的特征的属性。 |
| `begin`              | 是     | 是     |                                                                                                                            |
| `color`              | 是     | 是     | 包括 `#AABBGGRR` 和 `#BBGGRR`。 分析为 CSS 颜色字符串                                                           |
| `colorMode`          | 是     | 否      |                                                                                                                            |
| `coordinates`        | 是     | 是     |                                                                                                                            |
| `Data`               | 是     | 是     |                                                                                                                            |
| `description`        | 是     | 是     |                                                                                                                            |
| `displayName`        | 是     | 是     |                                                                                                                            |
| `Document`           | 是     | 是     |                                                                                                                            |
| `drawOrder`          | 部分 | 否      | 读取地面叠加层，并用于对地面叠加层进行排序。 
| `east`               | 是     | 是     |                                                                                                                            |
| `end`                | 是     | 是     |                                                                                                                            |
| `ExtendedData`       | 是     | 是     | 支持非类型化 `Data`、`SimpleData` 或 `Schema` 和窗体 `$[dataName]` 的实体替换。                      |
| `extrude`            | 部分 | 部分 | 只有多边形支持此元素。 具有不同高度的多边形的 MultiGeometry 将分解为单独的特征。 不支持线型。 海拔高度为 0 的多边形将呈现为平面多边形。 在读取时，系统会添加外环中第一个坐标的海拔高度作为多边形的 height 属性。 然后，第一个坐标的海拔高度将用于在地图上呈现多边形。 |
| `fill`               | 是     | 是     |                                                                                                                            |
| `Folder`             | 是     | 是     |                                                                                                                            |
| `GroundOverlay`      | 是     | 是     | 不支持 `color`                                                                                                   |
| `heading`            | 部分 | 否      | 系统会分析此元素，但它不由 `SimpleDataLayer` 呈现。 仅当数据存储在形状的属性中时才写入。                 |
| `hotSpot`            | 是     | 部分 | 仅当数据存储在形状的属性中时才写入。 单位仅输出为“像素”。                         |
| `href`               | 是     | 是     |                                                                                                                            |
| `Icon`               | 部分 | 部分 | 系统会分析此元素，但它不由 `SimpleDataLayer` 呈现。 如果形状包含 URI 数据，则仅写入该形状的 icon 属性。 仅支持 `href`。 |
| `IconStyle`          | 部分 | 部分 | 系统会分析 `icon`、`heading`、`colorMode` 和 `hotspots` 的值，但它们不由 `SimpleDataLayer` 呈现         |
| `innerBoundaryIs`    | 是     | 是     |                                                                                                                            |
| `kml`                | 是     | 是     |                                                                                                                            |
| `LabelStyle`         | 否      | 否      |                                                                                                                            |
| `LatLonBox`          | 是     | 是     |                                                                                                                            |
| `gx:LatLonQuad`      | 是     | 是     |                                                                                                                            |
| `LinearRing`         | 是     | 是     |                                                                                                                            |
| `LineString`         | 是     | 是     |                                                                                                                            |
| `LineStyle`          | 是     | 是     | 不支持 `colorMode`。                                                                                         |
| `Link`               | 是     | 否      | 网络链接仅支持 `href` 属性。                                                                   |
| `MultiGeometry`      | 部分 | 部分 | 读取时可能会分解为单独的特征。                                                                     |
| `name`               | 是     | 是     |                                                                                                                            |
| `NetworkLink`        | 是     | 否      | 链接必须与文档位于同一个域中。                                                                  |
| `NetworkLinkControl` | 否      | 否      |                                                                                                                            |
| `north`              | 是     | 是     |                                                                                                                            |
| `open`               | 是     | 是     |                                                                                                                            |
| `outerBoundaryIs`    | 是     | 是     |                                                                                                                            |
| `outline`            | 是     | 是     |                                                                                                                            |
| `overlayXY`          | 否      | 否      |                                                                                                                            |
| `Pair`               | 部分 | 否      | 仅支持 `StyleMap` 中的 `normal` 样式。 不支持 `highlight`。                                   |
| `phoneNumber`        | 是     | 是     |                                                                                                                            |
| `PhotoOverlay`       | 否      | 否      |                                                                                                                            |
| `Placemark`          | 是     | 是     |                                                                                                                            |
| `Point`              | 是     | 是     |                                                                                                                            |
| `Polygon`            | 是     | 是     |                                                                                                                            |
| `PolyStyle`          | 是     | 是     |                                                                                                                            |
| `Region`             | 部分 | 部分 | 在文档级别支持 `LatLongBox`。                                                                      |
| `rotation`           | 否      | 否      |                                                                                                                            |
| `rotationXY`         | 否      | 否      |                                                                                                                            |
| `scale`              | 否      | 否      |                                                                                                                            |
| `Schema`             | 是     | 是     |                                                                                                                            |
| `SchemaData`         | 是     | 是     |                                                                                                                            |
| `schemaUrl`          | 部分 | 是     | 不支持从 KMZ 中不包含的外部文档加载样式。                             |
| `ScreenOverlay`      | 否      | 否      |                                                                                                                            |
| `screenXY`           | 否      | 否      |                                                                                                                            |
| `SimpleData`         | 是     | 是     |                                                                                                                            |
| `SimpleField`        | 是     | 是     |                                                                                                                            |
| `size`               | 否      | 否      |                                                                                                                            |
| `Snippet`            | 部分 | 部分 | 系统会忽略 `maxLines` 属性。                                                                                  |
| `south`              | 是     | 是     |                                                                                                                            |
| `Style`              | 是     | 是     |                                                                                                                            |
| `StyleMap`           | 部分 | 否      | 仅支持 `StyleMap` 中的 normal 样式。                                                                        |
| `styleUrl`           | 部分 | 是     | 不支持外部样式 URL。                                                                         |
| `text`               | 是     | 是     | 不支持替换 `$[geDirections]`                                                                          |
| `textColor`          | 是     | 是     |                                                                                                                            |
| `TimeSpan`           | 是     | 是     |                                                                                                                            |
| `TimeStamp`          | 是     | 是     |                                                                                                                            |
| `value`              | 是     | 是     |                                                                                                                            |
| `viewRefreshMode`    | 部分 | 否      |  如果指向 WMS 服务，则仅支持对地面叠加层使用 `onStop`。 系统会将 `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` 附加到 URL，并随着地图的移动进行更新。  |
| `visibility`         | 是     | 是     |                                                                                                                            |
| `west`               | 是     | 是     |                                                                                                                            |
| `when`               | 是     | 是     |                                                                                                                            |
| `width`              | 是     | 是     |                                                                                                                            |

### <a name="georss-elements"></a>GeoRSS 元素

空间 IO 模块支持以下 GeoRSS 元素。

| 元素名称             | 读取    | 写入 | 备注                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | 是     | 是   |                                                                                                |
| `atom:category`          | 是     | 是   |                                                                                                |
| `atom:content`           | 是     | 是   |                                                                                                |
| `atom:contributor`       | 是     | 是   |                                                                                                |
| `atom:email`             | 是     | 是   |                                                                                                |
| `atom:entry`             | 是     | 是   |                                                                                                |
| `atom:feed`              | 是     | 是   |                                                                                                |
| `atom:icon`              | 是     | 是   |                                                                                                |
| `atom:id`                | 是     | 是   |                                                                                                |
| `atom:link`              | 是     | 是   |                                                                                                |
| `atom:logo`              | 是     | 是   |                                                                                                |
| `atom:name`              | 是     | 是   |                                                                                                |
| `atom:published`         | 是     | 是   |                                                                                                |
| `atom:rights`            | 是     | 是   |                                                                                                |
| `atom:source`            | 是     | 是   |                                                                                                |
| `atom:subtitle`          | 是     | 是   |                                                                                                |
| `atom:summary`           | 是     | 是   |                                                                                                |
| `atom:title`             | 是     | 是   |                                                                                                |
| `atom:updated`           | 是     | 是   |                                                                                                |
| `atom:uri`               | 是     | 是   |                                                                                                |
| `geo:lat`                | 是     | 否    | 写入为 `georss:point`。                                                                   |
| `geo:lon`                | 是     | 否    | 写入为 `georss:point`。                                                                   |
| `geo:long`               | 是     | 否    | 写入为 `georss:point`。                                                                   |
| `georss:box`             | 是     | 否    | 读取为多边形，并提供“矩形”的 `subType` 属性                                |
| `georss:circle`          | 是     | 是   |                                                                                                |
| `georss:elev`            | 是     | 是   |                                                                                                |
| `georss:featurename`     | 是     | 是   |                                                                                                |
| `georss:featuretypetag`  | 是     | 是   |                                                                                                |
| `georss:floor`           | 是     | 是   |                                                                                                |
| `georss:line`            | 是     | 是   |                                                                                                |
| `georss:point`           | 是     | 是   |                                                                                                |
| `georss:polygon`         | 是     | 是   |                                                                                                |
| `georss:radius`          | 是     | 是   |                                                                                                |
| `georss:relationshiptag` | 是     | 是   |                                                                                                |
| `georss:where`           | 是     | 是   |                                                                                                |
| `geourl:latitude`        | 是     | 否    | 写入为 `georss:point`。                                                                   |
| `geourl:longitude`       | 是     | 否    | 写入为 `georss:point`。                                                                   |
| `position`               | 是     | 否    | 部分 XML 源将使用 position 标记而非 `georss:where` 标记来包装 GML。 系统会读取此标记，但将使用 `georss:where` 标记来写入。 |
| `rss`                    | 是     | 否    | GeoRSS 以 ATOM 格式写入。                                                                 |
| `rss:author`             | 是     | 部分 | 写入为 `atom:author`。                                                                 |
| `rss:category`           | 是     | 部分 | 写入为 `atom:category`。                                                               |
| `rss:channel`            | 是     | 否    |                                                                                                |
| `rss:cloud`              | 是     | 否    |                                                                                                |
| `rss:comments`           | 是     | 否    |                                                                                                |
| `rss:copyright`          | 是     | 部分 | 如果形状还没有 `rights` `properties` 属性，则写入为 `atom:rights`。       |
| `rss:description`        | 是     | 部分 | 如果形状还没有 `content` `properties` 属性，则写入为 `atom:content`。      |
| `rss:docs`               | 是     | 否    |                                                                                                |
| `rss:enclosure`          | 是     | 否    |                                                                                                |
| `rss:generator`          | 是     | 否    |                                                                                                |
| `rss:guid`               | 是     | 部分 | 如果形状还没有 `id` `properties` 属性，则写入为 `atom:id`。         |
| `rss:image`              | 是     | 部分 | 如果形状还没有 `logo` `properties` 属性，则写入为 `atom:logo`。      |
| `rss:item`               | 是     | 部分 | 写入为 `atom:entry`。                                                                  |
| `rss:language`           | 是     | 否    |                                                                                                |
| `rss:lastBuildDate`      | 是     | 部分 | 如果形状还没有 `updated` `properties` 属性，则写入为 `atom:updated`。     |
| `rss:link`               | 是     | 部分 | 写入为 `atom:link`。                                                                   |
| `rss:managingEditor`     | 是     | 部分 | 写入为 `atom:contributor`。                                                            |
| `rss:pubDate`            | 是     | 部分 | 如果形状还没有 `published` `properties` 属性，则写入为 `atom:published`。  |
| `rss:rating`             | 是     | 否    |                                                                                                |
| `rss:skipDays`           | 是     | 否    |                                                                                                |
| `rss:skipHours`          | 是     | 否    |                                                                                                |
| `rss:source`             | 是     | 部分 | 写入为包含 `atom:link` 的 `atom:source`。                                       |
| `rss:textInput`          | 是     | 否    |                                                                                                |
| `rss:title`              | 是     | 部分 | 写入为 `atom:title`。                                                                  |
| `rss:ttl`                | 是     | 否    |                                                                                                |
| `rss:webMaster`          | 是     | 否    |                                                                                                |

### <a name="gml-elements"></a>GML 元素

空间 IO 模块支持以下 GML 元素。 

| 元素名称            | 读取 | 写入 | 备注                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | 是  | 否    | 写入为 `gml:posList`。                                                              |
| `gml:curveMember`       | 是  | 否    |                                                                                        |
| `gml:curveMembers`      | 是  | 否    |                                                                                        |
| `gml:Box`               | 是  | 否    | 写入为 `gml:Envelope`。                                                             |
| `gml:description`       | 是  | 是   |                                                                                        |
| `gml:Envelope`          | 是  | 是   |                                                                                        |
| `gml:exterior`          | 是  | 是   |                                                                                        |
| `gml:Feature`           | 是  | 否    | 写入为形状。                                                                    |
| `gml:FeatureCollection` | 是  | 否    | 写入为几何集合。                                                      |
| `gml:featureMember`     | 是  | 否    | 写入为几何集合。                                                      |
| `gml:geometry`          | 是  | 否    | 写入为形状。                                                                    |
| `gml:geometryMember`    | 是  | 是   |                                                                                        |
| `gml:geometryMembers`   | 是  | 是   |                                                                                        |
| `gml:identifier`        | 是  | 是   |                                                                                        |
| `gml:innerBoundaryIs`   | 是  | 否    | 使用 `gml.interior` 时写入。                                                          |
| `gml:interior`          | 是  | 是   |                                                                                        |
| `gml:LinearRing`        | 是  | 是   |                                                                                        |
| `gml:LineString`        | 是  | 是   |                                                                                        |
| `gml:lineStringMember`  | 是  | 是   |                                                                                        |
| `gml:lineStringMembers` | 是  | 否    |                                                                                        |
| `gml:MultiCurve`        | 是  | 否    | 仅读取 `gml:LineString` 成员。 写入为 `gml.MultiLineString`                  |
| `gml:MultiGeometry`     | 部分  | 部分   | 仅读取为 FeatureCollection。                                              |
| `gml:MultiLineString`   | 是  | 是   |                                                                                        |
| `gml:MultiPoint`        | 是  | 是   |                                                                                        |
| `gml:MultiPolygon`      | 是  | 是   |                                                                                        |
| `gml:MultiSurface`      | 是  | 否    | 仅读取 `gml:Polygon` 成员。 写入为 `gml.MultiPolygon`                        |
| `gml:name`              | 是  | 是   |                                                                                        |
| `gml:outerBoundaryIs`   | 是  | 否    | 使用 `gml.exterior` 时写入。                                                          |
| `gml:Point`             | 是  | 是   |                                                                                        |
| `gml:pointMember`       | 是  | 是   |                                                                                        |
| `gml:pointMembers`      | 是  | 否    |                                                                                        |
| `gml:Polygon`           | 是  | 是   |                                                                                        |
| `gml:polygonMember`     | 是  | 是   |                                                                                        |
| `gml:polygonMembers`    | 是  | 否    |                                                                                        |
| `gml:pos`               | 是  | 是   |                                                                                        |
| `gml:posList`           | 是  | 是   |                                                                                        |
| `gml:surfaceMember`     | 是  | 是   |                                                                                        |

#### <a name="additional-notes"></a>其他说明

- 系统会搜索 Member 元素，查找可能隐藏在子元素中的几何。 此搜索操作是必需的，因为许多从 GML 扩展的 XML 格式可能不会将几何放置为 Member 元素的直接子级。
- WGS84 坐标和以下代码仅部分支持 `srsName`：[EPSG:4326](https://epsg.io/4326) 和 Web 墨卡托 [EPSG:3857](https://epsg.io/3857) 或其替代代码之一。 任何其他坐标系统都将按原样分析为 WGS84。
- 除非另有指定，否则系统在读取 XML 源时会根据 XML 源中的提示确定轴顺序。 系统首选使用“纬度，经度”轴顺序。
- 除非为属性指定了自定义 GML 命名空间，否则系统在写入到 GML 文件时不会添加其他属性信息。

### <a name="gpx-elements"></a>GPX 元素

空间 IO 模块支持以下 GPX 元素。

| 元素名称             | 读取    | 写入   | 备注                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | 是     | 是     |                                                                                             |
| `gpx:author`             | 是     | 是     |                                                                                             |
| `gpx:bounds`             | 是     | 是     | 读取时转换为 LocationRect。                                                    |
| `gpx:cmt`                | 是     | 是     |                                                                                             |
| `gpx:copyright`          | 是     | 是     |                                                                                             |
| `gpx:desc`               | 是     | 是     | 在读取时复制到 description 属性，以与其他 XML 格式对齐。               |
| `gpx:dgpsid`             | 是     | 是     |                                                                                             |
| `gpx:ele`                | 是     | 是     |                                                                                             |
| `gpx:extensions`         | 部分 | 部分 | 读取时，系统会提取样式信息。 所有其他扩展将平展为简单 JSON 对象。 仅写入形状样式信息。 |
| `gpx:geoidheight`        | 是     | 是     |                                                                                             |
| `gpx:gpx`                | 是     | 是     |                                                                                             |
| `gpx:hdop`               | 是     | 是     |                                                                                             |
| `gpx:link`               | 是     | 是     |                                                                                             |
| `gpx:magvar`             | 是     | 是     |                                                                                             |
| `gpx:metadata`           | 是     | 是     |                                                                                             |
| `gpx:name`               | 是     | 是     |                                                                                             |
| `gpx:pdop`               | 是     | 是     |                                                                                             |
| `gpx:rte`                | 是     | 是     |                                                                                             |
| `gpx:rtept`              | 是     | 是     |                                                                                             |
| `gpx:sat`                | 是     | 是     |                                                                                             |
| `gpx:src`                | 是     | 是     |                                                                                             |
| `gpx:sym`                | 是     | 是     | 系统会捕获值，但值不用于更改图钉图标。                               |
| `gpx:text`               | 是     | 是     |                                                                                             |
| `gpx:time`               | 是     | 是     |                                                                                             |
| `gpx:trk`                | 是     | 是     |                                                                                             |
| `gpx:trkpt`              | 是     | 是     |                                                                                             |
| `gpx:trkseg`             | 是     | 是     |                                                                                             |
| `gpx:type`               | 是     | 是     |                                                                                             |
| `gpx:vdop`               | 是     | 是     |                                                                                             |
| `gpx:wpt`                | 是     | 是     |                                                                                             |
| `gpx_style:color`        | 是     | 是     |                                                                                             |
| `gpx_style:line`         | 部分 | 部分 | 支持 `color`、`opacity`、`width` 和 `lineCap`。                                           |
| `gpx_style:opacity`      | 是     | 是     |                                                                                             |
| `gpx_style:width`        | 是     | 是     |                                                                                             |
| `gpxx:DisplayColor`      | 是     | 否      | 用于指定形状的颜色。 写入时，系统将改为使用 `gpx_style:line` 颜色。  |
| `gpxx:RouteExtension`    | 部分 | 否      | 所有属性都读取到 `properties`。 仅使用 `DisplayColor`。                     |
| `gpxx:TrackExtension`    | 部分 | 否      | 所有属性都读取到 `properties`。 仅使用 `DisplayColor`。                     |
| `gpxx:WaypointExtension` | 部分 | 否      | 所有属性都读取到 `properties`。 仅使用 `DisplayColor`。                     |
| `gpx:keywords`           | 是     | 是     |                                                                                             |
| `gpx:fix`                | 是     | 是     |                                                                                             |

#### <a name="additional-notes"></a>其他说明

写入时：

- MultiPoint 将分解为单独的航点。
- Polygon 和 MultiPolygon 将写入为轨迹。 
  
## <a name="supported-well-known-text-geometry-types"></a>支持的常见文本几何类型

| 几何类型 | 读取 | 写入 |
|--------------|:----:|:-----:|
| 点 | x | x |
| POINT Z | x | x | 
| POINT M | x | x<sup>[2]</sup> |
| POINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| LINESTRING | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| POLYGON | x | x |
| POLYGON Z | x | x |
| POLYGON M | x | x<sup>[2]</sup> |
| POLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOINT | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] 系统只会捕获 Z 参数并将其添加为 Position 值中的第三个值。

\[2\] 系统不会捕获 M 参数。

## <a name="delimited-spatial-data-support"></a>支持带分隔符的空间数据

逗号分隔值文件 (CSV) 等带分隔符的空间数据通常包含列，而列包含空间数据。 例如，列可能包含纬度和经度信息。 在常见文本格式中，列可能包含空间几何数据。

### <a name="spatial-data-column-detection"></a>空间数据列检测

在读取包含空间数据的带分隔符文件时，系统会分析文件头以确定哪些列包含位置字段。 如果文件头包含类型信息，这些信息会用于将单元格值强制转换为相应的类型。 如果未指定文件头，则系统会分析第一行并将其用于生成文件头。 分析第一行时，系统会执行检查，以将列名与以下名称进行不区分大小写匹配。 如果文件中存在两个或更多名称，则以名称顺序优先。

#### <a name="latitude"></a>纬度

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>经度

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Elevation

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>地理位置

系统会扫描常见文本格式中的第一行数据，以查找字符串。 

### <a name="delimited-data-column-types"></a>带分隔符数据列类型

在扫描标题行时，系统会提取列名中的任何类型信息，并将其用于转换该列中的单元格。 下面是类型值为“ColumnName (typeName)”的列名的示例。 支持以下不区分大小写的类型名称：

#### <a name="numbers"></a>数字

- edm.int64
- int
- long
- edm.double
- FLOAT
- Double
- 数字

#### <a name="booleans"></a>布尔型

- edm.boolean
- bool
- boolean

#### <a name="dates"></a>日期

- edm.datetime
- date
- datetime

#### <a name="geography"></a>地理位置

- edm.geography
- geography

#### <a name="strings"></a>字符串

- edm.string
- varchar
- text
- 字符串

如果文件头没有类型信息可供提取，并且在读取时启用了动态类型匹配选项，则会单独分析每个单元格以确定最适合转换为的数据类型。

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

[读取和写入空间数据](spatial-io-read-write-spatial-data.md)
