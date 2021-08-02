---
title: 使用 Cloud Shell 部署 Bicep 文件
description: 使用 Azure 资源管理器和 Azure Cloud Shell 将资源部署到 Azure。 Bicep 文件中定义了资源。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: fa74250a290cc3e254e5c9f3757bb3e712c020da
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025876"
---
# <a name="deploy-bicep-files-from-azure-cloud-shell"></a>从 Azure Cloud Shell 部署 Bicep 文件

可以使用 [Azure Cloud Shell](../../cloud-shell/overview.md) 来部署 Bicep 文件。 目前只能从 Cloud Shell 部署本地 Bicep 文件。

你可以部署到任何范围。 本文介绍如何部署到资源组。

## <a name="deploy-local-bicep-file"></a>部署本地 Bicep 文件

若要部署本地 Bicep 文件，必须先将 Bicep 文件上传到 Cloud Shell 会话。

1. 登录到 [Cloud Shell](https://shell.azure.com)。
1. 选择“PowerShell”或“Bash”。 

    :::image type="content" source="./media/deploy-cloud-shell/bicep-cloud-shell-bash-powershell.png" alt-text="选择 Bash 或 PowerShell":::

1. 依次选择“上传/下载文件”、“上传” 。

    :::image type="content" source="./media/deploy-cloud-shell/bicep-cloud-shell-upload.png" alt-text="上传文件":::

1. 选择要上传的 Bicep 文件，然后选择“打开”。
1. 若要部署 Bicep 文件，请使用以下命令：

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.bicep \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.bicep `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>后续步骤

- 有关部署命令的详细信息，请参阅[通过 Bicep 和 Azure CLI 部署资源](deploy-cli.md)和[通过 Bicep 和 Azure PowerShell 部署资源](deploy-powershell.md)。
- 若要在部署 Bicep 文件之前预览更改，请参阅 [Bicep 部署 What-if 操作](./deploy-what-if.md)。
