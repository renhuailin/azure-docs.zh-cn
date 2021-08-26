---
title: Bicep 文件中的参数
description: 介绍如何定义 Bicep 文件中的参数。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 8701d437a34d364ff6f6e2d58cbf84dc28a79798
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634209"
---
# <a name="parameters-in-bicep"></a>Bicep 中的参数

本文介绍如何定义和使用 Bicep 文件中的参数。 为参数提供不同的值即可针对不同环境重复使用 Bicep 文件。

资源管理器会在启动部署操作之前解析参数值。 只要使用参数，资源管理器就会将其替换为解析的值。

每个参数都必需设置为[数据类型](data-types.md)之一。

## <a name="minimal-declaration"></a>最小声明

每个参数需都要一个名称和类型。 参数不能与同一范围内的变量、资源、输出或其他参数同名。

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

## <a name="decorators"></a>修饰符

参数对约束或元数据使用修饰器。 修饰器采用 `@expression` 格式，并放置在参数的声明上方。

```bicep
@expression
param stgAcctName string
```

本文在下面的部分中演示如何使用 Bicep 文件中提供的修饰器。

## <a name="secure-parameters"></a>安全参数

可以将字符串或对象参数标记为安全。 安全参数的值不会保存到部署历史记录中，不会有日志记录。

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

## <a name="allowed-values"></a>允许的值

可以为参数定义允许的值。 可在数组中提供允许的值。 如果为参数传入的值不是允许的值之一，则部署会在验证过程中失败。

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

## <a name="default-value"></a>默认值

可以为参数指定默认值。 如果在部署过程中未提供值，则使用默认值。

```bicep
param demoParam string = 'Contoso'
```

若要为参数指定默认值以及其他属性，请使用以下语法。

```bicep
@allowed([
  'Contoso'
  'Fabrikam'
])
param demoParam string = 'Contoso'
```

可以使用具有默认值的表达式。 不能在 parameters 节中使用 [reference](bicep-functions-resource.md#reference) 函数或任何 [list](bicep-functions-resource.md#list) 函数。 在解析参数时，这些函数获取资源的运行时状态，并且不能在部署之前执行。

表达式不允许有其他参数属性。

```bicep
param location string = resourceGroup().location
```

可以使用另一个参数值来生成默认值。 以下模板基于站点名称构造主机计划名称。

```bicep
param siteName string = 'site${uniqueString(resourceGroup().id)}'
param hostingPlanName string = '${siteName}-plan'
```

## <a name="length-constraints"></a>长度约束

可以为字符串和数组参数指定最小和最大长度。 可以设置一个或两个约束。 对于字符串，长度指示字符数。 对于数组，长度指示数组中的项数。

以下示例声明两个参数。 一个参数对应于一个必须具有 3-24 个字符的存储帐户名称。 另一个参数是一个必须包含 1-5 个项的数组。

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

## <a name="integer-constraints"></a>整数约束

可以为整数参数设置最小值和最大值。 可以设置一个或两个约束。

```bicep
@minValue(1)
@maxValue(12)
param month int
```

## <a name="description"></a>说明

若要帮助用户了解要提供的值，请向参数添加说明。 通过门户部署模板时，说明文本将自动用作该参数的提示。 仅当文本提供的信息超过可从参数名称推断出的信息时，才添加说明。

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

## <a name="use-parameter"></a>使用参数

若要引用参数的值，请使用参数名称。 以下示例使用参数值表示密钥保管库名称。

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

## <a name="objects-as-parameters"></a>对象作为参数

通过将相关值作为对象传入，可以更轻松地对这些值进行组织。 此方式还可以减少模板中的参数的数量。

以下示例显示的参数是一个对象。 默认值显示对象的预期属性。 定义要部署的资源时，将使用这些属性。

```bicep
param vNetSettings object = {
  name: 'VNet1'
  location: 'eastus'
  addressPrefixes: [
    {
      name: 'firstPrefix'
      addressPrefix: '10.0.0.0/22'
    }
  ]
  subnets: [
    {
      name: 'firstSubnet'
      addressPrefix: '10.0.0.0/24'
    }
    {
      name: 'secondSubnet'
      addressPrefix: '10.0.1.0/24'
    }
  ]
}
resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetSettings.name
  location: vNetSettings.location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetSettings.addressPrefixes[0].addressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSettings.subnets[0].name
        properties: {
          addressPrefix: vNetSettings.subnets[0].addressPrefix
        }
      }
      {
        name: vNetSettings.subnets[1].name
        properties: {
          addressPrefix: vNetSettings.subnets[1].addressPrefix
        }
      }
    ]
  }
}
```

## <a name="example-templates"></a>示例模板

以下示例演示了使用参数的方案。

|模板  |说明  |
|---------|---------|
|[包含用于默认值的函数的参数](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/parameterswithfunctions.bicep) | 演示了为参数定义默认值时如何使用 Bicep 函数。 Bicep 文件不部署任何资源。 它构造参数值并返回这些值。 |
|[参数对象](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/parameterobject.bicep) | 演示了将对象用于参数。 Bicep 文件不部署任何资源。 它构造参数值并返回这些值。 |

## <a name="next-steps"></a>后续步骤

- 若要了解参数的可用属性，请参阅[了解 Bicep 文件的结构和语法](file.md)。
- 若要了解如何将参数值作为文件传入，请参阅[创建 Bicep 参数文件](parameter-files.md)。
