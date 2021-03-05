---
title: 从 Azure CLI 管理 IoT Central | Microsoft Docs
description: 本文介绍如何使用 CLI 创建和管理 IoT Central 应用程序。 可以使用 CLI 查看、修改和删除应用程序。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: d414b86ff81a33f9e818a0a28031e73d88cabec2
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202257"
---
# <a name="manage-iot-central-from-azure-cli"></a>从 Azure CLI 管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

如果不在 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上创建和管理 IoT Central 应用程序，可以使用 [Azure CLI](/cli/azure/) 来管理应用程序。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 如果需要在不同的 Azure 订阅中运行 CLI 命令，请参阅 [更改活动订阅](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)。

## <a name="create-an-application"></a>创建应用程序

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

使用 [az iot central app create](/cli/azure/iot/central/app#az-iot-central-app-create) 命令在 Azure 订阅中创建 IoT Central 应用程序。 例如：

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

这些命令首先在 "美国东部" 区域中为应用程序创建一个资源组。 下表介绍了 **az iot central app create** 命令使用的参数：

| 参数         | 说明 |
| ----------------- | ----------- |
| resource-group    | 包含该应用程序的资源组。 此资源组必须已存在于订阅中。 |
| location          | 此命令默认使用资源组中的位置。 目前，可以在 **澳大利亚**、 **亚太**、 **欧洲**、 **美国**、 **英国** 和 **日本** 地区创建 IoT Central 应用程序。 |
| name              | 应用程序在 Azure 门户中的名称。 |
| subdomain         | 应用程序 URL 中的子域。 在该示例中，应用程序 URL 为 `https://mysubdomain.azureiotcentral.com`。 |
| sku               | 目前，可以使用 **ST1** 或 **ST2**。 请参阅 [Azure IoT Central 定价](https://azure.microsoft.com/pricing/details/iot-central/)。 |
| template          | 要使用的应用程序模板。 有关详细信息，请参阅下表。 |
| display-name      | UI 中显示的应用程序名称。 |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>查看应用程序

使用 [az iot central app list](/cli/azure/iot/central/app#az-iot-central-app-list) 命令列出你的 IoT Central 应用程序并查看元数据。

## <a name="modify-an-application"></a>修改应用程序

使用 [az iot central app update](/cli/azure/iot/central/app#az-iot-central-app-update) 命令更新 IoT Central 应用程序的元数据。 例如，若要更改应用程序的显示名称：

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>删除应用程序

使用 [az iot central app delete](/cli/azure/iot/central/app#az-iot-central-app-delete) 命令删除 IoT Central 应用程序。 例如：

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>后续步骤

了解如何从 Azure CLI 管理 Azure IoT Central 应用程序后，建议接下来学习：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)
