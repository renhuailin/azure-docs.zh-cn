---
title: 在 JSON 和 Bicep 之间转换模板
description: 介绍用于将 Azure 资源管理器模板从 Bicep 转换为 JSON 并从 JSON 转换为 Bicep 的命令。
ms.topic: conceptual
ms.date: 03/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 81cad004ef106bc3d9e5732dac9fbb7b4f0a60e1
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108316490"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>在 JSON 和 Bicep 之间转换 ARM 模板

本文介绍如何将 Azure 资源管理器模板（ARM 模板）从 JSON 转换为 Bicep 并从 Bicep 转换为 JSON。

必须[已安装 Bicep CLI](bicep-install.md) 才能运行转换命令。

转换命令产生具有相同功能的模板。 但是，它们在实现中可能并不完全相同。 将模板从 JSON 转换为 Bicep 再转换回 JSON 可能会导致产生其语法与原始模板不同的模板。 部署后，已转换的模板将产生相同的结果。

## <a name="convert-from-json-to-bicep"></a>从 JSON 转换为 Bicep

Bicep CLI 提供用于将任何现有 JSON 模板反向编译为 Bicep 文件的命令。 若要反向编译 JSON 文件，请使用：

```azurecli
bicep decompile mainTemplate.json
```

此命令提供 Bicep 创作的起点。 此命令不适用于所有模板。 当前，嵌套模板只有在使用了“inner”表达式评估作用域时才能进行反向编译。 无法反向编译使用复制循环的模板。

## <a name="convert-from-bicep-to-json"></a>从 Bicep 转换为 JSON

Bicep CLI 还提供了一个命令，用于将 Bicep 转换为 JSON。 若要生成 JSON 文件，请使用：

```azurecli
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>导出模板并进行转换

可以导出资源组的模板，然后将其直接传递到反向编译命令。 下面的示例展示了如何反向编译已导出的模板。

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

通过门户[导出模板](export-template-portal.md)。

对已下载的文件使用 `bicep decompile <filename>`。

---

## <a name="side-by-side-view"></a>并排视图

使用 [Bicep 操场](https://aka.ms/bicepdemo)，你可以并排查看等效的 JSON 和 Bicep 文件。 你可以选择一个示例模板来查看这两种版本。 还可以选择 `Decompile` 以上传你自己的 JSON 模板并查看等效的 Bicep 文件。

## <a name="next-steps"></a>后续步骤

有关 Bicep 的信息，请参阅 [Bicep 教程](./bicep-tutorial-create-first-bicep.md)。
