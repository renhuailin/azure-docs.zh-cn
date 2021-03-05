---
title: Azure 事件网格事件筛选
description: 介绍如何在创建 Azure 事件网格订阅时筛选事件。
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 94445341891149d5d02c7f33caef20bf45123e9b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197769"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>了解事件网格订阅的事件筛选

本本介绍了筛选发送到终结点的事件的不同方法。 创建事件订阅时，有三个筛选选项：

* 事件类型
* 主题开头内容或结尾内容
* 高级字段和运算符

## <a name="event-type-filtering"></a>事件类型筛选

默认情况下，事件源的所有[事件类型](event-schema.md)都将发送到终结点。 可以决定仅将某些事件类型发送到终结点。 例如，可以收到有关资源更新的通知，但不会收到删除等其他操作的通知。 在这种情况下，按 `Microsoft.Resources.ResourceWriteSuccess` 事件类型进行筛选。 提供包含事件类型的数组或指定 `All` 以获取事件源的所有事件类型。

按事件类型筛选的 JSON 语法是：

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>主题筛选

对于按主题的简单筛选，请指定主题的开头值或结尾值。 例如，可以使用 `.txt` 指定主题的结尾，以仅获取与将文本文件上传到存储帐户相关的事件。 或者，可以筛选以 `/blobServices/default/containers/testcontainer` 开头的主题以获取该容器的所有事件，但不获取存储帐户中的其他容器。

将事件发布到自定义主题时，可为事件创建主题，便于订阅者们了解他们是否对该事件感兴趣。 订阅者使用主题属性来筛选和路由事件。 考虑添加事件发生位置的路径，以便订阅者可以按该路径的段进行筛选。 通过路径，订阅者可精确或宽泛地筛选事件。 如果在主题中提供一个由三个段构成的路径（如 `/A/B/C`），订阅者可根据第一个段 `/A` 进行筛选，获取范围较宽泛的一组事件。 这些订阅者会获取主题为 `/A/B/C` 或 `/A/D/E` 这样的事件。 其他订阅者可通过 `/A/B` 进行筛选，这样可以获取范围更精确的一组事件。

按主题筛选的 JSON 语法是：

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>高级筛选

要按数据字段中的值进行筛选并指定比较运算符，请使用高级筛选选项。 在高级筛选中，指定：

* 运算符类型 - 比较的类型。
* 键 - 用于筛选的事件数据中的字段。 它可以是数字、布尔值、字符串或数组。
* 值 - 要与键进行比较的值。

## <a name="key"></a>键
Key 是用于筛选的事件数据中的字段。 它可以是以下类型之一：

- Number
- 布尔
- 字符串
- 数组。 需要将 `enableAdvancedFilteringOnArrays` 属性设置为 true 才能使用此功能。 目前，Azure 门户不支持启用此功能。 

    ```json
    "filter":
    {
        "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
        "subjectEndsWith": ".jpg",
        "enableAdvancedFilteringOnArrays": true
    }
    ```

对于 **事件网格架构** 中的事件，请将以下值用于键： `ID` 、 `Topic` 、、 `Subject` 、 `EventType` `DataVersion` 或事件数据 (如 `data.key1`) 。

对于 **云事件架构** 中的事件，请将以下值用于键： `eventid` 、 `source` 、 `eventtype` 、 `eventtypeversion` 或事件数据 (如 `data.key1`) 。

对于 **自定义输入架构**，请使用 "事件数据" 字段 (如 `data.key1`) 。 若要访问 data 节中的字段，请使用 `.` (点) 表示法。 例如， `data.sitename` `data.appEventTypeDetail.action` 若要访问 `sitename` 或 `action` 以下示例事件，则为。

```json
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
```

## <a name="values"></a>值
值可以是： number、string、boolean 或 array

## <a name="operators"></a>运算符

可用的数字运算符为：

## <a name="numberin"></a>NumberIn
如果 **键值** 为指定的 **筛选器** 值之一，则数值运算符的计算结果为 true。 在下面的示例中，它将检查 `counter` 部分中属性的值是否 `data` 为5或1。 

```json
"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]
```


如果该键为数组，则会对照筛选器值的数组检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 和筛选器： `[a, b, c]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="numbernotin"></a>NumberNotIn
如果 **键值****不** 是任何指定的 **筛选器** 值，NumberNotIn 的计算结果为 true。 在下面的示例中，它将检查 `counter` 节中特性的值是否 `data` 不是41和0。 

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0
    ]
}]
```

如果该键为数组，则会对照筛选器值的数组检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 和筛选器： `[a, b, c]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```

## <a name="numberlessthan"></a>NumberLessThan
如果 **键值****小于** 指定的 **筛选器** 值，则 NumberLessThan 运算符的计算结果为 true。 在下面的示例中，它将检查 `counter` 部分中属性的值是否 `data` 小于100。 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

如果该键为数组，则会对照筛选器值检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH key IN (v1, v2, v3)
    IF key < filter
        MATCH
```

## <a name="numbergreaterthan"></a>NumberGreaterThan
如果 **键值****大于** 指定的 **筛选器** 值，则 NumberGreaterThan 运算符的计算结果为 true。 在下面的示例中，它将检查 `counter` 节中特性的值是否 `data` 大于20。 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

如果该键为数组，则会对照筛选器值检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH key IN (v1, v2, v3)
    IF key > filter
        MATCH
```

## <a name="numberlessthanorequals"></a>NumberLessThanOrEquals
如果 **键值****小于或等于** 指定的 **筛选器** 值，则 NumberLessThanOrEquals 运算符的计算结果为 true。 在下面的示例中，它将检查 `counter` 部分中属性的值是否 `data` 小于或等于100。 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

如果该键为数组，则会对照筛选器值检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH key IN (v1, v2, v3)
    IF key <= filter
        MATCH
```

## <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals
如果 **键值****大于或等于** 指定的 **筛选器** 值，则 NumberGreaterThanOrEquals 运算符的计算结果为 true。 在下面的示例中，它将检查 `counter` 部分中特性的值是否 `data` 大于或等于30。 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

如果该键为数组，则会对照筛选器值检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH key IN (v1, v2, v3)
    IF key >= filter
        MATCH
```

## <a name="numberinrange"></a>NumberInRange
如果 **键值** 在指定的 **筛选范围** 之一内，则 NumberInRange 运算符的计算结果为 true。 在下面的示例中，它将检查部分中属性的值是否处于以下 `key1` `data` 两个范围之一： 3.14159-999.95、3000-4000。 

```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```

`values`属性是范围的数组。 在上面的示例中，它是两个范围的数组。 下面是一个具有要检查的范围的数组的示例。 

**具有一个范围的数组：** 
```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

如果该键为数组，则会对照筛选器值的数组检查数组中的所有值。 下面是包含键的伪代码： `[v1, v2, v3]` 和筛选器：范围的数组。 在此伪代码中， `a` 和 `b` 为数组中每个范围的最小值和最大值。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
       IF key >= a AND key <= b
           MATCH
```


## <a name="numbernotinrange"></a>NumberNotInRange
如果 **键值****不** 在任何指定的 **筛选范围** 内，则 NumberNotInRange 运算符的计算结果为 true。 在下面的示例中，它将检查部分中属性的值是否处于以下 `key1` `data` 两个范围之一： 3.14159-999.95、3000-4000。 如果是，则运算符返回 false。 

```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```
`values`属性是范围的数组。 在上面的示例中，它是两个范围的数组。 下面是一个具有要检查的范围的数组的示例。

**具有一个范围的数组：** 
```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

如果该键为数组，则会对照筛选器值的数组检查数组中的所有值。 下面是包含键的伪代码： `[v1, v2, v3]` 和筛选器：范围的数组。 在此伪代码中， `a` 和 `b` 为数组中每个范围的最小值和最大值。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
        IF key >= a AND key <= b
            FAIL_MATCH
```


可用的布尔值运算符为： 

## <a name="boolequals"></a>BoolEquals
如果 **键值** 为指定的布尔值 **筛选器**，则 BoolEquals 运算符的计算结果为 true。 在下面的示例中，它将检查 `isEnabled` 部分中属性的值是否 `data` 为 `true` 。 

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```

如果该键为数组，则会对照筛选器布尔值检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH key IN (v1, v2, v3)
    IF filter == key
        MATCH
```

可用的字符串运算符为：

## <a name="stringcontains"></a>StringContains
如果 **键值****包含** (为子字符串) 的任何指定 **筛选器** 值，则 **StringContains** 的计算结果为 true。 在下面的示例中，它将检查 `key1` 节中特性的值是否 `data` 包含指定的子字符串之一： `microsoft` 或 `azure` 。 例如， `azure data factory` 有一个 `azure` 。 

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

如果该键为数组，则会对照筛选器值的数组检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 和筛选器： `[a,b,c]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            MATCH
```

## <a name="stringnotcontains"></a>StringNotContains
如果 **键** 不 **包含** 指定的 **筛选器** 值作为子字符串，则 **StringNotContains** 运算符的计算结果为 true。 如果键包含一个指定值作为子字符串，则运算符的计算结果为 false。 在下面的示例中，仅当 `key1` 部分中的属性的值 `data` 不包含 `contoso` 和 `fabrikam` 作为子字符串时，运算符才返回 true。 

```json
"advancedFilters": [{
    "operatorType": "StringNotContains",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam"
    ]
}]
```

如果该键为数组，则会对照筛选器值的数组检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 和筛选器： `[a,b,c]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            FAIL_MATCH
```

## <a name="stringbeginswith"></a>StringBeginsWith
如果 **键值** 以任何指定的 **筛选器** 值 **开头**，则 **StringBeginsWith** 运算符的计算结果为 true。 在下面的示例中，它将检查 `key1` 节中特性的值是否 `data` 以或开头 `event` `grid` 。 例如，以 `event hubs` 开头 `event` 。  

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

如果该键为数组，则会对照筛选器值的数组检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 和筛选器： `[a,b,c]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            MATCH
```

## <a name="stringnotbeginswith"></a>StringNotBeginsWith
如果 **键值** 不是以任何指定的 **筛选器** 值 **开头**，则 **StringNotBeginsWith** 运算符的计算结果为 true。 在下面的示例中，它将检查 `key1` 节中特性的值是否 `data` 不以或开头 `event` `message` 。

```json
"advancedFilters": [{
    "operatorType": "StringNotBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

如果该键为数组，则会对照筛选器值的数组检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 和筛选器： `[a,b,c]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            FAIL_MATCH
```

## <a name="stringendswith"></a>StringEndsWith
如果 **键值** 以指定的 **筛选器** 值之一 **结束**，则 **StringEndsWith** 运算符的计算结果为 true。 在下面的示例中，它将检查节中特性的值是否以 `key1` `data` `jpg` 或或结尾 `jpeg` `png` 。 例如，以 `eventgrid.png` 结尾 `png` 。


```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

如果该键为数组，则会对照筛选器值的数组检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 和筛选器： `[a,b,c]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            MATCH
```

## <a name="stringnotendswith"></a>StringNotEndsWith
如果 **键值** 不是以任何指定的 **筛选器** 值 **结束**，则 **StringNotEndsWith** 运算符的计算结果为 true。 在下面的示例中，它将检查 `key1` 节中特性的值是否 `data` 不是以 `jpg` 或 `jpeg` 或结尾 `png` 。 


```json
"advancedFilters": [{
    "operatorType": "StringNotEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

如果该键为数组，则会对照筛选器值的数组检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 和筛选器： `[a,b,c]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            FAIL_MATCH
```

## <a name="stringin"></a>StringIn
**字符串** 运算符检查 **密钥** 值是否与指定的 **筛选器** 值之一 **完全匹配**。 在下面的示例中，它将检查 `key1` 部分中特性的值是否 `data` 为 `exact` 或 `string` 或 `matches` 。 

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam", 
        "factory"
    ]
}]
```

如果该键为数组，则会对照筛选器值的数组检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 和筛选器： `[a,b,c]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="stringnotin"></a>StringNotIn
**StringNotIn** 运算符检查 **键值** 是否与任何指定的 **筛选器** 值 **不匹配**。 在下面的示例中，它将检查 `key1` 节中特性的值是否 `data` 不为 `aws` 和 `bridge` 。 

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

如果该键为数组，则会对照筛选器值的数组检查数组中的所有值。 下面是带有密钥的伪代码： `[v1, v2, v3]` 和筛选器： `[a,b,c]` 。 将忽略任何数据类型与筛选器的数据类型都不匹配的键值。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```


所有字符串比较不区分大小写。

> [!NOTE]
> 如果事件 JSON 不包含高级筛选键，则对于以下运算符，筛选器将 evaulated 为 **不匹配** ： NumberGreaterThan、NumberGreaterThanOrEquals、NumberLessThan、NumberLessThanOrEquals、数值、BoolEquals、StringContains、StringNotContains、StringBeginsWith、StringNotBeginsWith、StringEndsWith、StringNotEndsWith、字符串。
> 
>对于以下运算符，筛选器与 evaulated **匹配** ： NumberNotIn、StringNotIn。


## <a name="isnullorundefined"></a>IsNullOrUndefined
如果键的值为 NULL 或未定义，则 IsNullOrUndefined 运算符的计算结果为 true。 

```json
{
    "operatorType": "IsNullOrUndefined",
    "key": "data.key1"
}
```

在下面的示例中，缺少 key1，因此运算符的计算结果为 true。 

```json
{ 
    "data": 
    { 
        "key2": 5 
    } 
}
```

在下面的示例中，key1 设置为 null，因此运算符的计算结果为 true。

```json
{
    "data": 
    { 
        "key1": null
    }
}
```

如果 key1 在这些示例中具有其他任何值，则运算符的计算结果将为 false。 

## <a name="isnotnull"></a>IsNotNull
如果键的值不为 NULL 或未定义，则 IsNotNull 运算符的计算结果为 true。 

```json
{
    "operatorType": "IsNotNull",
    "key": "data.key1"
}
```

## <a name="or-and-and"></a>或和
如果指定具有多个值的单个筛选器，则将执行 **OR** 操作，因此键字段的值必须是这些值之一。 以下是示例：

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

如果指定多个不同的筛选器，则将执行 **AND** 操作，因此必须满足每个筛选器条件。 下面是一个示例： 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

## <a name="cloudevents"></a>CloudEvents 
对于 **CloudEvents 架构** 中的事件，请将以下值用于键： `eventid` 、 `source` 、 `eventtype` 、 `eventtypeversion` 或事件数据 (如 `data.key1`) 。 

你还可以 [在 CloudEvents 1.0 中使用扩展上下文属性](https://github.com/cloudevents/spec/blob/v1.0.1/spec.md#extension-context-attributes)。 在下面的示例中， `comexampleextension1` 和 `comexampleothervalue` 是扩展上下文特性。 

```json
{
    "specversion" : "1.0",
    "type" : "com.example.someevent",
    "source" : "/mycontext",
    "id" : "C234-1234-1234",
    "time" : "2018-04-05T17:31:00Z",
    "subject": null,
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
        "appinfoA" : "abc",
        "appinfoB" : 123,
        "appinfoC" : true
    }
}
```

下面是在筛选器中使用扩展上下文属性的示例。

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "comexampleothervalue",
    "values": [
        "5", 
        "1"
    ]
}]
```


## <a name="limitations"></a>限制

高级筛选具有以下限制：

* 每个事件网格订阅的所有筛选器都有 5 个高级筛选器和 25 个筛选器值
* 每个字符串值有 512 个字符
* “in”和“not in”运算符有 5 个值 
* 具有 `.`（点）字符的键。 例如： `http://schemas.microsoft.com/claims/authnclassreference` 或 `john.doe@contoso.com` 。 目前不支持键中使用转义字符。 

可以在多个筛选器中使用相同的键。





## <a name="next-steps"></a>后续步骤

* 要了解有关使用 PowerShell 和 Azure CLI 筛选事件的详细信息，请参阅[筛选事件网格的事件](how-to-filter-events.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
