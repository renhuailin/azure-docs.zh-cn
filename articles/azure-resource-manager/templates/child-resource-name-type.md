---
title: 模板中的子资源
description: 介绍如何在 Azure 资源管理器模板（ARM 模板）中设置子资源的名称和类型。
ms.topic: conceptual
ms.date: 04/23/2021
ms.openlocfilehash: 1928a94fbfefc694091a3f09ea577e8250540bf4
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107949517"
---
# <a name="set-name-and-type-for-child-resources"></a>设置子资源的名称和类型

子资源是只存在于另一资源的上下文内的资源。 例如，如果没有[虚拟机](/azure/templates/microsoft.compute/virtualmachines)，则[虚拟机扩展](/azure/templates/microsoft.compute/virtualmachines/extensions)不能存在。 扩展资源是虚拟机的子项。

每个父资源仅接受特定的资源类型作为子资源。 子资源的资源类型包括父资源的资源类型。 例如，`Microsoft.Web/sites/config` 和 `Microsoft.Web/sites/extensions` 都是 `Microsoft.Web/sites` 的子资源  。 可接受的资源类型在父资源的 [模板架构](https://github.com/Azure/azure-resource-manager-schemas) 中指定。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

在 Azure 资源管理器模板（ARM 模板）中，可以在父资源内部或外部指定子资源。 你为资源名称和资源类型提供的值取决于是在父资源内部还是在外部定义子资源。

## <a name="within-parent-resource"></a>在父资源内部

以下示例显示子资源包括在父资源的资源属性中。

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

子资源总共只能定义五级。

在父资源类型内部定义时，可以将类型和名称值的格式设置为不带斜杠的单个段。

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

以下示例显示一个虚拟网络，有一个子网。 请注意，子网包含在虚拟网络的资源数组中。 名称设置为 **Subnet1**，类型设置为 **subnets**。 子资源被标记为依赖父资源，因为在部署子资源之前，必须存在父资源。

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

完整的资源类型仍然为 `Microsoft.Network/virtualNetworks/subnets`。 可不提供 `Microsoft.Network/virtualNetworks/`，因为假设它继承父资源类型。

子资源名称设置为 **Subnet1**，但完整名称包含父名称。 可不提供 **VNet1**，因为假设它继承父资源。

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  <parent-resource-properties>

  resource <child-resource-symbolic-name> '<child-resource-type>' = {
    <child-resource-properties>
  }
}
```

嵌套资源声明必须出现在父资源语法的顶级。 只要每个级别都是其父资源的子类型，就可以以任意深度嵌套声明。

在父资源类型内部定义时，可以将类型和名称值的格式设置为不带斜杠的单个段。 以下示例显示一个虚拟网络，有一个子网。 请注意，子网包含在虚拟网络的资源数组中。 名称设置为 **Subnet1**，类型设置为 **subnets**。

```bicep
param location string

resource VNet1 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: 'VNet1'
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
  }

  resource VNet1_Subnet1 'subnets' = {
    name: 'Subnet1'
    properties: {
      addressPrefix: '10.0.0.0/24'
    }
  }
}
```

完整的资源类型仍然为 `Microsoft.Network/virtualNetworks/subnets`。 无需提供 `Microsoft.Network/virtualNetworks/`，因为已假设它继承父资源类型和版本。 嵌套资源可以选择使用语法 `<segment>@<version>` 声明 API 版本。 如果嵌套资源省略了 API 版本，则使用父资源的 API 版本。 如果嵌套资源指定了 API 版本，则使用指定的 API 版本。

子资源名称设置为 **Subnet1**，但完整名称包含父名称。 可不提供 VNet1，因为假设它继承父资源。

若要访问子资源符号名称，需要使用 `::` 运算符。 例如，若要输出子资源中的属性，请执行以下操作：

```bicep
output childAddressPrefix string = VNet1::VNet1_Subnet1.properties.addressPrefix
```

嵌套资源可以访问其父资源的属性。 在同一父资源的正文中声明的其他资源可以相互引用，并可应用有关循环依赖关系的典型规则。 父资源无法访问它所包含的资源的属性，这将导致循环依赖关系。

---

## <a name="outside-parent-resource"></a>父资源外部

以下示例显示了父资源外部的子资源。 如果父资源未部署在同一模板中，或者想要使用 [copy](copy-resources.md) 创建多个子资源，可以使用此方法。

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

在父资源外部定义时，请使用斜杠来格式化类型和名称值，使之包含父类型和名称。

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

以下示例显示一个虚拟网络和子网，二者都在根级别进行定义。 请注意，子网不包含在虚拟网络的资源数组中。 名称设置为 **VNet1/Subnet1**，类型设置为 `Microsoft.Network/virtualNetworks/subnets`。 子资源被标记为依赖父资源，因为在部署子资源之前，必须存在父资源。

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  <parent-resource-properties>
}

resource <child-resource-symbolic-name> '<child-resource-type>@<api-version>' = {
  <child-resource-properties>
}
```

在父资源外部定义时，请使用斜杠来格式化类型和名称值，使之包含父类型和名称。

以下示例显示一个虚拟网络和子网，二者都在根级别进行定义。 请注意，子网不包含在虚拟网络的资源数组中。 名称设置为 **VNet1/Subnet1**，类型设置为 `Microsoft.Network/virtualNetworks/subnets`。 子资源被标记为依赖父资源，因为在部署子资源之前，必须存在父资源。

```bicep
param location string

resource VNet1 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: 'VNet1'
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
  }
}

resource VNet1_Subnet1 'Microsoft.Network/virtualNetworks/subnets@2018-10-01' = {
  name: '${VNet1.name}/Subnet1'
  properties: {
    addressPrefix: '10.0.0.0/24'
  }
}
```

---

## <a name="next-steps"></a>后续步骤

* 若要了解如何创建 ARM 模板，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。
* 若要了解引用资源时的资源名称格式，请参阅[引用函数](template-functions-resource.md#reference)。
