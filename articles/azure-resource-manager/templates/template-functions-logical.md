---
title: 模板函数 - 逻辑
description: 介绍 Azure 资源管理器模板（ARM 模板）中用于确定逻辑值的函数。
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: b94f7aa38c708278f2ccf54a5592016873fcd285
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744353"
---
# <a name="logical-functions-for-arm-templates"></a>ARM 模板的逻辑函数

资源管理器提供了多个用于在 Azure 资源管理器模板（ARM 模板）中进行比较的函数：

* [and](#and)
* [bool](#bool)
* [false](#false)
* [if](#if)
* [not](#not)
* [or](#or)
* true

## <a name="and"></a>and

`and(arg1, arg2, ...)`

检查所有参数值是否均为 true。

Bicep 不支持 `and` 函数，请改用 [&& 运算符](../bicep/operators-logical.md#and-)。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |boolean |要检查是否为 true 的第一个值。 |
| arg2 |是 |boolean |要检查是否为 true 的第二个值。 |
| 更多参数 |否 |boolean |用于检查是否为 true 的更多参数。 |

### <a name="return-value"></a>返回值

如果所有值均为 true，则返回 True；否则返回 False 。

### <a name="examples"></a>示例

以下示例演示如何使用逻辑函数。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/andornot.json":::

前述示例的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>bool

`bool(arg1)`

将参数转换为布尔值。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |字符串或整数 |要转换为布尔值的值。 |

### <a name="return-value"></a>返回值

转换后的值的布尔值。

### <a name="remarks"></a>备注

还可以使用 [true()](#true) 和 [false()](#false) 来获取布尔值。

### <a name="examples"></a>示例

以下示例演示如何对字符串或整数使用 bool。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/bool.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="false"></a>false

`false()`

返回 false。

`false` 函数在 Bicep 中不可用。  请改用 `false` 关键字。

### <a name="parameters"></a>参数

false 函数不接受任何参数。

### <a name="return-value"></a>返回值

一个布尔值，该值始终为 false。

### <a name="example"></a>示例

下面的示例返回一个 false 输出值。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/false.json":::

前述示例的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| falseOutput | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

根据条件为 true 或 false 返回值。

Bicep 不支持 `if` 函数。 请改用 [?: 运算符](../bicep/operators-logical.md#conditional-expression--)。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| condition |是 |boolean |要检查是为 true 还是为 false 的值。 |
| trueValue |是 | 字符串、int、对象或数组 |条件为 true 时返回的值。 |
| falseValue |是 | 字符串、int、对象或数组 |条件为 false 时返回的值。 |

### <a name="return-value"></a>返回值

如果第一个参数为 True，则返回第二个参数；否则返回第三个参数。

### <a name="remarks"></a>备注

条件为 **True** 时，仅评估 true 值。 条件为 **False** 时，仅评估 false 值。 使用 `if` 函数时，可以包含仅在特定条件下有效的表达式。 例如，可以引用一个资源，该资源在某个条件下存在，在另一个条件下不存在。 以下部分显示了一个条件性评估表达式的示例。

### <a name="examples"></a>示例

以下示例演示如何使用 `if` 函数。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/if.json":::

前述示例的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| yesOutput | String | 是 |
| noOutput | String | 否 |
| objectOutput | Object | { "test": "value1" } |

以下[示例模板](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json)演示了如何将此函数与仅在特定条件下有效的表达式配合使用。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "logAnalytics": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
    {
      "condition": "[not(empty(parameters('logAnalytics')))]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
      "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
        },
        "protectedSettings": {
          "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
        }
      }
    }
  ],
  "outputs": {
    "mgmtStatus": {
      "type": "string",
      "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
    }
  }
}
```

## <a name="not"></a>not

`not(arg1)`

将布尔值转换为其相反值。

Bicep 不支持 `not` 函数，请改用 [! 运算符](../bicep/operators-logical.md#not-)。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |boolean |要转换的值。 |

### <a name="return-value"></a>返回值

参数为 False 时返回 True 。 参数为 True 时返回 False 。

### <a name="examples"></a>示例

以下示例演示如何使用逻辑函数。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/andornot.json":::

前述示例的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

以下示例结合使用 `not` 和 [equals](template-functions-comparison.md#equals)。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/not-equals.json":::

前述示例的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>或

`or(arg1, arg2, ...)`

检查任何参数值是否为 true。

Bicep 不支持 `or` 函数，请改用 [|| 运算符](../bicep/operators-logical.md#or-)。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |boolean |要检查是否为 true 的第一个值。 |
| arg2 |是 |boolean |要检查是否为 true 的第二个值。 |
| 更多参数 |否 |boolean |用于检查是否为 true 的更多参数。 |

### <a name="return-value"></a>返回值

如果任何值为 true，则返回 True；否则返回 False 。

### <a name="examples"></a>示例

以下示例演示如何使用逻辑函数。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/andornot.json":::

前述示例的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="true"></a>是

`true()`

返回 true。

`true` 函数在 Bicep 中不可用。  请改用 `true` 关键字。

### <a name="parameters"></a>参数

true 函数不接受任何参数。

### <a name="return-value"></a>返回值

一个布尔值，该值始终为 true。

### <a name="example"></a>示例

下面的示例返回一个 true 输出值。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/true.json":::

前述示例的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| trueOutput | Bool | True |

## <a name="next-steps"></a>后续步骤

* 有关 ARM 模板中各部分的说明，请参阅[了解 ARM 模板的结构和语法](./syntax.md)。
