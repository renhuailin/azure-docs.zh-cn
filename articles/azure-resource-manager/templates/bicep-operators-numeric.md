---
title: Bicep 数字运算符
description: 介绍计算值的 Bicep 数字运算符。
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: f2c7f722a3f13648c94b69039d31e5095a3256f7
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211228"
---
# <a name="bicep-numeric-operators"></a>Bicep 数字运算符

此数字运算符使用整数进行计算并返回整数值。 若要运行这些示例，请使用 Azure CLI 或 Azure PowerShell 来[部署 Bicep 文件](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)。

| 操作员 | 名称 |
| ---- | ---- |
| `*` | [乘](#multiply-) |
| `/` | [除](#divide-) |
| `%` | [取模](#modulo-) |
| `+` | [添加](#add-) |
| `-` | [减](#subtract--) |
| `-` | [一元减法](#minus--) |

> [!NOTE]
> 减法和一元减法运算使用同一个运算符。 但功能不同，减法使用两个操作数，而一元减法只使用一个操作数。

## <a name="multiply-"></a>乘 *

`operand1 * operand2`

两个整数相乘。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1`  | integer | 要乘以的数。 |
| `operand2`  | integer  | 数字的乘数。 |

### <a name="return-value"></a>返回值

乘法以整数形式返回乘积。

### <a name="example"></a>示例

两个整数相乘并返回乘积。

```bicep
param firstInt int = 5
param secondInt int = 2

output product int = firstInt * secondInt
```

示例中的输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `product` | integer | 10 |

## <a name="divide-"></a>除 /

`operand1 / operand2`

整数除以整数。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1` | integer | 被除的整数。 |
| `operand2` | integer | 作为除数的整数。 不能为零。 |

### <a name="return-value"></a>返回值

除法以整数形式返回商。

### <a name="example"></a>示例

将两个整数相除并返回商。

```bicep
param firstInt int = 10
param secondInt int = 2

output quotient int = firstInt / secondInt
```

示例中的输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `quotient` | integer | 5 |

## <a name="modulo-"></a>取模 %

`operand1 % operand2`

将整数除以整数并返回余数。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1`  | integer  | 被除的整数。 |
| `operand2`  | integer  | 作为除数的整数。 不能为 0。 |

### <a name="return-value"></a>返回值

余数以整数形式返回。 如果相除不产生余数，则返回 0。

### <a name="example"></a>示例

两对整数相除并返回余数。

```bicep
param firstInt int = 10
param secondInt int = 3

param thirdInt int = 8
param fourthInt int = 4

output remainder int = firstInt % secondInt
output zeroRemainder int = thirdInt % fourthInt
```

示例中的输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `remainder` | integer | 1 |
| `zeroRemainder` | integer | 0 |

## <a name="add-"></a>加 +

`operand1 + operand2`

两个整数相加。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1` | integer | 要添加的数。 |
| `operand2` | integer | 要添加到一个数的数。 |

### <a name="return-value"></a>返回值

加法以整数形式返回和。

### <a name="example"></a>示例

两个整数相加并返回总和。

```bicep
param firstInt int = 10
param secondInt int = 2

output sum int = firstInt + secondInt
```

示例中的输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `sum` | integer | 12 |

## <a name="subtract--"></a>相减 -

`operand1 - operand2`

从一个整数中减去另一个整数。

### <a name="operands"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `operand1` | integer | 作为被减数的较大数。 |
| `operand2` | integer | 从较大数中减去的数。 |

### <a name="return-value"></a>返回值

减法以整数形式返回差。

### <a name="example"></a>示例

减去一个整数并返回差。

```bicep
param firstInt int = 10
param secondInt int = 4

output difference int = firstInt - secondInt
```

示例中的输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `difference` | integer | 6 |

## <a name="minus--"></a>减 -

`-integerValue`

将一个整数乘以 `-1`。

### <a name="operand"></a>操作数

| 操作数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `integerValue` | integer | 整数乘以 `-1`。 |

### <a name="return-value"></a>返回值

将一个整数乘以 `-1`。 正整数返回一个负整数，负整数返回一个正整数。 可以用括号将这些值括起来。

### <a name="example"></a>示例

```bicep
param posInt int = 10
param negInt int = -20

output startedPositive int = -posInt
output startedNegative int = -(negInt)
```

示例中的输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `startedPositive` | integer | -10 |
| `startedNegative` | integer | 20 |

## <a name="next-steps"></a>后续步骤

- 若要创建 Bicep 文件，请参阅[教程：创建和部署第一个 Azure 资源管理器 Bicep 文件](bicep-tutorial-create-first-bicep.md)。
- 有关如何解决 Bicep 类型错误的信息，请参阅 [Bicep 的任何函数](template-functions-any.md)。
- 若要比较 Bicep 和 JSON 的语法，请参阅[比较模板的 JSON 和 Bicep](compare-template-syntax.md)。
- 有关 Bicep 和 ARM 模板函数的示例，请参阅 [ARM 模板函数](template-functions.md)。
