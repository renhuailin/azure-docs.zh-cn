---
title: 教程 - 将函数添加到 Azure 资源管理器 Bicep 文件
description: 将函数添加到 Bicep 文件以构造值。
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: b909beb0cce9ad04ba00068ee25247520dcff47d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102633149"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>教程：将函数添加到 Azure 资源管理器 Bicep 文件

本教程介绍如何将[模板函数](template-functions.md)添加到 Bicep 模板。 我们使用函数来动态构造值。 除了这些系统提供的模板函数，还可以创建[用户定义的函数](./template-user-defined-functions.md)。 完成本教程需要 **7 分钟**。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>先决条件

建议完成[有关参数的教程](bicep-tutorial-add-parameters.md)，但这不是必需的。

必须具有包含 Bicep 扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Bicep 工具](bicep-tutorial-create-first-bicep.md#get-tools)。

## <a name="review-bicep-file"></a>查看 Bicep 文件

在上一篇教程的末尾，你的 Bicep 文件有以下内容：

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

存储帐户的位置硬编码为“美国东部”。  但是，可能需要将存储帐户部署到其他区域。 你会再次面对一个问题：Bicep 文件缺乏灵活性。 可以添加一个位置参数，但理想的情况是默认值更理性，而不仅仅是一个硬编码值。

## <a name="use-function"></a>使用函数

函数可以在部署过程中动态获取值，为 Bicep 文件增加了灵活性。 在本教程中，我们使用函数来获取用于部署的资源组的位置。

以下示例演示了添加名为 `location` 的参数而需要做出的更改。 参数默认值调用 [resourceGroup](template-functions-resource.md#resourcegroup) 函数。 此函数返回的对象描述用于部署的资源组。 对象上的一个属性是位置属性。 使用默认值时，存储帐户位置与资源组位置相同。 资源组中的资源不需共享同一位置。 也可根据需要提供不同的位置。

请复制整个文件，并将你的 Bicep 文件替换为该文件的内容。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>部署 Bicep 文件

在前面的教程中，你创建的存储帐户位于“美国东部”，但资源组创建于“美国中部”。 在本教程中，存储帐户与资源组在同一区域创建。 请使用位置的默认值，这样就不需提供该参数值。 必须为存储帐户提供新名称，因为是在另一位置创建存储帐户。 例如，使用 **store2** 而不是 **store1** 作为前缀。

如果尚未创建资源组，请参阅[创建资源组](bicep-tutorial-create-first-bicep.md#create-resource-group)。 此示例假定你已按[第一篇教程](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)所述将 `bicepFile` 变量设置为 Bicep 文件的路径。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要运行此部署 cmdlet，你必须具有 Azure PowerShell 的[最新版本](/powershell/azure/install-az-ps)。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要运行此部署命令，必须具有 Azure CLI 的 [最新版本](/cli/azure/install-azure-cli)。

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> 如果部署失败，请使用 `verbose` 开关获取有关正在创建的资源的信息。 使用 `debug` 开关获取调试的详细信息。

## <a name="verify-deployment"></a>验证部署

可以通过在 Azure 门户中浏览资源组来验证部署。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单中选择“资源组”。 
1. 选择已部署到的资源组。
1. 可以看到一项存储帐户资源已部署，且其位置与资源组一样。

## <a name="clean-up-resources"></a>清理资源

若要继续学习下一篇教程，则不需删除该资源组。

如果你不打算继续学习，请删除该资源组以清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

在本教程中，我们在为参数定义默认值时使用了一个函数。 在本系列教程中，我们将继续使用函数。 到本系列完成时，我们会将函数添加到 Bicep 文件的每个部分。

> [!div class="nextstepaction"]
> [添加变量](bicep-tutorial-add-variables.md)
