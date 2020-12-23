---
title: 快速入门：使用 Azure PowerShell/Azure CLI 创建 Azure Purview 帐户（预览版）
description: 本快速入门介绍如何使用 Azure PowerShell/Azure CLI 创建 Azure Purview 帐户。
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 11/23/2020
ms.openlocfilehash: 7db546ec9038403bd9e34f637e9d9de368277d52
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400296"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>快速入门：使用 Azure PowerShell/Azure CLI 创建 Azure Purview 帐户

> [!IMPORTANT]
> Azure Purview 当前以预览版提供。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本快速入门中，你将使用 Azure PowerShell/Azure CLI 创建 Azure Purview 帐户。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 用于登录到 Azure 的用户帐户必须属于参与者或所有者角色，或者是 Azure 订阅的管理员。

* 你拥有 [Azure Active Directory 租户](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)。

* 在客户端计算机上安装 Azure PowerShell 或 Azure CLI 以部署模板：[命令行部署](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

## <a name="configure-your-subscription"></a>配置订阅

如有必要，请按照以下步骤配置订阅，以启用 Azure Purview 在订阅中运行：

   1. 在 Azure 门户中，搜索并选择“订阅”。

   1. 从订阅列表中选择要使用的订阅。 需要订阅的管理访问权限。

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="显示如何在 Azure 门户中选择订阅的屏幕截图。":::

   1. 对于订阅，选择“资源提供程序”  。 在“资源提供程序”窗格上，搜索并注册所有三个资源提供程序： 
       1. Microsoft.Purview
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      如果尚未注册，请选择“注册”以进行注册。

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="显示如何在 Azure 门户中注册 Microsoft dot Azure Purview 资源提供程序的屏幕截图。":::

## <a name="create-an-azure-purview-account-instance"></a>创建 Azure Purview 帐户实例

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

请继续阅读下一篇文章，了解如何允许用户访问 Azure Purview 帐户。 

> [!div class="nextstepaction"]
> [将用户添加到 Azure Purview 帐户](catalog-permissions.md)