---
title: 创建和部署模板规格
description: 了解如何通过 ARM 模板创建模板规格。 然后，将模板规格部署到订阅中的资源组。
author: tfitzmac
ms.date: 05/04/2021
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: f44c485d0a7eb7f546c1b227ca121d906225b43e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631203"
---
# <a name="quickstart-create-and-deploy-template-spec"></a>快速入门：创建和部署模板规格

本快速入门介绍如何将 Azure 资源管理器模板（ARM 模板）打包为[模板规格](template-specs.md)。然后部署该模板规格。模板规格包含用于部署存储帐户的 ARM 模板。

## <a name="prerequisites"></a>先决条件

具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 若要将模板规则与 Azure PowerShell 一起使用，必须安装[版本 5.0.0 或更高版本](/powershell/azure/install-az-ps)。 若要将其与 Azure CLI 一起使用，请使用[版本 2.14.2 或更高版本](/cli/azure/install-azure-cli)。

## <a name="create-template"></a>创建模板

可以根据本地模板创建模板规格。 复制以下模板并将其本地保存到名为 azuredeploy.json 的文件中。 本快速入门假设你已将模板保存到路径 c:\Templates\azuredeploy.json，不过你可以使用任何路径。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json":::

## <a name="create-template-spec"></a>创建模板规格

模板规格是名为 `Microsoft.Resources/templateSpecs` 的资源类型。 若要创建模板规格，可以使用 PowerShell、Azure CLI、门户或 ARM 模板。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 创建新的资源组以包含模板规格。

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. 在该资源组中创建模板规格。 将新的模板规格命名为 storageSpec。

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 创建新的资源组以包含模板规格。

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. 在该资源组中创建模板规格。 将新的模板规格命名为 storageSpec。

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索模板规格。 从可用选项中选择“模板规格”。

    :::image type="content" source="./media/quickstart-create-template-specs/search-template-spec.png" alt-text="搜索模板规格":::

1. 选择“导入模板”。

    :::image type="content" source="./media/quickstart-create-template-specs/import-template.png" alt-text="导入模板":::

1. 选择文件夹图标。

    :::image type="content" source="./media/quickstart-create-template-specs/open-folder.png" alt-text="打开文件夹":::

1. 导航到保存的本地模板，然后将其选中。 选择“打开”。
1. 选择“导入”  。

    :::image type="content" source="./media/quickstart-create-template-specs/select-import.png" alt-text="选择导入选项":::

1. 提供以下值：

    - **名称**：输入模板规格的名称。例如 storageSpec
    - **订阅**：选择用于创建模板规格的 Azure 订阅。
    - **资源组**：选择“新建”，然后输入新的资源组名称。  例如 templateSpecRG。
    - **位置**：选择资源组的位置。 例如美国西部 2。
    - **版本**：输入模板规格的版本。使用 1.0。

1. 选择“查看 + 创建”  。
1. 选择“创建”  。

# <a name="arm-template"></a>[ARM 模板](#tab/azure-resource-manager)

> [!NOTE]
> 建议不使用 ARM 模板，而是使用 PowerShell 或 CLI 来创建模板规格。这些工具会自动将链接的模板转换为连接到主模板的项目。 使用 ARM 模板创建模板规格时，必须手动将这些链接的模板添加为项目，这可能会很复杂。

1. 使用 ARM 模板创建模板规格时，该模板将嵌入资源定义。 你需对本地模板进行一些更改。 复制以下模板并将其本地保存为 azuredeploy.json。

    > [!NOTE]
    > 在嵌入的模板中，必须使用第二个左括号对所有[模板表达式](template-expressions.md)进行转义。 请使用 `"[[`，而不是 `"[`。 JSON 数组仍使用单个左括号。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2021-05-01",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2021-05-01",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "mainTemplate": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2021-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. 使用 Azure CLI 或 PowerShell 创建新的资源组。

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. 使用 Azure CLI 或 PowerShell 部署模板。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>部署模板规格

若要部署模板规格，请使用部署模板所用的部署命令。 传入模板规格的资源 ID 以进行部署。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 创建资源组以包含新的存储帐户。

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. 获取模板规格的资源 ID。

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. 部署模板规格。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. 提供的参数与 ARM 模板的完全一样。 使用存储帐户类型的参数重新部署模板规格。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 创建资源组以包含新的存储帐户。

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. 获取模板规格的资源 ID。

    ```azurecli
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > 获取模板规格 ID 并将其分配到 Windows PowerShell 中的变量时存在一个已知问题。

1. 部署模板规格。

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. 提供的参数与 ARM 模板的完全一样。 使用存储帐户类型的参数重新部署模板规格。

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 选择已创建的模板规格。

    :::image type="content" source="./media/quickstart-create-template-specs/select-template-spec.png" alt-text="选择模板规格":::

1. 选择“部署”。

    :::image type="content" source="./media/quickstart-create-template-specs/deploy-template-spec.png" alt-text="部署模板规格":::

1. 提供以下值：

    - **订阅**：选择用于创建资源的 Azure 订阅。
    - **资源组**：选择“新建”，然后输入“storageRG” 。
    - **存储帐户类型**：选择“Standard_GRS”。

1. 选择“查看 + 创建”。
1. 选择“创建”  。

# <a name="arm-template"></a>[ARM 模板](#tab/azure-resource-manager)

1. 复制以下模板并将其本地保存到名为 storage.json 的文件中。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2021-04-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. 使用 Azure CLI 或 PowerShell 为存储帐户创建新的资源组。

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. 使用 Azure CLI 或 PowerShell 部署模板。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>授予访问权限

如果要让组织中的其他用户部署模板规格，你需要向他们授予读取权限。 对于包含要共享的模板规格的资源组，你可以将读取者角色分配给相应的 Azure AD 组。 有关详细信息，请参阅[教程：使用 Azure PowerShell 授予组对 Azure 资源的访问权限](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)。

## <a name="update-template"></a>更新模板

假设你已在模板规格中标识了要对模板进行的更改。以下模板类似于之前的模板，不同之处在于其存储帐户名称增加了前缀。 复制以下模板并更新 azuredeploy.json 文件。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/templatespecs/azuredeploy2.json":::

## <a name="update-template-spec-version"></a>更新模板规格版本

在现有模板规格中添加名为 `2.0` 的新版本，而不是为修改后的模板创建新的模板规格。用户可以选择任一版本进行部署。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 创建新的模板规格版本。

   ```powershell
   New-AzTemplateSpec `
     -Name storageSpec `
     -Version "2.0" `
     -ResourceGroupName templateSpecRG `
     -Location westus2 `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

1. 若要部署新版本，请获取 `2.0` 版本的资源 ID。

   ```powershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "2.0").Versions.Id
   ```

1. 部署该版本。 为存储帐户名称提供一个前缀。

   ```powershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -namePrefix "demoaccount"
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 创建新的模板规格版本。

   ```azurecli
   az ts create \
     --name storageSpec \
     --version "2.0" \
     --resource-group templateSpecRG \
     --location "westus2" \
     --template-file "c:\Templates\azuredeploy.json"
   ```

1. 若要部署新版本，请获取 `2.0` 版本的资源 ID。

   ```azurecli
   id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "2.0" --query "id")
   ```

1. 部署该版本。 为存储帐户名称提供一个前缀。

   ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters namePrefix='demoaccount'
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在模板规格中，选择“创建新版本”。

   :::image type="content" source="./media/quickstart-create-template-specs/select-versions.png" alt-text="创建新版本":::

1. 将新版本命名为 `2.0`，并且可以根据需要添加说明。 选择“编辑模板”。

   :::image type="content" source="./media/quickstart-create-template-specs/add-version-name.png" alt-text="命名新版本":::

1. 将模板的内容替换为更新后的模板。 选择 **“查看 + 保存”** 。
1. 选择“保存更改”。

1. 若要部署新版本，请选择“版本”

   :::image type="content" source="./media/quickstart-create-template-specs/see-versions.png" alt-text="列出版本":::

1. 对于要部署的版本，请依次选择三个点和“部署”。

   :::image type="content" source="./media/quickstart-create-template-specs/deploy-version.png" alt-text="选择要部署的版本":::

1. 像部署早期版本时一样填写字段。
1. 选择“查看 + 创建”。
1. 选择“创建”  。

# <a name="arm-template"></a>[ARM 模板](#tab/azure-resource-manager)

1. 同样，必须对本地模板进行一些更改，确保其在模板规格下正常工作。 复制以下模板并将其本地保存为 azuredeploy.json。

   ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2021-05-01",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2021-05-01",
              "name": "2.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "mainTemplate": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    },
                    "namePrefix": {
                      "type": "string",
                      "maxLength": 11,
                      "defaultValue": "store",
                      "metadata": {
                        "description": "Prefix for storage account name"
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2021-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. 若要将新版本添加到模板规格中，请使用 Azure CLI 或 PowerShell 部署模板。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

1. 复制以下模板并将其本地保存到名为 storage.json 的文件中。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2021-04-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '2.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. 使用 Azure CLI 或 PowerShell 部署模板。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="clean-up-resources"></a>清理资源

若要清理本快速入门中部署的资源，请删除创建的两个资源组。

1. 在 Azure 门户上的左侧菜单中选择“资源组”。

1. 在“按名称筛选”字段中输入资源组名称（templateSpecRG 和 storageRG）。

1. 选择资源组名称。

1. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

若要了解有关创建包含关联模板的模板规格的信息，请参阅[创建关联模板的模板规格](template-specs-create-linked.md)。
