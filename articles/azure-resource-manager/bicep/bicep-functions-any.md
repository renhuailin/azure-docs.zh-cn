---
title: Bicep 函数 - any
description: 介绍了 Bicep 中可用于转换类型的 any 函数。
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: c4bf30d88f9cad54e83ad1d813242dc09e6c01a5
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025971"
---
# <a name="any-function-for-bicep"></a>Bicep 的 any 函数

Bicep 支持名为 `any()` 的函数，以解决 Bicep 类型系统中的类型错误。 当你提供的值的格式与类型系统预期的不一致时，可以使用此函数。 例如，如果属性需要数值，但你需要将其作为字符串提供，如 `'0.5'`。 使用 `any()` 函数可以抑制类型系统报告的错误。

此函数不存在于 Azure 资源管理器模板运行时中。 它只被 Bicep 使用，不会在生成的模板的 JSON 中发出。

## <a name="any"></a>any

`any(value)`

返回与任何数据类型都兼容的值。

### <a name="parameters"></a>参数

| 参数 | 必须 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| value | 是 | 所有类型 | 要转换为兼容类型的值。 |

### <a name="return-value"></a>返回值

格式与任何数据类型都兼容的值。

### <a name="examples"></a>示例

下面的示例展示了如何使用 `any()` 函数以字符串形式提供数值。

```bicep
resource wpAci 'microsoft.containerInstance/containerGroups@2019-12-01' = {
  name: 'wordpress-containerinstance'
  location: location
  properties: {
    containers: [
      {
        name: 'wordpress'
        properties: {
          ...
          resources: {
            requests: {
              cpu: any('0.5')
              memoryInGB: any('0.7')
            }
          }
        }
      }
    ]
  }
}
```

此函数应用于 Bicep 中的任何赋值。 下面的示例使用带有三元表达式作为参数的 `any()`。

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>后续步骤

有关 `any()` 函数的更复杂用法，请参阅以下示例：

* [需要特定名称的子资源](https://github.com/Azure/bicep/blob/main/docs/examples/201/api-management-create-all-resources/main.bicep#L246)
* [在资源类型中未定义的资源属性（即使它存在）](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

