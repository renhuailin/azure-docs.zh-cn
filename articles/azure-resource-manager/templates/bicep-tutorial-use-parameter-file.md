---
title: 教程 - 使用参数文件部署 Azure 资源管理器 Bicep 文件
description: 使用参数文件，其中包含用于部署 Bicep 文件的值。
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ca3a73cde9549bfcdfd47bc4f1955904fac69d1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632350"
---
# <a name="tutorial-use-parameter-files-to-deploy-azure-resource-manager-bicep-file"></a>教程：使用参数文件部署 Azure 资源管理器 Bicep 文件

本教程介绍如何使用[参数文件](parameter-files.md)存储在部署过程中传递的值。 在以前的教程中，我们通过部署命令使用了内联参数。 此方法适用于测试 Bicep 文件，但在自动进行部署时，为环境传递一组值可能会更容易。 参数文件可以方便你针对特定的环境将参数值打包。 使用部署 JSON 模板时所用的相同 JSON 参数文件。 在本教程中，我们将针对开发和生产环境创建参数文件。 完成该过程需要大约 **12 分钟**。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>先决条件

建议完成[有关标记的教程](bicep-tutorial-add-tags.md)，但这不是必需的。

必须有包含 Bicep 扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Bicep 工具](bicep-tutorial-create-first-bicep.md#get-tools)。

## <a name="review-bicep-file"></a>查看 Bicep 文件

Bicep 文件包含许多可以在部署期间提供的参数。 在上一篇教程结束时，Bicep 文件如下所示：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

此 Bicep 文件可正常运行，但现在你希望能够轻松管理为 Bicep 文件传入的参数。

## <a name="add-parameter-files"></a>添加参数文件

参数文件是结构类似于 JSON 模板的 JSON 文件。 在文件中，我们提供需要在部署过程中传入的参数值。

在参数文件中，为 Bicep 文件中的参数提供值。 参数文件中每个参数的名称必须与 Bicep 文件中的参数名称相匹配。 该名称不区分大小写，但为了方便查看匹配的值，我们建议匹配 Bicep 文件中的大小写。

无需为每个参数提供值。 如果未指定的参数具有默认值，则在部署过程中将使用该值。 如果参数没有默认值，并且未在参数文件中指定值，系统会在部署过程中提示你提供值。

不能在参数文件中指定与 Bicep 文件中的参数名称不匹配的参数名称。 如果提供了未知参数，会收到错误。

在 Visual Studio Code 中，创建包含以下内容的新文件。 使用名称 _azuredeploy.parameters.dev.json_ 保存文件。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

此文件是用于开发环境的参数文件。 请注意，它将“Standard_LRS”用于存储帐户，使用“dev”前缀为资源命名，并将 `Environment` 标记设置为“Dev”  。

同样，创建包含以下内容的新文件。 使用名称 _azuredeploy.parameters.prod.json_ 保存文件。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

此文件是用于生产环境的参数文件。 请注意，它将“Standard_GRS”用于存储帐户，使用“contoso”前缀为资源命名，并将 `Environment` 标记设置为“Production”  。 在实际生产环境中，还需要将应用服务与非免费版 SKU 配合使用，但在本教程中，我们将继续使用该免费版 SKU。

## <a name="deploy-bicep-file"></a>部署 Bicep 文件

使用 Azure CLI 或 Azure PowerShell 部署 Bicep 文件。

在本教程中，让我们创建两个新的资源组。 一个用于开发环境，一个用于生产环境。

对于模板和参数变量，请将 `{path-to-the-bicep-file}`、`{path-to-azuredeploy.parameters.dev.json}`、`{path-to-azuredeploy.parameters.prod.json}` 和大括号 `{}` 替换为你的 Bicep 文件和参数文件路径。

首先，我们部署到开发环境。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要运行此部署 cmdlet，你必须具有 Azure PowerShell 的[最新版本](/powershell/azure/install-az-ps)。

```azurepowershell
$bicepFile = "{path-to-the-bicep-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $bicepFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要运行此部署命令，必须具有 Azure CLI 的 [最新版本](/cli/azure/install-azure-cli)。

```azurecli
bicepFile="{path-to-the-bicep-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $bicepFile \
  --parameters $devParameterFile
```

---

现在，我们部署到生产环境。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> 如果部署失败，请使用 `verbose` 开关获取有关正在创建的资源的信息。 使用 `debug` 开关获取调试的详细信息。

## <a name="verify-deployment"></a>验证部署

可以通过在 Azure 门户中浏览资源组来验证部署。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单中选择“资源组”。 
1. 可以看到在本教程中部署的两个新资源组。
1. 选择任一资源组，查看部署的资源。 请注意，这些资源与我们在参数文件中为该环境指定的值匹配。

## <a name="clean-up-resources"></a>清理资源

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

在本教程中，你使用参数文件部署了 Bicep 文件。 下一篇教程将介绍如何模块化 Bicep 文件。

> [!div class="nextstepaction"]
> [添加模块](./bicep-tutorial-add-modules.md)
