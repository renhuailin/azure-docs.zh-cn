---
title: 教程：使用 ARM 模板发布 Azure Static Web Apps
description: 为 Azure Static Web Apps 创建和部署 ARM 模板
services: static-web-apps
author: petender
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 07/13/2021
ms.author: petender
ms.openlocfilehash: 06f415492157a52b3a258fa2191e1a88e211bccd
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122608834"
---
# <a name="tutorial-publish-azure-static-web-apps-using-an-arm-template"></a>教程：使用 ARM 模板发布 Azure Static Web Apps

本文说明如何使用 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)（ARM 模板）部署 [Azure Static Web Apps](./overview.md)。

在本教程中，学习：

> [!div class="checklist"]
> - 为 Azure Static Web Apps 创建 ARM 模板
> - 部署 ARM 模板以创建 Azure Static Web Apps 实例

## <a name="prerequisites"></a>先决条件

- **有效的 Azure 帐户：** 如果你没有帐户，可以 [免费创建一个帐户](https://azure.microsoft.com/free/)。
- **GitHub 帐户：** 如果没有帐户，可以 [免费创建 GitHub 帐户](https://github.com)
- **ARM 模板的编辑器：** 查看和编辑模板需要 JSON 编辑器。 具有 [Azure 资源管理器工具扩展](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)的 Visual Studio Code 适用于编辑 ARM 模板。 有关如何安装和配置 Visual Studio Code 的说明，请参阅[快速入门：使用 Visual Studio Code 创建 ARM 模板](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md)。

- **Azure CLI 或 Azure PowerShell**：部署 ARM 模板需要使用命令行工具。 有关安装说明，请参阅：
  - [在 Windows OS 上安装 Azure CLI](/cli/azure/install-azure-cli-windows)
  - [在 Linux OS 上安装 Azure CLI](/cli/azure/install-azure-cli-linux)
  - [在 macOS 上安装 Azure CLI](/cli/azure/install-azure-cli-macos)
  - [安装 Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="create-a-github-personal-access-token"></a>创建 GitHub 个人访问令牌

ARM 模板中参数之一是 `repositoryToken`，它使 ARM 部署过程可以与包含静态站点源代码的 GitHub 存储库交互。 

1. 从 GitHub 帐户配置文件（位于右上角）中选择“设置”。

1. 选择“开发人员设置”。

1. 选择“个人访问令牌”。

1. 选择“生成新令牌”。

1. 在“说明”字段为此令牌提供名称，例如 myfirstswadeployment。

1. 指定以下范围：存储库、工作流、写入:包

1. 选择“生成令牌”。

1. 复制令牌值并将其粘贴到文本编辑器以备稍后使用。

> [!IMPORTANT]
> 请确保复制此令牌并将其存储在安全的位置。 请考虑将此令牌存储在 [Azure Key Vault](../azure-resource-manager/templates/template-tutorial-use-key-vault.md)，并在 ARM 模板中对其进行访问。

## <a name="create-a-github-repo"></a>创建 GitHub 存储库

本文使用 GitHub 模板存储库，使你能够轻松入门。 该模板具有一个入门应用，你可以使用 Azure Static Web Apps 部署它。

1. 导航到以下位置以创建新存储库：
    1. [https://github.com/staticwebdev/vanilla-basic/generate](https://github.com/login?return_to=/staticwebdev/vanilla-basic/generate)

1. 将存储库命名为 myfirstswadeployment

    > [!NOTE]
    > Azure 静态 Web 应用需要至少一个 HTML 文件来创建 Web 应用。 在此步骤中创建的存储库包括单个 index.html 文件。

1. 选择“从模板创建存储库”。

    :::image type="content" source="./media/getting-started/create-template.png" alt-text="从模板创建存储库":::

## <a name="create-the-arm-template"></a>创建 ARM 模板

满足先决条件后，下一步是定义 ARM 部署模板文件。

1. 创建新文件夹以保存 ARM 模板。

1. 创建一个新文件并将其命名为 azuredeploy.json。

1. 将以下 ARM 模板片段粘贴到 azuredeploy.json。

    ```json
        {
            "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "name": {
                    "type": "string"
                },
                "location": {
                    "type": "string"
                },
                "sku": {
                    "type": "string"
                },
                "skucode": {
                    "type": "string"
                },
                "repositoryUrl": {
                    "type": "string"
                },
                "branch": {
                    "type": "string"
                },
                "repositoryToken": {
                    "type": "securestring"
                },
                "appLocation": {
                    "type": "string"
                },
                "apiLocation": {
                    "type": "string"
                },
                "appArtifactLocation": {
                    "type": "string"
                },
                "resourceTags": {
                    "type": "object"
                },
                "appSettings": {
                    "type": "object"
                }
            },
            "resources": [
                {
                    "apiVersion": "2021-01-15",
                    "name": "[parameters('name')]",
                    "type": "Microsoft.Web/staticSites",
                    "location": "[parameters('location')]",
                    "tags": "[parameters('resourceTags')]",
                    "properties": {
                        "repositoryUrl": "[parameters('repositoryUrl')]",
                        "branch": "[parameters('branch')]",
                        "repositoryToken": "[parameters('repositoryToken')]",
                        "buildProperties": {
                            "appLocation": "[parameters('appLocation')]",
                            "apiLocation": "[parameters('apiLocation')]",
                            "appArtifactLocation": "[parameters('appArtifactLocation')]"
                        }
                    },
                    "sku": {
                        "Tier": "[parameters('sku')]",
                        "Name": "[parameters('skuCode')]"
                    },
                    "resources":[
                        {
                            "apiVersion": "2021-01-15",
                            "name": "appsettings",
                            "type": "config",
                            "location": "[parameters('location')]",
                            "properties": "[parameters('appSettings')]",
                            "dependsOn": [
                                "[resourceId('Microsoft.Web/staticSites', parameters('name'))]"
                            ]
                        }
                    ]
                }
            ]
        }

    ```

1. 创建一个新文件并将其命名为 azuredeploy.parameters.json。

1. 将以下 ARM 模板片段粘贴到 azuredeploy.parameters.json。

    ```json
        {
            "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "name": {
                    "value": "myfirstswadeployment"
                },
                "location": { 
                    "value": "Central US"
                },   
                "sku": {
                    "value": "Free"
                },
                "skucode": {
                    "value": "Free"
                },
                "repositoryUrl": {
                    "value": "https://github.com/<YOUR-GITHUB-USER-NAME>/<YOUR-GITHUB-REPOSITORY-NAME>"
                },
                "branch": {
                    "value": "main"
                },
                "repositoryToken": {
                    "value": "<YOUR-GITHUB-PAT>" 
                },
                "appLocation": {
                    "value": "/"
                },
                "apiLocation": {
                    "value": ""
                },
                "appArtifactLocation": {
                    "value": "src"
                },
                "resourceTags": {
                    "value": {
                        "Environment": "Development",
                        "Project": "Testing SWA with ARM",
                        "ApplicationName": "myfirstswadeployment"
                    }
                },
                "appSettings": {
                    "value": {
                        "MY_APP_SETTING1": "value 1",
                        "MY_APP_SETTING2": "value 2"
                    }
                }
            }
        }
    ```

1. 更新以下参数。

    | 参数 | 预期值 |
    | --- | --- |
    | `repositoryUrl` | 提供 Static Web Apps GitHub 存储库的 URL。 |
    | `repositoryToken` | 提供 GitHub PAT 令牌。 |

1. 在下一步中运行部署之前保存更新。

## <a name="running-the-deployment"></a>运行部署

你需要使用 Azure CLI 或 Azure PowerShell 来部署模板。

### <a name="sign-in-to-azure"></a>登录 Azure

若要部署模板，请登录到 Azure CLI 或 Azure PowerShell。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

---

如果你有多个 Azure 订阅，请选择要使用的订阅。 将 `<SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>` 替换为订阅信息：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription <SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext <SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>
```

---

## <a name="create-a-resource-group"></a>创建资源组

部署模板时，请指定一个包含相关资源的资源组。 在运行部署命令之前，请使用 Azure CLI 或 Azure PowerShell 创建该资源组。

> [!NOTE]
> 本文中的 CLI 示例针对 Bash shell 编写。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
resourceGroupName="myfirstswadeployRG"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = "myfirstswadeployRG"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

---

## <a name="deploy-template"></a>部署模板

使用这些部署选项之一来部署模板。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file <PATH-TO-AZUREDEPLOY.JSON> \
  --parameters <PATH-TO-AZUREDEPLOY.PARAMETERS.JSON> \
  --verbose
```

若要详细了解如何使用 Azure CLI 部署模板，请参阅[使用 ARM 模板和 Azure CLI 部署资源](../azure-resource-manager/templates/deploy-cli.md)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$templateparameterFile = Read-Host -Prompt "Enter the template parameter file path and file name"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -TemplateParameterFile $templateparameterfile `
  -verbose
```

若要详细了解如何使用 Azure PowerShell 部署模板，请参阅[使用 ARM 模板和 Azure PowerShell 部署资源](../azure-resource-manager/templates/deploy-powershell.md)。

---

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>清理资源

通过删除资源组来清理你部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [配置静态 Web 应用](./configuration.md)