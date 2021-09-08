---
title: Android 地图中的数据驱动样式表达式 | Microsoft Azure Maps
description: 了解数据驱动样式表达式 了解如何使用 Azure Maps Android SDK 中的这些表达式来调整地图中的样式。
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 7c73c52045504dfeeaccf5292aea7ab07b75d8d8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438523"
---
# <a name="data-driven-style-expressions-android-sdk"></a>数据驱动样式表达式 (Android SDK)

使用表达式可将业务逻辑应用到样式选项，这些选项遵循数据源中每个形状内定义的属性。 表达式可以筛选数据源或层中的数据。 表达式可以包含条件逻辑，例如 if 语句。 此外，可以结合字符串运算符、逻辑运算符和数学运算符使用表达式来处理数据。

数据驱动的样式减少了实现样式相关业务逻辑所需的代码量。 对层使用表达式时，呈现时将在单独的线程中评估表达式。 与在 UI 线程中评估业务逻辑相比，此功能提供更高的性能。

Azure Maps Android SDK 支持的所有样式表达式几乎与 Azure Maps Web SDK 相同，因此，[数据驱动样式表达式 (Web SDK)](data-driven-style-expressions-web-sdk.md) 中所述的概念都同样适用于 Android 应用。 Azure Maps Android SDK 中的所有样式表达式都在 `com.microsoft.azure.maps.mapcontrol.options.Expression` 命名空间下提供。 有许多不同类型的样式表达式。

| 表达式的类型 | 描述 |
|---------------------|-------------|
| [布尔表达式](#boolean-expressions) | 布尔表达式提供一组布尔运算符表达式用于评估布尔比较结果。 |
| [颜色表达式](#color-expressions) | 使用颜色表达式可以更轻松地创建和处理颜色值。 |
| [条件表达式](#conditional-expressions) | 条件表达式提供类似于 if 语句的逻辑运算。 |
| [数据表达式](#data-expressions) | 提供对特征中属性数据的访问。 |
| [interpolate 和 step 表达式](#interpolate-and-step-expressions) | Interpolate 和 Step 表达式可用于计算内插曲线或 step 函数中的值。 |
| [基于 JSON 的表达式](#json-based-expressions) | 在 Android SDK 中使用这些表达式可以轻松重用为 Web SDK 创建的基于 JSON 的原始样式表达式。 |  
| [层特定的表达式](#layer-specific-expressions) | 仅适用于单个层的特殊表达式。 |
| [数学表达式](#math-expressions) | 提供数学运算符用于在表达式框架中执行数据驱动的计算。 |
| [字符串运算符表达式](#string-operator-expressions) | 字符串运算符表达式针对字符串执行转换操作，例如连接字符串和转换大小写。 |
| [类型表达式](#type-expressions) | 类型表达式提供用于测试和转换不同数据类型（例如字符串、数字和布尔值）的工具。 |
| [变量绑定表达式](#variable-binding-expressions) | 变量绑定表达式将计算结果存储在某个变量中，并在表达式中的其他位置多次引用该变量，这样而无需重新计算存储的值。 |
| [zoom 表达式](#zoom-expression) | 在呈现时检索地图的当前缩放级别。 |

> [!NOTE]
> 表达式的语法在 Java 和 Kotlin 中大体是相同的。 如果你将文档设置为 Kotlin，但看到的却是 Java 代码块，请不要担心，这些代码在这两种语言中是相同的。

本文档部分中的所有示例使用以下特征来演示这些表达式的不同用法。

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

以下代码演示如何在应用中手动创建此 GeoJSON 特征。

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add properties to the feature.
feature.addNumberProperty("id", 123)
feature.addStringProperty("entityType", "restaurant")
feature.addNumberProperty("revenue", 12345)
feature.addStringProperty("subTitle", "Building 40")
feature.addNumberProperty("temperature", 64)
feature.addStringProperty("title", "Cafeteria")
feature.addStringProperty("zoneColor", "purple")

val abcArray = JsonArray()
abcArray.add("a")
abcArray.add("b")
abcArray.add("c")

feature.addProperty("abcArray", abcArray)

val array1 = JsonArray()
array1.add("a")
array1.add("b")

val array2 = JsonArray()
array1.add("x")
array1.add("y")

val array2d = JsonArray()
array2d.add(array1)
array2d.add(array2)

feature.addProperty("array2d", array2d)

val style = JsonObject()
style.addProperty("fillColor", "red")

feature.addProperty("_style", style)
```

::: zone-end

以下代码演示如何在应用中将 JSON 对象的字符串化版本反序列化为 GeoJSON 特征。

::: zone pivot="programming-language-java-android"

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}"

val feature = Feature.fromJson(featureString)
```

::: zone-end

## <a name="json-based-expressions"></a>基于 JSON 的表达式

Azure Maps Web SDK 还支持使用 JSON 数组表示的数据驱动样式表达式。 可以在 Android SDK 中使用本机 `Expression` 类重新创建这些表达式。 或者，可以使用诸如 `JSON.stringify` 的 Web 函数将这些基于 JSON 的表达式转换为字符串，并将其传入 `Expression.raw(String rawExpression)` 方法。 例如，我们采用以下 JSON 表达式。

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

以上表达式的字符串化版本为 `"['get','title']"`，可按如下所示读入到 Android SDK 中。

::: zone pivot="programming-language-java-android"

```java
Expression exp = Expression.raw("['get','title']")
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val exp = Expression.raw("['get','title']")
```

::: zone-end

通过这种方法可以轻松在使用 Azure Maps 的移动应用与 Web 应用之间重用样式表达式。

此视频提供了 Azure Maps 中数据驱动样式的概述。

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>数据表达式

数据表达式提供对特征中属性数据的访问。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `accumulated()` | 数字 | 获取到目前为止累积的群集属性值。 这只能在聚集的 `DataSource` 源的 `clusterProperties` 选项中使用。 |
| `at(number | Expression, Expression)` | 值 | 从数组中检索项。 |
| `geometryType()` | 字符串 | 获取特征的几何类型：Point、MultiPoint、LineString、MultiLineString、Polygon、MultiPolygon。 |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | value | 从所提供对象的属性中获取属性值。 如果缺少请求的属性，则返回 null。 |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | boolean | 确定特征的属性是否具有指定的属性。 |
| `id()` | value | 获取特征的 ID（如果有）。 |
| `in(string | number | Expression, Expression)` | boolean | 确定某个项是否存在于数组中 |
| `length(string | Expression)` | 数字 | 获取字符串或数组的长度。 |
| `properties()`| value | 获取特征属性对象。 |

Android SDK 中不支持以下 Web SDK 样式表达式：

- index-of
- 切片

**示例**

使用 `get` 表达式可以直接在表达式中访问特征的属性。 此示例使用特征的 `zoneColor` 值来指定气泡层的颜色属性。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
)
```

::: zone-end

如果所有点特征都具有 `zoneColor` 属性，则以上示例将正常运行。 否则，颜色可能会回退为“黑色”。 若要修改回退颜色，请将 `switchCase` 表达式与 `has` 表达式结合使用，以检查该属性是否存在。 如果该属性不存在，则返回回退颜色。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
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
)
```

::: zone-end

默认情况下，气泡层和符号层将呈现数据源中所有形状的坐标。 此行为可以突出显示多边形或线条的顶点。 可以在布尔表达式中使用 `geometryType` 表达式，通过层的 `filter` 选项来限制该层呈现的特征的几何类型。 以下示例限制气泡层，以便仅呈现 `Point` 特征。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
)
```

::: zone-end

以下示例允许呈现 `Point` 和 `MultiPoint` 特征。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
)
```

::: zone-end

同样，多边形的轮廓将在线条层中呈现。 若要在线条层中禁用此行为，请添加仅允许 `LineString` 和 `MultiLineString` 特征的筛选器。  

下面是有关如何使用数据表达式的其他一些示例：

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

数学表达式提供数学运算符用于在表达式框架中执行数据驱动的计算。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `abs(number | Expression)` | 数字 | 计算指定数字的绝对值。 |
| `acos(number | Expression)` | 数字 | 计算指定数的反余弦值。 |
| `asin(number | Expression)` | 数字 | 计算指定数的反正弦值。 |
| `atan(number | Expression)` | 数字 | 计算指定数的反正切值。 |
| `ceil(number | Expression)` | 数字 | 将数字向上舍入为下一个整数。 |
| `cos(number | Expression)` | 数字 | 计算指定数的余弦值。 |
| `division(number, number)` \| `division(Expression, Expression)` | 数字 | 将第一个数除以第二个数。 Web SDK 等效表达式：`/` |
| `e()` | 数字 | 返回数学常数 `e`。 |
| `floor(number | Expression)` | 数字 | 将数字向下舍入为上一个整数。 |
| `log10(number | Expression)` | 数字 | 计算指定数的以 10 为底的对数。 |
| `log2(number | Expression)` | 数字 | 计算指定数的以 2 为底的对数。 |
| `ln(number | Expression)` | 数字 | 计算指定数的自然对数。 |
| `ln2()` | 数字 | 返回数学常数 `ln(2)`。 |
| `max(numbers... | expressions...)` | 数字 | 计算指定一组数中的最大数。 |
| `min(numbers... | expressions...)` | 数字 | 计算指定一组数中的最小数。 |
| `mod(number, number)` \| `mod(Expression, Expression)` | 数字 | 计算将第一个数除以第二个数后得到的余数。 Web SDK 等效表达式：`%` |
| `pi()` | 数字 | 返回数学常数 `PI`。 |
| `pow(number, number)` \| `pow(Expression, Expression)` | 数字 | 计算第一个值经过第二个数的幂乘后的值。 |
| `product(numbers... | expressions...)` | 数字 | 将指定的数相乘。 Web SDK 等效表达式：`*` |
| `round(number | Expression)` | 数字 | 将数字舍入为最接近的整数。 中点值采用远离零舍入模式。 例如，`round(-1.5)` 计算为 `-2`。 |
| `sin(number | Expression)` | 数字 | 计算指定数的正弦值。 |
| `sqrt(number | Expression)` | 数字 | 计算指定数字的平方根。 |
| `subtract(number | Expression` | 数字 | 0 减指定数。 |
| `subtract(number | Expression, number | Expression)` | 数字 | 将第一个数减第二个数。 |
| `sum(numbers... | expressions...)` | 数字 | 计算指定数之和。 |
| `tan(number | Expression)` | 数字 | 计算指定数的正切值。 |

## <a name="boolean-expressions"></a>布尔表达式

布尔表达式提供一组布尔运算符表达式用于评估布尔比较结果。

比较值时，将严格区分类型。 不同类型的值始终被视为不相等。 在分析时类型已知不同的情况被视为无效，将生成分析错误。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `all(Expression...)` | boolean | 如果所有输入为 `true`，则返回 `true`，否则返回 `false`。 |
| `any(Expression...)` | boolean | 如果任一输入为 `true`，则返回 `true`，否则返回 `false`。 |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | 如果输入值相等，则返回 `true`，否则返回 `false`。 两个参数必须均为字符串，或者均为数字。 |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | 如果第一个输入严格大于第二个输入，则返回 `true`，否则返回 `false`。 两个参数必须均为字符串，或者均为数字。 |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | 如果第一个输入大于或等于第二个输入，则返回 `true`，否则返回 `false`。 两个参数必须均为字符串，或者均为数字。 |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | 如果第一个输入严格小于第二个输入，则返回 `true`，否则返回 `false`。 两个参数必须均为字符串，或者均为数字。 |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | 如果第一个输入小于或等于第二个输入，则返回 `true`，否则返回 `false`。 两个参数必须均为字符串，或者均为数字。 |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | 如果输入值不相等，则返回 `true`，否则返回 `false`。 |
| `not(Expression | boolean)` | boolean | 逻辑非。 如果输入为 `true`，则返回 `false`；如果输入为 `false`，则返回 `true`。 |

## <a name="conditional-expressions"></a>条件表达式

条件表达式提供类似于 if 语句的逻辑运算。

以下表达式对输入数据执行条件逻辑运算。 例如，`switchCase` 表达式提供“if/then/else”逻辑，而 `match` 表达式则类似于“switch 语句”。

### <a name="switch-case-expression"></a>Switch case 表达式

`switchCase` 表达式是一种提供“if/then/else”逻辑的条件表达式。 这种类型的表达式会逐步评估一系列布尔条件。 计算为 true 时，它会返回第一个布尔条件的输出值。

以下伪代码定义 `switchCase` 表达式的结构。

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

以下示例逐步评估不同的布尔条件，直到找到评估为 `true` 的条件，然后返回该关联值。 如果没有任何布尔条件评估为 `true`，则返回回退值。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
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
)
```

::: zone-end

### <a name="match-expression"></a>match 表达式

`match` 表达式是一种提供类似 switch 语句逻辑的条件表达式。 输入可以是返回字符串或数字的任意表达式，例如 `get( "entityType")`。 每个 stop（输出值）必须有一个标签，该标签采用单个文本值或文本值的数组，该数组中的值必须全部为字符串，或者全部为数字。 如果数组中的任一值匹配，则输入匹配。 每个 stop 标签必须唯一。 如果输入类型与标签类型不匹配，则结果将是默认的回退值。

以下伪代码定义 `match` 表达式的结构。

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**示例**

以下示例查找气泡层中某个点特征的 `entityType` 属性以搜索匹配项。 如果找到匹配项，则返回该指定值，否则返回回退值。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
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
)
```

::: zone-end

以下示例使用数组列出一组应全部返回相同值的标签。 此方法比单独列出每个标签要高效得多。 在本例中，如果 `entityType` 属性为“restaurant”或“grocery_store”，则返回颜色“red”。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(arrayOf("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

### <a name="coalesce-expression"></a>coalesce 表达式

`coalesce` 表达式逐步评估一组表达式，直到获取第一个非 null 值，然后返回该值。

以下伪代码定义 `coalesce` 表达式的结构。

```java
coalesce(Expression... input)
```

**示例**

以下示例使用 `coalesce` 表达式设置符号层的 `textField` 选项。 如果 `title` 属性在特征中缺失或设置为 `null`，则该表达式将尝试查找 `subTitle` 属性；如果该属性缺失或为 `null`，则将回退为空字符串。

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
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
)
```

::: zone-end

## <a name="type-expressions"></a>类型表达式

类型表达式提供用于测试和转换不同数据类型（例如字符串、数字和布尔值）的工具。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `array(Expression)` | Object[] | 断言输入为数组。 |
| `bool(Expression)` | boolean | 断言输入值为布尔值。 |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | 排序器 | 返回一个可在区域设置相关比较运算中使用的排序器。 区分大小写和音调符号敏感选项默认为 false。 locale 参数指定要使用的区域设置的 IETF 语言标记。 如果未提供任何值，则使用默认区域设置。 如果请求的区域设置不可用，排序器将使用系统定义的回退区域设置。 使用已解析的区域设置测试区域设置回退行为的结果。  |
| `literal(boolean \| number \| string \| Object \| Object[])` | 布尔 \| 数字 \| 字符串 \| 对象 \| Object[] | 返回文本数组或对象值。 使用此表达式可防止将数组或对象作为表达式进行评估。 当表达式需要返回数组或对象时，有必要这样做。 |
| `number(Expression)` | 数字 | 断言输入值为数字。 |
| `object(Expression)` | 对象 | 断言输入值为对象。 |
| `string(Expression)` | 字符串 | 断言输入值为字符串。 |
| `toArray(Expression)` | Object[] | 将表达式转换为 JSON 对象数组。 |
| `toBool(Expression)` | boolean | 将输入值转换为布尔值。 |
| `toNumber(Expression)` | 数字 | 将输入值转换为数字（如果可能）。 |
| `toString(Expression)` | 字符串 | 将输入值转换为字符串。 |
| `typeoOf(Expression)` | 字符串 | 返回一个字符串用于描述给定值的类型。 |

## <a name="color-expressions"></a>颜色表达式

使用颜色表达式可以更轻松地创建和处理颜色值。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `color(int)` | color | 将颜色整数值转换为颜色表达式。 |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | color | 基于 red、green 和 blue 分量（值必须为 `0` 至 `255`）和一个值为 `1` 的 alpha 分量创建颜色值。 如果任一分量超出范围，则表达式会生成错误。 |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | color | 基于 red、green 和 blue 分量（值必须为 `0` 至 `255`）和一个值为 `0` 到 `1` 的 alpha 分量创建颜色值。 如果任一分量超出范围，则表达式会生成错误。 |
| `toColor(Expression)` | color  | 将输入值转换为颜色。 |
| `toRgba(Expression)` | color | 返回一个四元素数组，其中包含输入颜色的红色、绿色、蓝色和 alpha 分量（按该顺序排列）。 |

**示例**

以下示例创建一个 RGB 颜色值，其中的 red 值为 `255`，green 和 blue 值是通过将 `2.5` 与 `temperature` 属性值相乘计算得出的。 随着温度的变化，颜色将变为红色的不同阴影。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
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
)
```

::: zone-end

如果所有颜色参数均为数字，则无需使用 `literal` 表达式包装这些参数。 例如：

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
)
```

::: zone-end

> [!TIP]
> 可以使用 `android.graphics.Color.parseColor` 方法将字符串颜色值转换为颜色。 以下示例将十六进制颜色字符串转换为可在层中使用的颜色表达式。
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>字符串运算符表达式

字符串运算符表达式针对字符串执行转换操作，例如连接字符串和转换大小写。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | 字符串 | 将多个字符串连接到一起。 每个值必须是字符串。 如果需要，请使用 `toString` 类型表达式将其他值类型转换为字符串。 |
| `downcase(string)` \| `downcase(Expression)` | 字符串 | 将指定的字符串转换为小写。 |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| boolean | 确定输入字符串是否使用当前字体堆栈支持的字符集。 例如：`isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | 字符串 | 返回所提供排序器使用的区域设置的 IETF 语言标记。 此标记可用于确定默认的系统区域设置，或确定是否已成功加载请求的区域设置。 |
| `upcase(string)` \| `upcase(Expression)` | 字符串 | 将指定的字符串转换为大写。 |

**示例**

以下示例将点特征的 `temperature` 属性转换为字符串，然后将“°F”连接到该字符串的末尾。

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
)
```

::: zone-end

以上表达式在地图上呈现一个图钉，该图钉上叠加了文本“64°F”，如下图所示。

![字符串运算符表达式示例](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions&quot;></a>Interpolate 和 Step 表达式

Interpolate 和 Step 表达式可用于计算内插曲线或 step 函数中的值。 这些表达式采用一个返回数字值作为输入的表达式（例如 `get(&quot;temperature")`）。 将根据输入和输出值对评估输入值，以确定最拟合内插曲线或 step 函数的值。 输出值称为“stop”。 每个 stop 的输入值必须是数字并按升序排列。 输出值必须是数字、数字数组或颜色。

### <a name="interpolate-expression"></a>interpolate 表达式

使用 `interpolate` 表达式可以通过在 stop 值之间进行内插来计算连续的平滑值集。 返回颜色值的 `interpolate` 表达式生成颜色梯度，结果值是从中选择的。 `interpolate` 表达式采用以下格式：

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

可在 `interpolate` 表达式中使用三种类型的内插方法：

| 名称 | 描述 |
|------|-------------|
| `linear()` | 在 stop 对之间线性内插。  |
| `exponential(number)` \| `exponential(Expression)` | 在 stop 之间指数内插。 将指定一个“底数”，用于控制输出的递增速度。 值越大，输出就会以越快的速度朝范围的上限递增。 接近 1 的“底数”值会生成以更线性方式递增的输出。|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | 使用按给定控制点定义的[三次方贝塞尔曲线](https://developer.mozilla.org/docs/Web/CSS/timing-function)进行内插。 |

`stop` 表达式采用 `stop(stop, value)` 格式。

以下示例演示了这些不同类型的内插的大致形式。

| 线性  | 指数 | 三次方贝塞尔 |
|---------|-------------|--------------|
| ![线性内插图](media/how-to-expressions/linear-interpolation.png) | ![指数内插图](media/how-to-expressions/exponential-interpolation.png) | ![三次方贝塞尔曲线内插图](media/how-to-expressions/bezier-curve-interpolation.png) |

**示例**

以下示例使用 `linear interpolate` 表达式基于点特征的 `temperature` 属性设置气泡层的 `bubbleColor` 属性。 如果 `temperature` 值小于 60，则返回“blue”。 如果该值大于等于 60 且小于 70，则返回“yellow”。 如果该值大于等于 70 且小于 80，则返回“orange”(`#FFA500`)。 如果该值大于等于 80，则返回“red”。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
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
)
```

::: zone-end

下图演示了如何为以上表达式选择颜色。

![Interpolate 表达式示例](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Step 表达式

可以使用 `step` 表达式通过评估按 stop 定义的[分段常数函数](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)来计算离散的阶跃结果值。

`interpolate` 表达式采用以下格式：

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

Step 表达式返回紧靠在输入值前面的 stop 的输出值；如果输入小于第一个 stop，则返回第一个输入值。

**示例**

以下示例使用 `step` 表达式基于点特征的 `temperature` 属性设置气泡层的 `bubbleColor` 属性。 如果 `temperature` 值小于 60，则返回“blue”。 如果该值大于等于 60 且小于 70，则返回“yellow”。 如果该值大于等于 70 且小于 80，则返回“orange”。 如果该值大于等于 80，则返回“red”。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

下图演示了如何为以上表达式选择颜色。

![Step 表达式示例](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>层特定的表达式

仅适用于特定层的特殊表达式。

### <a name="heat-map-density-expression"></a>热度地图密度表达式

热度地图密度表达式检索热度地图层中每个像素的热度地图密度值，定义为 `heatmapDensity`。 此值是介于 `0` 和 `1` 之间的数字。 将此表达式与 `interpolation` 或 `step` 表达式相结合可以定义颜色梯度，用于在热度地图中赋色。 只能在热度地图层的 `heatmapColor` 选项中使用此表达式。

> [!TIP]
> 内插表达式中索引 0 处的颜色或递阶色的默认颜色定义没有数据的区域的颜色。 索引 0 处的颜色可用于定义背景色。 很多用户喜欢将此值设置为透明或半透明黑色。

**示例**

此示例使用线性内插表达式创建平滑颜色梯度来呈现热度地图。

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
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
)
```

::: zone-end

除了使用平滑梯度为热度地图赋色外，还可以使用 `step` 表达式在一组范围内指定颜色。 使用 `step` 表达式为热度地图赋色可以直观地将密度分解为类似于等高线或雷达式地图的范围。

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
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
)
```

::: zone-end

有关详细信息，请参阅[添加热度地图层](map-add-heat-map-layer-android.md)文档。

### <a name="line-progress-expression"></a>折线进度表达式

折线进度表达式检索线条层中沿梯度线走向的进度，定义为 `lineProgress()`。 此值是介于 0 和 1 之间的数字。 此表达式与 `interpolation` 或 `step` 表达式结合使用。 只能结合线条层的 `strokeGradient` 选项使用此表达式。

> [!NOTE]
> 线条层的 `strokeGradient` 选项要求将数据源的 `lineMetrics` 选项设置为 `true`。

**示例**

此示例使用 `lineProgress()` 表达式对线条笔划应用颜色梯度。

::: zone pivot="programming-language-java-android"

```java
LineLayer layer = new LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

[查看实时示例](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>文本字段格式表达式

可以结合符号层的 `textField` 选项使用 `format` 表达式，以提供混合文本格式。 此表达式采用一个或多个 `formatEntry` 表达式（这些表达式指定要追加到文本字段的一个字符串和一组 `formatOptions`）。

| 表达式 | 说明 |
|------------|-------------|
| `format(Expression...)` | 返回带格式的文本，其中包含要在混合格式文本字段条目中使用的注释。 |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | 返回要在 `format` 表达式中使用的带格式字符串条目。 |

下面是可用的格式选项：

| 表达式 | 说明 |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | 指定字体大小的缩放系数。 如果已指定，此值将替代单个字符串的 `textSize` 属性。 |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | 指定呈现文本时要应用于文本的颜色。 |

**示例**

以下示例通过添加粗体字体并放大特征的 `title` 属性字体大小，来设置文本字段的格式。 此示例还在新行中添加了特征的 `subTitle` 属性以及缩小的字体大小。

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(arrayOf("StandardFont-Bold")),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
)
```

::: zone-end

此层将如下图所示呈现点特征：

![具有带格式文本字段的点特征的插图](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>Zoom 表达式

`zoom` 表达式用于在呈现时检索地图的当前缩放级别，定义为 `zoom()`。 此表达式返回地图最小和最大缩放级别范围内的数字。 适用于 Web 和 Android 的 Azure Maps 交互式地图控件支持 25 个缩放级别，其编号为 0 到 24。 使用 `zoom` 表达式可以在地图缩放级别发生更改时动态修改样式。 `zoom` 表达式只能与 `interpolate` 和 `step` 表达式配合使用。

**示例**

默认情况下，热度地图层中呈现的数据点半径对所有缩放级别采用固定像素的半径。 缩放地图时，数据将会聚合，热度地图层将呈现不同的外观。 可以使用 `zoom` 表达式缩放每个缩放级别的半径，使每个数据点涵盖相同的地图物理区域。 这会使热度地图层看上去更加静态且一致。 地图的每个缩放级别的垂直和水平像素是前一个缩放级别的两倍。 调整半径使其在每个缩放级别中翻倍，会创建一个在所有缩放级别下均保持一致外观的热度地图。 若要实现此目的，可将 `zoom` 表达式与 `base 2 exponential interpolation` 表达式配合使用，并为最小缩放级别设置像素半径，为最大缩放级别设置标度半径（计算方式为 `2 * Math.pow(2, minZoom - maxZoom)`，如下所示）。

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
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
)
```

::: zone-end

## <a name="variable-binding-expressions"></a>变量绑定表达式

变量绑定表达式将计算结果存储在变量中。 因此，可以在表达式中的其他位置多次引用计算结果。 这对于涉及许多计算的表达式而言是一种有效的优化。

| Expression | 返回类型 | 说明 |
|--------------|---------------|--------------|
| `let(Expression... input)` | | 将一个或多个值存储为变量，供 `var` 表达式在返回结果的子表达式中使用。 |
| `var(Expression expression)` \| `var(string variableName)` | 对象 | 引用使用 `let` 表达式创建的变量。 |

**示例**

此示例使用一个表达式计算相对于温度比的收入，然后使用 `case` 表达式评估针对此值执行的不同布尔运算。 `let` 表达式用于存储相对于温度比的收入，以便只需计算此值一次。 `var` 表达式根据所需的频率引用此变量，而不必重新计算此变量。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
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
)
```

::: zone-end

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
