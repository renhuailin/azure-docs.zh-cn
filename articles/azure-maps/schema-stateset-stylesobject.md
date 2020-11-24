---
title: 动态 Azure Maps 的 StylesObject 架构参考指南
description: 动态 Azure Maps StylesObject 架构和语法的参考指南。
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/20/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f6bc4c62febf24dee790ac6136b1661426d4d619
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536942"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>动态映射的 StylesObject 架构参考指南

 `StylesObject`是 `StyleObject` 表示 stateset 样式的数组。 使用 Azure Maps 创建者 [功能状态服务](/rest/api/maps/featurestate) 将 stateset 样式应用到室内地图数据功能。 创建 stateset 样式，并将其与室内地图功能关联后，可以使用它们来创建动态室内地图。 有关创建动态室内地图的详细信息，请参阅 [实现 Creator 室内地图的动态样式](indoor-map-dynamic-styling.md)。

## <a name="styleobject"></a>StyleObject

`StyleObject`是以下样式规则之一：

 * [`BooleanTypeStyleRule`](#booleantypestylerule)
 * [`NumericTypeStyleRule`](#numerictypestylerule)
 * [`StringTypeStyleRule`](#stringtypestylerule)

下面的 JSON 显示了这三种样式类型的用法示例。  `BooleanTypeStyleRule`用于确定 `occupied` 属性为 true 和 false 的功能的动态样式。  `NumericTypeStyleRule`用于确定 `temperature` 属性在某个范围内的功能的样式。 最后， `StringTypeStyleRule` 用于匹配特定样式 `meetingType` 。



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

 `NumericTypeStyleRule`是一个 [`StyleObject`](#styleobject) 并且包含以下属性：

| 属性 | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `keyName` | string | *状态* 或动态属性名称。 在 `keyName` 数组中应是唯一的 `StyleObject` 。| 是 |
| `type` | string | 值为 "数值"。 | 是 |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| 具有关联颜色的数字样式范围的数组。 每个范围都定义一个颜色，当 *状态值* 满足此范围时，将使用该颜色。| 是 |

### <a name="numberruleobject"></a>NumberRuleObject

`NumberRuleObject`由 [`RangeObject`](#rangeobject) 和 `color` 属性组成。 如果 *状态* 值落在范围内，则其显示颜色将是在属性中指定的颜色 `color` 。

如果定义多个重叠范围，选择的颜色将是在满足的第一个范围中定义的颜色。

在下面的 JSON 示例中，当 *状态值* 介于50-60 之间时，两个范围都将为 true。 但是，将使用的颜色是 `#343deb` 因为它是列表中已满足的第一个范围。

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
| `range` | [RangeObject](#rangeobject) | [RangeObject](#rangeobject)定义一组逻辑范围条件，如果为 `true` ，则将 *状态* 的显示颜色更改为属性中指定的颜色 `color` 。 如果未 `range` 指定， `color` 将始终使用在属性中定义的颜色。   | 否 |
| `color` | string | 状态值落到范围中时要使用的颜色。 `color`属性是采用以下任意一种格式的 JSON 字符串： <ul><li> HTML 样式的十六进制值 </li><li> RGB ( "#ff0"、"#ffff00"、"rgb (255、255、0) " ) </li><li> RGBA ( "rgba (255、255、0、1) " ) </li><li> HSL ( "hsl (100、50%、50% ) " ) </li><li> HSLA ( "HSLA (100，50%，50%，1) " ) </li><li> 预定义的 HTML 颜色名称，如黄色和蓝色。</li></ul> | 是 |

### <a name="rangeobject"></a>RangeObject

`RangeObject`定义的数值范围值 [`NumberRuleObject`](#numberruleobject) 。 要使 *状态值* 进入范围，所有定义的条件都必须为 true。

| 属性 | 类型 | 说明 | 必需 |
|-----------|----------|-------------|-------------|
| `minimum` | Double | X ≥的所有数字 x `minimum` 。| 否 |
| `maximum` | Double | X ≤的所有数字 x `maximum` 。 | 否 |
| `exclusiveMinimum` | Double | X > 的所有数字 x `exclusiveMinimum` 。| 否 |
| `exclusiveMaximum` | Double | X < 的所有数字 x `exclusiveMaximum` 。| 否 |

### <a name="example-of-numerictypestylerule"></a>NumericTypeStyleRule 的示例

下面的 JSON 阐释了一个 `NumericTypeStyleRule` 名为的 *状态* `temperature` 。 在此示例中， [`NumberRuleObject`](#numberruleobject) 包含两个定义的温度范围及其关联的颜色样式。 如果温度范围为50-69，则显示应该使用颜色 `#343deb` 。  如果温度范围为31-70，则显示应该使用颜色 `#eba834` 。

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

`StringTypeStyleRule`是一个 [`StyleObject`](#styleobject) 并且包含以下属性：

| 属性 | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `keyName` | string |  *状态* 或动态属性名称。  在 `keyName` 数组中应是唯一的  `StyleObject` 。| 是 |
| `type` | string |值为 "string"。 | 是 |
| `rules` | [`StringRuleObject`](#stringruleobject)[]| N 个 *状态值* 的数组。| 是 |

### <a name="stringruleobject"></a>StringRuleObject

`StringRuleObject`包含最多 N 个状态值，这些状态值是功能的属性的可能字符串值。 如果该功能的属性值与任何已定义的状态值都不匹配，则该功能将没有动态样式。 如果给定了重复的状态值，则优先使用第一个状态值。

匹配的字符串值区分大小写。

| 属性 | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `stateValue1` | string | 值字符串为 stateValue1 时的颜色。 | 否 |
| `stateValue2` | string | 值字符串为 stateValue 时的颜色。 | 否 |
| `stateValueN` | string | 值字符串为 stateValueN 时的颜色。 | 否 |

### <a name="example-of-stringtypestylerule"></a>StringTypeStyleRule 的示例

下面的 JSON 阐释了一个 `StringTypeStyleRule` ，它定义与特定会议类型关联的样式。

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

`BooleanTypeStyleRule`是一个 [`StyleObject`](#styleobject) 并且包含以下属性：

| 属性 | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `keyName` | string |  *状态* 或动态属性名称。  在 `keyName` 数组中应是唯一的 `StyleObject`  。| 是 |
| `type` | string |值为 "boolean"。 | 是 |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)2| 具有和状态值的颜色的布尔对 `true` `false` *state* 。| 是 |

### <a name="booleanruleobject"></a>BooleanRuleObject

`BooleanRuleObject`定义 `true` 和值的颜色 `false` 。

| 属性 | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `true` | string | *状态* 值为时要使用的颜色 `true` 。 `color`属性是采用以下任意一种格式的 JSON 字符串： <ul><li> HTML 样式的十六进制值 </li><li> RGB ( "#ff0"、"#ffff00"、"rgb (255、255、0) " ) </li><li> RGBA ( "rgba (255、255、0、1) " ) </li><li> HSL ( "hsl (100、50%、50% ) " ) </li><li> HSLA ( "HSLA (100，50%，50%，1) " ) </li><li> 预定义的 HTML 颜色名称，如黄色和蓝色。</li></ul>| 是 |
| `false` | string | *状态* 值为时要使用的颜色 `false` 。 | 是 |

### <a name="example-of-booleantypestylerule"></a>BooleanTypeStyleRule 的示例

下面的 JSON 阐释了一个 `BooleanTypeStyleRule` 名为的 *状态* `occupied` 。 [`BooleanRuleObject`](#booleanruleobject)定义 `true` 和值的颜色 `false` 。

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