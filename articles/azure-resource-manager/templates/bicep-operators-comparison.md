---
title: Bicep 比较运算符
description: 介绍比较值的 Bicep 比较运算符。
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c382ef355131d271d9f4fa4a978a807b9aac1e4f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211229"
---
# <a name="bicep-comparison-operators"></a>Bicep 比较运算符

比较运算符比较值并返回 `true` 或 `false`。 若要运行这些示例，请使用 Azure CLI 或 Azure PowerShell 来[部署 Bicep 文件](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)。

| 运算符 | 名称 |
| ---- | ---- |
| `>=` | [大于或等于](#greater-than-or-equal-) |
| `>`  | 大于 |
| `<=` | [小于或等于](#less-than-or-equal-) |
| `<`  | [小于](#less-than-) |
| `==` | [等于](#equals-) |
| `!=` | [不等于](#not-equal-) |
| `=~` | [等于不区分大小写](#equal-case-insensitive-) |
| `!~` | [不等于不区分大小写](#not-equal-case-insensitive-) |

## <a name="greater-than-or-equal-"></a>大于或等于 >=

`operand1 >= operand2`

计算第一个值是否大于或等于第二个值。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1` | 整数、字符串 | 进行比较的第一个值。 |
| `operand2` | 整数、字符串 | 进行比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值大于或等于第二个值，返回 `true`。 否则将返回 `false`。

### <a name="example"></a>示例

比较一对整数和一对字符串。

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'A'
param secondString string = 'A'

output intGtE bool = firstInt >= secondInt
output stringGtE bool = firstString >= secondString
```

示例中的输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `intGtE` | boolean | 是 |
| `stringGtE` | boolean | 是 |

## <a name="greater-than-"></a>大于 >

`operand1 > operand2`

计算第一个值是否大于第二个值。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1` | 整数、字符串 | 进行比较的第一个值。 |
| `operand2` | 整数、字符串 | 进行比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值大于第二个值，返回 `true`。 否则将返回 `false`。

### <a name="example"></a>示例

比较一对整数和一对字符串。

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'bend'
param secondString string = 'band'

output intGt bool = firstInt > secondInt
output stringGt bool = firstString > secondString
```

示例中的输出：

“bend”中的“e”使第一个字符串更大 。

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `intGt` | boolean | 是 |
| `stringGt` | boolean | 是 |

## <a name="less-than-or-equal-"></a>小于或等于 <=

`operand1 <= operand2`

计算第一个值是否小于或等于第二个值。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1` | 整数、字符串 | 进行比较的第一个值。 |
| `operand2` | 整数、字符串 | 进行比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值小于或等于第二个值，返回 `true`。 否则将返回 `false`。

### <a name="example"></a>示例

比较一对整数和一对字符串。

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'demo'

output intLtE bool = firstInt <= secondInt
output stringLtE bool = firstString <= secondString
```

示例中的输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `intLtE` | boolean | 是 |
| `stringLtE` | boolean | 是 |

## <a name="less-than-"></a>小于 <

`operand1 < operand2`

计算第一个值是否小于第二个值。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1` | 整数、字符串 | 进行比较的第一个值。 |
| `operand2` | 整数、字符串 | 进行比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值小于第二个值，返回 `true`。 否则将返回 `false`。

### <a name="example"></a>示例

比较一对整数和一对字符串。

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'Demo'

output intLt bool = firstInt < secondInt
output stringLt bool = firstString < secondString
```

示例中的输出：

字符串为 `true`，因为小写字母小于大写字母。

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `intLt` | boolean | 是 |
| `stringLt` | boolean | 是 |

## <a name="equals-"></a>等于 ==

`operand1 == operand2`

计算值是否相等。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1` | 字符串、整数、布尔、数组、对象 | 进行比较的第一个值。 |
| `operand2` | 字符串、整数、布尔、数组、对象 | 进行比较的第二个值。 |

### <a name="return-value"></a>返回值

如果操作数都相等，返回 `true`。 如果操作数不同，返回 `false`。

### <a name="example"></a>示例

对整数、字符串和布尔值对进行比较。

```bicep
param firstInt int = 5
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'demo'

param firstBool bool = true
param secondBool bool = true

output intEqual bool = firstInt == secondInt
output stringEqual bool = firstString == secondString
output boolEqual bool = firstBool == secondBool
```

示例中的输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `intEqual` | boolean | 是 |
| `stringEqual` | boolean | 是 |
| `boolEqual` | boolean | 是 |

## <a name="not-equal-"></a>不等于 !=

`operand1 != operand2`

计算两个值是否不相等。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1` | 字符串、整数、布尔、数组、对象 | 进行比较的第一个值。 |
| `operand2` | 字符串、整数、布尔、数组、对象 | 进行比较的第二个值。 |

### <a name="return-value"></a>返回值

如果操作数不相等，返回 `true`。 如果操作数都相等，返回 `false`。

### <a name="example"></a>示例

对整数、字符串和布尔值对进行比较。

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'test'

param firstBool bool = false
param secondBool bool = true

output intNotEqual bool = firstInt != secondInt
output stringNotEqual bool = firstString != secondString
output boolNotEqual bool = firstBool != secondBool
```

示例中的输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `intNotEqual` | boolean | 是 |
| `stringNotEqual` | boolean | 是 |
| `boolNotEqual` | boolean | 是 |

## <a name="equal-case-insensitive-"></a>等于不区分大小写 =~

`operand1 =~ operand2`

忽略大小写以确定这两个值是否相等。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1`  | string | 进行比较的第一个字符串。 |
| `operand2`  | string | 进行比较的第二个字符串。 |

### <a name="return-value"></a>返回值

如果字符串相等，返回 `true`。 否则将返回 `false`。

### <a name="example"></a>示例

比较使用混合大小写字母的字符串。

```bicep
param firstString string = 'demo'
param secondString string = 'DEMO'

param thirdString string = 'demo'
param fourthString string = 'TEST'

output strEqual1 bool = firstString =~ secondString
output strEqual2 bool = thirdString =~ fourthString
```

示例中的输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `strEqual1` | boolean | 是 |
| `strEqual2` | boolean | false |

## <a name="not-equal-case-insensitive-"></a>不等于不区分大小写 !~

`operand1 !~ operand2`

忽略大小写以确定这两个值是否不相等。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1` | string | 进行比较的第一个字符串。 |
| `operand2` | string | 进行比较的第二个字符串。 |

### <a name="return-value"></a>返回值

如果字符串不相等，返回 `true`。 否则将返回 `false`。

### <a name="example"></a>示例

比较使用混合大小写字母的字符串。

```bicep
param firstString string = 'demo'
param secondString string = 'TEST'

param thirdString string = 'demo'
param fourthString string = 'DeMo'

output strNotEqual1 bool = firstString !~ secondString
output strEqual2 bool = thirdString !~ fourthString
```

示例中的输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `strNotEqual1` | boolean | 是 |
| `strNotEqual2` | boolean | false |

## <a name="next-steps"></a>后续步骤

- 若要创建 Bicep 文件，请参阅[教程：创建和部署第一个 Azure 资源管理器 Bicep 文件](bicep-tutorial-create-first-bicep.md)。
- 有关如何解决 Bicep 类型错误的信息，请参阅 [Bicep 的任何函数](template-functions-any.md)。
- 若要比较 Bicep 和 JSON 的语法，请参阅[比较模板的 JSON 和 Bicep](compare-template-syntax.md)。
- 有关 Bicep 和 ARM 模板函数的示例，请参阅 [ARM 模板函数](template-functions.md)。
