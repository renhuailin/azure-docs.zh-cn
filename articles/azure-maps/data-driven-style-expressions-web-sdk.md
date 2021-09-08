---
title: Azure Maps Web SDK 中的数据驱动表达式 | Microsoft Azure Maps
description: 了解数据驱动样式表达式。 了解如何使用 Azure Maps Web SDK 中的这些表达式来调整地图中的样式。
author: anastasia-ms
ms.author: v-stharr
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 7875184456d03e08abb6168793fc9021bd953a0d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438509"
---
# <a name="data-driven-style-expressions-web-sdk"></a>数据驱动样式表达式 (Web SDK)

使用表达式可将业务逻辑应用到样式选项，这些选项遵循数据源中每个形状内定义的属性。 表达式可以筛选数据源或层中的数据。 表达式可以包含条件逻辑，例如 if 语句。 此外，可以结合字符串运算符、逻辑运算符和数学运算符使用表达式来处理数据。

数据驱动的样式减少了实现样式相关业务逻辑所需的代码量。 对层使用表达式时，呈现时将在单独的线程中评估表达式。 与在 UI 线程中评估业务逻辑相比，此功能提供更高的性能。

此视频提供了 Azure Maps Web SDK 中数据驱动样式的概述。

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

表达式表示为 JSON 数组。 数组中表达式的第一个元素是一个字符串，该字符串指定表达式运算符的名称。 例如：“+”或“case”。 后面的元素（如果有）是适用于表达式的自变量。 每个参数可以是一个文本值（字符串、数字、布尔值或 `null`）或其他表达式数组。 以下伪代码定义表达式的基本结构。

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps Web SDK 支持多种类型的表达式。 表达式可以单独使用，也可以与其他表达式结合使用。

| 表达式的类型 | 说明 |
|---------------------|-------------|
| [聚合表达式](#aggregate-expression) | 该表达式定义对一组数据进行处理并可与 `DataSource` 的 `clusterProperties` 选项一起使用的计算。 |
| [布尔表达式](#boolean-expressions) | 布尔表达式提供一组布尔运算符表达式用于评估布尔比较结果。 |
| [颜色表达式](#color-expressions) | 使用颜色表达式可以更轻松地创建和处理颜色值。 |
| [条件表达式](#conditional-expressions) | 条件表达式提供类似于 if 语句的逻辑运算。 |
| [数据表达式](#data-expressions) | 提供对特征中属性数据的访问。 |
| [interpolate 和 step 表达式](#interpolate-and-step-expressions) | interpolate 和 step 表达式可用于计算内插曲线或 step 函数中的值。 |
| [层特定的表达式](#layer-specific-expressions) | 仅适用于单个层的特殊表达式。 |
| [数学表达式](#math-expressions) | 提供数学运算符用于在表达式框架中执行数据驱动的计算。 |
| [字符串运算符表达式](#string-operator-expressions) | 字符串运算符表达式针对字符串执行转换操作，例如连接字符串和转换大小写。 |
| [类型表达式](#type-expressions) | 类型表达式提供用于测试和转换不同数据类型（例如字符串、数字和布尔值）的工具。 |
| [变量绑定表达式](#variable-binding-expressions) | 变量绑定表达式将计算结果存储在某个变量中，并在表达式中的其他位置多次引用该变量，这样而无需重新计算存储的值。 |
| [zoom 表达式](#zoom-expression) | 在呈现时检索地图的当前缩放级别。 |

本文档中的所有示例使用以下特征来演示这些不同类型表达式的不同用法。

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

## <a name="data-expressions"></a>数据表达式

数据表达式提供对特征中属性数据的访问。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['at', number, array]` | 值 | 从数组中检索项。 |
| `['geometry-type']` | 字符串 | 获取特征的几何类型：Point、MultiPoint、LineString、MultiLineString、Polygon、MultiPolygon。 |
| `['get', string]` | 值 | 从当前功能的属性获取属性值。 如果缺少请求的属性，则返回 null。 |
| `['get', string, object]` | 值 | 从所提供对象的属性中获取属性值。 如果缺少请求的属性，则返回 null。 |
| `['has', string]` | boolean | 确定特征的属性是否具有指定的属性。 |
| `['has', string, object]` | boolean | 确定对象的属性是否具有指定的属性。 |
| `['id']` | 值 | 获取特征的 ID（如果有）。 |
| `['in', boolean | string | number, array]` | boolean | 确定某个项是否存在于数组中 |
| `['in', substring, string]` | boolean | 确定一个子字符串是否存在在字符串中 |
| `['index-of', boolean | string | number, array | string]`<br/><br/>`['index-of', boolean | string | number, array | string, number]` | 数字 | 返回可在数组中找到项或在字符串中找到子字符串的第一个位置，如果找不到输入，则返回 `-1`。 接受从该处开始搜索的可选索引。 |
| `['length', string | array]` | 数字 | 获取字符串或数组的长度。 |
| `['slice', array | string, number]`<br/><br/>`['slice', array | string, number, number]` | 字符串 \| 数组 | 从数组返回项，或从指定起始索引的字符串返回子字符串，或在起始索引和结束索引之间（如果设置）返回项。 返回值包含起始索引，但不包含结束索引。 |

**示例**

使用 `get` 表达式可以直接在表达式中访问特征的属性。 此示例使用特征的 `zoneColor` 值来指定气泡层的颜色属性。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

如果所有点特征都具有 `zoneColor` 属性，则以上示例将正常运行。 否则，颜色可能会回退为“黑色”。 若要修改回退颜色，请将 `case` 表达式与 `has` 表达式结合使用，以检查该属性是否存在。 如果该属性不存在，则返回回退颜色。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

默认情况下，气泡层和符号层将呈现数据源中所有形状的坐标。 此行为可以突出显示多边形或线条的顶点。 可以在布尔表达式中使用 `['geometry-type']` 表达式，通过层的 `filter` 选项来限制该层呈现的特征的几何类型。 以下示例限制气泡层，以便仅呈现 `Point` 特征。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

以下示例允许呈现 `Point` 和 `MultiPoint` 特征。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

同样，多边形的轮廓将在线条层中呈现。 若要在线条层中禁用此行为，请添加仅允许 `LineString` 和 `MultiLineString` 特征的筛选器。  

下面是有关如何使用数据表达式的其他一些示例：

```javascript
//Get item [2] from an array "properties.abcArray[1]" = "c"
['at', 2, ['get', 'abcArray']]

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
['at', 1, ['at', 0, ['get', 'array2d']]]

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
['in', 'a', ['get', 'abcArray']]

//Gets the index of the value 'b' in an array "properties.abcArray.indexOf('b')" = 1
['index-of', 'b', ['get', 'abcArray']]

//Get the length of an array "properties.abcArray.length" = 3
['length', ['get', 'abcArray']]

//Get the value of a subproperty "properties._style.fillColor" = "red"
['get', 'fillColor', ['get', '_style']]

//Check that "fillColor" exists as a subproperty of "_style".
['has', 'fillColor', ['get', '_style']]

//Slice an array starting at index 2 "properties.abcArray.slice(2)" = ['c']
['slice', ['get', 'abcArray'], 2]

//Slice a string from index 0 to index 4 "properties.entityType.slice(0, 4)" = 'rest'
['slice', ['get', 'entityType'], 0, 4]
```

## <a name="math-expressions"></a>数学表达式

数学表达式提供数学运算符用于在表达式框架中执行数据驱动的计算。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['+', number, number, …]` | 数字 | 计算指定数之和。 |
| `['-', number]` | 数字 | 0 减指定数。 |
| `['-', number, number]` | 数字 | 将第一个数减第二个数。 |
| `['*', number, number, …]` | 数字 | 将指定的数相乘。 |
| `['/', number, number]` | 数字 | 将第一个数除以第二个数。 |
| `['%', number, number]` | 数字 | 计算将第一个数除以第二个数后得到的余数。 |
| `['^', number, number]` | 数字 | 计算第一个值经过第二个数的幂乘后的值。 |
| `['abs', number]` | 数字 | 计算指定数字的绝对值。 |
| `['acos', number]` | 数字 | 计算指定数的反余弦值。 |
| `['asin', number]` | 数字 | 计算指定数的反正弦值。 |
| `['atan', number]` | 数字 | 计算指定数的反正切值。 |
| `['ceil', number]` | 数字 | 将数字向上舍入为下一个整数。 |
| `['cos', number]` | 数字 | 计算指定数的余弦值。 |
| `['e']` | 数字 | 返回数学常数 `e`。 |
| `['floor', number]` | 数字 | 将数字向下舍入为上一个整数。 |
| `['ln', number]` | 数字 | 计算指定数的自然对数。 |
| `['ln2']` | 数字 | 返回数学常数 `ln(2)`。 |
| `['log10', number]` | 数字 | 计算指定数的以 10 为底的对数。 |
| `['log2', number]` | 数字 | 计算指定数的以 2 为底的对数。 |
| `['max', number, number, …]` | 数字 | 计算指定一组数中的最大数。 |
| `['min', number, number, …]` | 数字 | 计算指定一组数中的最小数。 |
| `['pi']` | 数字 | 返回数学常数 `PI`。 |
| `['round', number]` | 数字 | 将数字舍入为最接近的整数。 中点值采用远离零舍入模式。 例如，`['round', -1.5]` 计算为 `-2`。 |
| `['sin', number]` | 数字 | 计算指定数的正弦值。 |
| `['sqrt', number]` | 数字 | 计算指定数字的平方根。 |
| `['tan', number]` | 数字 | 计算指定数的正切值。 |

## <a name="aggregate-expression"></a>聚合表达式

聚合表达式定义对一组数据进行处理并可与 `DataSource` 的 `clusterProperties` 选项一起使用的计算。 这些表达式的输出必须是数字或布尔值。

聚合表达式采用三个值：运算符值和初始值，以及从数据中的每个功能检索属性以对其应用聚合运算的表达式。 该表达式采用以下格式：

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- 运算符：一个表达式函数，然后将其应用于由 `mapExpression` 为群集中的每个点计算的所有值。 支持的运算符：
  - 对于数字：`+`、`*`、`max`、`min`
  - 对于布尔值：`all`、`any`
- initialValue：对第一个计算值进行聚合的初始值。
- mapExpression：应用于数据集中的每个点的表达式。

**示例**

如果数据集中的所有功能都有一个 `revenue` 属性（它是一个数字）。 那么，可以计算出群集中所有点的总收入，这些点是由数据集创建的。 此计算使用以下聚合表达式完成：`['+', 0, ['get', 'revenue']]`

### <a name="accumulated-expression"></a>累积表达式

`accumulated` 表达式获取到目前为止累积的群集属性值。 这只能在聚集的 `DataSource` 源的 `clusterProperties` 选项中使用。

**使用情况**

```javascript
["accumulated"]
```

## <a name="boolean-expressions"></a>布尔表达式

布尔表达式提供一组布尔运算符表达式用于评估布尔比较结果。

比较值时，将严格区分类型。 不同类型的值始终被视为不相等。 在分析时类型已知不同的情况被视为无效，将生成分析错误。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['!', boolean]` | boolean | 逻辑非。 如果输入为 `true`，则返回 `false`；如果输入为 `false`，则返回 `true`。 |
| `['!=', value, value]` | boolean | 如果输入值不相等，则返回 `true`，否则返回 `false`。 |
| `['<', value, value]` | boolean | 如果第一个输入严格小于第二个输入，则返回 `true`，否则返回 `false`。 两个参数必须均为字符串，或者均为数字。 |
| `['<=', value, value]` | boolean | 如果第一个输入小于或等于第二个输入，则返回 `true`，否则返回 `false`。 两个参数必须均为字符串，或者均为数字。 |
| `['==', value, value]` | boolean | 如果输入值相等，则返回 `true`，否则返回 `false`。 两个参数必须均为字符串，或者均为数字。 |
| `['>', value, value]` | boolean | 如果第一个输入严格大于第二个输入，则返回 `true`，否则返回 `false`。 两个参数必须均为字符串，或者均为数字。 |
| `['>=' value, value]` | boolean | 如果第一个输入大于或等于第二个输入，则返回 `true`，否则返回 `false`。 两个参数必须均为字符串，或者均为数字。 |
| `['all', boolean, boolean, …]` | boolean | 如果所有输入为 `true`，则返回 `true`，否则返回 `false`。 |
| `['any', boolean, boolean, …]` | boolean | 如果任一输入为 `true`，则返回 `true`，否则返回 `false`。 |
| `['within', Polygon | MultiPolygon | Feature<Polygon | MultiPolygon>]` | boolean | 如果计算的特征完全包含在输入几何的边界内，则返回 `true`；否则返回 false。 输入值可以是类型为 `Polygon`、`MultiPolygon`、`Feature` 或 `FeatureCollection` 的有效 GeoJSON。 支持的计算特征：<br/><br/>-Point：如果某个点在边界上或在边界外，则返回 `false`。<br/>-LineString：如果一条线的任何部分落在边界外，该线条与边界相交，或者线条的端点位于边界上，则返回 `false`。 |

## <a name="conditional-expressions"></a>条件表达式

条件表达式提供类似于 if 语句的逻辑运算。

以下表达式对输入数据执行条件逻辑运算。 例如，`case` 表达式提供“if/then/else”逻辑，而 `match` 表达式则类似于“switch 语句”。

### <a name="case-expression"></a>case 表达式

`case` 表达式是一种提供“if/then/else”逻辑的条件表达式。 这种类型的表达式会逐步评估一系列布尔条件。 计算为 true 时，它会返回第一个布尔条件的输出值。

以下伪代码定义 `case` 表达式的结构。

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**示例**

以下示例逐步评估不同的布尔条件，直到找到评估为 `true` 的条件，然后返回该关联值。 如果没有任何布尔条件评估为 `true`，则返回回退值。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>match 表达式

`match` 表达式是一种提供类似 switch 语句逻辑的条件表达式。 输入可以是返回字符串或数字的任意表达式，例如 `['get', 'entityType']`。 每个标签必须采用单个文本值或文本值的数组，该数组中的值必须全部为字符串，或者全部为数字。 如果数组中的任一值匹配，则输入匹配。 每个标签必须唯一。 如果输入类型与标签类型不匹配，则结果将是回退值。

以下伪代码定义 `match` 表达式的结构。

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**示例**

以下示例查找气泡层中某个点特征的 `entityType` 属性以搜索匹配项。 如果找到匹配项，则返回该指定值，否则返回回退值。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

以下示例使用数组列出一组应全部返回相同值的标签。 此方法比单独列出每个标签要高效得多。 在本例中，如果 `entityType` 属性为“restaurant”或“grocery_store”，则返回颜色“red”。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>coalesce 表达式

`coalesce` 表达式逐步评估一组表达式，直到获取第一个非 null 值，然后返回该值。

以下伪代码定义 `coalesce` 表达式的结构。

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**示例**

以下示例使用 `coalesce` 表达式设置符号层的 `textField` 选项。 如果 `title` 属性在特征中缺失或设置为 `null`，则该表达式将尝试查找 `subTitle` 属性；如果该属性缺失或为 `null`，则将回退为空字符串。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subTitle. 
            ['get', 'subTitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

下面的示例使用 `coalesce` 表达式从指定的映像名称列表中检索地图子画面中提供的第一个可用映像图标。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>类型表达式

类型表达式提供用于测试和转换不同数据类型（例如字符串、数字和布尔值）的工具。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['array', value]` \| `['array', type: "string" | "number" | "boolean", value]` | Object[] | 断言输入为数组。 |
| `['boolean', value]` \| `["boolean", value, fallback: value, fallback: value, ...]` | boolean | 断言输入值为布尔值。 如果提供了多个值，则按顺序对每个值进行计算，直到获得布尔值。 如果没有任何输入为布尔值，则表达式错误。 |
| `['collator', { 'case-sensitive': boolean, 'diacritic-sensitive': boolean, 'locale': string }]` | 排序器 | 返回一个可在区域设置相关比较运算中使用的排序器。 区分大小写和音调符号敏感选项默认为 false。 locale 参数指定要使用的区域设置的 IETF 语言标记。 如果未提供任何值，则使用默认区域设置。 如果请求的区域设置不可用，排序器将使用系统定义的回退区域设置。 使用已解析的区域设置测试区域设置回退行为的结果。 |
| `['literal', array]`<br/><br/>`['literal', object]` | 数组 \| 对象 | 返回文本数组或对象值。 使用此表达式可防止将数组或对象作为表达式进行评估。 当表达式需要返回数组或对象时，有必要这样做。 |
| `['image', string]` | 字符串 | 检查是否已将指定的映像 ID 加载到地图映像子画面。 如果是，则返回 ID，否则返回 NULL。 |
| `['number', value]` \| `["number", value, fallback: value, fallback: value, ...]` | 数字 | 断言输入值为数字。 如果提供了多个值，则按顺序对每个值进行计算，直到获得数字。 如果没有任何输入为数字，则表达式错误。 |
| `['object', value]`  \| `["object", value, fallback: value, fallback: value, ...]` | 对象 | 断言输入值为对象。  如果提供了多个值，则按顺序对每个值进行计算，直到获得对象。 如果没有任何输入为对象，则表达式错误。 |
| `['string', value]` \| `["string", value, fallback: value, fallback: value, ...]` | 字符串 | 断言输入值为字符串。 如果提供了多个值，则按顺序对每个值进行计算，直到获得字符串。 如果没有任何输入为字符串，则表达式错误。 |
| `['to-boolean', value]` | boolean | 将输入值转换为布尔值。 如果输入为空字符串、`0`、`false`、`null` 或 `NaN`，则结果为 `false`，否则为 `true`。 |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | 将输入值转换为颜色。 如果提供了多个值，则按顺序对每个值进行计算，直到获得第一个成功转换。 如果没有任何输入可转换，则表达式错误。 |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | 数字 | 将输入值转换为数字（如果可能）。 如果输入为 `null` 或 `false`，则结果为 0。 如果输入为 `true`，则结果为 1。 如果输入是字符串，则使用 ECMAScript 语言规范的 [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) 字符串函数将其转换为数字。 如果提供了多个值，则按顺序对每个值进行计算，直到获得第一个成功转换。 如果没有任何输入可转换，则表达式错误。 |
| `['to-string', value]` | 字符串 | 将输入值转换为字符串。 如果输入为 `null`，则结果为 `""`。 如果输入为布尔值，则结果为 `"true"` 或 `"false"`。 如果输入是数字，则使用 ECMAScript 语言规范的 [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) 数字函数将其转换为字符串。 如果输入是一种颜色，则将其转换为 CSS RGBA 颜色字符串 `"rgba(r,g,b,a)"`。 否则，使用 ECMAScript 语言规范的 [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) 函数将输入转换为字符串。 |
| `['typeof', value]` | 字符串 | 返回一个字符串用于描述给定值的类型。 |

> [!TIP]
> 如果浏览器控制台中出现类似于 `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` 的错误消息，则表示代码中有一个表达式，该表达式数组的第一个值没有字符串。 如果希望表达式返回数组，请使用 `literal` 表达式包装数组。 下面的示例设置符号层的图标 `offset` 选项，该选项需要是一个包含两个数字的数组，方法是使用 `match` 表达式根据点特征的 `entityType` 属性的值在两个偏移值之间进行选择。
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>颜色表达式

使用颜色表达式可以更轻松地创建和处理颜色值。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | 基于 red、green 和 blue 分量（值必须为 `0` 至 `255`）和一个值为 `1` 的 alpha 分量创建颜色值。 如果任一分量超出范围，则表达式会生成错误。 |
| `['rgba', number, number, number, number]` | color | 基于 red、green 和 blue 分量（值必须为 `0` 至 `255`）和一个值为 `0` 到 `1` 的 alpha 分量创建颜色值。 如果任一分量超出范围，则表达式会生成错误。 |
| `['to-rgba']` | \[数字，数字，数字，数字\] | 返回一个四元素数组，其中包含输入颜色的 red、green、blue 和 alpha 分量（按该顺序排列）。 |

**示例**

以下示例创建一个 RGB 颜色值，其中的 red 值为 `255`，green 和 blue 值是通过将 `2.5` 与 `temperature` 属性值相乘计算得出的。 随着温度的变化，颜色将变为红色的不同阴影。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>字符串运算符表达式

字符串运算符表达式针对字符串执行转换操作，例如连接字符串和转换大小写。 

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['concat', string, string, …]` | 字符串 | 将多个字符串连接到一起。 每个值必须是字符串。 如果需要，请使用 `to-string` 类型表达式将其他值类型转换为字符串。 |
| `['downcase', string]` | 字符串 | 将指定的字符串转换为小写。 |
| `['is-supported-script', string]` \| `['is-supported-script', Expression]`| boolean | 确定输入字符串是否使用当前字体堆栈支持的字符集。 例如：`['is-supported-script', 'ಗೌರವಾರ್ಥವಾಗಿ']` |
| `['resolved-locale', string]` | 字符串 | 返回所提供排序器使用的区域设置的 IETF 语言标记。 此标记可用于确定默认的系统区域设置，或确定是否已成功加载请求的区域设置。 |
| `['upcase', string]` | 字符串 | 将指定的字符串转换为大写。 |

**示例**

以下示例将点特征的 `temperature` 属性转换为字符串，然后将“°F”连接到该字符串的末尾。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

以上表达式在地图上呈现一个图钉，该图钉上叠加了文本“64°F”，如下图所示。

![字符串运算符表达式示例](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions"></a>interpolate 和 step 表达式

interpolate 和 step 表达式可用于计算内插曲线或 step 函数中的值。 这些表达式采用一个返回数字值作为输入的表达式（例如 `['get',  'temperature']`）。 将根据输入和输出值对评估输入值，以确定最拟合内插曲线或 step 函数的值。 输出值称为“stop”。 每个 stop 的输入值必须是数字并按升序排列。 输出值必须是数字、数字数组或颜色。

### <a name="interpolate-expression"></a>interpolate 表达式

使用 `interpolate` 表达式可以通过在 stop 值之间进行内插来计算连续的平滑值集。 返回颜色值的 `interpolate` 表达式生成颜色梯度，结果值是从中选择的。

可在 `interpolate` 表达式中使用三种类型的内插方法：

- `['linear']` - 在 stop 对之间线性内插。
- `['exponential', base]` - 在 stop 之间以指数方式内插。 `base` 值控制输出的增加速度。 值越大，输出就会以越快的速度朝范围的上限递增。 接近 1 的 `base` 值会生成以更线性方式递增的输出。
- `['cubic-bezier', x1, y1, x2, y2]` - 使用按给定控制点定义的[三次方贝塞尔曲线](https://developer.mozilla.org/docs/Web/CSS/timing-function)进行内插。

以下示例演示了这些不同类型的内插的大致形式。

| 线性  | 指数 | 三次方贝塞尔 |
|---------|-------------|--------------|
| ![线性内插图](media/how-to-expressions/linear-interpolation.png) | ![指数内插图](media/how-to-expressions/exponential-interpolation.png) | ![三次方贝塞尔曲线内插图](media/how-to-expressions/bezier-curve-interpolation.png) |

以下伪代码定义 `interpolate` 表达式的结构。

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**示例**

以下示例使用 `linear interpolate` 表达式基于点特征的 `temperature` 属性设置气泡层的 `color` 属性。 如果 `temperature` 值小于 60，则返回“blue”。 如果该值大于等于 60 且小于 70，则返回“yellow”。 如果该值大于等于 70 且小于 80，则返回“orange”。 如果该值大于等于 80，则返回“red”。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50, 'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

下图演示了如何为以上表达式选择颜色。
 
![Interpolate 表达式示例](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Step 表达式

可以使用 `step` 表达式通过评估按 stop 定义的[分段常数函数](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)来计算离散的阶跃结果值。

以下伪代码定义 `step` 表达式的结构。

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

step 表达式返回紧靠在输入值前面的 stop 的输出值；如果输入小于第一个 stop，则返回第一个输入值。

**示例**

以下示例使用 `step` 表达式基于点特征的 `temperature` 属性设置气泡层的 `color` 属性。 如果 `temperature` 值小于 60，则返回“blue”。 如果该值大于等于 60 且小于 70，则返回“yellow”。 如果该值大于等于 70 且小于 80，则返回“orange”。 如果该值大于等于 80，则返回“red”。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

下图演示了如何为以上表达式选择颜色。

![Step 表达式示例](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>层特定的表达式

仅适用于特定层的特殊表达式。

### <a name="heat-map-density-expression"></a>热度地图密度表达式

热度地图密度表达式检索热度地图层中每个像素的热度地图密度值，定义为 `['heatmap-density']`。 此值是介于 `0` 和 `1` 之间的数字。 将此表达式与 `interpolation` 或 `step` 表达式相结合可以定义颜色梯度，用于在热度地图中赋色。 只能在热度地图层的[颜色选项](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions#color)中使用此表达式。

> [!TIP]
> 内插表达式中索引 0 处的颜色或递阶色的默认颜色定义没有数据的区域的颜色。 索引 0 处的颜色可用于定义背景色。 很多用户喜欢将此值设置为透明或半透明黑色。

**示例**

此示例使用线性内插表达式创建平滑颜色梯度来呈现热度地图。

```javascript
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

除了使用平滑梯度为热度地图赋色外，还可以使用 `step` 表达式在一组范围内指定颜色。 使用 `step` 表达式为热度地图赋色可以直观地将密度分解为类似于等高线或雷达式地图的范围。  

```javascript
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

有关详细信息，请参阅[添加热度地图层](map-add-heat-map-layer.md)文档。

### <a name="line-progress-expression"></a>折线进度表达式

折线进度表达式检索线条层中沿梯度线走向的进度，定义为 `['line-progress']`。 此值是介于 0 和 1 之间的数字。 此表达式与 `interpolation` 或 `step` 表达式结合使用。 只能结合线条层的 [strokeGradient 选项](/javascript/api/azure-maps-control/atlas.linelayeroptions#strokegradient)使用此表达式。

> [!NOTE]
> 线条层的 `strokeGradient` 选项要求将数据源的 `lineMetrics` 选项设置为 `true`。

**示例**

此示例使用 `['line-progress']` 表达式对线条笔划应用颜色梯度。

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[查看实时示例](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>文本字段格式表达式

可以结合符号层 `textOptions` 属性的 `textField` 选项使用文本字段格式表达式，以提供混合文本格式。 此表达式允许指定一组输入字符串和格式设置选项。 可以为该表达式中的每个输入字符串指定以下选项。

- `'font-scale'` - 指定字号的缩放系数。 如果已指定，此值将替代单个字符串的 `textOptions` 的 `size` 属性。
- `'text-font'` - 指定应用于此字符串的一个或多个字体系列。 如果已指定，此值将替代单个字符串的 `textOptions` 的 `font` 属性。

以下伪代码定义文本字段格式表达式的结构。

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[]
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[]
    },
    …
]
```

**示例**

以下示例通过添加粗体字体并放大特征的 `title` 属性字体大小，来设置文本字段的格式。 此示例还在新行中添加了特征的 `subTitle` 属性以及缩小的字体大小。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subTitle property. 
            ['get', 'subTitle'],
            { 
                'font-scale': 0.75
            }
        ]
    }
});
```

此层将如下图所示呈现点特征：

![具有带格式文本字段的点特征的插图](media/how-to-expressions/text-field-format-expression.png)

### <a name="number-format-expression"></a>number format 表达式

只能结合符合层的 `textField` 选项使用 `number-format` 表达式。 此表达式将提供的数字转换为带格式的字符串。 此表达式包装 JavaScript 的 [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) 函数，并支持以下选项集。

- `locale` - 指定此选项，以与指定语言一致的方式将数字转换为字符串。 将 [BCP 47 语言标记](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation)传递到此选项。
- `currency` - 将数字转换为表示货币的字符串。 可能的值为 [ISO 4217 货币代码](https://en.wikipedia.org/wiki/ISO_4217)，如美元的“USD”、欧元的“EUR”或人民币的“CNY”。
- `'min-fraction-digits'` - 指定要包含在数字的字符串版本中的最小小数位数。
- `'max-fraction-digits'` - 指定要包含在数字的字符串版本中的最大小数位数。

以下伪代码定义文本字段格式表达式的结构。

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**示例**

下面的示例使用 `number-format` 表达式来修改点特征的 `revenue` 属性在符号层的 `textField` 选项中的呈现方式，使其显示为美元值。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { 'currency': 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

此层将如下图所示呈现点特征：

![number format 表达式示例](media/how-to-expressions/number-format-expression.png)

### <a name="image-expression"></a>image 表达式

image 表达式可与符号层的 `image` 和 `textField` 选项以及多边形层的 `fillPattern` 选项一起使用。 此表达式将检查请求的图像是否存在于样式中，并返回已解析的图像名称或 `null`，这取决于图像当前是否为样式。 此验证过程是同步的，需要先将该图像添加到该样式，然后才能在 image 参数中请求它。

**示例**

下面的示例使用 `image` 表达式在符号层中添加一个与文本内联的图标。

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));
    
    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

该层将呈现符号层中的文本字段，如下图所示：

![image 表达式示例](media/how-to-expressions/image-expression.png)

## <a name="zoom-expression"></a>zoom 表达式

`zoom` 表达式用于在呈现时检索地图的当前缩放级别，定义为 `['zoom']`。 此表达式返回地图最小和最大缩放级别范围内的数字。 适用于 Web 和 Android 的 Azure Maps 交互式地图控件支持 25 个缩放级别，其编号为 0 到 24。 使用 `zoom` 表达式可以在地图缩放级别发生更改时动态修改样式。 `zoom` 表达式只能与 `interpolate` 和 `step` 表达式配合使用。

**示例**

默认情况下，热度地图层中呈现的数据点半径对所有缩放级别采用固定像素的半径。 缩放地图时，数据将会聚合，热度地图层将呈现不同的外观。 可以使用 `zoom` 表达式缩放每个缩放级别的半径，使每个数据点涵盖相同的地图物理区域。 这会使热度地图层看上去更加静态且一致。 地图的每个缩放级别的垂直和水平像素是前一个缩放级别的两倍。 调整半径使其在每个缩放级别中翻倍，会创建一个在所有缩放级别下均保持一致外观的热度地图。 若要实现此目的，可将 `zoom` 表达式与 `base 2 exponential interpolation` 表达式配合使用，并为最小缩放级别设置像素半径，为最大缩放级别设置标度半径（计算方式为 `2 * Math.pow(2, minZoom - maxZoom)`，如下所示）。

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        1, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[查看实时示例](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>变量绑定表达式

变量绑定表达式将计算结果存储在变量中。 因此，可以在表达式中的其他位置多次引用计算结果。 这对于涉及许多计算的表达式而言是一种有效的优化。

| Expression | 返回类型 | 说明 |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'let',<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | 将一个或多个值存储为变量，供 `var` 表达式在返回结果的子表达式中使用。 |
| `['var', name: string]` | any | 引用使用 `let` 表达式创建的变量。 |

**示例**

此示例使用一个表达式计算相对于温度比的收入，然后使用 `case` 表达式评估针对此值执行的不同布尔运算。 `let` 表达式用于存储相对于温度比的收入，以便只需计算此值一次。 `var` 表达式根据所需的频率引用此变量，而不必重新计算此变量。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>后续步骤

有关实现表达式的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加热度地图层](map-add-heat-map-layer.md)

详细了解支持表达式的层选项：

> [!div class="nextstepaction"]
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)