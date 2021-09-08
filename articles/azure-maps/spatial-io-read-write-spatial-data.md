---
title: 读取和写入空间数据 | Microsoft Azure Maps
description: 了解如何使用 Azure Maps Web SDK 提供的空间 IO 模块来读取和写入数据。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: dfd5da016732d0424935e72e00b38cf597dd5a6e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430389"
---
# <a name="read-and-write-spatial-data"></a>读取和写入空间数据

下表列出了使用空间 IO 模块执行读取和写入操作所支持的空间文件格式。

| 数据格式       | 读取 | 写入 |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| 空间 CSV       | ✓  |  ✓  |
| 已知文本   | ✓  |  ✓  |

以下各节概述了使用空间 IO 模块读取和写入空间数据的各种工具。

## <a name="read-spatial-data"></a>读取空间数据

`atlas.io.read` 函数是用于读取通用空间数据格式（例如 KML、GPX、GeoRSS、GeoJSON，以及具有空间数据的 CSV 文件）的主要函数。 此函数还可以读取这些格式的压缩版本，如 zip 文件或 KMZ 文件。 KMZ 文件格式是 KML 的压缩版本，这种格式还可以包含诸如图像之类的资产。 或者，read 函数可以采用指向此类格式文件的 URL。 URL 应托管在已启用 CORS 的终结点上，或者应在读取选项中提供代理服务。 代理服务可用于在未启用 CORS 的域上加载资源。 read 函数会返回向地图添加图像图标的承诺，并异步处理数据，从而最大程度地减小对 UI 线程的影响。

读取压缩文件（zip 或 KMZ 格式）时，将对其中第一个有效文件进行解压缩和扫描。 例如，doc.kml 或具有其他有效扩展名（如 .kml, .xml, geojson, .json, .csv, .tsv 或 .txt）的文件。 然后，将预加载 KML 和 GeoRSS 文件中引用的图像，以确保它们可访问。 不可访问的图像数据可能会加载备用回退图像，或将从样式中删除。 从 KMZ 文件中提取的图像将转换为数据 URL。

read 函数的结果是一个 `SpatialDataSet` 对象。 此对象可扩展 GeoJSON FeatureCollection 类。 它可以轻松地按原样传递到 `DataSource`，以在地图上呈现其功能。 如下表中所述，`SpatialDataSet` 不仅包含功能信息，还可能包含 KML 地面叠层、处理指标等详细信息。

| 属性名称 | 类型 | 说明 | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | 数据集中所有数据的边界框。 |
| `features` | `Feature[]` | GeoJSON 数据集内的功能。 |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | 由 KML GroundOverlays 构成的数组。 |
| `icons` | 记录&lt;字符串，字符串&gt; | 图标 URL 集。 键 = 图标名称，值 = URL。 |
| properties | any | 在空间数据集的文档级别提供的属性信息。 |
| `stats` | `SpatialDataSetStats` | 有关空间数据集的内容和处理时间的统计信息。 |
| `type` | `'FeatureCollection'` | 只读 GeoJSON 类型值。 |

## <a name="examples-of-reading-spatial-data"></a>读取空间数据的示例

以下代码将演示如何读取空间数据集，并使用 `SimpleDataLayer` 类在地图上呈现该数据集。 该代码使用通过 URL 指向的 GPX 文件。

<br/>

<iframe height='500' scrolling='no' title='加载简单空间数据' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>加载简单空间数据</a>。
</iframe>

以下代码演示将展示如何读取 KML 或 KMZ，并将其加载到地图上。 KML 可以包含地面叠层，其形式为 `ImageLyaer` 或 `OgcMapLayer`。 这些叠层必须与功能分开添加到地图中。 此外，如果数据集包含自定义图标，则在加载功能之前需要将这些图标加载到地图资源上。

<br/>

<iframe height='500' scrolling='no' title='将 KML 加载到地图上' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>将 KML 加载到地图上</a>。
</iframe>

用户可以选择提供代理服务，用于访问可能未启用 CORS 的跨域资产。 read 函数将尝试先使用 CORS 访问另一个域中的文件。 第一次使用 CORS 访问另一个域中的任何资源失败时，如果已提供代理服务，该函数只会请求附加文件。 read 函数会将文件 URL 附加到所提供代理 URL 的末尾。 此代码片段将展示如何将代理服务传递到 read 函数：

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

下面的演示将展示如何读取带分隔符的文件并将其呈现在地图上。 在这种情况下，代码会使用具有空间数据列的 CSV 文件。

<br/>

<iframe height='500' scrolling='no' title='添加带分隔符的文件' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>添加带分隔符的文件</a>。
</iframe>

## <a name="write-spatial-data"></a>写入空间数据

空间 IO 模块中有两个主要写入函数。 `atlas.io.write` 函数会生成一个字符串，而 `atlas.io.writeCompressed` 函数会生成压缩的 zip 文件。 压缩的 zip 文件将包含一个基于文本的文件，其中含有空间数据。 这两个函数都返回一项承诺，以将数据添加到文件。 而且，它们都可以写入以下任何数据：`SpatialDataSet`、`DataSource`、`ImageLayer`、`OgcMapLayer`、功能集合、功能、几何或由这些数据类型任意组合构成的数组。 使用任一函数写入时，可以指定所需的文件格式。 如果未指定文件格式，则数据将以 KML 格式写入。

以下工具演示了可与 `atlas.io.write` 函数一起使用的大多数写入选项。

<br/>

<iframe height='700' scrolling='no' title='空间数据写入选项' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>空间数据写入选项</a>。
</iframe>

## <a name="example-of-writing-spatial-data"></a>写入空间数据的示例

以下示例允许您在地图上拖放并加载空间文件。 可以从地图中导出 GeoJSON 数据，并将其作为字符串或压缩文件写入一种受支持的空间数据格式。

<br/>

<iframe height='700' scrolling='no' title='将空间文件拖放到地图上' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>将空间文件拖放到地图上</a>。
</iframe>

用户可以选择提供代理服务，用于访问可能未启用 CORS 的跨域资产。 此代码片段演示了如何合并代理服务：

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>读取和写入已知文本 (WKT)

[已知文本](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) 是一项开放地理空间信息联盟（OGC）标准，用于将空间几何作为文本表示。 许多地理空间系统使用 PostGIS 插件支持 WKT，例如 Azure SQL 和 Azure PostgreSQL。 与大多数 OGC 标准一样，坐标的格式设置为“经度纬度”，以与“x y”约定保持一致。 例如，可以使用 WKT 格式，将经度-110、纬度45的点以 `POINT(-110 45)` 的形式写入。

可以使用 `atlas.io.ogc.WKT.read` 函数读取已知文本，并使用 `atlas.io.ogc.WKT.write` 函数写入该文本。

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>读取和写入已知文本（WKT）的示例

以下代码将演示如何读取已知文本字符串 `POINT(-122.34009 47.60995)`，并使用气泡图层在地图上呈现该字符串。

<br/>

<iframe height='500' scrolling='no' title='读取已知文本' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'>参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>读取已知文本</a>。
</iframe>

以下代码将演示如何来回读取和写入已知文本。

<br/>

<iframe height='700' scrolling='no' title='读取和写入已知文本' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'>参阅 <a href='https://codepen.io'>CodePen</a>上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>读取和写入已知文本</a>。
</iframe>

## <a name="read-and-write-gml"></a>读取和写入 GML

GML 是一种空间 XML 文件规范，通常用于扩展其他 XML 规范。 可以使用 `atlas.io.core.GmlWriter.write` 函数以带有 GML 标记的 XML 的形式写入 GeoJSON 数据。 可以使用 `atlas.io.core.GmlReader.read` 函数读取包含 GML 的 XML。 read 函数有两个选项：

- `isAxisOrderLonLat` 选项 — 在数据集之间，坐标“纬度，经度”或“经度，纬度”的轴顺序可能会有所不同，并且并非始终定义良好。 默认情况下，GML 读取器会将坐标数据读取为“纬度，经度”，但如将此选项设置为 true，则坐标数据会读取为“经度，纬度”。
- `propertyTypes` 选项 — 此选项是键值查找表，其中的键是数据集中属性的名称。 该值是在分析时将值强制转换为的对象类型。 支持的类型值为：`string`、`number`、`boolean` 和 `date`。 如果某个属性不在查找表中或未定义该类型，则该属性将以字符串的形式进行分析。

`atlas.io.read` 函数在检测到输入数据为 XML 时将默认为 `atlas.io.core.GmlReader.read` 函数，但该数据不是其他支持空间 XML 格式之一。

`GmlReader` 将分析具有以下 SRID 的坐标：

- EPSG：4326（首选）
- EPSG：4269、EPSG：4283、EPSG：4258、EPSG：4308、EPSG：4230、EPSG：4272、EPSG：4271、EPSG：4267、EPSG：4608、EPSG：4674 可能出现小边距的错误。
- EPSG：3857、EPSG：102100、EPSG：3785、EPSG：900913、EPSG：102113、EPSG：41001、EPSG：54004

## <a name="more-resources"></a>更多资源

详细了解本文中使用的类和方法：

[atlas.io 静态函数](/javascript/api/azure-maps-spatial-io/atlas.io)

[SpatialDataSet](/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

[SpatialDataSetStats](/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

[GmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader)

[GmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter)

[atlas.io.ogc.WKT 函数](/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

[连接到 WFS 服务](spatial-io-connect-wfs-service.md)

[利用核心操作](spatial-io-core-operations.md)

[支持的数据格式详细信息](spatial-io-supported-data-format-details.md)


## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

[添加 OGC 地图层](spatial-io-add-ogc-map-layer.md)