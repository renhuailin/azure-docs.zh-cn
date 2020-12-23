---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: ea5d4ef26fb14e22b871bb4bfa1054cb749d38e8
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673013"
---
## <a name="prepare-an-iot-hub"></a>准备 IoT 中心

Azure 订阅中需要有一个 Azure IoT 中心才能完成本文中的步骤。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果要在本地使用 Azure CLI，请先使用 `az login` 登录到 Azure 订阅。 如果在 Azure Cloud Shell 中运行这些命令，则会自动登录。

如果在本地使用 Azure CLI，则 `az` 的版本应为 2.8.0 或更高版本；Azure Cloud Shell 使用最新版本。 使用 `az --version` 命令检查计算机上安装的版本。

运行以下命令将用于 Azure CLI 的 Microsoft Azure IoT 扩展添加到你的实例：

```azurecli-interactive
az extension add --name azure-iot
```

如果还没有可使用的 IoT 中心，请运行以下命令，以在订阅中创建资源组和免费层的 IoT 中心。 将 `<YourIoTHubName>` 替换为所选的中心名称：

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

运行以下命令，在 IoT 中心创建设备标识。 将 `<YourIoTHubName>` 和 `<YourDeviceID>` 占位符替换为你自己的“IoT 中心名称”和所选“设备 ID” 。

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
