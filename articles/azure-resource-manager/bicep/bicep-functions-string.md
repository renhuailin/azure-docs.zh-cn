---
title: Bicep 函数 - 字符串
description: 描述 Bicep 文件中用于处理字符串的函数。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: e8691fd357b8f8cbde87309f4881fa7424f7f44e
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389070"
---
# <a name="string-functions-for-bicep"></a>Bicep 的字符串函数

本文介绍用于处理字符串的 Bicep 函数。

## <a name="base64"></a>base64

`base64(inputString)`

返回输入字符串的 base64 表示形式。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| inputString |是 |string |要以 base64 表示形式返回的值。 |

### <a name="return-value"></a>返回值

包含 base64 表示形式的字符串。

### <a name="examples"></a>示例

以下示例演示如何使用 base64 函数。

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

将 base64 表示形式转换为 JSON 对象。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| base64Value |是 |string |要转换为 JSON 对象的 base64 表示形式。 |

### <a name="return-value"></a>返回值

一个 JSON 对象。

### <a name="examples"></a>示例

以下示例使用 base64ToJson 函数转换 base64 值：

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)

```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

将 base64 表示形式转换为字符串。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| base64Value |是 |string |要转换为字符串的 base64 表示形式。 |

### <a name="return-value"></a>返回值

转换后的 base64 值的字符串。

### <a name="examples"></a>示例

以下示例使用 base64ToString 函数转换 base64 值：

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

改为使用字符串内插，而不要使用 concat 函数。

```bicep
param prefix string = 'prefix'

output concatOutput string = '${prefix}And${uniqueString(resourceGroup().id)}'
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| concatOutput | String | prefixAnd5yj4yjf5mbg72 |

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

## <a name="contains"></a>contains

`contains (container, itemToFind)`

检查数组是否包含某个值、某个对象是否包含某个键，或者某个字符串是否包含某个子字符串。 字符串比较区分大小写。 但在测试某个对象是否包含某个键时，该比较不区分大小写。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| container |是 |数组、对象或字符串 |包含要查找的值的值。 |
| itemToFind |是 |字符串或整数 |要查找的值。 |

### <a name="return-value"></a>返回值

如果找到该项，则为 **True**；否则为 **False**。

### <a name="examples"></a>示例

以下示例演示如何对不同的类型使用 contains：

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

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

将一个值转换为数据 URI。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| stringToConvert |是 |string |要转换为数据 URI 的值。 |

### <a name="return-value"></a>返回值

格式为数据 URI 的字符串。

### <a name="examples"></a>示例

以下示例将值转换为数据 URI，然后将数据 URI 转换为字符串：

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

将采用数据 URI 格式的值转换为字符串。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |是 |string |要转换的数据 URI 值。 |

### <a name="return-value"></a>返回值

包含转换后的值的字符串。

### <a name="examples"></a>示例

以下示例将值转换为数据 URI，然后将数据 URI 转换为字符串：

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="empty"></a>empty

`empty(itemToTest)`

确定数组、对象或字符串是否为空。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| itemToTest |是 |数组、对象或字符串 |要检查是否为空的值。 |

### <a name="return-value"></a>返回值

如果该值为空，则返回 **True**；否则返回 **False**。

### <a name="examples"></a>示例

以下示例检查某个数组、对象和字符串是否为空。

```bicep
param testArray array = []
param testObject object = {}
param testString string = ''

output arrayEmpty bool = empty(testArray)
output objectEmpty bool = empty(testObject)
output stringEmpty bool = empty(testString)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

确定某个字符串是否以某个值结尾。 比较不区分大小写。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |是 |string |包含要查找的项的值。 |
| stringToFind |是 |string |要查找的值。 |

### <a name="return-value"></a>返回值

如果字符串的最后一个或多个字符与该值匹配，则为 **True**；否则为 **False**。

### <a name="examples"></a>示例

以下示例演示如何使用 startsWith 和 endsWith 函数：

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>first

`first(arg1)`

返回字符串的第一个字符，或数组的第一个元素。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |数组或字符串 |要检索第一个元素或字符的值。 |

### <a name="return-value"></a>返回值

第一个字符的字符串，或者数组中第一个元素的类型（字符串、整数、数组或对象）。

### <a name="examples"></a>示例

以下示例演示如何对不同的类型使用 first 函数。

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = first(arrayToTest)
output stringOutput string = first('One Two Three')
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

基于输入值创建带格式的字符串。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| formatString | 是 | string | 复合格式字符串。 |
| arg1 | 是 | 字符串、整数或布尔值 | 要包含在带格式字符串中的值。 |
| 其他参数 | 否 | 字符串、整数或布尔值 | 要包含在带格式字符串中的附加值。 |

### <a name="remarks"></a>备注

使用此函数来为 Bicep 文件中的字符串设置格式。 此函数使用的格式设置选项与 .NET 中的 [System.String.Format](/dotnet/api/system.string.format) 方法相同。

### <a name="examples"></a>示例

如何使用此格式函数的演示示例如下。

```bicep
param greeting string = 'Hello'
param name string = 'User'
param numberToFormat int = 8175133

output formatTest string = format('{0}, {1}. Formatted number: {2:N0}', greeting, name, numberToFormat)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| formatTest | String | Hello, User。 带格式的数字：8,175,133 |

## <a name="guid"></a>GUID

`guid(baseString, ...)`

基于以参数形式提供的值创建一个采用全局唯一标识符格式的值。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| baseString |是 |string |哈希函数中用于创建 GUID 的值。 |
| 根据需要使用其他参数 |否 |string |可以添加任意数目的字符串，以创建指定唯一性级别的值。 |

### <a name="remarks"></a>备注

当需要以全局唯一标识符格式创建值时，此功能十分有用。 提供参数值，这些值用于限制结果的唯一性范围。 可以指定该名称对于订阅、资源组或部署是否唯一。

返回的值不是随机字符串，而是参数中哈希函数的结果。 返回的值长度为 36 个字符。 此值并非全局唯一。 若要创建不是基于该参数哈希值的新 GUID，请使用 [newGuid](#newguid) 函数。

以下示例演示如何使用 guid 创建常用级别唯一值。

仅对订阅唯一

```bicep
guid(subscription().subscriptionId)
```

仅对资源组唯一

```bicep
guid(resourceGroup().id)
```

仅对资源组的部署唯一

```bicep
guid(resourceGroup().id, deployment().name)
```

### <a name="return-value"></a>返回值

包含 36 个字符的全局唯一标识符格式的字符串。

### <a name="examples"></a>示例

以下示例会从 guid 返回结果：

```bicep
output guidPerSubscription string = guid(subscription().subscriptionId)
output guidPerResourceGroup string = guid(resourceGroup().id)
output guidPerDeployment string = guid(resourceGroup().id, deployment().name)
```

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

返回字符串中某个值的第一个位置。 比较不区分大小写。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |是 |string |包含要查找的项的值。 |
| stringToFind |是 |string |要查找的值。 |

### <a name="return-value"></a>返回值

一个整数，表示要查找的项的位置。 该值从零开始。 如果未找到该项，则返回 -1。

### <a name="examples"></a>示例

以下示例演示如何使用 indexOf 和 lastIndexOf 函数：

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

将有效的 JSON 字符串转换为 JSON 数据类型。 有关详细信息，请参阅 [json 函数](./bicep-functions-object.md#json)。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

## <a name="last"></a>last

`last (arg1)`

返回字符串的最后一个字符，或数组的最后一个元素。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |数组或字符串 |要检索最后一个元素或字符的值。 |

### <a name="return-value"></a>返回值

最后一个字符的字符串，或者数组中最后一个元素的类型（字符串、整数、数组或对象）。

### <a name="examples"></a>示例

以下示例演示如何对不同的类型使用 last 函数。

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = last(arrayToTest)
output stringOutput string = last('One Two Three')
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

返回字符串中某个值的最后一个位置。 比较不区分大小写。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |是 |string |包含要查找的项的值。 |
| stringToFind |是 |string |要查找的值。 |

### <a name="return-value"></a>返回值

一个整数，表示要查找的项的最后一个位置。 该值从零开始。 如果未找到该项，则返回 -1。

### <a name="examples"></a>示例

以下示例演示如何使用 indexOf 和 lastIndexOf 函数：

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

## <a name="length"></a>length

`length(string)`

返回字符串中的字符数、数组中的元素数或对象中的根级属性数。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |数组、字符串或对象 |用于获取元素数的数组、用于获取字符数的字符串，或用于获取根级属性数的对象。 |

### <a name="return-value"></a>返回值

一个整数。

### <a name="examples"></a>示例

以下示例演示如何对数组和字符串使用 length：

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

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |
| objectLength | int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

以全局唯一标识符的格式返回一个值。 **此函数只能在参数的默认值中使用。**

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="remarks"></a>备注

只能在表达式中对参数的默认值使用此函数。 在 Bicep 文件中的其他任何位置使用此函数都会返回错误。 不允许在 Bicep 文件的其他部分使用该函数，因为每次调用该函数，都会返回不同的值。 使用相同的参数部署同一 Bicep 文件不能可靠地生成相同的结果。

newGuid 函数不同于 [guid](#guid) 函数，因为它不采用任何参数。 每次结合相同的参数调用 guid 都会返回相同的标识符。 需要为特定的环境可靠地生成相同的 GUID 时，请使用 guid。 如果每次需要不同的标识符（例如，将资源部署到测试环境），请使用 newGuid。

newGuid 函数会使用 .NET Framework 中的 [Guid 结构](/dotnet/api/system.guid)生成全局唯一标识符。

如果[使用相应的选项来重新部署以前已成功的部署](../templates/rollback-on-error.md)，而以前的部署包含一个使用 newGuid 的参数，则不会重新评估该参数， 而是在回滚部署中自动重复使用以前部署中的参数值。

在测试环境中，可能需要重复部署生存期较短的资源。 无需构造唯一的名称，可以结合 [uniqueString](#uniquestring) 使用 newGuid 来创建唯一的名称。

重新部署依赖 newGuid 函数获取默认值的 Bicep 文件时，请保持谨慎。 如果重新部署且不提供参数的值，则会重新评估该函数。 若要更新现有的资源而不是新建资源，请传入以前部署中的参数值。

### <a name="return-value"></a>返回值

包含 36 个字符的全局唯一标识符格式的字符串。

### <a name="examples"></a>示例

带有新标识符参数的演示示例如下。

```bicep
param guidValue string = newGuid()

output guidOutput string = guidValue
```

---

上述示例的输出根据每个部署的不同而异，但类似于：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

以下示例使用 newGuid 函数创建存储帐户的唯一名称。 此 Bicep 文件可能适用于存储帐户存在时间较短且未重新部署的测试环境。

```bicep
param guidValue string = newGuid()

var storageName = 'storage${uniqueString(guidValue)}'

resource myStorage 'Microsoft.Storage/storageAccounts@2018-07-01' = {
  name: storageName
  location: 'West US'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {}
}

output nameOutput string = storageName
```

上述示例的输出根据每个部署的不同而异，但类似于：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |

## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

通过向左侧添加字符直至到达指定的总长度返回右对齐的字符串。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| valueToPad |是 |字符串或整数 |要右对齐的值。 |
| totalLength |是 |int |返回字符串中的字符总数。 |
| paddingCharacter |否 |单个字符 |要用于向左填充直到达到总长度的字符。 默认值为空格。 |

如果原始字符串的长度超过要填充的字符数，则不会添加任何字符。

### <a name="return-value"></a>返回值

一个字符串，其中至少包含指定的字符数。

### <a name="examples"></a>示例

以下示例演示如何通过添加零字符直到字符总数，来填充用户提供的参数值。

```bicep
param testString string = '123'

output stringOutput string = padLeft(testString, 10, '0')
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

## <a name="replace"></a>replace

`replace(originalString, oldString, newString)`

返回其中某个字符串的所有实例均替换为另一个字符串的新字符串。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| originalString |是 |string |包含某一个字符串的所有实例均替换为另一个字符串的值。 |
| oldString |是 |string |要从原始字符串中删除的字符串。 |
| newString |是 |string |要添加以替代已删除字符串的字符串。 |

### <a name="return-value"></a>返回值

包含被替换字符的字符串。

### <a name="examples"></a>示例

以下示例演示如何从用户提供的字符串中删除所有短划线，以及如何将字符串的一部分替换为其他字符串。

```bicep
param testString string = '123-123-1234'

output firstOutput string = replace(testString, '-', '')
output secondOutput string = replace(testString, '1234', 'xxxx')
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secondOutput | String | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

返回一个字符串，其中包含指定字符数后面的所有字符；或者返回一个数组，其中包含指定元素数后面的所有元素。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| originalValue |是 |数组或字符串 |用于跳过的数组或字符串。 |
| numberToSkip |是 |int |要跳过的元素数或字符数。 如果此值小于或等于 0，则返回值中的所有元素或字符。 如果此值大于数组或字符串的长度，则返回空数组或字符串。 |

### <a name="return-value"></a>返回值

数组或字符串。

### <a name="examples"></a>示例

以下示例跳过数组中指定数目的元素，以及字符串中指定数目的字符。

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 4

output arrayOutput array = skip(testArray, elementsToSkip)
output stringOutput string = skip(testString, charactersToSkip)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="split"></a>split

`split(inputString, delimiter)`

返回包含输入字符串的子字符串的字符串数组，其中的子字符串使用指定的分隔符进行分隔。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| inputString |是 |string |要拆分的字符串。 |
| delimiter |是 |字符串或字符串数组 |用于拆分字符串的分隔符。 |

### <a name="return-value"></a>返回值

字符串数组。

### <a name="examples"></a>示例

以下示例使用逗号以及使用逗号或分号拆分输入字符串。

```bicep
param firstString string = 'one,two,three'
param secondString string = 'one;two,three'

var delimiters = [
  ','
  ';'
]

output firstOutput array = split(firstString, ',')
output secondOutput array = split(secondString, delimiters)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| firstOutput | Array | ["one", "two", "three"] |
| secondOutput | Array | ["one", "two", "three"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

确定某个字符串是否以某个值开头。 比较不区分大小写。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |是 |string |包含要查找的项的值。 |
| stringToFind |是 |string |要查找的值。 |

### <a name="return-value"></a>返回值

如果字符串的最前面一个或多个字符与该值匹配，则为 **True**；否则为 **False**。

### <a name="examples"></a>示例

以下示例演示如何使用 startsWith 和 endsWith 函数：

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>string

`string(valueToConvert)`

将指定的值转换为字符串。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| valueToConvert |是 | 任意 |要转换为字符串的值。 可以转换任何类型的值，包括对象和数组。 |

### <a name="return-value"></a>返回值

转换后的值的字符串。

### <a name="examples"></a>示例

以下示例演示如何将不同类型的值转换为字符串：

```bicep
param testObject object = {
  'valueA': 10
  'valueB': 'Example Text'
}
param testArray array = [
  'a'
  'b'
  'c'
]
param testInt int = 5

output objectOutput string = string(testObject)
output arrayOutput string = string(testArray)
output intOutput string = string(testInt)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| objectOutput | String | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | String | ["a","b","c"] |
| intOutput | String | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

返回从指定的字符位置开始且包含指定数量的字符的子字符串。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| stringToParse |是 |string |从中提取子字符串的原始字符串。 |
| startIndex |否 |int |子字符串的从零开始的字符位置。 |
| length |否 |int |子字符串的字符数。 必须引用该字符串内的一个位置。 必须为零或更大值。 |

### <a name="return-value"></a>返回值

子字符串。 或者，如果长度为零，则为空字符串。

### <a name="remarks"></a>备注

当子字符串延伸超出字符串末尾或长度小于零时，函数将失败。 以下示例将失败，并出现错误“索引和长度参数必须引用字符串内的一个位置。 索引参数：“0”，长度参数：“11”，字符串参数长度：“10”。”。

```bicep
param inputString string = '1234567890'

var prefix = substring(inputString, 0, 11)
```

### <a name="examples"></a>示例

以下示例从参数中提取子字符串。

```bicep
param testString string = 'one two three'
output substringOutput string = substring(testString, 4, 3)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| substringOutput | String | two |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

返回一个字符串，其中包含从字符串开头位置算起的指定数目的字符；或返回一个数组，其中包含从数组开头位置算起的指定数目的元素。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| originalValue |是 |数组或字符串 |要从中提取元素的数组或字符串。 |
| numberToTake |是 |int |要提取的元素或字符数。 如果此值为 0 或更小，则返回一个空数组或字符串。 如果此值大于给定数组或字符串的长度，则返回数组或字符串中的所有元素。 |

### <a name="return-value"></a>返回值

数组或字符串。

### <a name="examples"></a>示例

以下示例从数组中提取指定数目的元素，并从字符串中提取指定数目的字符。

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToTake int = 2
param testString string = 'one two three'
param charactersToTake int = 2

output arrayOutput array = take(testArray, elementsToTake)
output stringOutput string = take(testString, charactersToTake)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | on |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

将指定的字符串转换为小写。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| stringToChange |是 |string |要转换为小写的值。 |

### <a name="return-value"></a>返回值

已转换为小写的字符串。

### <a name="examples"></a>示例

以下示例将参数值转换为小写和大写。

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

将指定的字符串转换为大写。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| stringToChange |是 |string |要转换为大写的值。 |

### <a name="return-value"></a>返回值

已转换为大写的字符串。

### <a name="examples"></a>示例

以下示例将参数值转换为小写和大写。

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="trim"></a>trim

`trim (stringToTrim)`

从指定的字符串中删除所有前导和尾随空白字符。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| stringToTrim |是 |string |要剪裁的值。 |

### <a name="return-value"></a>返回值

不带前导和尾随空白字符的字符串。

### <a name="examples"></a>示例

以下示例裁剪掉参数中的空白字符。

```bicep
param testString string = '    one two three   '

output return string = trim(testString)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| return | String | one two three |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

根据作为参数提供的值创建确定性哈希字符串。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| baseString |是 |string |哈希函数中用于创建唯一字符串的值。 |
| 根据需要使用其他参数 |否 |string |可以添加任意数目的字符串，以创建指定唯一性级别的值。 |

### <a name="remarks"></a>备注

当需要创建资源的唯一名称时，此函数很有帮助。 提供参数值，这些值用于限制结果的唯一性范围。 可以指定该名称对于订阅、资源组或部署是否唯一。

返回的值不是随机字符串，而是哈希函数的结果。 返回的值长度为 13 个字符。 此值并非全局唯一。 可能需要根据命名约定使用前缀来组合值，以创建有意义的名称。 以下示例显示了返回值的格式。 实际值取决于提供的参数。

`tcvhiyu5h2o5o`

以下示例演示如何使用 uniqueString 创建通用级别唯一值。

仅对订阅唯一

```bicep
uniqueString(subscription().subscriptionId)
```

仅对资源组唯一

```bicep
uniqueString(resourceGroup().id)
```

仅对资源组的部署唯一

```bicep
uniqueString(resourceGroup().id, deployment().name)
```

以下示例演示显示如何根据资源组创建存储帐户的唯一名称。 在资源组中，如果构造方式相同，则名称不唯一。

```bicep
resource mystorage 'Microsoft.Storage/storageAccounts@@2018-07-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
  ...
}
```

如果每次部署 Bicep 文件都需要新建不重复的名称且不希望更新资源，则可以结合 uniqueString，使用 [utcNow](./bicep-functions-date.md#utcnow) 函数。 可以在测试环境中使用此方法。 有关示例，请参阅 [utcNow](./bicep-functions-date.md#utcnow)。 请注意，utcNow 函数只能在参数的默认值的表达式中使用。

### <a name="return-value"></a>返回值

包含 13 个字符的字符串。

### <a name="examples"></a>示例

以下示例从唯一字符串返回结果：

```bicep
output uniqueRG string = uniqueString(resourceGroup().id)
output uniqueDeploy string = uniqueString(resourceGroup().id, deployment().name)
```

## <a name="uri"></a>uri

`uri (baseUri, relativeUri)`

通过组合 baseUri 和 relativeUri 字符串来创建绝对 URI。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| baseUri |是 |string |基本 uri 字符串。 请注意观察与处理尾随斜杠 ('/') 有关的行为，如下表所述。  |
| relativeUri |是 |string |要添加到基本 uri 字符串的相对 uri 字符串。 |

* 如果 baseUri 以尾随斜杠结尾，则结果就是 baseUri 后跟 relativeUri。

* 如果 **baseUri** 不以尾随斜杠结尾，则会出现以下两种情况之一。

   * 如果 **baseUri** 根本没有斜杠（除了前面的“/”之外），则结果就是 **baseUri** 后跟 **relativeUri**。

   * 如果 baseUri 包含一些斜杠，但不是以斜杠结尾，则从最后一个斜杠开始的所有内容都将从 baseUri 中删除，结果是 baseUri 后跟 relativeUri。

下面是一些示例：

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```

有关完整的详细信息，请按照 [RFC 3986 第 5 部分](https://tools.ietf.org/html/rfc3986#section-5)中的说明解析 **baseUri** 和 **relativeUri** 参数。

### <a name="return-value"></a>返回值

表示基值和相对值的绝对 URI 的字符串。

### <a name="examples"></a>示例

以下示例演示如何使用 uri、uriComponent 和 uriComponentToString：

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

将 URI 编码。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| stringToEncode |是 |string |要编码的值。 |

### <a name="return-value"></a>返回值

URI 编码值的字符串。

### <a name="examples"></a>示例

以下示例演示如何使用 uri、uriComponent 和 uriComponentToString：

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

返回 URI 编码值的字符串。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| uriEncodedString |是 |string |要转换为字符串的 URI 编码值。 |

### <a name="return-value"></a>返回值

URI 编码值的解码后字符串。

### <a name="examples"></a>示例

以下示例演示如何使用 uri、uriComponent 和 uriComponentToString：

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>后续步骤

* 有关 Bicep 文件中各部分的说明，请参阅[了解 Bicep 文件的结构和语法](./file.md)。
* 若要在创建资源类型时迭代指定的次数，请参阅[在 Bicep 中部署多个资源实例](./loop-resources.md)。
* 若要了解如何部署已创建的 Bicep 文件，请参阅[使用 Bicep 和 Azure PowerShell 部署资源](./deploy-powershell.md)。
