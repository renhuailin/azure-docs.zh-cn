---
title: 模板函数 - 比较
description: 介绍可在 Azure 资源管理器模板（ARM 模板）中使用的用于比较值的函数。
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 0f9243cdc61ad5e7710663328eb4f85c9471fda5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812349"
---
# <a name="comparison-functions-for-arm-templates"></a>ARM 模板的比较函数

资源管理器提供了多个用于在 Azure 资源管理器模板（ARM 模板）中进行比较的函数：

* [coalesce](#coalesce)
* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

## <a name="coalesce"></a>coalesce

`coalesce(arg1, arg2, arg3, ...)`

从参数中返回第一个非 null 值。 空字符串、空数组和空对象不为 null。

在 Bicep 中，改用 `??` 运算符。 请参阅[联合 ??](../bicep/operators-logical.md#coalesce-)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int、string、array 或 object |要测试是否为 null 的第一个值。 |
| 其他参数 |否 |int、string、array 或 object | 要测试是否为 null 的其他值。 |

### <a name="return-value"></a>返回值

第一个非 null 参数的值，可以是字符串、整数、数组或对象。 如果所有参数都为 null，则为 null。

### <a name="example"></a>示例

以下示例模板显示 coalesce 不同用法的输出。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/coalesce.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| stringOutput | String | 默认值 |
| intOutput | int | 1 |
| objectOutput | Object | {"first": "default"} |
| arrayOutput | Array | [1] |
| emptyOutput | Bool | True |

## <a name="equals"></a>equals

`equals(arg1, arg2)`

检查两个值是否相等。

在 Bicep 中，改用 `==` 运算符。 请参阅[等于 = =](../bicep/operators-comparison.md#equals-)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int、string、array 或 object |要检查是否相等的第一个值。 |
| arg2 |是 |int、string、array 或 object |要检查是否相等的第二个值。 |

### <a name="return-value"></a>返回值

如果值相等，返回 **True**；否则返回 **False**。

### <a name="remarks"></a>备注

equals 函数通常与 `condition` 元素一起使用来测试资源是否已部署。

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('storageAccountName')]",
  "apiVersion": "2017-06-01",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

### <a name="example"></a>示例

以下示例检查不同类型的值是否相等。 所有默认值都返回 True。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/equals.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

以下示例模板结合使用 [not](template-functions-logical.md#not) 和 **equals**。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/not-equals.json":::

前述示例的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="greater"></a>greater

`greater(arg1, arg2)`

检查第一个值是否大于第二个值。

在 Bicep 中，改用 `>` 运算符。 请参阅[大于 >](../bicep/operators-comparison.md#greater-than-)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int 或 string |用于大于比较的第一个值。 |
| arg2 |是 |int 或 string |用于大于比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值大于第二个值，返回 **True**；否则返回 **False**。

### <a name="example"></a>示例

以下示例检查一个值是否大于另一个值。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/greater.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

检查第一个值是否大于或等于第二个值。

在 Bicep 中，改用 `>=` 运算符。 请参阅[大于或等于 >=](../bicep/operators-comparison.md#greater-than-or-equal-)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int 或 string |用于大于或等于比较的第一个值。 |
| arg2 |是 |int 或 string |用于大于或等于比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值大于或等于第二个值，返回 **True**；否则返回 **False**。

### <a name="example"></a>示例

以下示例检查一个值是否大于等于另一个值。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/greaterorequals.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

## <a name="less"></a>less

`less(arg1, arg2)`

检查第一个值是否小于第二个值。

在 Bicep 中，改用 `<` 运算符。 请参阅[小于 <](../bicep/operators-comparison.md#less-than-)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int 或 string |用于小于比较的第一个值。 |
| arg2 |是 |int 或 string |用于小于比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值小于第二个值，返回 **True**；否则返回 **False**。

### <a name="example"></a>示例

以下示例检查一个值是否小于另一个值。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/less.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

检查第一个值是否小于或等于第二个值。

在 Bicep 中，改用 `<=` 运算符。 请参阅[小于或等于 <=](../bicep/operators-comparison.md#less-than-or-equal-)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int 或 string |用于小于或等于比较的第一个值。 |
| arg2 |是 |int 或 string |用于小于或等于比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值小于或等于第二个值，返回 **True**；否则返回 **False**。

### <a name="example"></a>示例

以下示例检查一个值是否小于等于另一个值。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/lessorequals.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

## <a name="next-steps"></a>后续步骤

* 有关 ARM 模板中各部分的说明，请参阅[了解 ARM 模板的结构和语法](./syntax.md)。
