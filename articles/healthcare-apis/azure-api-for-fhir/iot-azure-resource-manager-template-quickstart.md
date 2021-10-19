---
title: 快速入门：使用 ARM 模板部署适用于 FHIR 的 Azure IoT 连接器（预览版）
description: 本快速入门介绍如何使用 Azure 资源管理器模板 (ARM 模板) 部署适用于 FHIR (preview) Azure IoT 连接器。
author: rbhaiya
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.author: rabhaiya
ms.date: 09/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 611d43d7b34b729beee162d6bf9db5d099095ade
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748824"
---
# <a name="quickstart-use-an-azure-resource-manager-arm-template-to-deploy-azure-iot-connector-for-fhir-preview"></a>快速入门：使用 Azure 资源管理器 (ARM) 模板部署适用于 FHIR 的 Azure IoT 连接器（预览版）

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）部署适用于快速医疗保健互操作性资源 (FHIR&#174;)* 的 Azure IoT 连接器，这是 Azure API for FHIR 的一个功能。 若要部署适用于 FHIR 的 Azure IoT 连接器的工作实例，此模板还会部署父级 Azure API for FHIR 服务和 Azure IoT Central 应用程序，该应用程序会将遥测数据从设备模拟器导出到适用于 FHIR 的 Azure IoT 连接器。 你可以通过 Azure 门户、PowerShell 或 CLI，执行 ARM 模板来部署适用于 FHIR 的 Azure IoT 连接器。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 登录后，该模板将在 Azure 门户中打开。

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="在 Azure 门户中使用 ARM 模板将适用于 FHIR 的 Azure IoT 连接器部署到 Azure。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

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

该[模板](https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json)定义以下 Azure 资源：

* Microsoft.HealthcareApis/services
* Microsoft.HealthcareApis/services/iomtconnectors
* **Microsoft.IoTCentral/IoTApps**

## <a name="deploy-the-template"></a>部署模板

# <a name="portal"></a>[门户](#tab/azure-portal)

选择以下链接，以在 Azure 门户中使用 ARM 模板部署适用于 FHIR 的 Azure IoT 连接器：

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="在 Azure 门户中使用 ARM 模板将适用于 FHIR 的 Azure IoT 连接器服务部署到 Azure。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

在“部署 Azure API for FHIR”页上：

1. 如果需要，可以将“订阅”从默认值更改为其他订阅。

2. 对于“资源组”，请选择“新建”，输入新资源组的名称，然后选择“确定”  。

3. 如果创建了新的资源组，请为该资源组选择一个区域。

4. 在“FHIR 服务名称”中，为新的 Azure API for FHIR 实例输入名称。

5. 选择 Azure API for FHIR 的“位置”。 该位置可以与资源组所在的区域相同，也可以与资源组所在的区域不同。

6. 在“IoT 连接器名称”中，为适用于 FHIR 的 Azure IoT 连接器实例提供名称。

7. 在“连接名称”中，为在适用于 FHIR 的 Azure IoT 连接器中创建的连接提供名称。 Azure IoT Central 应用程序使用此连接将模拟设备遥测数据推送到适用于 FHIR 的 Azure IoT 连接器。

8. 在“IoT Central 名称”中，输入新的 Azure IoT Central 应用程序的名称。 此应用程序将使用“持续患者监视”模板来模拟设备。

9. 从“IoT Central 位置”下拉列表中，选择 IoT Central 应用程序的位置。 

10. 选择“查看 + 创建”  。

11. 阅读条款和条件，然后选择“创建”。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> 若要在本地运行 PowerShell 脚本，请首先输入 `Connect-AzAccount` 来设置 Azure 凭据。

如果还没有为订阅注册 `Microsoft.HealthcareApis` 资源提供程序，则可以使用以下交互式代码进行注册。 若要在 Azure Cloud Shell 中运行该代码，请在任何代码块的右上角选择“尝试”。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

如果还没有为订阅注册 `Microsoft.IoTCentral` 资源提供程序，则可以使用以下交互式代码进行注册。 若要在 Azure Cloud Shell 中运行该代码，请在任何代码块的右上角选择“尝试”。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.IoTCentral
```

通过 ARM 模板，使用以下代码部署适用于 FHIR 的 Azure IoT 连接器服务。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$fhirServiceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$location = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$iotConnectorName = Read-Host -Prompt "Enter a name for the new Azure IoT Connector for FHIR"
$connectionName = Read-Host -Prompt "Enter a name for the connection with Azure IoT Connector for FHIR"
$iotCentralName = Read-Host -Prompt "Enter a name for the new Azure IoT Central Application"
$iotCentralLocation = Read-Host -Prompt "Enter a location for the new Azure IoT Central Application"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure IoT Connector for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json `
    -fhirServiceName $fhirServiceName `
    -location $location
    -iotConnectorName $iotConnectorName
    -connectionName $connectionName
    -iotCentralName $iotCentralName
    -iotCentralLocation $iotCentralLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

如果还没有为订阅注册 `Microsoft.HealthcareApis` 资源提供程序，则可以使用以下交互式代码进行注册。 若要在 Azure Cloud Shell 中运行该代码，请在任何代码块的右上角选择“尝试”。

```azurecli-interactive
az extension add --name healthcareapis
```

如果还没有为订阅注册 `Microsoft.IoTCentral` 资源提供程序，则可以使用以下交互式代码进行注册。

```azurecli-interactive
az extension add --name azure-iot
```

通过 ARM 模板，使用以下代码部署适用于 FHIR 的 Azure IoT 连接器服务。

```azurecli-interactive
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter a name for the new Azure API for FHIR service: " fhirServiceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " location &&
read -p "Enter a name for the new Azure IoT Connector for FHIR: " iotConnectorName &&
read -p "Enter a name for the connection with Azure IoT Connector for FHIR: " connectionName &&
read -p "Enter a name for the new Azure IoT Central Application: " iotCentralName &&
read -p "Enter a location for the new Azure IoT Central Application: " iotCentralLocation &&

params='fhirServiceName='$fhirServiceName' location='$location' iotConnectorName='$iotConnectorName' connectionName='$connectionName' iotCentralName='$iotCentralName' iotCentralLocation='$iotCentralLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure IoT Connector for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> 部署需要数分钟才能完成。 记下 Azure API for FHIR 服务、Azure IoT Central 应用程序和资源组的名称，稍后将使用它们来查看已部署的资源。

## <a name="review-deployed-resources"></a>查看已部署的资源

# <a name="portal"></a>[Portal](#tab/azure-portal)

按照以下步骤查看新 Azure API for FHIR 服务的概览：

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“Azure API for FHIR”。

2. 在 FHIR 列表中，选择你的新服务。 此时将显示新 Azure API for FHIR 服务的“概述”页。

3. 若要验证是否预配了新的 FHIR API 帐户，请选择“FHIR 元数据终结点”旁边的链接，以提取 FHIR API 功能语句。 该链接的格式为 `https://<service-name>.azurehealthcareapis.com/metadata`。 如果预配了帐户，则会显示一个较大的 JSON 文件。

4. 若要验证是否预配了新的适用于 FHIR 的 Azure IoT 连接器，请从左侧导航菜单中选择“IoT 连接器(预览版)”，以打开“IoT 连接器”页 。 对于已预配的适用于 FHIR 的 Azure IoT 连接器，该页面必须显示其“状态”值为“联机”、“连接”值为“1”，且“设备映射”和“FHIR 映射”均显示“成功”图标      。

5. 在 [Azure 门户](https://portal.azure.com)中，搜索“IoT Central 应用程序”并选择它。

6. 在 IoT Central 应用程序列表中，选择新的服务。 此时将显示新 IoT Central 应用程序的“概述”页面。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

运行以下交互式代码来查看有关 Azure API for FHIR 服务的详细信息。 必须输入新的 FHIR 服务和资源组的名称。

```azurepowershell-interactive
$fhirServiceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new FHIR service has been provisioned"

$requestUri="https://" + $fhirServiceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

> [!NOTE]
> 适用于 FHIR 的 Azure IoT 连接器目前不提供 PowerShell 命令。 若要验证是否已正确预配适用于 FHIR 的 Azure IoT 连接器，请使用“门户”选项卡中提供的验证过程。

运行以下交互式代码，查看有关 Azure IoT Central 应用程序的详细信息。 必须输入新的 IoT Central 应用程序和资源组的名称。

```azurepowershell-interactive
$iotCentralName = Read-Host -Prompt "Enter the name of your Azure IoT Central application"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName" -Verbose
Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName
```

# <a name="cli"></a>[CLI](#tab/CLI)

运行以下交互式代码来查看有关 Azure API for FHIR 服务的详细信息。 必须输入新的 FHIR 服务和资源组的名称。

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " fhirServiceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$fhirServiceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

> [!NOTE]
> 适用于 FHIR 的 Azure IoT 连接器目前不提供 CLI 命令。 若要验证是否已正确预配适用于 FHIR 的 Azure IoT 连接器，请使用“门户”选项卡中提供的验证过程。

运行以下交互式代码，查看有关 Azure IoT Central 应用程序的详细信息。 必须输入新的 IoT Central 应用程序和资源组的名称。

```azurecli-interactive
read -p "Enter the name of your IoT Central application: " iotCentralName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az iot central app show -g $resourceGroupName -n $iotCentralName" &&
az iot central app show -g $resourceGroupName -n $iotCentralName &&
```

---

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>使用适用于 FHIR 的 Azure IoT 连接器（预览版）连接 IoT 数据
> [!IMPORTANT]
> 本指南中提供的设备映射模板旨在与 IoT Central 中的数据导出（旧版）一起使用。

IoT Central 应用程序当前不提供 ARM 模板或 PowerShell 和 CLI 命令来设置数据导出。 因此，请使用 Azure 门户按以下说明操作。  

部署 IoT Central 应用程序后，两个现成的模拟设备将开始生成遥测。 在本教程中，我们将通过适用于 FHIR 的 Azure IoT 连接器将遥测从 Smart Vitals Patch 模拟器引入 FHIR。 若要将 IoT 数据导出到适用于 FHIR 的 Azure IoT 连接器，需要[在 IoT Central 内设置数据导出（旧版）](../../iot-central/core/howto-export-data-legacy.md)。 在“数据导出(旧版)”页中：
- 选择“Azure 事件中心”作为导出目标。
- 选择“事件中心命名空间”字段的“使用连接字符串”值。
- 为“连接字符串”字段提供在上一步中获取的适用于 FHIR 的 Azure IoT 连接器的连接字符串。
- 对于“要导出的数据”字段，保持“遥测”选项为启用状态。

---

## <a name="view-device-data-in-azure-api-for-fhir"></a>查看 Azure API for FHIR 中的设备数据

你可以使用 Postman 在 FHIR 服务上查看通过 Azure IoT Connector for FHIR 创建的基于 FHIR 的观察资源)  (。 有关信息，请参阅 [使用 Postman 访问 FHIR 服务](./../use-postman.md) 和发出 `GET` 请求以 `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` 查看使用心率值的观察 FHIR 资源。

> [!TIP]
> 确保用户可以访问 Azure API for FHIR 数据平面。 使用 [Azure 基于角色的访问控制 (Azure RBAC)](configure-azure-rbac.md) 分配所需的数据平面角色。

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

有关引导你完成 ARM 模板创建过程的分步教程，请参阅[创建和部署第一个 ARM 模板](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)教程

## <a name="next-steps"></a>后续步骤

在本快速入门指南中，你已在 Azure API for FHIR 资源中部署了适用于 FHIR 的 Azure IoT 连接器功能。 选择下面的后续步骤以了解有关适用于 FHIR 的 Azure IoT 连接器的详细信息：

了解适用于 FHIR 的 Azure IoT 连接器中数据流的不同阶段。

>[!div class="nextstepaction"]
>[适用于 FHIR 的 Azure IoT 连接器数据流](iot-data-flow.md)

了解如何使用设备和 FHIR 映射模板配置 IoT 连接器。

>[!div class="nextstepaction"]
>[适用于 FHIR 的 Azure IoT 连接器映射模板](iot-mapping-templates.md)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。 FHIR 是 HL7 的注册商标，经 HL7 许可使用。
