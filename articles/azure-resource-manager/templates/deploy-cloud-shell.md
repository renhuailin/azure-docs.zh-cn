---
title: Cloud Shell 部署模板
description: 使用 Azure 资源管理器和 Azure Cloud Shell 将资源部署到 Azure。 资源在 Azure 资源管理器模板 (ARM 模板) 中定义。
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: c67251a33b6197603be27086bcc6cd047e0c414b
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028601"
---
# <a name="deploy-arm-templates-from-azure-cloud-shell"></a>从 Azure Cloud Shell 部署 ARM 模板

你可以使用 [Azure Cloud Shell](../../cloud-shell/overview.md) 部署 Azure 资源管理器模板 (ARM 模板) 。 你可以部署远程存储的 ARM 模板或存储在本地存储帐户上用于 Cloud Shell 的 ARM 模板。

你可以部署到任何范围。 本文介绍如何部署到资源组。

## <a name="deploy-remote-template"></a>部署远程模板

要部署外部模板，请提供与用于任何外部部署的完全相同的模板 URI。 外部模板可以位于 GitHub 存储库中，也可以是外部存储帐户。

1. 打开 Cloud Shell 提示符。

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="打开 Cloud Shell":::

1. 若要部署模板，请使用以下命令：

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>部署本地模板

若要部署本地模板，你必须首先将模板上载到连接到 Cloud Shell 会话的存储帐户。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择 Cloud Shell 资源组。 名称模式为 `cloud-shell-storage-<region>`。

   ![选择资源组](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. 选择适用于 Cloud Shell 的存储帐户。

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="选择存储帐户":::

1. 选择 " **文件共享**"。

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="选择文件共享":::

1. 选择 Cloud Shell 的默认文件共享。 文件共享的名称格式为 `cs-<user>-<domain>-com-<uniqueGuid>` 。

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="默认文件共享":::

1. 添加一个新目录来保存模板。 选择该目录。

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="添加目录":::

1. 选择“上传”。

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="上载模板":::

1. 找到并上传模板。

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="选择模板":::

1. 打开 Cloud Shell 提示符。

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="打开 Cloud Shell":::

1. 导航到 **clouddrive** 目录。 导航到添加的用于保存模板的目录。

1. 若要部署模板，请使用以下命令：

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>后续步骤

- 有关部署命令的详细信息，请参阅 [通过 arm 模板部署资源和 Azure CLI](deploy-cli.md) 以及 [通过 arm 模板和 Azure PowerShell 部署资源](deploy-powershell.md)。
- 若要在部署模板前预览更改，请参阅 [ARM 模板部署假设操作](template-deploy-what-if.md)。
