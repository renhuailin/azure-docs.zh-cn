---
title: 使用模板创建 Azure IoT 中心 (PowerShell) | Microsoft Docs
description: 如何使用 Azure 资源管理器模板和 PowerShell 创建 IoT 中心。
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c2119cbc3d520b1fcf7c5efd094fd26ddb3d1c19
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740400"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>使用 Azure 资源管理器模板创建 IoT 中心 (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

了解如何使用 Azure 资源管理器模板创建 IoT 中心和使用者组。 资源管理器模板为 JSON 文件，用于定义针对解决方案进行部署时所需的资源。 有关开发资源管理器模板的详细信息，请参阅 [Azure 资源管理器文档](../azure-resource-manager/index.yml)。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

本快速入门中使用的资源管理器模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/iothub-with-consumergroup-create/)。 下面是该模板的副本：

[!code-json[iothub-creation](~/quickstart-templates/quickstarts/microsoft.devices/iothub-with-consumergroup-create/azuredeploy.json)]

该模板创建一个具有三个终结点（eventhub、cloud-to-device 和 messaging）的 Azure Iot 中心和一个使用者组。 有关更多模板示例，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular)。 可在[此处](/azure/templates/microsoft.devices/iothub-allversions)找到 Iot 中心模板架构。

可通过多种方法来部署模板。  在本教程中，将使用 Azure PowerShell。

若要运行 PowerShell 脚本，请选择“试用”以打开 Azure Cloud Shell。 若要粘贴脚本，请右键单击 shell，然后选择“粘贴”：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.devices/iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

正如你在 PowerShell 脚本中所见，使用的模板来自 Azure 快速入门模板。 若要使用你自己的模板，你需要先上传模板文件，然后使用 `-TemplateFile` 开关来指定文件名。  有关示例，请参阅[部署模板](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)。

## <a name="next-steps"></a>后续步骤

现在，你已使用 Azure 资源管理器模板部署了一个 IoT 中心，你可能希望进一步进行探索：

* 阅读了解 [IoT 中心资源提供程序 REST API][lnk-rest-api] 的相关功能。
* 有关 Azure 资源管理器功能的详细信息，请参阅 [Azure 资源管理器概述][lnk-azure-rm-overview]。
* 有关要在模板中使用的 JSON 语法和属性，请参阅 [Microsoft.Devices 资源类型](/azure/templates/microsoft.devices/iothub-allversions)。

若要详细了解如何开发 IoT 中心，请参阅以下文章：

* [C SDK 简介][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 Azure IoT Edge 将 AI 部署到边缘设备][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: /rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/quickstart-linux.md
