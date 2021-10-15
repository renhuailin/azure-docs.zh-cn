---
title: Bicep 函数 - 数值
description: 描述在 Bicep 文件中用于处理数字的函数。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: b5230d9fd7f228981c3ac3ec899ae8b641cc74fb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355337"
---
# <a name="numeric-functions-for-bicep"></a>Bicep 的数字函数

本文介绍用于处理整数的 Bicep 函数。

某些 Azure 资源管理器 JSON 数字函数将被替换为 [Bicep 数字运算符](./operators-numeric.md)。

## <a name="int"></a>int

`int(valueToConvert)`

将指定的值转换为整数。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| valueToConvert |是 |字符串或整数 |要转换为整数的值。 |

### <a name="return-value"></a>返回值

转换后的值的整数。

### <a name="example"></a>示例

以下示例将用户提供的参数值转换为整数。

```bicep
param stringToConvert string = '4'

output inResult int = int(stringToConvert)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| intResult | int | 4 |

## <a name="max"></a>max

`max (arg1)`

返回整数数组或逗号分隔的整数列表中的最大值。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |整数数组或逗号分隔的整数列表 |要获取最大值的集合。 |

### <a name="return-value"></a>返回值

一个整数，表示集合中的最大值。

### <a name="example"></a>示例

以下示例演示如何对整数数组和整数列表使用 max：

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

## <a name="min"></a>min

`min (arg1)`

返回整数数组或逗号分隔的整数列表中的最小值。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |整数数组或逗号分隔的整数列表 |要获取最小值的集合。 |

### <a name="return-value"></a>返回值

一个整数，表示集合中的最小值。

### <a name="example"></a>示例

以下示例演示如何对整数数组和整数列表使用 min：

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

## <a name="next-steps"></a>后续步骤

* 有关涉及数字的其他操作，请参阅 [Bicep 数字运算符](./operators-numeric.md)。
