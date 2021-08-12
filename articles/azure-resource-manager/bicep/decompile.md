---
title: 将 ARM 模板 JSON 反向编译为 Bicep
description: 介绍了将模板 Azure 资源管理器反向编译到 Bicep 文件的命令。
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b9244eb0a076785c424603dc79ba13af6c29fb03
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2021
ms.locfileid: "111537035"
---
# <a name="decompiling-arm-template-json-to-bicep"></a>将 ARM 模板 JSON 反向编译为 Bicep

本文介绍了如何将 Azure 资源管理器模板（ARM 模板）反向编译到 Bicep 文件。 必须[已安装 Bicep CLI](./install.md) 才能运行转换命令。

反向编译 ARM 模板可帮助你入门 Bicep 开发。 如果你有 ARM 模板库，并且想要使用 Bicep 进行未来的开发，可以将这些模板反向编译为 Bicep。 但是，Bicep 文件可能需要修订来实现 Bicep 最佳做法。

本文演示如何在 Azure CLI 中运行 `decompile` 命令。 如果你不使用 Azure CLI，则无需在命令开始部分使用 `az` 来运行命令。 例如，`az bicep decompile` 重命名为 ``bicep decompile``。

## <a name="decompile-from-json-to-bicep"></a>从 JSON 反向编译为 Bicep

若要将 ARM 模板 JSON 反向编译为 Bicep，请使用：

```azurecli
az bicep decompile --file main.json
```

该命令在与 ARM 模板相同的目录中创建名为 _main.bicep_ 的文件。

> [!CAUTION]
> 反向编译尝试转换文件，但不能保证从 ARM 模板 JSON 到 Bicep 的映射。 你可能需要修复生成的 Bicep 文件中警告和错误。 或者，如果无法进行准确的转换，则反向编译可能会失败。 若要报告任何问题或不准确的转换，请[创建问题](https://github.com/Azure/bicep/issues)。

decompile 和 [build](bicep-cli.md#build) 命令生成在功能上等效的模板。 但是，它们在实现中可能并不完全相同。 将模板从 JSON 转换为 Bicep 再转换回 JSON 可能会导致产生其语法与原始模板不同的模板。 部署后，已转换的模板将产生相同的结果。

## <a name="fix-conversion-issues"></a>修复转换问题

假设你有以下 ARM 模板：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

你对其进行反向编译时将会获得：

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
@description('Storage Account type')
param storageAccountType string = 'Standard_LRS'

@description('Location for all resources.')
param location string = resourceGroup().location

var storageAccountName_var = 'store${uniqueString(resourceGroup().id)}'

resource storageAccountName 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName_var
  location: location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = storageAccountName_var
```

已反向编译的文件可以正常工作，但你可能想更改它的一些名称。 变量 `var storageAccountName_var` 具有非常规命名约定。 让我们将其更改为：

```bicep
var uniqueStorageName = 'store${uniqueString(resourceGroup().id)}'
```

你可能会想更改资源的符号名称。 对于符号名称，请使用 `exampleStorage`，而不是 `storageAccountName`。

```bicep
resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
```

由于你更改了存储帐户名称的变量名称，因此需要更改其使用位置。

```bicep
resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
```

在输出中使用：

```bicep
output storageAccountName string = uniqueStorageName
```

完整文件为：

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
@description('Storage Account type')
param storageAccountType string = 'Standard_LRS'

@description('Location for all resources.')
param location string = resourceGroup().location

var uniqueStorageName = 'store${uniqueString(resourceGroup().id)}'

resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = uniqueStorageName
```

## <a name="export-template-and-convert"></a>导出模板并进行转换

可以导出资源组的模板，然后将其直接传递到 `decompile` 命令。 下面的示例展示了如何反向编译已导出的模板。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
az bicep decompile --file main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

通过门户[导出模板](../templates/export-template-portal.md)。

对已下载的文件使用 `bicep decompile <filename>`。

---

## <a name="side-by-side-view"></a>并排视图

借助 [Bicep playground](https://aka.ms/bicepdemo)，你能够并排查看等效的 ARM 模板和 Bicep 文件。 你可以选择一个示例模板来查看这两种版本。 或者，选择 `Decompile` 以上传自己的 ARM 模板并查看等效的 Bicep 文件。

## <a name="next-steps"></a>后续步骤

若要了解所有 Bicep CLI 命令，请参阅 [Bicep CLI 命令](bicep-cli.md)。
