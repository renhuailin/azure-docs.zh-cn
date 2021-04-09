---
title: 教程 - 将变量添加到 Azure 资源管理器 Bicep 文件
description: 将变量添加到 Bicep 文件以简化语法。
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: da2755c1f2c0f9fa891fe1a99b1fed21f64492c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632469"
---
# <a name="tutorial-add-variables-to-azure-resource-manager-bicep-file"></a>教程：将变量添加到 Azure 资源管理器 Bicep 文件

本教程介绍如何将变量添加到 Bicep 文件。 利用变量，你只需编写一次表达式，然后即可在整个 Bicep 文件中重复使用该表达式，这样就简化了 Bicep 文件。 完成本教程需要 **7 分钟**。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>先决条件

建议完成[有关函数的教程](bicep-tutorial-add-functions.md)，但这不是必需的。

必须具有包含 Bicep 扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Bicep 工具](bicep-tutorial-create-first-bicep.md#get-tools)。

## <a name="review-bicep-file"></a>查看 Bicep 文件

在上一篇教程的末尾，你的 Bicep 文件有以下内容：

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep":::

存储帐户名称的参数难以使用，因为你必须提供唯一名称。 如果已完成本系列中的前述教程，你可能已厌倦了猜测某个唯一的名称。 若要解决此问题，可以添加一个变量，以便为存储帐户构造唯一名称。

## <a name="use-variable"></a>使用变量

以下示例演示了为 Bicep 文件添加变量而做的更改，该变量用于创建一个独一无二的存储帐户名称。 请复制整个文件，并将你的 Bicep 文件替换为该文件的内容。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep" range="1-31" highlight="1-3,19,22":::

请注意，它包含一个名为 `uniqueStorageName` 的变量。 此变量使用三个函数来构造一个字符串值。

[resourceGroup](template-functions-resource.md#resourcegroup) 函数是你所熟悉的。 在此示例中，你获得 `id` 属性而不是 `location` 属性，如上一教程所示。 `id` 属性返回资源组的完整标识符，包括订阅 ID 和资源组名称。

[uniqueString](template-functions-string.md#uniquestring) 函数创建一个 13 个字符的哈希值。 返回的值取决于传入的参数。 在本教程中，我们使用资源组 ID 作为哈希值的输入。 这意味着，你可以将此 Bicep 文件部署到不同的资源组，并获取不同的唯一字符串值。 但是，如果部署到同一资源组，则获得同一值。

Bicep 支持[字符串内插](https://en.wikipedia.org/wiki/String_interpolation#)语法。 就此变量来说，它采用参数中的字符串和 `uniqueString` 函数中的字符串，并将二者组合成一个字符串。

可以通过 `storagePrefix` 参数传入一个用于标识存储帐户的前缀。 可以创建你自己的命名约定，以便在从长的资源列表完成部署后，通过该约定轻松地标识存储帐户。

最后请注意，存储帐户名称现在设置为变量而非参数。

## <a name="deploy-bicep-file"></a>部署 Bicep 文件

我们来部署这个 Bicep 文件。 部署此 Bicep 文件比部署前面的 Bicep 文件容易，因为你只提供存储名称的前缀。

如果尚未创建资源组，请参阅[创建资源组](bicep-tutorial-create-first-bicep.md#create-resource-group)。 此示例假定你已按[第一篇教程](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)所述将 `bicepFile` 变量设置为 Bicep 文件的路径。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要运行此部署 cmdlet，你必须具有 Azure PowerShell 的[最新版本](/powershell/azure/install-az-ps)。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要运行此部署命令，必须具有 Azure CLI 的 [最新版本](/cli/azure/install-azure-cli)。

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> 如果部署失败，请使用 `verbose` 开关获取有关正在创建的资源的信息。 使用 `debug` 开关获取调试的详细信息。

## <a name="verify-deployment"></a>验证部署

可以通过在 Azure 门户中浏览资源组来验证部署。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单中选择“资源组”。 
1. 选择已部署到的资源组。
1. 可以看到，我们已部署一项存储帐户资源。 该存储帐户的名称为 **store** 加上一个由随机字符组成的字符串。

## <a name="clean-up-resources"></a>清理资源

若要继续学习下一篇教程，则不需删除该资源组。

如果你不打算继续学习，请删除该资源组以清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

在本教程中，我们添加了一个变量，用于创建存储帐户的唯一名称。 在下一教程中，我们从已部署的存储帐户返回一个值。

> [!div class="nextstepaction"]
> [添加输出](bicep-tutorial-add-outputs.md)
