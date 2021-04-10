---
title: 教程 - 将参数添加到 Azure 资源管理器 Bicep 文件
description: 将参数添加到 Bicep 文件，使其可供重复使用。
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 00df2ffc6272011127c5a1eb0c1e302011f8de5f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632775"
---
# <a name="tutorial-add-parameters-to-azure-resource-manager-bicep-file"></a>教程：将参数添加到 Azure 资源管理器 Bicep 文件

在[上一篇教程](bicep-tutorial-create-first-bicep.md)中，你已了解如何部署存储帐户。 本教程将介绍如何通过添加参数来改进 Bicep 文件。 完成本教程大约需要 **14 分钟**。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>先决条件

建议完成[创建第一个 Bicep 文件](bicep-tutorial-create-first-bicep.md)教程，但这不是一项要求。

必须有包含 Bicep 扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Bicep 工具](bicep-tutorial-create-first-bicep.md#get-tools)。

## <a name="review-bicep-file"></a>查看 Bicep 文件

在上一篇教程结束时，你的 Bicep 如下所示：

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.bicep":::

你可能已注意到此 Bicep 文件有一个问题。 存储帐户名称已经过硬编码。 每次只能使用此 Bicep 文件部署同一个存储帐户。 若要使用不同的名称部署存储帐户，必须创建新的 Bicep 文件，这显然不是实现自动化部署的实用方法。

## <a name="make-bicep-file-reusable"></a>使 Bicep 文件可供重复使用

为使 Bicep 文件可供重复使用，让我们添加一个可用于传入存储帐户名称的参数。 下面的 Bicep 文件演示了文件中更改的内容。 `storageName` 参数已标识为字符串。 最大长度设置为 24 个字符，以防止名称过长。

复制整个文件并将其替换为以下内容。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.bicep" range="1-15" highlight="1-3,6":::

请注意，在 Bicep 中，可以使用参数的名称直接引用参数，而在 ARM JSON 模板中则必须使用 [parameters('storageName')]。

## <a name="deploy-bicep-file"></a>部署 Bicep 文件

让我们部署该 Bicep 文件。 以下示例使用 Azure CLI 或 PowerShell 来部署 Bicep 文件。 请注意，需要为部署命令中的某个值提供存储帐户名称。 对于存储帐户名称，请提供前一篇教程中所用的相同名称。

如果尚未创建资源组，请参阅[创建资源组](bicep-tutorial-create-first-bicep.md#create-resource-group)。 此示例假定你已按[第一篇教程](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)所述将 `bicepFile` 变量设置为 Bicep 文件的路径。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要运行此部署 cmdlet，你必须具有 Azure PowerShell 的[最新版本](/powershell/azure/install-az-ps)。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要运行此部署命令，必须具有 Azure CLI 的 [最新版本](/cli/azure/install-azure-cli)。

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>了解资源更新

在上一部分，你已使用以前创建的同一名称部署了一个存储帐户。 你可能想要知道，重新部署会对该资源造成怎样的影响。

如果该资源已存在，并且在属性中未检测到任何更改，则表示未采取任何措施。 如果该资源已存在，并且某个属性已更改，则表示该资源已更新。 如果资源不存在，则会创建它。

这种处理更新的方式意味着，Bicep 文件可以包含 Azure 解决方案所需的所有资源。 可以放心地重新部署该 Bicep 文件，并可以知道，只在必要时才会更改或创建资源。 例如，如果已将文件添加到存储帐户，则可以重新部署存储帐户，而不会丢失这些文件。

## <a name="customize-by-environment"></a>按环境进行自定义

可以使用参数提供针对特定环境定制的值，以便自定义部署。 例如，可以根据是否要部署到开发、测试和生产环境，来传递不同的值。

上述 Bicep 文件始终部署 **Standard_LRS** 存储帐户。 你可能希望能够根据环境灵活部署不同的 SKU。 以下示例演示了为 SKU 添加参数而需要做出的更改。 复制整个文件并将其粘贴到你的 Bicep 文件中。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep" range="1-27" highlight="5-15,21":::

`storageSKU` 参数有默认值。 如果在部署过程中未指定值，将使用此默认值。 它还有允许值列表。 这些值与创建存储帐户所需的值相匹配。 你不希望 Bicep 文件的用户传入没有作用的 SKU。

## <a name="redeploy-bicep-file"></a>重新部署 Bicep 文件

现在可以重新部署。 由于默认 SKU 设置为 **Standard_LRS**，因此不需要提供该参数的值。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> 如果部署失败，请使用 `verbose` 开关获取有关正在创建的资源的信息。 使用 `debug` 开关获取调试的详细信息。

为了观察 Bicep 文件的灵活性，让我们再次部署。 这一次，请将 SKU 参数设置为 **Standard_GRS**。 可以传入新名称来创建不同的存储帐户，或者使用同一个名称来更新现有的存储帐户。 这两个选项都是有效的。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

最后，让我们再次运行测试，以查看在传入一个未包含在允许值中的 SKU 时会发生什么情况。 在本例中，我们将测试 Bicep 文件用户认为 **basic** 是一个 SKU 时的情景。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

该命令立即失败并出现一条错误消息，指出哪些值是允许的。 在部署开始之前，资源管理器会识别到该错误。

## <a name="clean-up-resources"></a>清理资源

若要继续学习下一篇教程，则不需删除该资源组。

如果你不打算继续学习，请删除该资源组以清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

你已通过添加参数改进了[第一篇教程](bicep-tutorial-create-first-bicep.md)中创建的 Bicep 文件。 下一篇教程将介绍 Bicep 函数。

> [!div class="nextstepaction"]
> [添加函数](bicep-tutorial-add-functions.md)
