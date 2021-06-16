---
title: 教程 - 将标记添加到 Azure 资源管理器 Bicep 文件中的资源
description: 将标记添加到在 Bicep 文件中部署的资源。 可以通过标记对资源进行逻辑组织。
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ea5e078eb692d002b3f86cd43663dd042d692611
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353037"
---
# <a name="tutorial-add-tags-in-azure-resource-manager-bicep-files"></a>教程 - 在 Azure 资源管理器 Bicep 文件中添加标记

本教程介绍如何将标记添加到 Bicep 文件中的资源。 可以通过[标记](../management/tag-resources.md)对资源进行逻辑组织。 标记值显示在成本报告中。 完成本教程需要 **8 分钟**。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>先决条件

建议完成[有关快速入门模板的教程](bicep-tutorial-quickstart-template.md)，但这不是必需的。

必须具有包含 Bicep 扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Bicep 工具](bicep-tutorial-create-first-bicep.md#get-tools)。

## <a name="review-bicep-file"></a>查看 Bicep 文件

前一个 Bicep 文件部署了存储帐户、应用服务计划和 Web 应用。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep":::

部署这些资源以后，可能需要跟踪成本并找到属于某个类别的资源。 可以通过添加标记来解决这些问题。

## <a name="add-tags"></a>添加标记

可以通过对资源进行标记来添加有助于标识其用途的值。 例如，可以通过添加标记来列出环境和项目。 可以通过添加标记来确定某个成本中心或拥有该资源的团队。 添加对组织来说有意义的任何值。

以下示例演示了对该 Bicep 文件的更改。 请复制整个文件，并将你的 Bicep 文件替换为该文件的内容。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep" range="1-81" highlight="27-30,38,51,71":::

## <a name="deploy-bicep-file"></a>部署 Bicep 文件

现在可以部署该 Bicep 文件并查看结果了。

如果尚未创建资源组，请参阅[创建资源组](bicep-tutorial-create-first-bicep.md#create-resource-group)。 此示例假定你已按[第一篇教程](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)所述将 `bicepFile` 变量设置为 Bicep 文件的路径。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要运行此部署 cmdlet，你必须具有 Azure PowerShell 的[最新版本](/powershell/azure/install-az-ps)。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要运行此部署命令，必须具有 Azure CLI 的 [最新版本](/cli/azure/install-azure-cli)。

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> 如果部署失败，请使用 `verbose` 开关获取有关正在创建的资源的信息。 使用 `debug` 开关获取调试的详细信息。

## <a name="verify-deployment"></a>验证部署

可以通过在 Azure 门户中浏览资源组来验证部署。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单中选择“资源组”。 
1. 选择已部署到的资源组。
1. 选择一项资源，例如存储帐户资源。 可以看到，它现在有标记。

   ![显示标记](./media/bicep-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>清理资源

若要继续学习下一篇教程，则不需删除该资源组。

如果你不打算继续学习，请删除该资源组以清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

在本教程中，我们向资源添加了标记。 下一个教程介绍如何使用参数文件来简化将值传入部署的过程。

> [!div class="nextstepaction"]
> [使用参数文件](bicep-tutorial-use-parameter-file.md)
