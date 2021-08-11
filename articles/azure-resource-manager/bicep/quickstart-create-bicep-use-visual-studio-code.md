---
title: 创建 Bicep 文件 - Visual Studio Code
description: 使用 Visual Studio Code 和适用于 Bicep 文件的 Bicep 扩展来部署 Azure 资源
ms.date: 06/25/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8167516a41943ea17fdeb47bce84767178593e85
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970096"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>快速入门：使用 Visual Studio Code 创建 Bicep 文件

本快速入门逐步讲解使用 Visual Studio Code 创建 [Bicep 文件](overview.md)的步骤。 你将创建一个存储帐户和一个虚拟网络。 你还将了解 Bicep 扩展如何通过提供类型安全性、语法验证和自动完成来简化开发。

## <a name="prerequisites"></a>先决条件

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

若要设置环境以进行 Bicep 开发，请参阅[安装 Bicep 工具](install.md)。 完成这些步骤后，你将具备 [Visual Studio Code](https://code.visualstudio.com/) 和 [Bicep 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)。 你还将具备最新的[Azure CLI](/cli/azure/) 或最新的 [Azure PowerShell 模块](/powershell/azure/new-azureps-module-az)。

## <a name="add-resource-snippet"></a>添加资源代码片段

启动 Visual Studio Code 并创建名为 main.bicep 的新文件。

带有 Bicep 扩展的 VS Code 通过提供预定义的代码片段来简化开发。 在本快速入门中，你将添加用于创建虚拟网络的代码片段。

在 main.bicep 中，键入 vnet。 从列表中选择“res-vnet”，然后按 Tab 或 Enter 键。

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/add-snippet.png" alt-text="添加用于创建虚拟网络的代码片段":::

Bicep 文件现在包含以下代码：

```bicep
resource virtualNetwork 'Microsoft.Network/virtualNetworks@2019-11-01' = {
  name: 'name'
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
    subnets: [
      {
        name: 'Subnet-1'
        properties: {
          addressPrefix: '10.0.0.0/24'
        }
      }
      {
        name: 'Subnet-2'
        properties: {
          addressPrefix: '10.0.1.0/24'
        }
      }
    ]
  }
}
```

此代码片段包含定义虚拟网络所需的所有值。 不过，你可以根据自己的需求修改此代码。 例如，`name` 并不是很适合用作虚拟网络的名称。 将 `name` 属性更改为 `examplevnet`。

```bicep
name: 'examplevnet'
```

你可以部署此 Bicep 文件，但在部署之前，我们将添加一个参数和存储帐户。

## <a name="add-parameter"></a>添加参数

现在，我们添加一个要用作存储帐户名称的参数。 在虚拟网络上方添加几行，然后键入：

```bicep
param storageName
```

在 storageName 后面添加空格时，可以发现 Intellisense 提供了该参数可用的数据类型。

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/add-param.png" alt-text="为参数添加字符串类型":::

选择“string”。

你的参数如下：

```bicep
param storageName string
```

此参数可以正常工作，但存储帐户对名称的长度施加了限制。 该名称必须至少包含 3 个字符，并且不能超过 24 个字符。 可以通过为参数添加修饰器来指定这些要求。

在参数上方添加一行，然后键入 @。 你将看到可用的修饰器。 可以看到，minLength 和 maxLength 都有修饰器 。

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/add-decorators.png" alt-text="为参数添加修饰器":::

如下所示添加这两个修饰器并指定字符限制：

```bicep
@minLength(3)
@maxLength(24)
param storageName string
```

还可以添加参数的说明。 请包含相应的信息，帮助部署 Bicep 文件的人员了解要提供哪个值。

```bicep
@minLength(3)
@maxLength(24)
@description('Provide a name for the storage account. Use only lower case letters and numbers. The name must be unique across Azure.')
param storageName string
```

好了，你的参数现在可供使用。

## <a name="add-resource"></a>添加资源

我们将使用 Intellisense 来设置值，而不是使用代码片段来定义存储帐户。 与手动键入值相比，使用 Intellisense 完成此步骤要方便得多。

使用 `resource` 关键字定义资源。  在虚拟网络下方，键入 resource exampleStorage：

```bicep
resource exampleStorage
```

exampleStorage 是要部署的资源的符号名称。 它可让你轻松在 Bicep 文件的其他部分引用该资源。

在该符号名称的后面添加空格时，会显示资源类型的列表。 不断键入 storage，直到可以从可用选项中选择它。

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/select-resource-type.png" alt-text="选择资源类型的存储帐户":::

选择“Microsoft.Storage/storageAccounts”后，你将看到可用的 API 版本。 选择最新版本。

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/select-api-version.png" alt-text="选择资源类型的 API 版本":::

在资源类型的单引号后面，添加 = 和一个空格。 你将看到用于为资源添加属性的选项。 选择“required-properties”。

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/select-required-properties.png" alt-text="添加所需属性":::

此选项用于添加在部署时所需的所有资源类型属性。 选择此选项后，存储帐户具有以下属性：

```bicep
resource exampleStorage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name:
  location:
  sku: {
    name:
  }
  kind:

}
```

即将完成。 只需为这些属性提供值。

同样，Intellisense 可为你提供帮助。 对于 `name`，请提供包含存储帐户名称的参数。 对于 `location`，请将其设置为 `eastus`。 添加 SKU 名称和类型时，Intellisense 会显示有效选项。

完成后，你的 Bicep 文件将如下所示：

```bicep
@minLength(3)
@maxLength(24)
param storageName string

resource virtualNetwork 'Microsoft.Network/virtualNetworks@2019-11-01' = {
  name: 'examplevnet'
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
    subnets: [
      {
        name: 'Subnet-1'
        properties: {
          addressPrefix: '10.0.0.0/24'
        }
      }
      {
        name: 'Subnet-2'
        properties: {
          addressPrefix: '10.0.1.0/24'
        }
      }
    ]
  }
}

resource exampleStorage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: storageName
  location: 'eastus'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
}
```

有关 Bicep 语法的详细信息，请参阅 [Bicep 结构](./file.md)。

## <a name="visualize-resources"></a>可视化资源

可以查看文件中资源的表示形式。

在左上角，选择可视化工具按钮以打开 Bicep 可视化工具。

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/bicep-visualizer.png" alt-text="Bicep 可视化工具":::

可视化工具显示 Bicep 文件中定义的资源以及资源依赖关系信息。 本快速入门中定义的两个资源不存在依赖关系，因此你在这两个资源之间看不到连接器。

## <a name="deploy-the-bicep-file"></a>部署 Bicep 文件

若要部署已创建的文件，请打开 PowerShell 或 Azure CLI。 如果要使用集成的 Visual Studio Code 终端，请选择 `ctrl` + ```` ` ```` 组合键。 将当前目录更改为 Bicep 文件所在的位置。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name exampleRG --location eastus

az deployment group create --resource-group exampleRG --template-file main.bicep --parameters storageName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name exampleRG -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName exampleRG -TemplateFile ./main.bicep -storageName "{your-unique-name}"
```

---

> [!NOTE]
> 将 {your-unique-name}（包括大括号）替换为唯一的存储帐户名称。

部署完成后，应会看到一条指出部署成功的消息。 如果收到错误消息，指出存储帐户已被占用，则表示提供的存储名称已被使用。 请提供更有可能是唯一的名称。

## <a name="clean-up-resources"></a>清理资源

如果不再需要本文中创建的 Azure 资源，请使用 Azure CLI 或 Azure PowerShell 模块删除快速入门资源组。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name exampleRG
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name exampleRG
```

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Microsoft Learn 中的 Bicep](learn-bicep.md)
