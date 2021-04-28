---
title: 快速入门：创建 Azure SignalR 服务 - ARM 模板
description: 在本快速入门中，学习如何使用 Azure 资源管理器模板（ARM 模板）创建 Azure SignalR 服务。
author: sffamily
ms.author: zhshang
ms.date: 10/02/2020
ms.topic: quickstart
ms.service: signalr
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 622b681121f742ba8de0f92adbcce1c7e1fa8f7a
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108070400"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>快速入门：使用 ARM 模板部署 Azure SignalR 服务

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）来创建 Azure SignalR 服务。 可通过 Azure 门户、PowerShell 或 CLI 部署 Azure SignalR 服务。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 登录后，该模板将在 Azure 门户中打开。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="在 Azure 门户中使用 ARM 模板将 Azure SignalR 服务部署到 Azure 的按钮。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

# <a name="portal"></a>[门户](#tab/azure-portal)

具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/)。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/)。
* 若要在本地运行代码，请安装 [Azure PowerShell](/powershell/azure/install-az-ps)。

# <a name="cli"></a>[CLI](#tab/CLI)

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/)。
* 若要在本地运行代码，请安装：
    * Bash shell（例如 [Git for Windows](https://gitforwindows.org) 中包含的 Git Bash）。
    * [Azure CLI](/cli/azure/install-azure-cli)。

---

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-signalr/)。

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

该模板定义了一个 Azure 资源：

* [**Microsoft.SignalRService/SignalR**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>部署模板

# <a name="portal"></a>[Portal](#tab/azure-portal)

选择以下链接，在 Azure 门户中使用 ARM 模板部署 Azure SignalR 服务：

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="在 Azure 门户中使用 ARM 模板将 Azure SignalR 服务部署到 Azure 的按钮。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

在“部署 Azure SignalR 服务”页面上：

1. 如果需要，可以更改“订阅”的默认值。

2. 对于“资源组”，请选择“新建”，输入新资源组的名称，然后选择“确定”  。

3. 如果创建了新的资源组，请为该资源组选择一个区域。

4. 如果需要，可输入 Azure SignalR 服务的新名称和位置（例如 eastus2）  。 如果未指定名称，则会自动生成名称。 Azure SignalR 服务可与资源组位于同一区域，也可与它分处不同的区域。 如果未指定位置，则将其设置为与资源组相同的区域。

5. 选择“定价层”（“Free_F1”或“Standard_S1”），输入“容量”（SignalR 单位数），然后选择“服务模式”：“默认”（需要中心服务器）、“无服务器”（不允许任何服务器连接）或“经典”（仅当中心具有服务器连接时才路由到中心服务器）       。 然后，选择是“启用连接日志”，还是“启用消息日志” 。

    > [!NOTE]
    > 对于“Free_F1”定价层，容量限制为 1 个单位。

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="用于在 Azure 门户中创建 Azure SignalR 服务的 ARM 模板的屏幕截图。":::

6. 选择“查看 + 创建”  。

7. 阅读条款和条件，然后选择“创建”。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> 若要在本地运行 PowerShell 脚本，请首先输入 `Connect-AzAccount` 来设置 Azure 凭据。

使用以下代码通过 ARM 模板部署 Azure SignalR 服务。 该代码将提示你输入以下项：

* 新的 Azure SignalR 服务的名称和区域
* 新资源组的名称和区域
* Azure 定价层（“Free_F1”或“Standard_S1”） 
* SignalR 单位容量（1、2、5、10、20、50 或 100）
  > [!NOTE]
  > 对于“Free_F1”定价层，容量限制为 1 个单位。
* 服务模式：“默认”要求具有中心服务器，“无服务器”不允许任何服务器连接，或“经典”仅在中心具有服务器连接时才路由到中心服务器  
* 是否启用日志以进行连接或消息传递（“是”或“否”） 

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure SignalR Service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

$priceTier = Read-Host -Prompt "Enter the pricing tier (Free_F1 or Standard_S1)"
$unitCapacity = Read-Host -Prompt "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100)"
$servicingMode = Read-Host -Prompt "Enter the service mode (Default, Serverless, or Classic)"
$enableConnectionLogs = Read-Host -Prompt "Specify whether to enable connectivity logs (true or false)"
$enableMessageLogs = Read-Host -Prompt "Specify whether to enable messaging logs (true or false)"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion

$paramObjHashTable = @{
    name = $serviceName
    location = $serviceLocation
    pricingTier = $priceTier
    capacity = [int]$unitCapacity
    serviceMode = $servicingMode
    enableConnectivityLogs = $enableConnectionLogs
    enableMessagingLogs = $enableMessageLogs
}

Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure SignalR Service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateParameterObject $paramObjHashTable `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

使用以下代码通过 ARM 模板部署 Azure SignalR 服务。 该代码将提示你输入以下项：

* 新的 Azure SignalR 服务的名称和区域
* 新资源组的名称和区域
* Azure 定价层（“Free_F1”或“Standard_S1”） 
* SignalR 单位容量（1、2、5、10、20、50 或 100）
    > [!NOTE]
    > 对于“Free_F1”定价层，容量限制为 1 个单位。
* 服务模式：“默认”要求具有中心服务器，“无服务器”不允许任何服务器连接，或“经典”仅在中心具有服务器连接时才路由到中心服务器  
* 是否启用日志以进行连接或消息传递（“是”或“否”） 

```azurecli-interactive
read -p "Enter a name for the new Azure SignalR Service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter the pricing tier (Free_F1 or Standard_S1): " priceTier &&
read -p "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100): " unitCapacity &&
read -p "Enter the service mode (Default, Serverless, or Classic): " servicingMode &&
read -p "Specify whether to enable connectivity logs (true or false): " enableConnectionLogs &&
read -p "Specify whether to enable messaging logs (true or false): " enableMessageLogs &&
params='name='$serviceName' location='$serviceLocation' pricingTier='$priceTier' capacity='$unitCapacity' serviceMode='$servicingMode' enableConnectivityLogs='$enableConnectionLogs' enableMessagingLogs='$enableMessageLogs &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure SignalR Service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> 部署可能需要几分钟才能完成。 记下 Azure SignalR 服务和资源组的名称，稍后将使用它们来查看已部署的资源。

## <a name="review-deployed-resources"></a>查看已部署的资源

# <a name="portal"></a>[Portal](#tab/azure-portal)

按照以下步骤概要了解新的 Azure SignalR 服务：

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“SignalR”。

2. 在 SignalR 列表中，选择你的新服务。 此时将显示新的 Azure SignalR 服务的“概述”页面。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

运行以下交互式代码来查看有关 Azure SignalR 服务的详细信息。 必须输入新服务和资源组的名称。

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

运行以下交互式代码来查看有关 Azure SignalR 服务的详细信息。 必须输入新服务和资源组的名称。

```azurecli-interactive
read -p "Enter the name of your Azure SignalR Service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>清理资源

如果不再需要该资源组，可以将其删除，这将删除资源组中的资源。

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“资源组”。

2. 在资源组列表中，选择你的资源组的名称。

3. 在资源组的“概览”页中，选择“删除资源组” 。

4. 在确认对话框中，键入资源组的名称，然后选择“删除”。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>后续步骤

有关引导你完成 ARM 模板创建过程的分步教程，请参阅：

> [!div class="nextstepaction"]
> [教程：创建和部署你的第一个 ARM 模板](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
