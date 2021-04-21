---
title: 教程 - 创建和部署 Azure 资源管理器 Bicep 文件
description: 创建用于部署 Azure 资源的第一个 Bicep 文件。 本教程介绍 Bicep 文件语法，以及如何部署存储帐户。
author: mumian
ms.date: 04/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 0ffd8a97d797144d458e7ec5836042cc6d8d8193
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306666"
---
# <a name="tutorial-create-and-deploy-first-azure-resource-manager-bicep-file"></a>教程：创建和部署第一个 Azure 资源管理器 Bicep 文件

本教程介绍 [Bicep](./bicep-overview.md)。 其中将会说明如何创建初学者 Bicep 文件并将其部署到 Azure。 本教程还将介绍 Bicep 文件的结构，以及处理 Bicep 文件时所需的工具。 完成本教程大约需要 **12 分钟**，但实际时间根据需要安装的工具数量而异。

本文是相关教学系列的第一篇教程。 在学习该教程系列的过程中，你将逐步修改入门 Bicep 文件，直到了解了 Bicep 文件的所有核心部分。 这些元素是更复杂的 Bicep 文件的构建基块。 我们希望在本教程系列结束时，你能够自信地创建自己的 Bicep 文件，并准备好使用 Bicep 文件自动完成部署。

若要了解使用 Bicep 所带来的好处，以及为何要使用 Bicep 文件自动完成部署，请参阅 [Bicep](./bicep-overview.md)。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-tools"></a>获取工具

首先，请确保已获取创建和部署 Bicep 文件所需的工具。 在本地计算机上安装这些工具。

### <a name="editor"></a>编辑器

若要创建 Bicep 文件，需要一个很好的编辑器。 我们建议使用装有 Bicep 扩展的 Visual Studio Code。 如果需要安装这些工具，请参阅[配置 Bicep 开发环境](./bicep-install.md#development-environment)。

### <a name="command-line-deployment"></a>命令行部署

您可以使用 Azure CLI 或 Azure PowerShell 来部署 Bicep 文件。 对于 Azure CLI，需要 2.20.0 或更高版本；对于 Azure PowerShell，需要 5.6.0 或更高版本。 有关安装说明，请参阅：

- [安装 Azure PowerShell](/powershell/azure/install-az-ps)
- [在 Windows 上安装 Azure CLI](/cli/azure/install-azure-cli-windows)
- [在 Linux 上安装 Azure CLI](/cli/azure/install-azure-cli-linux)
- [在 macOS 上安装 Azure CLI](/cli/azure/install-azure-cli-macos)

安装 Azure PowerShell 或 Azure CLI 后，请务必完成首次登录。 有关帮助，请参阅[登录 - PowerShell](/powershell/azure/install-az-ps#sign-in) 或[登录 - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in)。

好了，现在可以开始了解 Bicep。

## <a name="create-your-first-bicep-file"></a>创建第一个 Bicep 文件

1. 打开装有 Bicep 扩展的 Visual Studio Code。
1. 在“文件”菜单中，选择“新建文件”以创建新的文件。 
1. 在“文件”菜单中选择“另存为”。 
1. 将文件命名为 _azuredeploy_，并选择 _bicep_ 文件扩展名。 完整的文件名为 _azuredeploy.bicep_。
1. 将该文件保存到工作站。 选择容易记住的路径，因为稍后在部署 Bicep 文件时需要提供该路径。
1. 将以下内容复制并粘贴到该文件中：

    ```bicep
    resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
      name: '{provide-unique-name}'  // must be globally unique
      location: 'eastus'
      sku: {
        name: 'Standard_LRS'
      }
      kind: 'StorageV2'
      properties: {
        supportsHttpsTrafficOnly: true
      }
    }
    ```

    Visual Studio Code 环境如下所示：

    ![Visual Studio Code - 第一个 Bicep 文件](./media/Bicep-tutorial-create-first-bicep/resource-manager-visual-studio-code-first-bicep.png)

    资源声明具有四个组件：

    - **资源**：关键字。
    - **符号名称** (stg)：符号名称是用于在整个 bicep 文件中引用资源的标识符。 它不是在部署资源时的资源名称。 资源名称由 **name** 属性定义。  请参阅此列表中的第四个组件。 为使教程易于理解，在本教程系列中，我们将 **stg** 用作存储帐户资源的符号名称。 若要查看如何使用符号名称获取对象属性的完整列表，请参阅[添加输出](./bicep-tutorial-add-outputs.md)。
    - **资源类型** (Microsoft.Storage/storageAccounts@2019-06-01)：它包括资源提供程序 (Microsoft.Storage)、资源类型 (storageAccounts) 和 apiVersion (2019-06-01)。 每个资源提供程序都发布了其自身的 API 版本，因此此值与特定的类型相关。 可以从 [ARM 模板参考](/azure/templates/)中查找各种 Azure 资源的更多类型和 apiVersion。
    - **属性**（= {...} 内部的任何内容）：要为给定的资源类型指定的特定属性。 这些属性与 ARM 模板中提供的属性完全相同。 每个资源都有一个 `name` 属性。 大多数资源还有一个 `location` 属性，该属性用于设置资源部署到的区域。 其他属性因资源类型和 API 版本而异。 了解 API 版本与可用属性之间的关联非常重要，因此，让我们直接了解详情。

        对于此存储帐户，可以在 [storageAccounts 2019-06-01](/azure/templates/microsoft.storage/2019-06-01/storageaccounts) 中看到 API 版本。 请注意，你并未将所有属性添加到 Bicep 文件。 许多属性是可选的。 `Microsoft.Storage` 资源提供程序可能发布了新的 API 版本，但要部署的版本不需要更改。 可以继续使用该版本，部署的结果是一致的。

        如果查看较旧的 API 版本（例如 [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)），你会发现提供了一个较小的属性集。

        如果你决定更改资源的 API 版本，请确保评估该版本的属性，并相应地调整 Bicep 文件。

    有关详细信息，请参阅 [Bicep 结构](./bicep-file.md)。

    名称属性有一个注释。  将 `//` 用于单行注释或将 `/* ... */` 用于多行注释

1. 将 `{provide-unique-name}`（包括大括号 `{}`）替换为唯一的存储帐户名称。

    > [!IMPORTANT]
    > 存储帐户名称在 Azure 中必须是唯一的。 该名称只能包含小写字母或数字。 其长度不能超过 24 个字符。 可以尝试使用某种命名模式，例如，使用 **store1** 作为前缀，然后添加你的姓名首字母缩写和当天的日期。 例如，使用的名称类似于 **store1abc09092019**。

    存储帐户的唯一名称不容易猜出，尤其是在大型部署的自动化中不太适合进行这种猜测。 稍后在本教程系列中，你将使用 Bicep 功能来更轻松地创建唯一名称。

1. 保存文件。

祝贺你，现已创建第一个 Bicep 文件。

## <a name="sign-in-to-azure"></a>登录 Azure

若要开始使用 Azure PowerShell/Azure CLI，请使用你的 Azure 凭据登录。

使用以下代码部分中的选项卡在 Azure PowerShell 与 Azure CLI 之间进行选择。 本文中的 CLI 示例针对 Bash shell 编写。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

如果你有多个 Azure 订阅，请选择要使用的订阅。 将 `[SubscriptionID/SubscriptionName]` 和方括号 `[]` 替换为你的订阅信息：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>创建资源组

部署 Bicep 文件时，请指定一个包含资源的资源组。 在运行部署命令之前，请使用 Azure CLI 或 Azure PowerShell 创建该资源组。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-bicep-file"></a>部署 Bicep 文件

Bicep 是基于 Azure 资源管理器模板（ARM 模板）的透明抽象。 在部署每个 Bicep 文件之前，需将该文件编译成标准的 ARM 模板。 可以在部署 Bicep 文件之前将其编译成 ARM 模板，也可以直接部署 Bicep 文件。 若要部署 Bicep 文件，请使用 Azure CLI 或 Azure PowerShell。 使用创建的资源组。 为部署指定一个适当的名称，以便可以在部署历史记录中轻松识别该部署。 为方便起见，另请创建一个用于存储 Bicep 文件路径的变量。 此变量使你能够更轻松地运行部署命令，因为无需在每次部署时都重新键入路径。 将 `{provide-the-path-to-the-bicep-file}`（包括大括号 `{}`）替换成扩展名为 _.bicep_ 的 Bicep 文件的路径。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要运行此部署 cmdlet，你必须具有 Azure PowerShell 的[最新版本](/powershell/azure/install-az-ps)。

```azurepowershell
$bicepFile = "{provide-the-path-to-the-bicep-file}"
New-AzResourceGroupDeployment `
  -Name firstbicep `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要运行此部署命令，必须具有 Azure CLI 的 [最新版本](/cli/azure/install-azure-cli)。

```azurecli
bicepFile="{provide-the-path-to-the-bicep-file}"
az deployment group create \
  --name firstbicep \
  --resource-group myResourceGroup \
  --template-file $bicepFile
```

---

部署命令将返回结果。 查看 `ProvisioningState` 以确定部署是否成功。

> [!NOTE]
> 如果部署失败，请使用 `verbose` 开关获取有关正在创建的资源的信息。 使用 `debug` 开关获取调试的详细信息。

## <a name="verify-deployment"></a>验证部署

可以通过在 Azure 门户中浏览资源组来验证部署。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左侧菜单中选择“资源组”。 

1. 选择在上一过程中部署的资源组。 默认名称为 **myResourceGroup**。 你应会看到，该资源组中未部署任何资源。

1. 请注意，在概述的右上角显示了部署状态。 选择“1 成功”。

   ![查看部署状态](./media/bicep-tutorial-create-first-bicep/deployment-status.png)

1. 将会看到该资源组的部署历史记录。 选择“firstbicep”。

   ![选择部署](./media/bicep-tutorial-create-first-bicep/select-from-deployment-history.png)

1. 将会看到部署摘要。 已部署了一个存储帐户。 请注意，在左侧可以查看部署过程中使用的输入、输出和模板。

   ![查看部署摘要](./media/bicep-tutorial-create-first-bicep/view-deployment-summary.png)

## <a name="clean-up-resources"></a>清理资源

若要继续学习下一篇教程，则不需删除该资源组。

如果就此停止学习，请删除该资源组。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

现已创建一个要部署到 Azure 的简单 Bicep 文件。  后续教程会介绍如何将参数、变量、输出和模块添加到 Bicep 文件。 这些功能是更复杂的 Bicep 文件的构建基块。

> [!div class="nextstepaction"]
> [添加参数](bicep-tutorial-add-parameters.md)
