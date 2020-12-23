---
title: 模板函数 - 对象
description: 描述在 Azure 资源管理器模板中使用的用于处理对象 (ARM 模板) 的函数。
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 5e13177db1a7cf2f19a822363cb3884474566add
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920450"
---
# <a name="object-functions-for-arm-templates"></a>ARM 模板的对象函数

资源管理器提供了几个函数，用于在 Azure 资源管理器模板中处理对象 (ARM 模板) ：

* [contains](#contains)
* [createObject](#createobject)
* [empty](#empty)
* [intersection](#intersection)
* [json](#json)
* [length](#length)
* [null](#null)
* [union](#union)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="contains"></a>contains

`contains(container, itemToFind)`

检查数组是否包含某个值、某个对象是否包含某个键，或者某个字符串是否包含某个子字符串。 字符串比较区分大小写。 但在测试某个对象是否包含某个键时，该比较不区分大小写。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| container |是 |数组、对象或字符串 |包含要查找的值的值。 |
| itemToFind |是 |字符串或整数 |要查找的值。 |

### <a name="return-value"></a>返回值

如果找到该项，则为 **True**；否则为 **False**。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json)演示如何对不同的类型使用 contains：

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "OneTwoThree"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "stringTrue": {
      "type": "bool",
      "value": "[contains(parameters('stringToTest'), 'e')]"
    },
    "stringFalse": {
      "type": "bool",
      "value": "[contains(parameters('stringToTest'), 'z')]"
    },
    "objectTrue": {
      "type": "bool",
      "value": "[contains(parameters('objectToTest'), 'one')]"
    },
    "objectFalse": {
      "type": "bool",
      "value": "[contains(parameters('objectToTest'), 'a')]"
    },
    "arrayTrue": {
      "type": "bool",
      "value": "[contains(parameters('arrayToTest'), 'three')]"
    },
    "arrayFalse": {
      "type": "bool",
      "value": "[contains(parameters('arrayToTest'), 'four')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'OneTwoThree'
param objectToTest object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output stringTrue bool = contains(stringToTest, 'e')
output stringFalse bool = contains(stringToTest, 'z')
output objectTrue bool = contains(objectToTest, 'one')
output objectFalse bool = contains(objectToTest, 'a')
output arrayTrue bool = contains(arrayToTest, 'three')
output arrayFalse bool = contains(arrayToTest, 'four')
```

---

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

从键和值创建对象。 `createObject`Bicep 不支持该函数。  使用构造对象 `{}` 。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| key1 |否 |string |键的名称。 |
| value1 |否 |int、布尔值、字符串、对象或数组 |键的值。 |
| 其他键 |否 |string |键的其他名称。 |
| 其他值 |否 |int、布尔值、字符串、对象或数组 |键的其他值。 |

该函数只接受偶数个参数。 每个键必须具有匹配的值。

### <a name="return-value"></a>返回值

具有每个键和值对的对象。

### <a name="example"></a>示例

下面的示例从不同类型的值创建对象。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "newObject": {
      "type": "object",
      "value": "[createObject('intProp', 1, 'stringProp', 'abc', 'boolProp', true(), 'arrayProp', createArray('a', 'b', 'c'), 'objectProp', createObject('key1', 'value1'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output newObject object = {
  'intProp': 1
  'stringProp': 'abc'
  'boolProp': true
  'arrayProp': [
    'a'
    'b'
    'c'
  ]
  'objectProp': {
    'key1': 'value1'
  }
}
```

---

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

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| itemToTest |是 |数组、对象或字符串 |要检查是否为空的值。 |

### <a name="return-value"></a>返回值

如果该值为空，则返回 **True**；否则返回 **False**。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json)检查某个数组、对象和字符串是否为空。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": []
    },
    "testObject": {
      "type": "object",
      "defaultValue": {}
    },
    "testString": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testArray'))]"
    },
    "objectEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testObject'))]"
    },
    "stringEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = []
param testObject object = {}
param testString string = ''

output arrayEmpty bool = empty(testArray)
output objectEmpty bool = empty(testObject)
output stringEmpty bool = empty(testString)
```

---

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

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |数组或对象 |用于查找通用元素的第一个值。 |
| arg2 |是 |数组或对象 |用于查找通用元素的第二个值。 |
| 其他参数 |否 |数组或对象 |用于查找通用元素的其他值。 |

### <a name="return-value"></a>返回值

包含通用元素的数组或对象。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json)演示如何对数组和对象使用 intersection：

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "z",
        "three": "c"
      }
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "objectOutput": {
      "type": "object",
      "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param secondObject object = {
  'one': 'a'
  'two': 'z'
  'three': 'c'
}
param firstArray array = [
  'one'
  'two'
  'three'
]
param secondArray array = [
  'two'
  'three'
]

output objectOutput object = intersection(firstObject, secondObject)
output arrayOutput array = intersection(firstArray, secondArray)
```

---

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

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |string |要转换为 JSON 的值。 字符串必须是格式正确的 JSON 字符串。 |

### <a name="return-value"></a>返回值

指定字符串中的 JSON 数据类型，或指定 null 时的空值。

### <a name="remarks"></a>备注

如需将某个参数值或变量包含在 JSON 对象中，可使用 [concat](template-functions-string.md#concat) 函数创建要传递到此函数的字符串。

还可以使用 [null()](#null) 获取空值。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json)演示如何使用 json 函数。 请注意，可以为空对象传入 null。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "jsonEmptyObject": {
      "type": "string",
      "defaultValue": "null"
    },
    "jsonObject": {
      "type": "string",
      "defaultValue": "{\"a\": \"b\"}"
    },
    "jsonString": {
      "type": "string",
      "defaultValue": "\"test\""
    },
    "jsonBoolean": {
      "type": "string",
      "defaultValue": "true"
    },
    "jsonInt": {
      "type": "string",
      "defaultValue": "3"
    },
    "jsonArray": {
      "type": "string",
      "defaultValue": "[[1,2,3 ]"
    },
    "concatValue": {
      "type": "string",
      "defaultValue": "demo value"
    }
  },
  "resources": [
  ],
  "outputs": {
    "emptyObjectOutput": {
      "type": "bool",
      "value": "[empty(json(parameters('jsonEmptyObject')))]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[json(parameters('jsonObject'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[json(parameters('jsonString'))]"
    },
    "booleanOutput": {
      "type": "bool",
      "value": "[json(parameters('jsonBoolean'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[json(parameters('jsonInt'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[json(parameters('jsonArray'))]"
    },
    "concatObjectOutput": {
      "type": "object",
      "value": "[json(concat('{\"a\": \"', parameters('concatValue'), '\"}'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param jsonEmptyObject string = 'null'
param jsonObject string = '{\'a\': \'b\'}'
param jsonString string = '\'test\''
param jsonBoolean string = 'true'
param jsonInt string = '3'
param jsonArray string = '[[1,2,3]]'
param concatValue string = 'demo value'

output emptyObjectOutput bool = empty(json(jsonEmptyObject))
output objectOutput object = json(jsonObject)
output stringOutput string =json(jsonString)
output booleanOutput bool = json(jsonBoolean)
output intOutput int = json(jsonInt)
output arrayOutput array = json(jsonArray)
output concatObjectOutput object = json(concat('{"a": "', concatValue, '"}'))
```

---

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

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |数组、字符串或对象 |用于获取元素数的数组、用于获取字符数的字符串，或用于获取根级属性数的对象。 |

### <a name="return-value"></a>返回值

一个整数。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json)演示如何对数组和字符串使用 length：

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "stringToTest": {
      "type": "string",
      "defaultValue": "One Two Three"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "propA": "one",
        "propB": "two",
        "propC": "three",
        "propD": {
          "propD-1": "sub",
          "propD-2": "sub"
        }
      }
    }
  },
  "resources": [],
  "outputs": {
    "arrayLength": {
      "type": "int",
      "value": "[length(parameters('arrayToTest'))]"
    },
    "stringLength": {
      "type": "int",
      "value": "[length(parameters('stringToTest'))]"
    },
    "objectLength": {
      "type": "int",
      "value": "[length(parameters('objectToTest'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]
param stringToTest string = 'One Two Three'
param objectToTest object = {
  'propA': 'one'
  'propB': 'two'
  'propC': 'three'
  'propD': {
      'propD-1': 'sub'
      'propD-2': 'sub'
  }
}

output arrayLength int = length(arrayToTest)
output stringLength int = length(stringToTest)
output objectLength int = length(objectToTest)
```

---

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |
| objectLength | int | 4 |

## <a name="null"></a>null

`null()`

返回 Null。 `null`函数在 Bicep 中不可用。 请改用 `null` 关键字。

### <a name="parameters"></a>参数

Null 函数不接受任何参数。

### <a name="return-value"></a>返回值

一个始终为 null 的值。

### <a name="example"></a>示例

下面的示例使用 null 函数。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "emptyOutput": {
      "type": "bool",
      "value": "[empty(null())]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output emptyOutput bool = empty(null)
```

---

前述示例的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| emptyOutput | Bool | True |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

返回包含参数中所有元素的单个数组或对象。 重复的值或键仅包含一次。

### <a name="parameters"></a>parameters

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |数组或对象 |用于联接元素的第一个值。 |
| arg2 |是 |数组或对象 |用于联接元素的第二个值。 |
| 其他参数 |否 |数组或对象 |用于联接元素的其他值。 |

### <a name="return-value"></a>返回值

数组或对象。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json)演示如何对数组和对象使用 union：

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c1"
      }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": {
        "three": "c2",
        "four": "d",
        "five": "e"
      }
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "three", "four" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "objectOutput": {
      "type": "object",
      "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c1'
}

param secondObject object = {
  'three': 'c2'
  'four': 'd'
  'five': 'e'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'three'
  'four'
]

output objectOutput object = union(firstObject, secondObject)
output arrayOutput array = union(firstArray, secondArray)
```

---

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>后续步骤

* 有关 ARM 模板中各部分的说明，请参阅 [了解 arm 模板的结构和语法](template-syntax.md)。
