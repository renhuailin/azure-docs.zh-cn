---
title: 通过 Azure CLI 或 PowerShell 管理 IoT Central | Microsoft Docs
description: 本文介绍如何通过 Azure CLI 或 PowerShell 创建和管理 IoT Central 应用程序。 可以使用这些工具查看、修改和删除应用程序。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.custom:
- devx-track-azurecli
- devx-track-azurepowershell
ms.openlocfilehash: b2ba640fec6f9bf2cd09611456967e15ed8f7ade
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732893"
---
# <a name="manage-iot-central-from-azure-cli-or-powershell"></a>通过 Azure CLI 或 PowerShell 管理 IoT Central

可以使用 [Azure CLI](/cli/azure/) 或 [PowerShell](/powershell/azure/) 管理 IoT Central，而不是在 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上创建和管理 IoT Central 应用程序。

如果喜欢使用 JavaScript、Python、C#、Ruby 或 Go 等语言，请参阅 [Azure IoT Central ARM SDK 示例](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/)存储库以获取代码示例，其中演示了如何创建、更新、列出和删除 Azure IoT Central 应用程序。

## <a name="prerequisites"></a>先决条件

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [azure-powershell-requirements-no-header](../../../includes/azure-powershell-requirements-no-header.md)]

> [!TIP]
> 如果需要在另一个 Azure 订阅中运行 PowerShell 命令，请参阅[更改活动订阅](/powershell/azure/manage-subscriptions-azureps#change-the-active-subscription)。

运行以下命令来检查 PowerShell 环境中是否已安装 [IoT Central 模块](/powershell/module/az.iotcentral/)：

```powershell
Get-InstalledModule -name Az.I*
```

如果已安装的模块列表不包括 **Az.IotCentral**，请运行以下命令：

```powershell
Install-Module Az.IotCentral
```

---

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="create-an-application"></a>创建应用程序

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az iot central app create](/cli/azure/iot/central/app#az_iot_central_app_create) 命令在 Azure 订阅中创建 IoT Central 应用程序。 例如：

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

这些命令首先在“美国东部”区域为应用程序创建一个资源组。 下表描述了与“az iot central app create”命令配合使用的参数：

| 参数         | 说明 |
| ----------------- | ----------- |
| resource-group    | 包含该应用程序的资源组。 此资源组必须已存在于订阅中。 |
| location          | 此命令默认使用资源组中的位置。 目前，可以在澳大利亚、亚太、欧洲、美国、英国和日本创建 IoT Central 应用程序     。 |
| name              | 应用程序在 Azure 门户中的名称。 避免使用特殊字符，而应改用小写字母 (a-z)、数字 (0-9) 和破折号 (-)。|
| subdomain         | 应用程序 URL 中的子域。 在该示例中，应用程序 URL 为 `https://mysubdomain.azureiotcentral.com`。 |
| sku               | 目前，可以使用 ST1 或 ST2 。 请参阅 [Azure IoT Central 定价](https://azure.microsoft.com/pricing/details/iot-central/)。 |
| template          | 要使用的应用程序模板。 有关详细信息，请参阅下表。 |
| display-name      | UI 中显示的应用程序名称。 |

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 [New-AzIotCentralApp](/powershell/module/az.iotcentral/New-AzIotCentralApp) cmdlet 在 Azure 订阅中创建 IoT Central 应用程序。 例如：

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

该脚本首先在美国东部区域为应用程序创建一个资源组。 下表描述了与 **New-AzIotCentralApp** 命令配合使用的参数：

|参数         |说明 |
|------------------|------------|
|ResourceGroupName |包含该应用程序的资源组。 此资源组必须已存在于订阅中。 |
|位置 |此 cmdlet 默认使用资源组中的位置。 目前，可以在澳大利亚、亚太、欧洲、美国、英国和日本创建 IoT Central 应用程序     。 |
|名称              |应用程序在 Azure 门户中的名称。 避免使用特殊字符，而应改用小写字母 (a-z)、数字 (0-9) 和破折号 (-)。 |
|子域         |应用程序 URL 中的子域。 在该示例中，应用程序 URL 为 `https://mysubdomain.azureiotcentral.com`。 |
|SKU               |目前，可以使用 ST1 或 ST2 。 请参阅 [Azure IoT Central 定价](https://azure.microsoft.com/pricing/details/iot-central/)。 |
|模板          | 要使用的应用程序模板。 有关详细信息，请参阅下表。 |
|DisplayName       |UI 中显示的应用程序名称。 |

---

### <a name="application-templates"></a>应用程序模板

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

如果你创建了自己的应用程序模板，则可以用它来创建新的应用程序。 当系统提示你提供应用程序模板时，请在应用的[应用程序模板导出](howto-create-iot-central-application.md#create-and-use-a-custom-application-template)部分下，输入导出应用的 URL 可共享链接中显示的应用 ID。

## <a name="view-applications"></a>查看应用程序

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az iot central app list](/cli/azure/iot/central/app#az_iot_central_app_list) 命令列出 IoT Central 应用程序和查看元数据。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 [Get-AzIotCentralApp](/powershell/module/az.iotcentral/Get-AzIotCentralApp) cmdlet 可列出 IoT Central 应用程序和查看元数据。

---

## <a name="modify-an-application"></a>修改应用程序

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az iot central app update](/cli/azure/iot/central/app#az_iot_central_app_update) 命令更新 IoT Central 应用程序的元数据。 例如，若要更改应用程序的显示名称：

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 [Set-AzIotCentralApp](/powershell/module/az.iotcentral/set-aziotcentralapp) cmdlet 可更新 IoT Central 应用程序的元数据。 例如，若要更改应用程序的显示名称：

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

---

## <a name="delete-an-application"></a>删除应用程序

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az iot central app delete](/cli/azure/iot/central/app#az_iot_central_app_delete) 命令删除 IoT Central 应用程序。 例如：

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 [Remove-AzIotCentralApp](/powershell/module/az.iotcentral/Remove-AzIotCentralApp) cmdlet 可删除 IoT Central 应用程序。 例如：

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

---

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure CLI 或 PowerShell 管理 IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)
