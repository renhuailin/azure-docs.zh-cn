---
title: 模板中的参数
description: 介绍如何在 Azure 资源管理器模板（ARM 模板）中定义参数。
ms.topic: conceptual
ms.date: 05/14/2021
ms.openlocfilehash: 5c94dc3f4d37fa6c08e29e03e88dd3ba54e68271
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625518"
---
# <a name="parameters-in-arm-templates"></a>ARM 模板中的参数

本文介绍如何在 Azure 资源管理器模板（ARM 模板）中定义和使用参数。 为参数提供不同的值即可针对不同环境重复使用模板。

资源管理器会在启动部署操作之前解析参数值。 只要在模板中使用参数，资源管理器就会将其替换为解析的值。

每个参数都必需设置为[数据类型](data-types.md)之一。

## <a name="minimal-declaration"></a>最小声明

每个参数至少需要一个名称和类型。

通过 Azure 门户部署模板时，驼峰式大小写参数名称将转换为以空格分隔的名称。 例如，以下示例中的 demoString 显示为 Demo String 。 有关详细信息，请参阅[使用部署按钮从 GitHub 存储库部署模板](./deploy-to-azure-button.md)和[使用 ARM 模板和 Azure 门户部署资源](./deploy-portal.md)。

```json
"parameters": {
  "demoString": {
    "type": "string"
  },
  "demoInt": {
    "type": "int"
  },
  "demoBool": {
    "type": "bool"
  },
  "demoObject": {
    "type": "object"
  },
  "demoArray": {
    "type": "array"
  }
}
```

## <a name="secure-parameters"></a>安全参数

可以将字符串或对象参数标记为安全。 安全参数的值不会保存到部署历史记录中，不会有日志记录。

```json
"parameters": {
  "demoPassword": {
    "type": "secureString"
  },
  "demoSecretObject": {
    "type": "secureObject"
  }
}
```

## <a name="allowed-values"></a>允许的值

可以为参数定义允许的值。 可在数组中提供允许的值。 如果为参数传入的值不是允许的值之一，则部署会在验证过程中失败。

```json
"parameters": {
  "demoEnum": {
    "type": "string",
    "allowedValues": [
      "one",
      "two"
    ]
  }
}
```

## <a name="default-value"></a>默认值

可以为参数指定默认值。 如果在部署过程中未提供值，则使用默认值。

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

若要为参数指定默认值以及其他属性，请使用以下语法。

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso",
    "allowedValues": [
      "Contoso",
      "Fabrikam"
    ]
  }
}
```

可以使用具有默认值的表达式。 不能在 parameters 节中使用 [reference](template-functions-resource.md#reference) 函数或任何 [list](template-functions-resource.md#list) 函数。 在解析参数时，这些函数获取资源的运行时状态，不能在部署之前执行。

表达式不允许有其他参数属性。

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

可以使用另一个参数值来生成默认值。 以下模板基于站点名称构造主机计划名称。

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  }
}
```

## <a name="length-constraints"></a>长度约束

可以为字符串和数组参数指定最小和最大长度。 可以设置一个或两个约束。 对于字符串，长度指示字符数。 对于数组，长度指示数组中的项数。

以下示例声明两个参数。 一个参数对应于一个必须具有 3-24 个字符的存储帐户名称。 另一个参数是一个必须包含 1-5 个项的数组。

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "appNames": {
    "type": "array",
    "minLength": 1,
    "maxLength": 5
  }
}
```

## <a name="integer-constraints"></a>整数约束

可以为整数参数设置最小值和最大值。 可以设置一个或两个约束。

```json
"parameters": {
  "month": {
    "type": "int",
    "minValue": 1,
    "maxValue": 12
  }
}
```

## <a name="description"></a>说明

可以向参数添加说明，帮助模板用户了解要提供的值。 通过门户部署模板时，在说明中提供的文本自动用作该参数的提示。 仅当文本提供的信息超过可从参数名称推断出的信息时，才添加说明。

```json
"parameters": {
  "virtualMachineSize": {
    "type": "string",
    "metadata": {
      "description": "Must be at least Standard_A3 to support 2 NICs."
    },
    "defaultValue": "Standard_DS1_v2"
  }
}
```

## <a name="use-parameter"></a>使用参数

若要引用参数的值，请使用[参数](template-functions-deployment.md#parameters)函数。 以下示例使用参数值表示密钥保管库名称。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "defaultValue": "[format('keyVault{0}', uniqueString(resourceGroup().id))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2021-06-01-preview",
      "name": "[parameters('vaultName')]",
      ...
    }
  ]
}
```

## <a name="objects-as-parameters"></a>对象作为参数

通过将相关值作为对象传入，可以有序排布这些值。 此方式还可以减少模板中的参数的数量。

以下示例显示的参数是一个对象。 默认值显示对象的预期属性。 定义要部署的资源时，将使用这些属性。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vNetSettings": {
      "type": "object",
      "defaultValue": {
        "name": "VNet1",
        "location": "eastus",
        "addressPrefixes": [
          {
            "name": "firstPrefix",
            "addressPrefix": "10.0.0.0/22"
          }
        ],
        "subnets": [
          {
            "name": "firstSubnet",
            "addressPrefix": "10.0.0.0/24"
          },
          {
            "name": "secondSubnet",
            "addressPrefix": "10.0.1.0/24"
          }
        ]
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2021-02-01",
      "name": "[parameters('vNetSettings').name]",
      "location": "[parameters('vNetSettings').location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vNetSettings').addressPrefixes[0].addressPrefix]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('vNetSettings').subnets[0].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[0].addressPrefix]"
            }
          },
          {
            "name": "[parameters('vNetSettings').subnets[1].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[1].addressPrefix]"
            }
          }
        ]
      }
    }
  ]
}
```

## <a name="example-templates"></a>示例模板

以下示例演示了使用参数的方案。

|模板  |说明  |
|---------|---------|
|[包含用于默认值的函数的参数](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | 演示了为参数定义默认值时如何使用模板函数。 该模板不部署任何资源。 它构造参数值并返回这些值。 |
|[参数对象](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | 演示了将对象用于参数。 该模板不部署任何资源。 它构造参数值并返回这些值。 |

## <a name="next-steps"></a>后续步骤

* 若要了解参数的可用属性，请参阅[了解 ARM 模板的结构和语法](./syntax.md)。
* 若要了解如何以文件形式传入参数值，请参阅[创建资源管理器参数文件](parameter-files.md)。
* 有关创建参数的建议，请参阅[最佳做法 - 参数](./best-practices.md#parameters)。
