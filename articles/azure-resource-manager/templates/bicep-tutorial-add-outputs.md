---
title: 教程 - 将输出添加到 Azure 资源管理器 Bicep 文件
description: 将输出添加到 Bicep 文件以简化语法。
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 4b7d7a1414091c516dba2c474e1681ba357b55a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594302"
---
# <a name="tutorial-add-outputs-to-azure-resource-manager-bicep-file"></a>教程：将输出添加到 Azure 资源管理器 Bicep 文件

本教程介绍如何从部署返回值。 需要已部署资源提供的值时，请使用输出。 完成本教程需要 **7 分钟**。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>先决条件

建议完成[有关变量的教程](bicep-tutorial-add-variables.md)，但这不是必需的。

必须具有包含 Bicep 扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Bicep 工具](bicep-tutorial-create-first-bicep.md#get-tools)。

## <a name="review-bicep-file"></a>查看 Bicep 文件

在上一篇教程的末尾，你的 Bicep 文件有以下内容：

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep":::

它部署一个存储帐户，但不返回有关该存储帐户的任何信息。 可能需要从新资源捕获属性，以便以后引用它们。

## <a name="add-outputs"></a>添加输出

可以使用输出，以便从部署返回值。 例如，可以获取新存储帐户的终结点。

以下示例演示为了添加输出值而对 Bicep 文件做出的更改。 请复制整个文件，并将你的 Bicep 文件替换为该文件的内容。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep" range="1-33" highlight="33":::

关于添加的输出值，有一些重要的值得注意的项。

返回的值的类型设置为 `object`，这意味着它会返回模板对象。

若要从存储帐户获取 `primaryEndpoints` 属性，请使用存储帐户符号名称。 Visual Studio Code 的自动完成功能提供了属性的完整列表：

   ![Visual Studio Code Bicep 符号名称对象属性](./media/bicep-tutorial-add-outputs/visual-studio-code-bicep-output-properties.png)

## <a name="deploy-bicep-file"></a>部署 Bicep 文件

现在可以部署 Bicep 文件并查看返回的值了。

如果尚未创建资源组，请参阅[创建资源组](bicep-tutorial-create-first-bicep.md#create-resource-group)。 此示例假定你已按[第一篇教程](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)所述将 `bicepFile` 变量设置为 Bicep 文件的路径。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要运行此部署命令，必须具有 Azure CLI 的 [最新版本](/cli/azure/install-azure-cli)。

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

在部署命令的输出中，将会看到类似于以下示例的对象，但前提是输出采用 JSON 格式：

```json
{
  "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
  "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
  "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
  "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
  "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
  "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> 如果部署失败，请使用 `verbose` 开关获取有关正在创建的资源的信息。 使用 `debug` 开关获取调试的详细信息。

## <a name="review-your-work"></a>回顾所做的工作

我们在已完成的六个教程中做了很多工作。 让我们抽些时间来回顾所做的工作。 你使用易于提供的参数创建了 Bicep 文件。 该 Bicep 文件可以在不同环境中重复使用，因为它允许自定义，并且会动态创建所需值。 它还返回有关存储帐户的信息，这些信息可以用在脚本中。

现在，让我们来看一下资源组和部署历史记录。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单中选择“资源组”。 
1. 选择已部署到的资源组。
1. 我们会在资源组中有至少一个（也可能有多个）存储帐户，具体取决于所执行的步骤。
1. 此外还会在历史记录中列出多个成功的部署。 选择该链接。

   ![选择部署](./media/bicep-tutorial-add-outputs/select-deployments.png)

1. 可以在历史记录中看到所有部署。 选择名为 **addoutputs** 的部署。

   ![显示部署历史记录](./media/bicep-tutorial-add-outputs/show-history.png)

1. 可以查看输入。

   ![显示输入](./media/bicep-tutorial-add-outputs/show-inputs.png)

1. 可以查看输出。

   ![显示输出](./media/bicep-tutorial-add-outputs/show-outputs.png)

1. 可以查看 JSON 模板。

   ![显示模板](./media/bicep-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>清理资源

若要继续学习下一篇教程，则不需删除该资源组。

如果你不打算继续学习，请删除该资源组以清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

在本教程中，你将返回值添加到了 Bicep 文件。 下一个教程将介绍如何导出 JSON 模板，以及如何在 Bicep 文件中使用该导出的模板的部件。

> [!div class="nextstepaction"]
> [使用导出的模板](bicep-tutorial-export-template.md)
