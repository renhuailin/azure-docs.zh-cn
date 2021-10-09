---
title: 快速入门：使用 PowerShell/Azure CLI 创建 Purview 帐户
description: 本快速入门介绍如何使用 Azure PowerShell/Azure CLI 创建 Azure Purview 帐户。
author: hophanms
ms.author: hophan
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: purview
ms.custom:
- mode-api
ms.openlocfilehash: 0e3cb8399e42dc651a48ada6018c58cb4f48e5d8
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217346"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>快速入门：使用 Azure PowerShell/Azure CLI 创建 Azure Purview 帐户

在本快速入门中，你将使用 Azure PowerShell/Azure CLI 创建 Azure Purview 帐户。 可以使用 [Purview 的 PowerShell 参考](/powershell/module/az.purview/)，但本文将指导你完成使用 PowerShell 创建帐户所需的所有步骤。

Azure Purview 是一种数据管理服务，可帮助你管理和治理数据环境。 通过跨本地源、多云源和服务型软件 (SaaS) 源连接到数据，Purview 创建了最新的信息地图。 它对敏感数据进行标识和分类，并提供端到端的沿袭。 数据使用者能够发现整个组织的数据，而数据管理员能够审核数据、保护数据和确保对你的数据的正确使用。

有关 Purview 的详细信息，[请参阅概述页](overview.md)。 有关在组织中部署 Purview 的详细信息，[请参阅部署最佳做法](deployment-best-practices.md)。

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

### <a name="install-powershell"></a>安装 PowerShell

 在客户端计算机上安装 Azure PowerShell 或 Azure CLI 以部署模板：[命令行部署](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="create-an-azure-purview-account"></a>创建 Azure Purview 帐户

1. 使用 Azure 凭据登录

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. 如果你有多个 Azure 订阅，请选择要使用的订阅：

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. 为 Purview 帐户创建资源组。 如果已有资源组，则可跳过此步骤：

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. 创建 Purview 模板文件，如 `purviewtemplate.json`。 可以更新 `name`、`location` 和 `capacity`（`4` 或 `16`）：

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. 部署 Purview 模板

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    若要运行此部署命令，必须具有 Azure CLI 的 [最新版本](/cli/azure/install-azure-cli)。
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. 部署命令将返回结果。 查看 `ProvisioningState` 以确定部署是否成功。
    
## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建 Azure Purview 帐户。

请按照接下来的这些文章学习如何导航 Purview Studio、创建集合以及授予对 Purview 的访问权限。

* [如何使用 Purview Studio](use-purview-studio.md)
* [创建集合](quickstart-create-collection.md)
* [将用户添加到 Azure Purview 帐户](catalog-permissions.md)
