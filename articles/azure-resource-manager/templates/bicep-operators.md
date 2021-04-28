---
title: Bicep 运算符
description: 介绍适用于 Azure 资源管理器部署的 Bicep 运算符。
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 0838ebf6bc03f4237ef76e07f1eb6f25aa996fc0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537831"
---
# <a name="bicep-operators"></a>Bicep 运算符

本文介绍创建 Bicep 模板和使用 Azure 资源管理器部署资源时可用的 Bicep 运算符。 运算符用于计算值，比较值或计算条件。 有三种类型的 Bicep 运算符：

- [比较](#comparison)
- [逻辑](#logical)
- [numeric](#numeric)

将表达式用 `(` 和 `)` 括住，可替代默认 Bicep 运算符优先级。 例如，表达式 x + y/z 先计算除法，然后计算加法。 但表达式 (x + y)/z 则首先计算加法，然后计算除法。

## <a name="comparison"></a>比较

比较运算符用于比较值并返回 `true` 或 `false`。

| 运算符 | 名称 | 说明 |
| ---- | ---- | ---- |
| `>=` | [大于或等于](bicep-operators-comparison.md#greater-than-or-equal-) | 计算第一个值是否大于或等于第二个值。 |
| `>`  | 大于 | 计算第一个值是否大于第二个值。 |
| `<=` | [小于或等于](bicep-operators-comparison.md#less-than-or-equal-) | 计算第一个值是否小于或等于第二个值。 |
| `<`  | [小于](bicep-operators-comparison.md#less-than-) | 计算第一个值是否小于第二个值。 |
| `==` | [等于](bicep-operators-comparison.md#equals-) | 计算两个值是否相等。 |
| `!=` | [不等于](bicep-operators-comparison.md#not-equal-) | 计算两个值是否“不”相等。 |
| `=~` | [等于且不区分大小写](bicep-operators-comparison.md#equal-case-insensitive-) | 忽略大小写以确定两个值是否相等。 |
| `!~` | [不等于且不区分大小写](bicep-operators-comparison.md#not-equal-case-insensitive-) | 忽略大小写以确定两个值是否“不”相等。 |

## <a name="logical"></a>逻辑

逻辑运算符用于计算布尔值，返回非 NULL 值，或计算条件表达式。

| 运算符 | 名称 | 描述 |
| ---- | ---- | ---- |
| `&&` | [And](bicep-operators-logical.md#and-) | 如果所有值为 true，则返回 `true`。 |
| `||`| [Or](bicep-operators-logical.md#or-) | 确定任一值为 true，则返回 `true`。 |
| `!` | [Not](bicep-operators-logical.md#not-) | 对布尔值求反。 |
| `??` | [Coalesce](bicep-operators-logical.md#coalesce-) | 返回第一个非 NULL 值。 |
| `?` `:` | [条件表达式](bicep-operators-logical.md#conditional-expression--) | 计算条件的结果为 true 还是 false，并返回值。 |

## <a name="numeric"></a>Numeric

数值运算符使用整数进行计算并返回整数值。

| 运算符 | 名称 | 说明 |
| ---- | ---- | ---- |
| `*` | [乘](bicep-operators-numeric.md#multiply-) | 两个整数相乘。 |
| `/` | [除](bicep-operators-numeric.md#divide-) | 两个整数相除。 |
| `%` | [取模](bicep-operators-numeric.md#modulo-) | 两个整数相除并返回余数。 |
| `+` | [添加](bicep-operators-numeric.md#add-) | 两个整数相加。 |
| `-` | [减](bicep-operators-numeric.md#subtract--) | 两个整数相减。 |
| `-` | [一元减法](bicep-operators-numeric.md#minus--) | 将一个整数乘以 `-1`。 |

> [!NOTE]
> 减法和一元减法运算使用同一个运算符。 但功能不同，减法使用两个操作数，而一元减法只使用一个操作数。

## <a name="next-steps"></a>后续步骤

- 若要创建 Bicep 文件，请参阅[教程：创建和部署第一个 Azure 资源管理器 Bicep 文件](bicep-tutorial-create-first-bicep.md)。
- 有关如何解决 Bicep 类型错误的信息，请参阅 [Bicep 的 Any 函数](template-functions-any.md)。
- 若要比较 Bicep 和 JSON 的语法，请参阅[比较模板的 JSON 和 Bicep](compare-template-syntax.md)。
- 有关 Bicep 和 ARM 模板函数的示例，请参阅 [ARM 模板函数](template-functions.md)。
