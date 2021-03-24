---
title: 设置资源的部署顺序
description: 介绍如何将一个 Azure 资源设置为在部署过程中依赖于另一个资源。 依赖关系确保资源按正确的顺序部署。
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: f6b63b066da06a17c3a2e51ab0f3ab9bf521a144
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934741"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>在 ARM 模板中定义部署资源的顺序

部署资源时，可能需要确保某些资源在其他资源之前存在。 例如，在部署数据库之前，需要逻辑 SQL Server。 可通过将一个资源标记为依赖于其他资源来建立此关系。 使用 `dependsOn` 元素可以定义显式依赖关系。 使用 reference 或 list 函数可以定义隐式依赖关系。

Azure 资源管理器将评估资源之间的依赖关系，并根据其依赖顺序进行部署。 如果资源互不依赖，资源管理器将以并行方式部署资源。 只需为部署在同一模板中的资源定义依赖关系。

## <a name="dependson"></a>dependsOn

在 Azure 资源管理器模板（ARM 模板）中，`dependsOn` 元素可让你将一个资源定义为依赖于一个或多个资源。 其值是字符串的 JavaScript 对象表示法 (JSON) 数组，每个字符串都是一个资源名称或 ID。 该数组可以包括[有条件部署](conditional-resource-deployment.md)的资源。 条件资源未部署时，Azure 资源管理器会自动将其从所需依赖项中删除。

以下示例显示了一个依赖于虚拟网络、网络安全组和公共 IP 地址的网络接口。 有关完整的模板，请参阅[适用于 Linux VM 的快速入门模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json)。

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

尽管你可能倾向于使用 `dependsOn` 来映射资源之间的关系，但请务必了解这么做的理由。 例如，若要记录资源的互连方式，使用 `dependsOn` 并不是合适的方法。 部署之后，你无法查询 `dependsOn` 元素中定义了哪些资源。 设置不必要的依赖关系会减慢部署速度，因为资源管理器无法并行部署这些资源。

## <a name="child-resources"></a>子资源

[子资源](child-resource-name-type.md)和父资源之间不会自动创建隐式部署依赖关系。 如果需要在父资源之后部署子资源，请设置 `dependsOn` 属性。

以下示例显示了逻辑 SQL Server 和数据库。 请注意，在数据库与服务器之间定义了显式依赖关系，尽管数据库是服务器的子级。

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

有关完整的模板，请参阅[适用于 Azure SQL 数据库的快速入门模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json)。

## <a name="reference-and-list-functions"></a>reference 和 list 函数

[引用函数](template-functions-resource.md#reference)使表达式能够从其他 JSON 名值对或运行时资源中派生其值。 [list* 函数](template-functions-resource.md#list)从列表操作返回资源的值。

reference 和 list 表达式隐式声明一个资源依赖于另一个资源。 只要可能，可使用隐式引用以避免添加不必要的依赖项。

若要强制实施隐式依赖关系，请按名称（而不是资源 ID）引用资源。 如果将资源 ID 传入到 reference 或 list 函数中，则不会创建隐式引用。

`reference` 函数的一般格式为：

```json
reference('resourceName').propertyPath
```

`listKeys` 函数的一般格式为：

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

在以下示例中，CDN 终结点显式依赖于 CDN 配置文件，隐式依赖于 Web 应用。

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

若要了解详细信息，请参阅[引用函数](template-functions-resource.md#reference)。

## <a name="depend-on-resources-in-a-loop"></a>依赖于循环中的资源

若要部署依赖于[复制循环](copy-resources.md)中资源的资源，可以使用两个选项。 你可以设置对循环中的个别资源的依赖关系，也可以设置对整个循环的依赖关系。

> [!NOTE]
> 在大多数情况下，应当设置对复制循环中的个别资源的依赖关系。 仅当循环中的所有资源都需要在创建下一个资源之前存在时，才依赖整个循环。 设置对整个循环的依赖关系会导致依赖关系图显著扩展，特别是当那些循环的资源依赖于其他资源时。 扩展的依赖关系使得部署很难有效完成。

以下示例展示了如何部署多个虚拟机。 该模板会创建相同数量的网络接口。 每个虚拟机都依赖于一个网络接口，而不是依赖于整个循环。

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

以下示例演示了如何在部署虚拟机之前部署三个存储帐户。 请注意，`copy` 元素的 `name` 设置为 `storagecopy`，而虚拟机的 `dependsOn` 元素也设置为 `storagecopy`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="circular-dependencies"></a>循环依赖项

Resource Manager 可在模板验证过程中确定循环依赖项。 如果收到有关循环依赖关系的错误，请评估你的模板，了解是否存在可以删除的依赖关系。 如果删除依赖关系不起作用，则可将一些部署操作移至子资源中来避免循环依赖关系。 请在具有循环依赖关系的资源之后部署子资源。 例如，假设要部署两个虚拟机，但必须在每个虚拟机上设置引用另一虚拟机的属性。 可以按下述顺序部署这两个虚拟机：

1. vm1
2. vm2
3. vm1 上的扩展依赖于 vm1 和 vm2。 扩展在 vm1 上设置的值是从 vm2 获取的。
4. vm2 上的扩展依赖于 vm1 和 vm2。 扩展在 vm2 上设置的值是从 vm1 获取的。

有关评估部署顺序和解决依赖项错误的信息，请参阅[排查使用 Azure 资源管理器时的常见 Azure 部署错误](common-deployment-errors.md)。

## <a name="next-steps"></a>后续步骤

* 若要完成教程，请参阅[教程：创建包含依赖资源的 ARM 模板](template-tutorial-create-templates-with-dependent-resources.md)。
* 有关介绍资源依赖项的 Microsoft Learn 模块，请参阅[使用高级 ARM 模板功能管理复杂云部署](/learn/modules/manage-deployments-advanced-arm-template-features/)。
* 有关设置依赖项的建议，请参阅 [ARM 模板的最佳做法](template-best-practices.md)。
* 若要了解如何在部署期间排查依赖项故障，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](common-deployment-errors.md)。
* 若要了解如何创建 Azure 资源管理器模板，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。
* 有关模板中的可用函数列表，请参阅 [ARM 模板函数](template-functions.md)。
