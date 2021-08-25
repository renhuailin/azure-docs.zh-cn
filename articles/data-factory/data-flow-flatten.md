---
title: 映射数据流中的平展转换
titleSuffix: Azure Data Factory & Azure Synapse
description: 利用平展转换使分层数据非规范化
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: e632260e8af6e4bac9fac9ec43f25bf636b98b4d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638357"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>映射数据流中的平展转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用平展转换获取层次结构（如 JSON）中的数组值，并将其展开到各个行。 此过程称为非规范化。

## <a name="configuration"></a>Configuration

平展转换包含以下配置设置

![平展设置](media/data-flow/flatten1.png "平展设置")

### <a name="unroll-by"></a>Unroll by

选择要展开的数组。 对于每个数组中的每一项，输出数据都会占用一行。 如果输入行中的 unroll by 数组为 NULL 或为空，则会有一个展开值为 NULL 的输出行。

### <a name="unroll-root"></a>展开根

默认情况下，平展转换会将数组展开到其所在层次结构的顶部。 你可以选择将某个数组用作展开根。 展开根必须是复杂对象数组，该数组要么是 unroll by 数组，要么包含 unroll by 数组。 如果选择了展开根，则在展开根中的每一项中，输出数据都将至少包含一行。 如果输入行没有展开根中的任何项，该行便会从输出数据中删除。 选择展开根时，将始终输出小于或等于默认行为的行数。

### <a name="flatten-mapping"></a>平展映射

与选择转换类似，从传入字段和已实现非规范化的数组中选择新结构的投影。 如果映射了已实现非规范化的数组，则输出列的数据类型将与数组的数据类型相同。 如果 unroll by 数组是包含子数组的复杂对象数组，在映射该子数组的项时，便会输出一个数组。

请参阅检查选项卡和数据预览，验证映射输出。

## <a name="rule-based-mapping"></a>基于规则的映射

平展转换支持基于规则的映射，使你能够创建动态和灵活的转换，根据规则平展数组，并根据层次结构级别平展结构。

![平展模式](media/data-flow/flatten-pattern.png "平展模式")

### <a name="matching-condition"></a>匹配条件

输入要使用完全匹配或模式平展的一列或多列的模式匹配条件。 示例： ```like(name,'cust%')```

### <a name="deep-column-traversal"></a>深层列遍历

可选设置通知 ADF 逐个处理复杂对象的所有子列，而不是将复杂对象作为一个整列处理。

### <a name="hierarchy-level"></a>层次结构级别

选择要扩展的层次结构级别。

### <a name="name-matches-regex"></a>名称匹配 (regex)

还可以选择在此框中以正则表达式表示名称匹配，而不是使用以上匹配条件。

## <a name="examples"></a>示例

请参阅以下 JSON 对象的平展转换示例

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>没有包含字符串数组的展开根

| Unroll by | 展开根 | 投影 |
| --------- | ----------- | ---------- |
| goods.customers | 无 | name <br> customer = goods.customer |

#### <a name="output"></a>输出

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>没有包含复杂数组的展开根

| Unroll by | 展开根 | 投影 |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItems | 无 | name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> location = location |

#### <a name="output"></a>输出

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>与展开数组相同的根

| Unroll by | 展开根 | 投影 |
| --------- | ----------- | ---------- |
| goods.orders | goods.orders | name <br> goods.orders.shipped.orderItems.itemName <br> goods.customers <br> location |

#### <a name="output"></a>输出

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>包含复杂数组的展开根

| Unroll by | 展开根 | 投影 |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItem | goods.orders |name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> location = location |

#### <a name="output"></a>输出

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>示例

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>后续步骤

* 使用[透视转换](data-flow-pivot.md)将行转换为列。
* 使用[逆透视转换](data-flow-unpivot.md)将列转换为行。
