---
title: 添加简单的数据层 | Microsoft Azure Maps
description: 了解如何使用 Azure Maps Web SDK 提供的空间 IO 模块来添加简单的数据层。
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: 6f1855474454cc69489b76e03b8ce17dd97ccf32
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437646"
---
# <a name="add-a-simple-data-layer"></a>添加简单的数据层

空间 IO 模块提供 `SimpleDataLayer` 类。 通过该类可以轻松地在地图上呈现带样式的功能。 该类甚至可以呈现具有样式属性和包含混合几何图形类型的数据集。 简单数据层通过包装多个呈现层并使用样式表达式来实现此功能。 样式表达式搜索这些已包装层内功能的通用样式属性。 `atlas.io.read` 函数和 `atlas.io.write` 函数使用这些属性来读取样式，并将其写入支持的文件格式。 将属性添加到支持的文件格式后，文件可用于各种目的。 例如，文件可用于显示地图上带样式的功能。

除样式功能外， `SimpleDataLayer` 还提供了带有弹出模板的内置弹出功能。 单击功能时将显示弹出窗口。 如果需要，可以禁用默认的弹出功能。 该层还支持群集数据。 单击群集后，地图会放大到该群集，并将其扩展到各个点和子群集。

`SimpleDataLayer` 类可用于将许多几何图形类型和样式应用于功能的大型数据集。 此类的使用会增加包含样式表达式的六个层的开销。 因此，在某些情况下，使用核心呈现层会更有效。 例如，使用核心层在一个功能上呈现几个几何图形类型和样式

## <a name="use-a-simple-data-layer"></a>使用简单的数据层

`SimpleDataLayer` 类的使用方式类似于其他呈现层。 下面的代码演示如何在地图中使用简单的数据层：

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

将功能添加到数据源。 然后，简单的数据层会找出呈现功能的最佳方式。 单个功能的样式可设置为功能的属性。 下面的代码演示了 GeoJSON 点功能，其 `color` 属性设置为 `red`。 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

下面的代码使用简单数据层呈现上述点功能。 

<br/>

<iframe height="500" scrolling="no" title="使用简单的数据层" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> 请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>使用简单数据层</a>。
</iframe>

简单数据层的真正强大之处在以下情况中得以发挥：

- 数据源中有几种不同类型的功能；或
- 数据集中的功能分别设置了几个样式属性；或
- 不确定数据集的确切包含内容。

例如，在解析 XML 数据馈送时可能不知道功能的确切样式和几何图形类型。 下面的示例通过呈现 KML 文件的功能，显示了简单数据层的强大功能。 同时也演示了简单数据层类提供的各种选项。

<br/>

<iframe height="700" scrolling="no" title="简单数据层选项" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> 请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>简单数据层选项</a>文章。
</iframe>


> [!NOTE]
> 此简单数据层使用[弹出模板](map-add-popup.md#add-popup-templates-to-the-map)类以表的形式显示 KML 提示框或功能属性。 默认情况下，弹出窗口呈现的所有内容都将作为安全功能在 iframe 内进行沙盒处理。 但存在一些限制：
>
> - 所有脚本、窗体、指针锁定和顶部导航功能都处于禁用状态。 单击时，允许链接在新选项卡中打开。 
> - 不支持 iframe `srcdoc` 参数的旧版本浏览器会受到限制，仅能呈现少量内容。
> 
> 如果信任加载到弹出窗口中的数据，并且可能希望加载到弹出窗口的脚本可以访问应用程序，则可以通过将弹出模板的 `sandboxContent` 选项设置为 false 来禁用此设置。 

## <a name="default-supported-style-properties"></a>受支持的默认样式属性

如前文所述，简单数据层包装多个核心呈现层：气泡、符号、直线、多边形和突出多边形。 然后，简单数据层使用表达式搜索各个功能的有效样式属性。

Azure Maps 和 GitHub 样式属性是两个主要的受支持属性名称集。 不同 Azure 地图层选项的大多数属性名称都作为简单数据层中功能的样式属性受到支持。 已将表达式添加到一些层选项，以支持 GitHub 通常使用的样式属性名称。 这些属性名称由 [GitHub 的 GeoJSON 地图支持](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)定义，它们用于对平台中存储和呈现的 GeoJSON 文件进行样式处理。 除 `marker-symbol` 样式属性以外，所有 GitHub 样式属性在简单数据层中都受支持。

如果读取器遇到不太通用的样式属性，则会将其转换为最接近的 Azure Maps 样式属性。 此外，使用简单数据层的 `getLayers` 函数及更新任何层的选项，可以重写默认样式表达式。

以下部分提供了有关简单数据层支持的默认样式属性的详细信息。 受支持属性名称的顺序也是属性的优先级。 如果为同一层选项定义了两个样式属性，则列表中第一个属性的优先级更高。 颜色可以是任何 CSS3 颜色值；HEX、RGB、RGBA、HSL、HSLA 或命名的颜色值。

### <a name="bubble-layer-style-properties"></a>气泡层样式属性

如果功能是 `Point` 或 `MultiPoint` ，且该功能没有可用作自定义图标以将点呈现为符号的 `image` 属性，则该功能将使用 `BubbleLayer` 进行呈现。

| 层选项 | 受支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>、 `marker-size`<sup>2</sup>、 `scale`<sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1 \] 将 `size` 和 `scale` 值被视为标量值，并将其乘以 `8`

\[2 \] 如果指定了 GitHub `marker-size` 选项，则半径将使用以下值。

| 标记大小 | 半径 |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

群集也可以使用气泡层呈现。 默认情况下，群集的半径设置为 `16`。 群集的颜色因群集中的点数而异，如下所示：

| 点数 | Color    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>符号样式属性

如果功能是 `Point` 或 `MultiPoint`，且该功能没有可用作自定义图标以将点呈现为符号的 `image` 属性，则将使用 `SymbolLayer` 呈现该功能。

| 层选项 | 受支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`、 `marker-size`<sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] 如果指定了 GitHub `marker-size` 选项，则图标大小选项将使用以下值。

| 标记大小 | 符号大小 |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

如果点功能是一个群集，则 `point_count_abbreviated` 属性将呈现为文本标签。 不会呈现图像。

### <a name="line-style-properties"></a>线样式属性

如果功能为 `LineString`、 `MultiLineString`、 `Polygon` 或 `MultiPolygon`，则将使用 `LineLayer` 呈现该功能。

| 层选项 | 受支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>多边形样式属性

如果功能是 `Polygon` 或 `MultiPolygon` ，且功能不具有 `height` 属性或 `height` 属性为零，则将使用 `PolygonLayer` 呈现该功能。

| 层选项 | 受支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>突出多边形样式属性

如果功能是 `Polygon` 或 `MultiPolygon` ，且具有值大于 0 的 `height` 属性，则将使用 `PolygonExtrusionLayer` 呈现该功能。

| 层选项 | 受支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [读取和写入空间数据](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [添加 OGC 地图层](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [连接到 WFS 服务](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心操作](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)