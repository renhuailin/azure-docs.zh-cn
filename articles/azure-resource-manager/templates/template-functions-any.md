---
title: 模板函数-任意
description: 描述 Bicep 中可用于转换类型的任何函数。
ms.topic: conceptual
author: tfitzmac
ms.author: tomfitz
ms.service: azure-resource-manager
ms.subservice: templates
ms.date: 03/02/2021
ms.openlocfilehash: b0cb51c9a79d1100de7f1ef32fe326eddcdd6dcc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744512"
---
# <a name="any-function-for-bicep"></a>Bicep 的任何函数

Bicep 支持调用函数 `any()` 以解析 Bicep 类型系统中的类型错误。 当你提供的值的格式与类型系统预期的值不匹配时，可使用此函数。 例如，如果属性需要数字，但需要将其提供为字符串（如） `'0.5'` 。 使用 `any()` 函数可取消类型系统报告的错误。

此函数在 Azure 资源管理器模板运行时中不存在。 它仅供 Bicep 使用，并且不会在生成的模板的 JSON 中发出。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="any"></a>any

`any(value)`

返回与任何数据类型都兼容的值。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| 值 | 是 | 所有类型 | 要转换为兼容类型的值。 |

### <a name="return-value"></a>返回值

格式与任何数据类型兼容的值。

### <a name="examples"></a>示例

以下示例模板演示如何使用 `any()` 函数以字符串形式提供数值。

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

函数适用于在 Bicep 中指定的任何值。 下面的示例使用 `any()` 带有三元表达式的作为参数。  

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>后续步骤

有关函数的更复杂用法 `any()` ，请参阅以下示例：

* [需要特定名称的子资源](https://github.com/Azure/bicep/blob/main/docs/examples/201/api-management-create-all-resources/main.bicep#L246)
* [资源的类型中未定义的资源属性，即使该属性存在](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

