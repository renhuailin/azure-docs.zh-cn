---
title: Bicep 运算符
description: 介绍适用于 Azure 资源管理器部署的 Bicep 运算符。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 80fc9e4e1285d86858a476feba30621a7afe1c79
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221039"
---
# <a name="bicep-operators"></a>Bicep 运算符

本文介绍 Bicep 运算符。 运算符用于计算值，比较值或计算条件。 有 4 种类型的 Bicep 运算符：

- [取值函数 (accessor)](#accessor)
- [比较](#comparison)
- [逻辑](#logical)
- [numeric](#numeric)

## <a name="operator-precedence-and-associativity"></a>运算符优先级和关联性

下面的运算符按优先级降序列出（位置越靠近顶部，优先级越高）。 同一层上列出的运算符具有相等的优先级。

| 符号 | 运算类型 | 结合性 |
|:-|:-|:-|
| `(` `)` `[` `]` `.` `::` | 括号、数组索引器、属性访问器和嵌套资源访问器  | 从左到右 |
| `!` `-` | 一元 | 从右到左 |
| `%` `*` `/` | 乘法 | 从左到右 |
| `+` `-` | 加法 | 从左到右 |
| `<=` `<` `>` `>=` | 关系 | 从左到右 |
| `==` `!=` `=~` `!~` | 相等 | 从左到右 |
| `&&` | 逻辑与 | 从左到右 |
| `||` | 逻辑或 | 从左到右 |
| `?` `:` | 条件表达式（三元） | 从右到左
| `??` | Coalesce | 从左到右

将表达式用 `(` 和 `)` 括住，可替代默认 Bicep 运算符优先级。 例如，表达式 x + y/z 先计算除法，然后计算加法。 但表达式 (x + y)/z 则首先计算加法，然后计算除法。

## <a name="accessor"></a>访问器

访问器运算符用于访问对象上的嵌套资源和属性。

| 运算符 | 名称 | 说明 |
| ---- | ---- | ---- |
| `[]` | [索引访问器](./operators-access.md#index-accessor) | 访问对象上的数组或属性的元素。 |
| `.` | [函数访问器](./operators-access.md#function-accessor) | 在资源上调用函数。 |
| `::` | [嵌套资源访问器](./operators-access.md#nested-resource-accessor) | 从父资源外部访问嵌套资源。 |
| `.` | [属性访问器](./operators-access.md#property-accessor)。 | 访问对象的属性。 |

## <a name="comparison"></a>比较

比较运算符用于比较值并返回 `true` 或 `false`。

| 运算符 | 名称 | 说明 |
| ---- | ---- | ---- |
| `>=` | [大于或等于](./operators-comparison.md#greater-than-or-equal-) | 计算第一个值是否大于或等于第二个值。 |
| `>`  | 大于 | 计算第一个值是否大于第二个值。 |
| `<=` | [小于或等于](./operators-comparison.md#less-than-or-equal-) | 计算第一个值是否小于或等于第二个值。 |
| `<`  | [小于](./operators-comparison.md#less-than-) | 计算第一个值是否小于第二个值。 |
| `==` | [等于](./operators-comparison.md#equals-) | 计算两个值是否相等。 |
| `!=` | [不等于](./operators-comparison.md#not-equal-) | 计算两个值是否“不”相等。 |
| `=~` | [等于且不区分大小写](./operators-comparison.md#equal-case-insensitive-) | 忽略大小写以确定两个值是否相等。 |
| `!~` | [不等于且不区分大小写](./operators-comparison.md#not-equal-case-insensitive-) | 忽略大小写以确定两个值是否“不”相等。 |

## <a name="logical"></a>逻辑

逻辑运算符用于计算布尔值，返回非 NULL 值，或计算条件表达式。

| 运算符 | 名称 | 描述 |
| ---- | ---- | ---- |
| `&&` | [And](./operators-logical.md#and-) | 如果所有值为 true，则返回 `true`。 |
| `||`| [Or](./operators-logical.md#or-) | 确定任一值为 true，则返回 `true`。 |
| `!` | [Not](./operators-logical.md#not-) | 对布尔值求反。 |
| `??` | [Coalesce](./operators-logical.md#coalesce-) | 返回第一个非 NULL 值。 |
| `?` `:` | [条件表达式](./operators-logical.md#conditional-expression--) | 计算条件的结果为 true 还是 false，并返回值。 |

## <a name="numeric"></a>Numeric

数值运算符使用整数进行计算并返回整数值。

| 运算符 | 名称 | 说明 |
| ---- | ---- | ---- |
| `*` | [乘](./operators-numeric.md#multiply-) | 两个整数相乘。 |
| `/` | [除](./operators-numeric.md#divide-) | 两个整数相除。 |
| `%` | [取模](./operators-numeric.md#modulo-) | 两个整数相除并返回余数。 |
| `+` | [添加](./operators-numeric.md#add-) | 两个整数相加。 |
| `-` | [减](./operators-numeric.md#subtract--) | 两个整数相减。 |
| `-` | [一元减法](./operators-numeric.md#minus--) | 将一个整数乘以 `-1`。 |

> [!NOTE]
> 减法和一元减法运算使用同一个运算符。 但功能不同，减法使用两个操作数，而一元减法只使用一个操作数。


## <a name="next-steps"></a>后续步骤

- 若要创建 Bicep 文件，请参阅[快速入门：使用 Visual Studio Code 创建 Bicep 文件](./quickstart-create-bicep-use-visual-studio-code.md)。
- 有关如何解决 Bicep 类型错误的信息，请参阅 [Bicep 的 Any 函数](./bicep-functions-any.md)。
- 若要比较 Bicep 和 JSON 的语法，请参阅[比较模板的 JSON 和 Bicep](./compare-template-syntax.md)。
- 有关 Bicep 函数的示例，请参阅 [Bicep 函数](./bicep-functions.md)。
