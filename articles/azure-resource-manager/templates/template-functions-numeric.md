---
title: 模板函数 - 数值
description: 介绍了可在 Azure 资源管理器模板（ARM 模板）中用来处理数字的函数。
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: ccfed6794b81b7910310cc5a9fd02dcf0cdb0e0f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820429"
---
# <a name="numeric-functions-for-arm-templates"></a>ARM 模板的数值函数

资源管理器提供了以下可用于在 Azure 资源管理器模板（ARM 模板）中处理整数的函数：

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

## <a name="add"></a>add

`add(operand1, operand2)`

返回提供的两个整数的总和。

Bicep 不支持 `add` 函数。 请改用 [`+` 运算符](../bicep/operators-numeric.md#add-)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
|operand1 |是 |int |被加数。 |
|operand2 |是 |int |加数。 |

### <a name="return-value"></a>返回值

一个整数，包含参数的总和。

### <a name="example"></a>示例

以下示例将添加两个参数。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/add.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| addResult | int | 8 |

## <a name="copyindex"></a>copyIndex

`copyIndex(loopName, offset)`

返回迭代循环的索引。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| loopName | 否 | string | 用于获取迭代的循环的名称。 |
| offset |否 |int |要添加到的从零开始的迭代值的数字。 |

### <a name="remarks"></a>备注

此函数始终用于 **copy** 对象。 如果没有提供任何值作为 **偏移量**，则返回当前迭代值。 迭代值从零开始。

**loopName** 属性可用于指定 copyIndex 是引用资源迭代还是引用属性迭代。 如果没有为 **loopName** 提供值，则将使用当前的资源类型迭代。 在属性上迭代时，请为 **loopName** 提供值。

有关使用 copy 的详细信息，请参阅：

* [ARM 模板中的资源迭代](copy-resources.md)
* [ARM 模板中的属性迭代](copy-properties.md)
* [ARM 模板中的变量迭代](copy-variables.md)
* [ARM 模板中的输出迭代](copy-outputs.md)

### <a name="example"></a>示例

以下示例显示名称中包含 copy 循环和索引值。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/copyindex.json":::

### <a name="return-value"></a>返回值

一个表示迭代的当前索引的整数。

## <a name="div"></a>div

`div(operand1, operand2)`

返回提供的两个整数在整除后的商。

Bicep 不支持 `div` 函数。 请改用 [`/` 运算符](../bicep/operators-numeric.md#divide-)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| operand1 |是 |int |被除数。 |
| operand2 |是 |int |除数。 不能为 0。 |

### <a name="return-value"></a>返回值

一个表示商的整数。

### <a name="example"></a>示例

以下示例将一个参数除以另一个参数。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/div.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| divResult | int | 2 |

## <a name="float"></a>float

`float(arg1)`

将值转换为浮点数。 仅当将自定义参数传递给应用程序（例如，逻辑应用）时，才使用此函数。

Bicep 不支持 `float` 函数。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |字符串或整数 |要转换为浮点数的值。 |

### <a name="return-value"></a>返回值

一个浮点数。

### <a name="example"></a>示例

以下示例演示如何使用 float 将参数传递给逻辑应用：

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/float.json":::

## <a name="int"></a>int

`int(valueToConvert)`

将指定的值转换为整数。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| valueToConvert |是 |字符串或整数 |要转换为整数的值。 |

### <a name="return-value"></a>返回值

转换后的值的整数。

### <a name="example"></a>示例

以下示例模板将用户提供的参数值转换为整数。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/int.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| intResult | int | 4 |

## <a name="max"></a>max

`max (arg1)`

返回整数数组或逗号分隔的整数列表中的最大值。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |整数数组或逗号分隔的整数列表 |要获取最大值的集合。 |

### <a name="return-value"></a>返回值

一个整数，表示集合中的最大值。

### <a name="example"></a>示例

以下示例演示如何对整数数组和整数列表使用 max。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/max.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

## <a name="min"></a>min

`min (arg1)`

返回整数数组或逗号分隔的整数列表中的最小值。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |整数数组或逗号分隔的整数列表 |要获取最小值的集合。 |

### <a name="return-value"></a>返回值

一个整数，表示集合中的最小值。

### <a name="example"></a>示例

以下示例演示如何对整数数组和整数列表使用 min。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/min.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

## <a name="mod"></a>mod

`mod(operand1, operand2)`

返回使用提供的两个整数整除后的余数。

Bicep 不支持 `mod` 函数。 请改用 [% 运算符](../bicep/operators-numeric.md#modulo-)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| operand1 |是 |int |被除数。 |
| operand2 |是 |int |除数，不能为 0。 |

### <a name="return-value"></a>返回值

一个表示余数的整数。

### <a name="example"></a>示例

以下示例将返回一个参数除以另一个参数后所得的余数。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/mod.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| modResult | int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

返回提供的两个整数的积。

Bicep 不支持 `mul` 函数。 请改用 [* 运算符](../bicep/operators-numeric.md#multiply-)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| operand1 |是 |int |被乘数。 |
| operand2 |是 |int |乘数。 |

### <a name="return-value"></a>返回值

一个表示积的整数。

### <a name="example"></a>示例

以下示例将一个参数乘以另一个参数。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/mul.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| mulResult | int | 15 |

## <a name="sub"></a>sub

`sub(operand1, operand2)`

返回提供的两个整数在相减后的结果。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| operand1 |是 |int |被减数。 |
| operand2 |是 |int |减数。 |

### <a name="return-value"></a>返回值

一个表示减后结果的整数。

### <a name="example"></a>示例

以下示例将一个参数减另一个参数。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/sub.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| subResult | int | 4 |

## <a name="next-steps"></a>后续步骤

* 有关 ARM 模板中各部分的说明，请参阅[了解 ARM 模板的结构和语法](./syntax.md)。
* 若要在创建资源类型时迭代指定的次数，请参阅 [ARM 模板中的资源迭代](copy-resources.md)。
