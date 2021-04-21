---
title: 定义属性的多个实例
description: 在资源上创建属性时，可以使用 Azure 资源管理器模板（ARM 模板）中的复制操作进行多次迭代。
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 94bc153a49f80694ab9b2d5b04fdf57e8a12e8c8
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385745"
---
# <a name="property-iteration-in-arm-templates"></a>ARM 模板中的属性迭代

本文介绍如何在 Azure 资源管理器模板（ARM 模板）中创建一个属性的多个实例。 通过将复制循环添加到模板中资源的 properties 节，可以在部署过程中动态设置属性的项数。 还可以避免重复模板语法。

你只能对顶级资源使用复制循环，即使在将复制循环应用到属性时也是如此。 若要了解如何将子资源更改为顶级资源，请参阅[子资源的迭代](copy-resources.md#iteration-for-a-child-resource)。

还可以将复制循环用于 [resources](copy-resources.md)、[variables](copy-variables.md) 和 [outputs](copy-outputs.md)。

## <a name="syntax"></a>语法

# <a name="json"></a>[JSON](#tab/json)

将 `copy` 元素添加到模板的 resources 节以设置某个属性的项数。 copy 元素采用以下常规格式：

```json
"copy": [
  {
    "name": "<name-of-property>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

对于 `name`，提供要创建的资源属性的名称。

`count` 属性指定要对该属性进行的迭代次数。

`input` 属性指定要重复的属性。 可以创建一个由 `input` 属性中的值构造的元素数组。

# <a name="bicep"></a>[Bicep](#tab/bicep)

可以通过以下方式使用循环声明多个属性：

- 循环访问数组：

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }
  ```

- 循环访问数组的元素

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }
  ```

- 使用循环索引

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }
  ```

---

## <a name="copy-limits"></a>复制限制

count 不能超过 800。

count 不能为负数。 如果使用最新版本的 Azure CLI、PowerShell 或 REST API 部署模板，则它可以为零。 具体而言，必须使用：

- Azure PowerShell 2.6 或更高版本
- Azure CLI 2.0.74 或更高版本
- REST API 版本 2019-05-10 或更高版本
- [链接的部署](linked-templates.md)必须将 API 版本 2019-05-10 或更高版本用于部署资源类型

更早版本的 PowerShell、CLI 和 REST API 不支持将 count 设为零。

## <a name="property-iteration"></a>属性迭代

以下示例演示如何将复制循环应用于虚拟机上的 `dataDisks` 属性：

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 3,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty",
                "diskSizeGB": 1023
              }
            }
          ]
        }
        ...
      }
    }
  ]
}
```

请注意，在属性迭代中使用 `copyIndex` 时，必须提供迭代的名称。 属性迭代还支持 offset 参数。 偏移量必须在迭代名称之后，例如 `copyIndex('dataDisks', 1)`。

已部署的模板将成为：

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

处理数组时可以使用复制操作，因为可对数组中的每个元素执行迭代操作。 可以对数组使用 `length` 函数来指定迭代计数，并使用 `copyIndex` 来检索数组中的当前索引。

以下示例模板为作为数组传入的数据库创建故障转移组。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "primaryServerName": {
      "type": "string"
    },
    "secondaryServerName": {
      "type": "string"
    },
    "databaseNames": {
      "type": "array",
      "defaultValue": [
        "mydb1",
        "mydb2",
        "mydb3"
      ]
    }
  },
  "variables": {
    "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/failoverGroups",
      "apiVersion": "2015-05-01-preview",
      "name": "[variables('failoverName')]",
      "properties": {
        "readWriteEndpoint": {
          "failoverPolicy": "Automatic",
          "failoverWithDataLossGracePeriodMinutes": 60
        },
        "readOnlyEndpoint": {
          "failoverPolicy": "Disabled"
        },
        "partnerServers": [
          {
            "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
          }
        ],
        "copy": [
          {
            "name": "databases",
            "count": "[length(parameters('databaseNames'))]",
            "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
          }
        ]
      }
    }
  ],
  "outputs": {
  }
}
```

`copy` 元素是一个数组，因此，你可以为资源指定多个属性。

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(0)
@maxValue(16)
@description('The number of dataDisks to be returned in the output array.')
param numberOfDataDisks int = 16

resource vmName 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
  properties: {
    storageProfile: {
      ...
      dataDisks: [for i in range(0, numberOfDataDisks): {
        lun: i
        createOption: 'Empty'
        diskSizeGB: 1023
      }]
    }
    ...
  }
}
```

已部署的模板将成为：

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

---

可将资源迭代和属性迭代结合使用。 按名称引用属性迭代。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource vnetname_resource 'Microsoft.Network/virtualNetworks@2018-04-01' = [for i in range(0, 2): {
  name: concat(vnetname, i)
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        addressPrefix
      ]
    }
    subnets: [for j in range(0, 2): {
      name: 'subnet-${j}'
      properties: {
        addressPrefix: subnetAddressPrefix[j]
      }
    }]
  }
}]
```

---

## <a name="example-templates"></a>示例模板

以下示例显示了为一个属性创建多个值的常见方案。

|模板  |说明  |
|---------|---------|
|[数据磁盘数可变的 VM 部署](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |通过虚拟机部署多个数据磁盘。 |

## <a name="next-steps"></a>后续步骤

- 若要完成教程，请参阅[教程：使用 ARM 模板创建多个资源实例](template-tutorial-create-multiple-instances.md)。
- 有关复制循环的其他用法，请参阅：
  - [ARM 模板中的资源迭代](copy-resources.md)
  - [ARM 模板中的变量迭代](copy-variables.md)
  - [ARM 模板中的输出迭代](copy-outputs.md)
- 若要了解模板的各个部分，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。
- 若要了解如何部署模板，请参阅[使用 ARM 模板和 Azure PowerShell 来部署资源](deploy-powershell.md)。
