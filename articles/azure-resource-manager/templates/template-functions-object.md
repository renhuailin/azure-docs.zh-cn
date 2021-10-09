---
title: 模板函数 - 对象
description: 介绍可在 Azure 资源管理器模板（ARM 模板）中用来处理对象的函数。
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 3e6069744b1879e97ef3977916acbfc8aa6a9bd2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820442"
---
# <a name="object-functions-for-arm-templates"></a>ARM 模板的对象函数

资源管理器提供了多个函数，用于处理 Azure 资源管理器模板（ARM 模板）中的对象：

* [contains](#contains)
* [createObject](#createobject)
* [empty](#empty)
* [intersection](#intersection)
* [json](#json)
* [length](#length)
* [null](#null)
* [union](#union)

## <a name="contains"></a>contains

`contains(container, itemToFind)`

检查数组是否包含某个值、某个对象是否包含某个键，或者某个字符串是否包含某个子字符串。 字符串比较区分大小写。 但在测试某个对象是否包含某个键时，该比较不区分大小写。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| container |是 |数组、对象或字符串 |包含要查找的值的值。 |
| itemToFind |是 |字符串或整数 |要查找的值。 |

### <a name="return-value"></a>返回值

如果找到该项，则为 **True**；否则为 **False**。

### <a name="example"></a>示例

以下示例演示如何对不同的类型使用 contains：

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/contains.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="createobject"></a>createObject

`createObject(key1, value1, key2, value2, ...)`

从键和值创建对象。

Bicep 不支持 `createObject` 函数。  使用 `{}` 构造对象。 请参阅[对象](../bicep/data-types.md#objects)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| key1 |否 |string |键的名称。 |
| value1 |否 |int、布尔值、字符串、对象或数组 |键的值。 |
| 更多键 |否 |string |键的更多名称。 |
| 更多值 |否 |int、布尔值、字符串、对象或数组 |键的更多值。 |

该函数只接受偶数个参数。 每个键必须具有匹配的值。

### <a name="return-value"></a>返回值

具有每个键和值对的对象。

### <a name="example"></a>示例

下面的示例从不同类型的值创建对象。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/createobject.json":::

上述示例中使用默认值的输出是一个名为 `newObject` 的对象，其值如下：

```json
{
  "intProp": 1,
  "stringProp": "abc",
  "boolProp": true,
  "arrayProp": ["a", "b", "c"],
  "objectProp": {"key1": "value1"}
}
```

## <a name="empty"></a>empty

`empty(itemToTest)`

确定数组、对象或字符串是否为空。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| itemToTest |是 |数组、对象或字符串 |要检查是否为空的值。 |

### <a name="return-value"></a>返回值

如果该值为空，则返回 **True**；否则返回 **False**。

### <a name="example"></a>示例

以下示例检查某个数组、对象和字符串是否为空。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/empty.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="intersection"></a>intersection

`intersection(arg1, arg2, arg3, ...)`

返回包含参数中通用元素的单个数组或对象。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |数组或对象 |用于查找通用元素的第一个值。 |
| arg2 |是 |数组或对象 |用于查找通用元素的第二个值。 |
| 更多参数 |否 |数组或对象 |用于查找通用元素的更多值。 |

### <a name="return-value"></a>返回值

包含通用元素的数组或对象。

### <a name="example"></a>示例

以下示例演示如何对数组和对象使用 intersection。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/intersection.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| objectOutput | 对象 | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

将有效的 JSON 字符串转换为 JSON 数据类型。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |string |要转换为 JSON 的值。 字符串必须是格式正确的 JSON 字符串。 |

### <a name="return-value"></a>返回值

指定字符串中的 JSON 数据类型，或指定 null 时的空值。

### <a name="remarks"></a>备注

如需将某个参数值或变量包含在 JSON 对象中，可使用 [concat](template-functions-string.md#concat) 函数创建要传递到此函数的字符串。

还可以使用 [null()](#null) 获取空值。

### <a name="example"></a>示例

以下示例演示如何使用 `json` 函数。 请注意，你可以为空对象传入 `null`。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/json.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| emptyObjectOutput | 布尔 | True |
| objectOutput | Object | {"a": "b"} |
| stringOutput | String | 测试 |
| booleanOutput | 布尔 | True |
| intOutput | Integer | 3 |
| arrayOutput | Array | [ 1, 2, 3 ] |
| concatObjectOutput | 对象 | { "a": "demo value" } |

## <a name="length"></a>length

`length(arg1)`

返回数组中的元素数、字符串中的字符数或对象中的根级属性数。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |数组、字符串或对象 |用于获取元素数的数组、用于获取字符数的字符串，或用于获取根级属性数的对象。 |

### <a name="return-value"></a>返回值

一个整数。

### <a name="example"></a>示例

以下示例演示如何对数组和字符串使用 length：

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/length.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |
| objectLength | int | 4 |

## <a name="null"></a>null

`null()`

返回 Null。

`null` 函数在 Bicep 中不可用。 请改用 `null` 关键字。

### <a name="parameters"></a>参数

Null 函数不接受任何参数。

### <a name="return-value"></a>返回值

一个始终为 null 的值。

### <a name="example"></a>示例

下面的示例使用 null 函数。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/null.json":::

前述示例的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| emptyOutput | Bool | True |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

返回包含参数中所有元素的单个数组或对象。 重复的值或键仅包含一次。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |数组或对象 |用于联接元素的第一个值。 |
| arg2 |是 |数组或对象 |用于联接元素的第二个值。 |
| 更多参数 |否 |数组或对象 |用于联接元素的更多值。 |

### <a name="return-value"></a>返回值

数组或对象。

### <a name="example"></a>示例

以下示例演示如何对数组和对象使用 union：

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/union.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>后续步骤

* 有关 ARM 模板中各部分的说明，请参阅[了解 ARM 模板的结构和语法](./syntax.md)。
