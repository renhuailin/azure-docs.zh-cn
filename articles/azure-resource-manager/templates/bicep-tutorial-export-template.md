---
title: 教程 - 从 Azure 门户导出 JSON 模板以进行 Bicep 开发
description: 了解如何使用导出的 JSON 模板完成 Bicep 开发。
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3bc7ed4ada4f7810e9864778c7f76a0573c9dc89
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353032"
---
# <a name="tutorial-use-exported-json-template-from-the-azure-portal"></a>教程：从 Azure 门户使用导出的 JSON 模板

在本教程系列中，你已创建一个用于部署 Azure 存储帐户的 Bicep 文件。 在接下来的两篇教程中，你将添加一个应用服务计划和一个网站。   本教程介绍如何从 Azure 门户导出 JSON 模板（无需从头开始创建模板），以及如何使用 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)中的示例模板。 你可以根据自己的用途自定义这些模板。 本教程重点介绍如何导出模板，以及自定义 Bicep 文件的结果。 完成本教程需要大约 **14 分钟**。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>先决条件

建议完成[有关输出的教程](bicep-tutorial-add-outputs.md)，但这不是一项要求。

必须有包含 Bicep 扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Bicep 工具](bicep-tutorial-create-first-bicep.md#get-tools)。

## <a name="review-bicep-file"></a>查看 Bicep 文件

在上一篇教程结束时，Bicep 文件包含以下内容：

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep":::

此 Bicep 文件非常适合用于部署存储帐户，但你可能想要在其中添加更多资源。 可以从现有的资源导出 JSON 模板，以快速获取该资源的 JSON。 接下来将 JSON 转换为 Bicep，然后可将转换内容添加到 Bicep 文件中。

## <a name="create-app-service-plan"></a>创建应用服务计划

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“创建资源”。 
1. 在“搜索市场”中输入“应用服务计划”，然后选择“应用服务计划”。     请不要选择“应用服务计划(经典)” 
1. 选择“创建”  。
1. 输入：

    - **订阅**：选择 Azure 订阅。
    - **资源组**：选择“新建”，然后指定名称。  提供的资源组名称应该与过去在本教程系列中使用的名称不同。
    - **名称**：输入应用服务计划的名称。
    - **操作系统**：选择“Linux”。 
    - **区域**：选择一个 Azure 位置。 例如“美国中部”。 
    - **定价层**：为了节省成本，请将 SKU 更改为“基本 B1”（在“开发/测试”下）。 

    ![资源管理器模板 - 在门户中导出模板](./media/bicep-tutorial-export-template/resource-manager-template-export.png)
1. 选择“查看并创建”。 
1. 选择“创建”  。 创建资源需要花费片刻时间。

## <a name="export-template"></a>导出模板

Azure 门户目前仅支持导出 JSON 模板。 可使用一些工具将 JSON 模板反向编译为 Bicep 文件。

1. 选择“转到资源”。 

    ![转到资源](./media/bicep-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. 选择“导出模板”  。

    ![资源管理器模板 - 导出模板](./media/bicep-tutorial-export-template/resource-manager-template-export-template.png)

   导出模板功能将提取资源的当前状态，并生成用于部署该资源的模板。 导出模板可能有助于快速获取部署资源所需的 JSON。

1. `Microsoft.Web/serverfarms` 定义和参数定义是所需的部分。

    ![资源管理器模板 - 导出模板 - 导出的模板](./media/bicep-tutorial-export-template/resource-manager-template-exported-template.png)

    > [!IMPORTANT]
    > 通常，导出的模板比创建模板时所需的信息更详细。 例如，导出的模板中的 SKU 对象包含五个属性。 此模板是可行的，但你只需使用 `name` 属性。 可以从导出的模板着手，然后根据要求对其进行修改。

1. 选择“下载”  。  下载的 zip 文件包含 **template.json** 和 **parameters.json**。 解压缩文件。
1. 浏览到 **https://bicepdemo.z22.web.core.windows.net/** ，选择“反向编译”，然后打开“template.json”。  可在 Bicep 中获取模板。

## <a name="revise-existing-bicep-file"></a>修改现有的 Bicep 文件

反向编译的模板提供所需的大部分 Bicep，但你需要根据 Bicep 文件对其进行自定义。 请特别注意你的 Bicep 文件与导出的 Bicep 文件之间的参数和变量差异。 很明显，导出过程并不知道你在自己的 Bicep 文件中定义的参数和变量。

以下示例显示了在 Bicep 文件中添加的内容。 其中包含导出的代码以及一些更改。 第一，它会更改参数的名称以符合命名约定。 第二，它对应用服务计划的位置使用 location 参数。 第三，它删除默认值正常的所有属性。

请复制整个文件，并将你的 Bicep 文件替换为该文件的内容。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep" range="1-53" highlight="18,34-51":::

## <a name="deploy-bicep-file"></a>部署 Bicep 文件

使用 Azure CLI 或 Azure PowerShell 来部署 Bicep 文件。

如果尚未创建资源组，请参阅[创建资源组](bicep-tutorial-create-first-bicep.md#create-resource-group)。 此示例假定你已按[第一篇教程](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)所述将 `bicepFile` 变量设置为 Bicep 文件的路径。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要运行此部署 cmdlet，你必须具有 Azure PowerShell 的[最新版本](/powershell/azure/install-az-ps)。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要运行此部署命令，必须具有 Azure CLI 的 [最新版本](/cli/azure/install-azure-cli)。

```azurecli
az deployment group create \
  --name addappserviceplan \
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
1. 该资源组包含一个存储帐户和一个应用服务计划。

## <a name="clean-up-resources"></a>清理资源

若要继续学习下一篇教程，则不需删除该资源组。

如果你不打算继续学习，请删除该资源组以清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

你已了解如何从 Azure 门户导出 JSON 模板，如何将 JSON 模板转换为 Bicep 文件，以及如何使用导出的模板进行 Bicep 开发。 还可以使用 Azure 快速入门模板来简化 Bicep 开发。

> [!div class="nextstepaction"]
> [使用 Azure 快速入门模板](bicep-tutorial-quickstart-template.md)
