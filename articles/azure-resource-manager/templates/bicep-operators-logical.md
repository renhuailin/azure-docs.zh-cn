---
title: Bicep 逻辑运算符
description: 介绍用于计算条件的 Bicep 逻辑运算符。
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 1eecbe589ecf08e32246dd97d137c60fa7b5078f
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353304"
---
# <a name="bicep-logical-operators"></a>Bicep 逻辑运算符

逻辑运算符用于计算布尔值，返回非 NULL 值，或计算条件表达式。 若要运行示例，请使用 Azure CLI 或 Azure PowerShell 来[部署 Bicep 文件](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)。

| 操作员 | 名称 |
| ---- | ---- |
| `&&` | [And](#and-) |
| `||` | [Or](#or-) |
| `!` | [Not](#not-) |
| `??` | [Coalesce](#coalesce-) |
| `?` `:` | [条件表达式](#conditional-expression--) |

## <a name="and-"></a>与 &&

`operand1 && operand2`

确定两个值是否都为 true。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1` | boolean | 要检查是否为 true 的第一个值。 |
| `operand2` | boolean | 要检查是否为 true 的第二个值。 |
| 更多操作数 | boolean | 可以包括更多操作数。 |

### <a name="return-value"></a>返回值

如果两个值均为 true，则返回 `True`，否则返回 `false`。

### <a name="example"></a>示例

计算一组参数值和一组表达式。

```bicep
param operand1 bool = true
param operand2 bool = true

output andResultParm bool = operand1 && operand2
output andResultExp bool = bool(10 >= 10) && bool(5 > 2)
```

示例输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `andResultParm` | boolean | 是 |
| `andResultExp` | boolean | 是 |

## <a name="or-"></a>或 ||

`operand1 || operand2`

确定是否有一个值为 true。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1` | boolean | 要检查是否为 true 的第一个值。 |
| `operand2` | boolean | 要检查是否为 true 的第二个值。 |
| 更多操作数 | boolean | 可以包括更多操作数。 |

### <a name="return-value"></a>返回值

如果任一值为 true，则返回 `True`，否则返回 `false`。

### <a name="example"></a>示例

计算一组参数值和一组表达式。

```bicep
param operand1 bool = true
param operand2 bool = false

output orResultParm bool = operand1 || operand2
output orResultExp bool = bool(10 >= 10) || bool(5 < 2)
```

示例输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `orResultParm` | boolean | 是 |
| `orResultExp` | boolean | 是 |

## <a name="not-"></a>非 !

`!boolValue`

对布尔值求反。

### <a name="operand"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `boolValue` | boolean | 求反的布尔值。 |

### <a name="return-value"></a>返回值

对初始值求反，并返回一个布尔值。 如果初始值为 `true`，则返回 `false`。

### <a name="example"></a>示例

`not` 运算符对值求反。 可以用括号将值括起来。

```bicep
param initTrue bool = true
param initFalse bool = false

output startedTrue bool = !(initTrue)
output startedFalse bool = !initFalse
```

示例输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `startedTrue` | boolean | false |
| `startedFalse` | boolean | 是 |

## <a name="coalesce-"></a>Coalesce ??

`operand1 ?? operand2`

从操作数返回第一个非 NULL 值。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1` | 字符串、整数、布尔值、对象、数组 | 检测是否为 `null` 的值。 |
| `operand2` | 字符串、整数、布尔值、对象、数组 | 检测是否为 `null` 的值。 |
| 更多操作数 | 字符串、整数、布尔值、对象、数组 | 检测是否为 `null` 的值。 |

### <a name="return-value"></a>返回值

返回第一个非 NULL 值。 空字符串、空数组和空对象不是 `null` 值，并且会返回 \<empty> 值。

### <a name="example"></a>示例

输出语句返回非 NULL 值。 输出类型必须与比较中的类型匹配，否则会生成错误。

```bicep
param myObject object = {
  'isnull1': null
  'isnull2': null
  'string': 'demoString'
  'emptystr': ''
  'integer': 10
  }

output nonNullStr string = myObject.isnull1 ?? myObject.string ?? myObject.isnull2
output nonNullInt int = myObject.isnull1 ?? myObject.integer ?? myObject.isnull2
output nonNullEmpty string = myObject.isnull1 ?? myObject.emptystr ?? myObject.string ?? myObject.isnull2
```

示例输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `nonNullStr` | 字符串 | demoString |
| `nonNullInt` | int | 10 |
| `nonNullEmpty` | string | \<empty> |

## <a name="conditional-expression--"></a>条件表达式 ? :

`condition ? true-value : false-value`

计算条件并返回一个值，指示条件为 true 还是 false。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `condition` | boolean | 计算结果为 true 或 false 的条件。 |
| `true-value` | 字符串、整数、布尔值、对象、数组 | 条件为 true 时的值。 |
| `false-value` | 字符串、整数、布尔值、对象、数组 | 条件为 false 时的值。 |

### <a name="example"></a>示例

此示例计算参数的初始值，并返回一个指示条件为 true 或 false 的值。

```bicep
param initValue bool = true

output outValue string = initValue ? 'true value' : 'false value'
```

示例输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `outValue` | 字符串 | true 值 |

## <a name="next-steps"></a>后续步骤

- 若要创建 Bicep 文件，请参阅[教程：创建和部署第一个 Azure 资源管理器 Bicep 文件](bicep-tutorial-create-first-bicep.md)。
- 有关如何解决 Bicep 类型错误的信息，请参阅 [Bicep 的 Any 函数](template-functions-any.md)。
- 若要比较 Bicep 和 JSON 的语法，请参阅[比较模板的 JSON 和 Bicep](compare-template-syntax.md)。
- 有关 Bicep 和 ARM 模板函数的示例，请参阅 [ARM 模板函数](template-functions.md)。
