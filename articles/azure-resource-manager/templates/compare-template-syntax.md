---
title: 在 JSON 和 Bicep 中比较 Azure 资源管理器模板的语法
description: 比较通过 JSON 和 Bicep 开发的 Azure 资源管理器模板，并演示如何在语言之间进行转换。
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 29c2b9948957ebc10a26f22f0fe3daf383dfe5ba
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036208"
---
# <a name="comparing-json-and-bicep-for-templates"></a>比较模板的 JSON 和 Bicep

本文将 Bicep 语法与适用于 Azure 资源管理器模板的 JSON 语法 (ARM 模板) 进行比较。 在大多数情况下，Bicep 提供的语法不如 JSON 中等效的。

## <a name="syntax-equivalents"></a>等效语法

如果你熟悉如何使用 JSON 来开发 ARM 模板，请使用下表了解 Bicep 的等效语法。

| 方案 | Bicep | JSON |
| -------- | ------------ | ----- |
| 创作表达式 | `func()` | `"[func()]"` |
| 获取参数值 | `exampleParameter` | `[parameters('exampleParameter'))]` |
| 获取变量值 | `exampleVar` | `[variables('exampleVar'))]` |
| 连接字符串 | `'${namePrefix}-vm'` | `[concat(parameters('namePrefix'), '-vm')]` |
| 设置资源属性 | `sku: '2016-Datacenter'` | `"sku": "2016-Datacenter",` |
| 返回逻辑 AND | `isMonday && isNovember` | `[and(parameter('isMonday'), parameter('isNovember'))]` |
| 获取模板中资源的资源 ID | `nic1.id` | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` |
| 从模板中的资源获取属性 | `diagsAccount.properties.primaryEndpoints.blob` | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` |
| 有条件地设置值 | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` |
| 将解决方案分隔到多个文件中 | 使用模块 | 使用链接模板 |
| 设置部署的目标范围 | `targetScope = 'subscription'` | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` |
| 设置依赖关系 | 依赖依赖项的自动检测或手动设置依赖项 `dependsOn: [ stg ]` | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` |
| 资源声明 | `resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {...}` | `"resources": [ { "type": "Microsoft.Compute/virtualMachines", "apiVersion": "2020-06-01", ... } ]` |

## <a name="recommendations"></a>建议

* 如果可能，请避免在 Bicep 文件中使用 [reference](template-functions-resource.md#reference) 和 [resourceId](template-functions-resource.md#resourceid) 函数。 引用同一 Bicep 部署中的资源时，请改用资源标识符。 例如，如果你已在 Bicep 文件中将资源部署 `stg` 为资源标识符，请使用类似于或的 `stg.id` 语法 `stg.properties.primaryEndpoints.blob` 来获取属性值。 通过使用资源标识符，可以在资源之间创建隐式依赖项。 不需要显式设置与 dependsOn 属性的依赖关系。
* 为标识符使用一致的大小写。 如果不确定要使用哪种类型的大小写，请尝试使用大小写格式。 例如 `param myCamelCasedParameter string`。
* 仅当说明向用户提供基本信息时，才向参数添加描述。 您可以 `//` 对某些信息使用注释。

## <a name="decompile-json-to-bicep"></a>将 JSON 反编译到 Bicep

Bicep CLI 提供了一个命令，用于将任何现有 ARM 模板反编译为 Bicep 文件。 若要反编译 JSON 文件，请使用： `bicep decompile "path/to/file.json"`

此命令提供 Bicep 创作的起点，但该命令不适用于所有模板。 此命令可能会失败，或者你可能需要在 ilspy 之后修复问题。 目前，只有在嵌套模板使用 "内部" 表达式求值作用域时，才能对其进行反编译。

可以导出资源组的模板，然后将其直接传递到 bicep 反编译命令。 下面的示例演示如何反编译已导出的模板。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

通过门户[导出模板](export-template-portal.md)。 `bicep decompile <filename>`在下载的文件上使用。

---

## <a name="build-json-from-bicep"></a>从 Bicep 生成 JSON

Bicep CLI 还提供了一个命令，用于将 Bicep 转换为 JSON。 若要生成 JSON 文件，请使用： `bicep build "path/to/file.json"`

## <a name="side-by-side-view"></a>并行视图

[Bicep 板块](https://aka.ms/bicepdemo)可让你并排查看等效的 JSON 和 Bicep 文件。 您可以选择一个示例模板来查看这两个版本。 或者，选择 " `Decompile` 上传自己的 JSON 模板" 并查看等效的 Bicep 文件。

## <a name="next-steps"></a>后续步骤

有关 Bicep 的信息，请参阅 [Bicep 教程](./bicep-tutorial-create-first-bicep.md)。
