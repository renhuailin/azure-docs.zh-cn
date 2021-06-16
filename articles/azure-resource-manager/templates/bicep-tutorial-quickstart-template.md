---
title: 教程 - 使用快速入门模板进行 Azure 资源管理器 Bicep 开发
description: 了解如何使用 Azure 快速入门模板完成 Bicep 开发。
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: c20a8307fc61e180b378fee034e086a5f54ba03f
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353031"
---
# <a name="tutorial-use-azure-quickstart-templates-for-azure-resource-manager-bicep-development"></a>教程：使用 Azure 快速入门模板进行 Azure 资源管理器 Bicep 开发

[Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)是一个存储库，其中包含社区贡献的 JSON 模板。 可以在 Bicep 开发中使用示例模板。 在本教程中，你将查找网站资源定义，将其反向编译为 Bicep，然后将其添加到 Bicep 文件。 完成该过程需要大约 **12 分钟**。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>先决条件

建议完成[有关已导出模板的教程](bicep-tutorial-export-template.md)，但这不是必需的。

必须有包含 Bicep 扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Bicep 工具](bicep-tutorial-create-first-bicep.md#get-tools)。

## <a name="review-bicep-file"></a>查看 Bicep 文件

在上一篇教程结束时，Bicep 文件包含以下内容：

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep":::

此 Bicep 文件适用于部署存储帐户和应用服务计划，但你可能想要在其中添加网站。 可以使用预生成的模板来快速发现部署资源所需的 JSON。 在 Azure 快速入门模板提供 Bicep 示例之前，你可以使用转换工具将 JSON 模板转换为 Bicep 文件。

## <a name="find-template"></a>查找模板

Azure 快速入门模板目前仅提供 JSON 模板。 可使用一些工具将 JSON 模板反向编译为 Bicep 模板。

1. 打开 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)
1. 在“搜索”中，输入“部署 linux web 应用”。  
1. 选择标题为“部署基本的 linux web 应用”的磁贴。 如果找不到它，请单击此处的[直接链接](https://azure.microsoft.com/resources/templates/webapp-basic-linux/)。
1. 选择“在 GitHub 上浏览”。 
1. 选择“azuredeploy.json”。  这是可供使用的模板。
1. 选择“原始”，然后创建 URL 的副本。
1. 浏览到 **https://bicepdemo.z22.web.core.windows.net/** ，选择“反向编译”，然后提供原始模板 URL。
1. 查看 Bicep 模板。 具体说来，请查找 `Microsoft.Web/sites` 资源。

    ![资源管理器模板快速入门网站](./media/bicep-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

    如果你已使用过 JSON 模板，此新资源中有几个重要的 Bicep 功能值得注意。

    在 ARM JSON 模板中，必须手动使用 _dependsOn_ 属性指定资源依赖项。 网站资源依赖于应用服务计划资源。 使用 Bicep 时，如果使用符号名称引用资源的任何属性，则会自动添加 dependsOn 属性。

    可以轻松引用应用服务计划符号名称中的资源 ID (appServicePlanName.id)，该 ID 将转换为编译的 JSON 模板中的 resourceId(...) 函数。

## <a name="revise-existing-bicep-file"></a>修改现有的 Bicep 文件

将反向编译的快速入门模板与现有的 Bicep 文件合并。 就像在上一篇教程中所做的那样，请更新资源符号名称和资源名称，使之符合命名约定。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep" range="1-73" highlight="20-25,28,61-71":::

Web 应用名称必须在 Azure 中独一无二。 为了防止出现重复名称，我们已将 `webAppPortalName` 变量从 `var webAppPortalName_var = '${webAppName}-webapp'` 更新为 `var webAppPortalName = '${webAppName}${uniqueString(resourceGroup().id)}'` 。

## <a name="deploy-bicep-file"></a>部署 Bicep 文件

使用 Azure CLI 或 Azure PowerShell 来部署 Bicep 模板。

如果尚未创建资源组，请参阅[创建资源组](bicep-tutorial-create-first-bicep.md#create-resource-group)。 本示例假设你已按 [第一篇教程](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)中所述将 **bicepFile** 变量设置为 Bicep 文件的路径。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要运行此部署 cmdlet，你必须具有 Azure PowerShell 的[最新版本](/powershell/azure/install-az-ps)。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
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
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> 如果部署失败，请使用 `verbose` 开关获取有关正在创建的资源的信息。 使用 `debug` 开关获取调试的详细信息。

## <a name="clean-up-resources"></a>清理资源

若要继续学习下一篇教程，则不需删除该资源组。

如果你不打算继续学习，请删除该资源组以清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

你已了解如何使用快速入门模板进行 Bicep 开发。 在下一教程中，我们向资源添加标记。

> [!div class="nextstepaction"]
> [添加标记](bicep-tutorial-add-tags.md)
