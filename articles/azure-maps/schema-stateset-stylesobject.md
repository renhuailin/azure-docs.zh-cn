---
title: 动态 Azure Maps StylesObject 架构参考指南
description: 动态 Azure Maps StylesObject 架构和语法参考指南。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 0a4095cd5f15bae3089b597ef6773c3a96e48f45
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747757"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>动态 Maps StylesObject 架构参考指南

 `StylesObject`是表示状态集样式的`StyleObject`数组。 使用 Azure Maps Creator [功能状态服务](/rest/api/maps/v2/feature-state)将状态集样式应用到室内地图数据功能。 创建状态集样式，并将其与室内地图功能关联后，可以使用它们来创建动态室内地图。 有关创建动态室内地图的详细信息，请参阅[为 Creator 室内地图实现动态样式](indoor-map-dynamic-styling.md)。

## <a name="styleobject"></a>StyleObject

`StyleObject`为以下样式规则之一：

 * [`BooleanTypeStyleRule`](#booleantypestylerule)
 * [`NumericTypeStyleRule`](#numerictypestylerule)
 * [`StringTypeStyleRule`](#stringtypestylerule)

下列 JSON 演示了这三种样式类型的用法示例。  `BooleanTypeStyleRule`用于确定`occupied`属性为 "true" 或 "false" 的功能的动态样式。  `NumericTypeStyleRule`用于确定`temperature`属性在特定范围内的功能的样式。 最后，`StringTypeStyleRule`用于匹配特定样式与`meetingType`。



```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    },
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 `NumericTypeStyleRule`为[`StyleObject`](#styleobject) 且包括下列属性：

| 属性 | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `keyName` | string | “状态”或动态属性名称。 `keyName` 在 `StyleObject` 数组中具有特殊性。| 是 |
| `type` | 字符串 | 值为“数值”。 | 是 |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| 具有关联颜色的数字样式范围数组。 每个范围定义“状态”值在此范围内时使用的颜色。| 是 |

### <a name="numberruleobject"></a>NumberRuleObject

`NumberRuleObject`包含[`RangeObject`](#rangeobject)和`color`属性。 如果“状态”值在此范围内，则其显示颜色为`color`属性中指定的颜色。

如果定义多个重叠范围，所选颜色则是首个满足的范围定义的颜色。

在以下 JSON 示例中，当“状态”值介于 50-60 之间，两个范围均为 true。 但是作为列表中首个满足的范围，要使用的颜色是 `#343deb`。

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| 属性 | 类型 | 说明 | 必需 |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | [RangeObject](#rangeobject)定义一组逻辑范围条件，如果为`true`，则将“状态”显示颜色更改为`color`属性中指定的颜色。 如果未指定`range`，统一使用`color`属性中定义的颜色。   | 否 |
| `color` | 字符串 | 状态值满足一定范围时使用的颜色。 `color` 属性为采用以下任意格式的 JSON 字符串： <ul><li> HTML-样式的十六进制值 </li><li> RGB ("#ff0", "#ffff00", "rgb(255, 255, 0)")</li><li> RGBA ("rgba(255, 255, 0, 1)")</li><li> HSL("hsl(100, 50%, 50%)")</li><li> HSLA("hsla(100, 50%, 50%, 1)")</li><li> 预定义的 HTML 颜色名称，例如黄色和蓝色。</li></ul> | 是 |

### <a name="rangeobject"></a>RangeObject

`RangeObject`定义[`NumberRuleObject`](#numberruleobject) 的数值范围值。 若“状态”值满足此范围，所有定义条件必须为 "true"。

| 属性 | 类型 | 说明 | 必需 |
|-----------|----------|-------------|-------------|
| `minimum` | Double | 所有 x ≥`minimum`的数字 x。| 否 |
| `maximum` | Double | 所有 x ≤`maximum`的数字 x。 | 否 |
| `exclusiveMinimum` | Double | 所有 x >`exclusiveMinimum`的数字 x。| 否 |
| `exclusiveMaximum` | Double | 所有数字 x <`exclusiveMaximum`的数字 x。| 否 |

### <a name="example-of-numerictypestylerule"></a>NumericTypeStyleRule 示例

以下 JSON 显示名为 `temperature` 的 `NumericTypeStyleRule`“状态”。 此示例中的[`NumberRuleObject`](#numberruleobject)包含两个定义温度范围及其关联颜色样式。 若温度范围为 50-69，显示颜色为`#343deb`。  若温度范围为 31-70，显示颜色为`#eba834`。

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="stringtypestylerule"></a>StringTypeStyleRule

`StringTypeStyleRule`为[`StyleObject`](#styleobject)且包括下列属性：

| 属性 | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `keyName` | string |  “状态”或动态属性名称。  `keyName` 在 `StyleObject` 数组中具有特殊性。| 是 |
| `type` | 字符串 |值为“字符串”。 | 是 |
| `rules` | [`StringRuleObject`](#stringruleobject)[]| N 个“状态”值的数组。| 是 |

### <a name="stringruleobject"></a>StringRuleObject

`StringRuleObject` 包含最多 N 个状态值，这些状态值是功能属性的字符串可能值。 如果该功能的属性值与任何已定义的状态值都不匹配，则该功能将没有动态样式。 如果给定了重复的状态值，则优先使用第一个状态值。

字符串值匹配区分大小写。

| 属性 | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `stateValue1` | string | 值字符串为 stateValue1 时的颜色。 | 否 |
| `stateValue2` | 字符串 | 值字符串为 stateValue 时的颜色。 | 否 |
| `stateValueN` | 字符串 | 值字符串为 stateValueN 时的颜色。 | 否 |

### <a name="example-of-stringtypestylerule"></a>StringTypeStyleRule 示例

以下 JSON 显示定义特定会议类型关联样式的`StringTypeStyleRule`。

```json
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }

```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

`BooleanTypeStyleRule`为[`StyleObject`](#styleobject)且包括下列属性：

| 属性 | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `keyName` | string |  “状态”或动态属性名称。  `keyName` 在 `StyleObject` 数组中具有特殊性。| 是 |
| `type` | 字符串 |值为 "boolean"。 | 是 |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)[1]| `true` `false`“状态”值定义颜色的 boolean 对。| 是 |

### <a name="booleanruleobject"></a>BooleanRuleObject

`BooleanRuleObject` 定义 `true` 和 `false` 值的颜色。

| 属性 | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `true` | string | “状态”值为`true`时使用的颜色。 `color` 属性为采用以下任意格式的 JSON 字符串： <ul><li> HTML-样式的十六进制值 </li><li> RGB ("#ff0", "#ffff00", "rgb(255, 255, 0)")</li><li> RGBA ("rgba(255, 255, 0, 1)")</li><li> HSL("hsl(100, 50%, 50%)")</li><li> HSLA("hsla(100, 50%, 50%, 1)")</li><li> 预定义的 HTML 颜色名称，例如黄色和蓝色。</li></ul>| 是 |
| `false` | string | “状态”值为`false`时使用的颜色。 | 是 |

### <a name="example-of-booleantypestylerule"></a>BooleanTypeStyleRule 示例

以下 JSON 显示名为 `occupied` 的 `BooleanTypeStyleRule`“状态”。 [`BooleanRuleObject`](#booleanruleobject)定义`true`和`false`值的颜色。

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```