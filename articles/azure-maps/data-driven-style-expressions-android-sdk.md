---
title: Android maps 中的数据驱动样式表达式 |Microsoft Azure 映射
description: 了解数据驱动样式表达式。 请参阅如何在 Azure Maps Android SDK 中使用这些表达式来调整地图中的样式。
author: rbrundritt
ms.author: richbrun
ms.date: 12/1/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 61d7a295d86fd7da74dee03cd35c79feea0218ed
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681445"
---
# <a name="data-driven-style-expressions-android-sdk"></a>数据驱动样式表达式 (Android SDK) 

使用表达式可以将业务逻辑应用到样式选项，这些选项可观察数据源中每个形状中定义的属性。 表达式可以筛选数据源或层中的数据。 表达式可以包含条件逻辑，如 if 语句。 而且，它们可用于使用：字符串运算符、逻辑运算符和数学运算符来操作数据。

数据驱动的样式可减少在样式设置周围实现业务逻辑所需的代码量。 与层一起使用时，将在单独的线程上的呈现时计算表达式。 与评估 UI 线程上的业务逻辑相比，此功能可提供更高的性能。

Azure Maps Android SDK 支持几乎所有与 Azure Maps Web SDK 相同的样式表达式，因此， [ (WEB sdk) 的数据驱动样式表达式 ](data-driven-style-expressions-web-sdk.md) 中所述的所有概念都可以转移到 Android 应用中。 Azure Maps Android SDK 中的所有样式表达式都在 `com.microsoft.azure.maps.mapcontrol.options.Expression` 命名空间中可用。 有许多不同类型的样式表达式。

| 表达式的类型 | 描述 |
|---------------------|-------------|
| [布尔表达式](#boolean-expressions) | 布尔表达式提供一组用于计算布尔值比较的布尔运算符表达式。 |
| [颜色表达式](#color-expressions) | 颜色表达式使您可以更轻松地创建和操作颜色值。 |
| [条件表达式](#conditional-expressions) | 条件表达式提供类似于 if 语句的逻辑运算。 |
| [数据表达式](#data-expressions) | 提供对功能中的属性数据的访问。 |
| [内插和单步表达式](#interpolate-and-step-expressions) | 内插和单步表达式可用于沿内插曲线或单步执行函数计算值。 |
| [基于 JSON 的表达式](#json-based-expressions) | 使用 Android SDK，可以轻松地重复使用为 Web SDK 创建的基于 JSON 的样式原始表达式。 |  
| [层特定的表达式](#layer-specific-expressions) | 仅适用于单个层的特殊表达式。 |
| [数学表达式](#math-expressions) | 提供数学运算符，用于在 expression framework 内执行数据驱动的计算。 |
| [字符串运算符表达式](#string-operator-expressions) | 字符串运算符表达式对字符串执行转换操作，例如连接和转换大小写。 |
| [类型表达式](#type-expressions) | 类型表达式提供了用于测试和转换不同数据类型（如字符串、数字和布尔值）的工具。 |
| [变量绑定表达式](#variable-binding-expressions) | 变量绑定表达式将计算的结果存储在一个变量中，并多次引用表达式中的其他位置，而无需重新计算存储的值。 |
| [Zoom 表达式](#zoom-expression) | 在呈现时检索地图的当前缩放级别。 |

文档的此部分中的所有示例都使用以下功能来演示如何使用这些表达式的不同方式。

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

下面的代码演示如何在应用程序中手动创建此 GeoJSON 功能。

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

下面的代码演示如何将 JSON 对象的字符串化版本反序列化为应用程序中的 GeoJSON 功能。

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

## <a name="json-based-expressions"></a>基于 JSON 的表达式

Azure Maps Web SDK 还支持使用 JSON 数组表示的数据驱动样式表达式。 使用 Android SDK 中的本机类可以重新创建这些相同 `Expression` 的表达式。 或者，可以使用 web 函数（如）将这些基于 JSON 的表达式转换为字符串 `JSON.stringify` ，并将其传递给 `Expression.raw(String rawExpression)` 方法。 例如，采用以下 JSON 表达式。

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

上述表达式的字符串化版本将为 `"['get','title']"` ，可按如下所示读取到 Android SDK 中。

```java
Expression exp = Expression.raw("['get','title']")
```

使用此方法可以轻松地在使用 Azure Maps 的移动和 web 应用之间重用样式表达式。

此视频概述了 Azure Maps 中的数据驱动样式。

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>数据表达式

数据表达式提供对功能中的属性数据的访问。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `accumulated()` | 数字 | 获取到目前为止累积的群集属性的值。 |
| `at(number | Expression, Expression)` | value | 从数组中检索项。 |
| `geometryType()` | string | 获取功能的几何图形类型： Point、MultiPoint、LineString、MultiLineString、多边形、MultiPolygon。 |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | value | 从提供的对象的属性获取属性值。 如果缺少请求的属性，则返回 null。 |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | boolean | 确定功能的属性是否具有指定的属性。 |
| `id()` | value | 获取功能的 ID （如果有）。 |
| `in(string | number | Expression, Expression)` | boolean | 确定某一项是否存在于数组中 |
| `length(string | Expression)` | 数字 | 获取字符串或数组的长度。 |
| `properties()`| value | 获取功能属性对象。 |

Android SDK 中不支持以下 Web SDK 样式表达式：

- 索引
- 切片

**示例**

通过使用表达式，可以在表达式中直接访问功能的属性 `get` 。 此示例使用 `zoneColor` 功能的值来指定气泡图层的颜色属性。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

如果所有点功能都具有属性，则上述示例将正常运行 `zoneColor` 。 否则，颜色可能会回退到 "黑色"。 若要修改回退颜色，请将 `switchCase` 表达式与表达式结合使用 `has` 以检查该属性是否存在。 如果该属性不存在，则返回回退颜色。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

默认情况下，气泡图和符号层将呈现数据源中所有形状的坐标。 此行为可以突出显示多边形或线条的顶点。 `filter`通过使用布尔表达式中的表达式，层的选项可用于限制其呈现的功能的几何类型 `geometryType` 。 下面的示例将限制气泡图层，以便仅 `Point` 呈现功能。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    filter(eq(geometryType(), "Point"))
);
```

下面的示例允许 `Point` 呈现和 `MultiPoint` 功能。 

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

同样，多边形的轮廓将以线条层呈现。 若要在线条层中禁用此行为，请添加只允许和功能的筛选器 `LineString` `MultiLineString` 。  

下面是有关如何使用数据表达式的一些其他示例：

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>数学表达式

数学表达式提供数学运算符，用于在 expression framework 内执行数据驱动的计算。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `abs(number | Expression)` | 数字 | 计算指定数字的绝对值。 |
| `acos(number | Expression)` | 数字 | 计算指定数字的反余弦值。 |
| `asin(number | Expression)` | 数字 | 计算指定数字的反正弦值。 |
| `atan(number | Expression)` | 数字 | 计算指定数字的反正切值。 |
| `ceil(number | Expression)` | 数字 | 将数字向上舍入到下一个整数。 |
| `cos(number | Expression)` | 数字 | 计算指定数字的 cos。 |
| `division(number, number)` \| `division(Expression, Expression)` | 数字 | 将第一个数字除以第二个数字。 Web SDK 等效表达式： `/` |
| `e()` | 数字 | 返回数学常量 `e` 。 |
| `floor(number | Expression)` | 数字 | 将数字向下舍入到上一个整数。 |
| `log10(number | Expression)` | 数字 | 计算指定数字的以10为底的对数。 |
| `log2(number | Expression)` | 数字 | 计算指定数字的以2为底的对数。 |
| `ln(number | Expression)` | 数字 | 计算指定数字的自然对数。 |
| `ln2()` | 数字 | 返回数学常量 `ln(2)` 。 |
| `max(numbers... | expressions...)` | 数字 | 计算指定的一组数字中的最大数目。 |
| `min(numbers... | expressions...)` | 数字 | 计算指定的一组数字中的最小数目。 |
| `mod(number, number)` \| | `mod(Expression, Expression)` | 数字 | 计算第一个数字除以第二个数字时的余数。 Web SDK 等效表达式： `%` |
| `pi()` | 数字 | 返回数学常量 `PI` 。 |
| `pow(number, number)` \| `pow(Expression, Expression)` | 数字 | 计算第一个值的值，该值为第二个数字的幂。 |
| `product(numbers... | expressions...)` | 数字 | 将指定的数字相乘。 Web SDK 等效表达式： `*` |
| `round(number | Expression)` | 数字 | 将数字舍入到最接近的整数。 将值从零向外舍入。 例如， `round(-1.5)` 计算结果为 `-2` 。 |
| `sin(number | Expression)` | 数字 | 计算指定数字的正弦值。 |
| `sqrt(number | Expression)` | 数字 | 计算指定数字的平方根。 |
| `subtract(number | Expression` | 数字 | 将0减去指定数字。 |
| `subtract(number | Expression, number | Expression)` | 数字 | 用第二个数字减去第一个数字。 |
| `sum(numbers... | expressions...)` | 数字 | 计算指定数字的和。 |
| `tan(number | Expression)` | 数字 | 计算指定数值的正切值。 |

## <a name="boolean-expressions"></a>布尔表达式

布尔表达式提供一组用于计算布尔值比较的布尔运算符表达式。

比较值时，将严格类型化比较。 不同类型的值始终被视为不相等。 在分析时已知不同类型的情况被视为无效，并将生成分析错误。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `all(Expression...)` | boolean | `true`如果所有输入都为，则返回 `true` `false` ; 否则返回。 |
| `any(Expression...)` | boolean | `true`如果任何输入为，则返回 `true` `false` ; 否则返回。 |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`如果输入值相等，则返回 `false` ; 否则返回。 参数需要同时为字符串和/或数字。 |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`如果第一个输入严格大于第二个输入，则返回 `false` ; 否则返回。 参数需要同时为字符串和/或数字。 |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`如果第一个输入大于或等于第二个输入，则返回 `false` ; 否则返回。 参数需要同时为字符串和/或数字。 |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`如果第一个输入严格小于第二个输入，则返回 `false` ; 否则返回。 参数需要同时为字符串和/或数字。 |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`如果第一个输入小于或等于第二个输入，则返回 `false` ; 否则返回。 参数需要同时为字符串和/或数字。 |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`如果输入值不相等，则返回 `false` ; 否则返回。 |
| `not(Expression | boolean)` | boolean | 逻辑求反。 `true`如果输入为，则返回 `false` ; `false` 如果输入为，则返回 `true` 。 |

## <a name="conditional-expressions"></a>条件表达式

条件表达式提供类似于 if 语句的逻辑运算。

以下表达式对输入数据执行条件逻辑操作。 例如， `switchCase` 表达式提供 "if/then/else" 逻辑，而 `match` 表达式就像 "switch 语句"。 

### <a name="switch-case-expression"></a>Switch case 表达式

`switchCase`表达式是一种提供 "if/then/else" 逻辑的条件表达式。 此类型的表达式逐步完成布尔条件的列表。 它将返回第一个布尔条件的输出值，其计算结果为 true。

以下伪代码定义表达式的结构 `switchCase` 。

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**示例**

下面的示例单步执行不同的布尔条件，直到找到计算结果为的 `true` 值，然后返回该关联值。 如果没有布尔条件的计算结果为 `true` ，则返回回退值。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

### <a name="match-expression"></a>Match 表达式

`match`表达式是一种提供类似于逻辑的 switch 语句的条件表达式。 输入可以是任何表达式 `get( "entityType")` ，如返回字符串或数字。 每个停止必须具有一个标签，该标签既是单个文本值，也是文本值的数组，其值必须是所有字符串或所有数字。 如果数组中的任何值匹配，则输入匹配。 每个停止标签都必须是唯一的。 如果输入类型与标签类型不匹配，则结果将是默认回退值。

以下伪代码定义表达式的结构 `match` 。

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**示例**

下面的示例在 `entityType` 气泡图层中查找点功能的属性搜索匹配项。 如果找到匹配项，则返回指定的值或返回回退值。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

下面的示例使用数组列出一组应返回相同值的标签。 此方法比单独列出每个标签要高效得多。 在这种情况下，如果 `entityType` 属性为 "餐馆" 或 "grocery_store"，则将返回颜色 "red"。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

### <a name="coalesce-expression"></a>合并表达式

`coalesce`表达式逐句通过一组表达式，直到获取第一个非 null 值并返回该值。

以下伪代码定义表达式的结构 `coalesce` 。

```java
coalesce(Expression... input)
```

**示例**

下面的示例使用 `coalesce` 表达式来设置 `textField` 符号层的选项。 如果 `title` 特性缺少该特性或将其设置为 `null` ，则该表达式将尝试查找 `subTitle` 属性（如果缺少或 `null` ，它将回退到空字符串）。 

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

## <a name="type-expressions"></a>类型表达式

类型表达式提供了用于测试和转换不同数据类型（如字符串、数字和布尔值）的工具。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `array(Expression)` | Object [] | 断言输入为数组。 |
| `bool(Expression)` | boolean | 断言输入值为布尔值。 |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | 机 | 返回要在与区域设置相关的比较操作中使用的排序程序。 区分大小写和音调符号的选项默认为 false。 Locale 参数指定要使用的区域设置的 IETF 语言标记。 如果未提供任何值，则使用默认区域设置。 如果请求的区域设置不可用，则排序程序将使用系统定义的回退区域设置。 使用已解决的区域设置测试区域设置回退行为的结果。  |
| `literal(boolean \| number \| string \| Object \| Object[])` | 布尔 \| 数字 \| 字符串 \| 对象 \| 对象 [] | 返回文本数组或对象值。 使用此表达式可防止将数组或对象作为表达式进行计算。 当表达式需要返回数组或对象时，这是必需的。 |
| `number(Expression)` | 数字 | 断言输入值是一个数字。 |
| `object(Expression)` | 对象 | 断言输入值是一个对象。 |
| `string(Expression)` | string | 断言输入值为字符串。 |
| `toArray(Expression)` | Object [] | 将表达式转换为 JSON 对象数组。 |
| `toBool(Expression)` | boolean | 将输入值转换为布尔值。 |
| `toNumber(Expression)` | 数字 | 如果可能，将输入值转换为数字。 |
| `toString(Expression)` | string | 将输入值转换为字符串。 |
| `typeoOf(Expression)` | string | 返回一个字符串，该字符串描述给定值的类型。 |

## <a name="color-expressions"></a>颜色表达式

颜色表达式使您可以更轻松地创建和操作颜色值。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `color(int)` | color | 将颜色整数值转换为颜色表达式。 |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | color | 从 *红色*、 *绿色* 和 *蓝色* 分量创建颜色值，该颜色值必须介于和之间， `0` `255` 并具有的 alpha 分量 `1` 。 如果任何组件超出范围，则表达式为错误。 |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | color | 从 *红色*、 *绿色*、 *蓝色* 分量创建颜色值，这些分量必须介于和之间， `0` 并且在 `255` 和范围内的 alpha `0` 分量 `1` 。 如果任何组件超出范围，则表达式为错误。 |
| `toColor(Expression)` | color  | 将输入值转换为颜色。 |
| `toRgba(Expression)` | color | 返回一个由四个元素组成的数组，其中包含输入颜色的红色、绿色、蓝色和 alpha 分量，按顺序排列。 |

**示例**

下面的示例创建一个 RGB 颜色值，其 *红色* 值为 `255` ，而 *绿色* 和 *蓝色* 值是通过与属性的值相乘计算得出的 `2.5` `temperature` 。 温度变化时，颜色将变为不同的 *红色* 阴影。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

如果所有颜色参数都是数字，则无需将其与表达式一起换行 `literal` 。 例如：

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

> [!TIP]
> 可以使用方法将字符串颜色值转换为颜色 `android.graphics.Color.parseColor` 。 以下将十六进制颜色字符串转换为可与层一起使用的颜色表达式。
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>字符串运算符表达式

字符串运算符表达式对字符串执行转换操作，例如连接和转换大小写。 

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | string | 将多个字符串连接在一起。 每个值必须是字符串。 如果需要，请使用 `toString` 类型表达式将其他值类型转换为字符串。 |
| `downcase(string)` \| `downcase(Expression)` | string | 将指定的字符串转换为小写。 |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| boolean | 确定输入字符串是否使用当前字体堆栈支持的字符集。 例如：`isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | string | 返回所提供的排序程序所使用的区域设置的 IETF 语言标记。 这可用于确定默认的系统区域设置，或确定是否已成功加载请求的区域设置。 |
| `upcase(string)` \| `upcase(Expression)` | string | 将指定的字符串转换为大写。 |

**示例**

下面的示例将 `temperature` 点功能的属性转换为字符串，然后将 "° f" 连接到该字符串的末尾。

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

上面的表达式在地图上呈现文本 "64 ° F" 的 pin，如下图所示。

![字符串运算符表达式示例](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions"></a>内插和单步表达式

内插和单步表达式可用于沿内插曲线或单步执行函数计算值。 这些表达式采用返回数值作为输入的表达式，例如 `get("temperature")` 。 根据输入和输出值对计算输入值，以确定最适合于内插曲线或单步执行函数的值。 输出值称为 "停止"。 每个停止的输入值必须是数字，并且必须是升序。 输出值必须是数字、数字数组或颜色。

### <a name="interpolate-expression"></a>内插表达式

`interpolate`表达式可用于通过在 stop 值之间进行插值计算来计算连续平滑的值集。 `interpolate`返回颜色值的表达式将产生从中选择结果值的颜色渐变。 `interpolate`表达式具有以下格式：

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

可在表达式中使用三种类型的内插方法 `interpolate` ：

| 名称 | 描述 | 
|------|-------------|
| `linear()` | 在停止对之间线性地进行内插。  |
| `exponential(number)` \| `exponential(Expression)` | 停止之间以指数方式内插。 指定了 "base"，并控制输出的增加速度。 值越大，输出就会增加到范围的高端。 接近于1的 "基数" 值会生成输出，该输出将增加线性增长。|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | 使用由给定控制点定义的 [三次方贝塞尔曲线](https://developer.mozilla.org/docs/Web/CSS/timing-function) 进行内插。 |

`stop`表达式的格式为 `stop(stop, value)` 。
 
下面是这些不同类型的内插的示例。 

| 线性  | 指数 | 三次方贝塞尔 |
|---------|-------------|--------------|
| ![线性内插图](media/how-to-expressions/linear-interpolation.png) | ![指数内插关系图](media/how-to-expressions/exponential-interpolation.png) | ![三次方贝塞尔插值图](media/how-to-expressions/bezier-curve-interpolation.png) |

**示例**

下面的示例使用 `linear interpolate` 表达式 `bubbleColor` 基于点功能的属性设置气泡图层的属性 `temperature` 。 如果 `temperature` 值小于60，则将返回 "blue"。 如果介于60到70之间，则将返回黄色。 如果介于70到80之间，则将返回 "橙色" (`#FFA500`) 。 如果为80或更高，则返回 "red"。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

下图演示了如何为上面的表达式选择颜色。

![内插表达式示例](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>步骤表达式

`step`通过计算由停止定义的[分段函数](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)，表达式可用于计算离散的阶数结果值。 

`interpolate`表达式具有以下格式：

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

步骤表达式返回的输出值刚好早于输入值，或第一个输入值（如果输入小于第一次停止）。 

**示例**

下面的示例使用 `step` 表达式 `bubbleColor` 基于点功能的属性设置气泡图层的属性 `temperature` 。 如果 `temperature` 值小于60，则将返回 "blue"。 如果介于60到70之间，则将返回 "黄色"。 如果介于70到80之间，则将返回 "橙色"。 如果为80或更高，则返回 "red"。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

下图演示了如何为上面的表达式选择颜色。
 
![步骤表达式示例](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>层特定的表达式

仅适用于特定层的特殊表达式。

### <a name="heat-map-density-expression"></a>热度地图密度表达式

热度地图密度表达式检索热度地图层中每个像素的热度地图密度值，并将其定义为 `heatmapDensity` 。 此值是一个介于和之间的数字 `0` `1` 。 它与 `interpolation` or 表达式结合使用 `step` 来定义用于着色热度地图的颜色渐变。 此表达式只能用于 `heatmapColor` 热度地图层的选项。

> [!TIP]
> 位于索引0、内插表达式或步骤颜色的默认颜色中的颜色定义了没有数据的区域的颜色。 索引0处的颜色可用于定义背景色。 很多用户喜欢将此值设置为透明或半透明黑色。

**示例**

此示例使用 "内插内插" 表达式为呈现热度地图创建平滑颜色渐变。 

```java
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

除了使用平滑渐变来着色热度地图外，还可以使用表达式在一组范围内指定颜色 `step` 。 使用 `step` 用于着色热度地图的表达式直观地将密度分解为类似于等高线或雷达样式图的范围。  

```java 
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

有关详细信息，请参阅 [添加热度地图层](map-add-heat-map-layer-android.md) 文档。

### <a name="text-field-format-expression"></a>文本字段格式表达式

`format`表达式可以与 `textField` 符号层的选项一起使用，以提供混合文本格式。 此表达式采用一个或多个 `formatEntry` 表达式，该表达式指定 `formatOptions` 要追加到文本字段的字符串和的集合。

| 表达式 | 说明 |
|------------|-------------|
| `format(Expression...)` | 返回包含用于混合格式文本字段项的批注的格式化文本。 |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | 返回要在表达式中使用的格式化字符串项 `format` 。 |

可用的格式选项如下：

| 表达式 | 说明 |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | 指定字体大小的缩放系数。 如果指定，此值将覆盖 `textSize` 单个字符串的属性。 |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | 指定呈现时要应用于文本的颜色。 |

**示例**

下面的示例通过添加粗体字体并放大功能的属性的字体大小，设置文本字段的格式 `title` 。 此示例还将功能的属性添加到 `subTitle` 带缩小字体大小的换行符上。

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

此层将呈现点功能，如下图所示：

![带格式文本字段的点功能图像](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>Zoom 表达式

`zoom`表达式用于在呈现时检索地图的当前缩放级别，并定义为 `zoom()` 。 此表达式在地图的最小和最大缩放级别范围之间返回一个数字。 适用于 web 和 Android 的 Azure Maps 交互式地图控件支持25个缩放级别，其编号为0到24。 通过使用 `zoom` 表达式，可以在更改地图的缩放级别时动态修改样式。 `zoom`表达式只能与和表达式一起使用 `interpolate` `step` 。

**示例**

默认情况下，热度地图层中呈现的数据点的半径对于所有缩放级别都具有固定像素半径。 放大地图后，数据聚合在一起，热度地图层看起来不同。 `zoom`表达式可用于缩放每个缩放级别的半径，使每个数据点都涵盖地图的同一物理区域。 这会使热度地图层看起来更加静态和一致。 地图的每个缩放级别都具有两倍于与上一个缩放级别垂直和水平的像素。 缩放 radius，使其与每个缩放级别翻倍，将创建一个在所有缩放级别上都保持一致的热度地图。 可以使用 `zoom` 表达式和表达式来实现此目的 `base 2 exponential interpolation` ，并为最小缩放级别设置像素半径，并为计算的最大缩放级别设置缩放半径，如下 `2 * Math.pow(2, minZoom - maxZoom)` 所示。

```java 
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

## <a name="variable-binding-expressions"></a>变量绑定表达式

变量绑定表达式将计算结果存储在变量中。 因此，可以多次在表达式中的其他地方引用计算结果。 对于涉及许多计算的表达式，它是一个有用的优化。

| Expression | 返回类型 | 说明 |
|--------------|---------------|--------------|
| `let(Expression... input)` | | 将一个或多个值作为变量存储，以供 `var` 返回结果的子表达式中的表达式使用。 |
| `var(Expression expression)` \| `var(string variableName)` | 对象 | 引用使用表达式创建的变量 `let` 。 |

**示例**

此示例使用一个表达式，该表达式计算相对于温度比率的收入，并使用 `case` 表达式来计算对该值的不同布尔运算。 该 `let` 表达式用于存储相对于温度比率的收入，因此只需计算一次。 表达式将根据 `var` 需要经常引用此变量，而不必重新计算此变量。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

## <a name="next-steps"></a>后续步骤

详细了解支持表达式的层：

> [!div class="nextstepaction"]
> [添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [添加线条层](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [添加热度地图](map-add-heat-map-layer-android.md)
