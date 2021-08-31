---
title: Bicep 访问器运算符
description: 介绍 Bicep 资源访问运算符和属性访问运算符。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: addf6f552d6c409c77a11d666b8b9ade619ca8f2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746620"
---
# <a name="bicep-accessor-operators"></a>Bicep 访问器运算符

访问器运算符用于访问对象上的子资源和属性。 还可以使用属性访问器来使用某些函数。

| 运算符 | 名称 |
| ---- | ---- |
| `::` | [嵌套资源访问器](#nested-resource-accessor) |
| `.`  | [属性访问器](#property-accessor) |
| `.`  | [函数访问器](#function-accessor) |

## <a name="nested-resource-accessor"></a>嵌套资源访问器

`parentResource::nestedResource`

嵌套资源是在另一资源内声明的资源。 使用嵌套资源访问器 `::` 从父资源外部访问该嵌套资源。

在父资源内，仅使用符号名称来引用嵌套资源。 从父资源外部引用嵌套资源时，只需使用嵌套资源访问器。

### <a name="example"></a>示例

以下示例显示如何从父资源内部和父资源外部引用嵌套资源。

```bicep
resource demoParent 'demo.Rp/parentType@2020-01-01' = {
  name: 'demoParent'
  location: 'West US'

  // Declare a nested resource within 'demoParent'
  resource demoNested 'childType' = {
    name: 'demoNested'
    properties: {
      displayName: 'The nested instance.'
    }
  }

  // Declare another nested resource
  resource demoSibling 'childType' = {
    name: 'demoSibling'
    properties: {
      // Use symbolic name to reference because this line is within demoParent
      displayName: 'Sibling of ${demoNested.properties.displayName}'
    }
  }
}

// Use nested accessor to reference because this line is outside of demoParent
output displayName string = demoParent::demoNested.properties.displayName
```

## <a name="property-accessor"></a>属性访问器

`objectName.propertyName`

使用属性访问器来访问对象的属性。 属性访问器可与任何对象一起使用，包括作为对象的参数和变量。 在不是对象的表达式上使用属性访问时，会收到错误。

### <a name="example"></a>示例

以下示例显示一个对象变量以及如何访问属性。

```bicep
var x = {
  y: {
    z: 'Hello'
    a: true
  }
  q: 42
}

output outputZ string = x.y.z
output outputQ int = x.q
```

示例中的输出：

| 名称 | 类型 | 值 |
| ---- | ---- | ---- |
| `outputZ` | 字符串 | 'Hello' |
| `outputQ` | integer | 42 |

通常，将属性访问器与在 Bicep 文件中部署的资源一起使用。 以下示例创建一个公共 IP 地址，并使用属性访问器从部署的资源返回值。

```bicep
resource publicIp 'Microsoft.Network/publicIPAddresses@2020-06-01' = {
  name: publicIpResourceName
  location: location
  properties: {
    publicIPAllocationMethod: dynamicAllocation ? 'Dynamic' : 'Static'
    dnsSettings: {
      domainNameLabel: publicIpDnsLabel
    }
  }
}

// Use property accessor to get value
output ipFqdn string = publicIp.properties.dnsSettings.fqdn
```

## <a name="function-accessor"></a>函数访问器

`resourceName.functionName()`

两个函数 - [getSecret](bicep-functions-resource.md#getsecret) 和 [list*](bicep-functions-resource.md#list) - 支持用于调用函数的访问器运算符。 只有这两个函数支持访问器运算符。

### <a name="example"></a>示例

以下示例引用现有密钥保管库，然后使用 `getSecret` 将机密传递到模块。

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

## <a name="next-steps"></a>后续步骤

- 若要运行这些示例，请使用 Azure CLI 或 Azure PowerShell 来[部署 Bicep 文件](./quickstart-create-bicep-use-visual-studio-code.md#deploy-the-bicep-file)。
- 若要创建 Bicep 文件，请参阅[快速入门：使用 Visual Studio Code 创建 Bicep 文件](./quickstart-create-bicep-use-visual-studio-code.md)。
- 有关如何解决 Bicep 类型错误的信息，请参阅 [Bicep 的 Any 函数](./bicep-functions-any.md)。
