---
title: 教程 - 将模块添加到 Azure 资源管理器 Bicep 文件
description: 使用模块来封装原始资源声明的复杂细节。
author: mumian
ms.date: 03/25/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0149858e4e63e4110af2a18a31b9c66a99ff79c9
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108318579"
---
# <a name="tutorial-add-modules-to-azure-resource-manager-bicep-file"></a>教程：将模块添加到 Azure 资源管理器 Bicep 文件

在[上一篇教程](bicep-tutorial-use-parameter-file.md)中，你已学习如何使用参数文件来部署 Bicep 文件。 本教程将介绍如何使用 Bicep 模块来封装原始资源声明的复杂细节。 可以在解决方案内部共享和重用这些模块。  完成该过程需要大约 **12 分钟**。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>先决条件

建议完成[有关参数文件的教程](bicep-tutorial-use-parameter-file.md)，但这不是一项要求。

必须有包含 Bicep 扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Bicep 工具](bicep-tutorial-create-first-bicep.md#get-tools)。

## <a name="review-bicep-file"></a>查看 Bicep 文件

在上一篇教程结束时，Bicep 文件包含以下内容：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

此 Bicep 文件可正常运行。 但对于更大的解决方案，需要将 Bicep 文件分解成多个相关模块，以便可以共享和重用这些模块。 当前的 Bicep 文件将部署存储帐户、应用服务计划和网站。  让我们将存储帐户隔离到模块中。

## <a name="create-bicep-module"></a>创建 Bicep 模块

每个 Bicep 文件都可用作模块，因此在定义模块方面没有特定的语法。 创建包含以下内容的 _storage.bicep_ 文件：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/storage.bicep":::

此模块包含存储帐户资源以及相关的参数和变量。 已删除 _location_ 参数和 _resourceTags_ 参数的值。 这些值将从 Bicep 主文件传递。

## <a name="consume-bicep-module"></a>使用 Bicep 模块

将现有 _azuredeploy.bicep_ 中的存储帐户资源定义替换为以下 Bicep 内容：

```bicep
module stg './storage.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: storagePrefix
    location: location
    resourceTags: resourceTags
  }
}
```

- **模块**：关键字。
- **符号名称** (stg)：模块的标识符。
- **模块文件**：在此示例中，模块的路径是使用相对路径 (./storage.bicep) 指定的。 必须使用正斜杠 (/) 目录分隔符来指定 Bicep 中的所有路径，以确保跨平台编译时的一致性。 不支持 Windows 反斜杠 (\) 字符。

若要检索存储终结点，请将 _azuredeploy.bicep_ 中的输出更新为以下 Bicep：

```bicep
output storageEndpoint object = stg.outputs.storageEndpoint
```

完成的 azuredeploy.bicep 包含以下内容：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/azuredeploy.bicep":::

## <a name="deploy-template"></a>部署模板

使用 Azure CLI 或 Azure PowerShell 来部署模板。

如果尚未创建资源组，请参阅[创建资源组](bicep-tutorial-create-first-bicep.md#create-resource-group)。 此示例假定你已按[第一篇教程](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)所述将 `bicepFile` 变量设置为 Bicep 文件的路径。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要运行此部署 cmdlet，你必须具有 Azure PowerShell 的[最新版本](/powershell/azure/install-az-ps)。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addmodule `
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
  --name addmodule \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---
> [!NOTE]
> 如果部署失败，请使用 `verbose` 开关获取有关正在创建的资源的信息。 使用 `debug` 开关获取调试的详细信息。

## <a name="verify-deployment"></a>验证部署

可以通过在 Azure 门户中浏览资源组来验证部署。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单中选择“资源组”。 
1. 你会看到在本教程中部署的新资源组。
1. 选择该资源组，查看部署的资源。 请注意，这些资源与我们在模板文件中指定的值匹配。

## <a name="clean-up-resources"></a>清理资源

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 如果已完成此系列，则需删除三个资源组：myResourceGroup、myResourceGroupDev 和 myResourceGroupProd  。
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

恭喜！你已完成本简介文章，知道如何将 Bicep 文件部署到 Azure 了。 如果你有任何意见和建议，请在反馈部分告知我们。 谢谢！

下一个教程系列将详细介绍如何部署模板。

> [!div class="nextstepaction"]
> [部署本地模板](./deployment-tutorial-local-template.md)
