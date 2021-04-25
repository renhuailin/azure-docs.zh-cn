---
title: 定义输出值的多个实例
description: 从部署中返回值时，可以在 Azure 资源管理器模板（ARM 模板）中使用复制操作进行多次迭代。
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 49050f4c0a494bbfb470b64704a09d8738a727f7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385728"
---
# <a name="output-iteration-in-arm-templates"></a>ARM 模板中的输出迭代

本文展示了如何为 Azure 资源管理器模板（ARM 模板）中的输出创建多个值。 通过将 copy 循环添加到模板的 outputs 节，可以在部署过程中动态返回许多项。

还可以将 copy 循环用于 [resources](copy-resources.md)、[properties in a resource](copy-properties.md) 和 [variables](copy-variables.md)。

## <a name="syntax"></a>语法

# <a name="json"></a>[JSON](#tab/json)

将 `copy` 元素添加到模板的 output 节可返回许多项。 copy 元素采用以下常规格式：

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

`count` 属性指定要对该输出值进行的迭代次数。

`input` 属性指定要重复的属性。 可以创建一个由 `input` 属性中的值构造的元素数组。 它可以是单个属性（例如字符串），也可以是具有多个属性的对象。

# <a name="bicep"></a>[Bicep](#tab/bicep)

循环可用于在部署过程中返回许多项：

- 循环访问数组：

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- 循环访问数组的元素

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- 使用循环索引

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
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

## <a name="outputs-iteration"></a>输出迭代

以下示例创建可变数量的存储帐户，并返回每个存储帐户的终结点：

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageEndpoints": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageEndpoints array = [for i in range(0, storageCount): reference(${i}${baseName_var}).primaryEndpoints.blob]
```

---

前面的模板返回包含以下值的数组：

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

下一示例返回新存储帐户的三个属性。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageInfo": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": {
          "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
          "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
          "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
        }
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: reference(concat(i, baseName_var), '2019-04-01', 'Full').resourceId
  blobEndpoint: reference(concat(i, baseName_var)).primaryEndpoints.blob
  status: reference(concat(i, baseName_var)).statusOfPrimary
}]
```

---

前面的示例返回包含以下值的数组：

```json
[
  {
    "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
    "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  },
  {
    "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
    "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  }
]
```

## <a name="next-steps"></a>后续步骤

- 若要完成教程，请参阅[教程：使用 ARM 模板创建多个资源实例](template-tutorial-create-multiple-instances.md)。
- 有关 copy 循环的其他用法，请参阅：
  - [ARM 模板中的资源迭代](copy-resources.md)
  - [ARM 模板中的属性迭代](copy-properties.md)
  - [ARM 模板中的变量迭代](copy-variables.md)
- 若要了解模板的各个部分，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。
- 若要了解如何部署模板，请参阅[使用 ARM 模板和 Azure PowerShell 来部署资源](deploy-powershell.md)。
