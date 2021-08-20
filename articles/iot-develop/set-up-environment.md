---
title: 教程 - 设置 IoT 即插即用所需的 IoT 资源 | Microsoft Docs
description: 教程 - 创建 IoT 中心和设备预配服务实例，以供在 IoT 即插即快速入门和教程中使用。
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: c932ee18bca79ce5607a27f6b6c6fe58e5ce7ef7
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406401"
---
# <a name="tutorial-set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>教程：针对 IoT 即插即用快速入门和教程设置环境

需要在 Azure 订阅中配置 IoT 中心和设备预配服务 (DPS)，然后才能完成任何 IoT 即插即用快速入门和教程。 你还需要具备示例应用程序和 Azure IoT 资源管理器工具所使用的模型文件的本地副本。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [azure-cli-prepare-your-environment-h3](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-the-resources"></a>创建资源

为资源创建一个 Azure 资源组：

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

创建 IoT 中心。 以下命令使用名称 `my-pnp-hub` 作为要创建的 IoT 中心的名称示例。 为 IoT 中心选择一个唯一名称来替代 `my-pnp-hub`：

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

创建一个 DPS 实例。 以下命令使用名称 `my-pnp-dps` 作为要创建的 DPS 实例的名称示例。 为 DPS 实例选择一个唯一名称来替代 `my-pnp-dps`：

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

若要将 DPS 实例链接到 IoT 中心，请使用以下命令。 将 `my-pnp-dps` 和 `my-pnp-hub` 替换为之前选择的唯一名称：

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>检索设置

某些快速入门和教程对 IoT 中心使用连接字符串。 设置 Azure IoT 资源管理器工具时，也需要连接字符串。 检索该连接字符串并立即记下它。 将 `my-pnp-hub` 替换为 IoT 中心选择的唯一名称：

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

大多数快速入门和教程都使用 DPS 配置的 ID 范围。 检索该 ID 范围并立即记下它。 将 `my-pnp-dps` 替换为 DPS 实例选择的唯一名称：

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

所有快速入门和教程都使用 DPS 设备注册。 使用以下命令在 DPS 实例中创建`my-pnp-device` 单设备注册。 将 `my-pnp-dps` 替换为 DPS 实例选择的唯一名称。 记下注册 ID 和主密钥，以便在快速入门和教程中使用：

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>创建环境变量

设置 5 个环境变量以在快速入门和教程中配置示例，从而使用 设备预配服务 (DPS) 连接到 IoT 中心：

* **IOTHUB_DEVICE_SECURITY_TYPE**：值 `DPS`。
* **IOTHUB_DEVICE_DPS_ID_SCOPE**:：你之前记下的 DPS ID 范围。
* **IOTHUB_DEVICE_DPS_DEVICE_ID**：值 `my-pnp-device`。
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**：你之前记下的注册主密钥。
* **IOTHUB_DEVICE_DPS_ENDPOINT**：值 `global.azure-devices-provisioning.net`

服务示例需要以下环境变量来确定要连接到的中心和设备：

* **IOTHUB_CONNECTION_STRING**：之前记下的 IoT 中心连接字符串。
* **IOTHUB_DEVICE_ID**：`my-pnp-device`。

例如，在 Linux bash shell 中：

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
```

例如，在 Windows 命令行：

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>下载模型文件

快速入门和教程对温度控制器和恒温器设备使用示例模型文件。 若要下载示例模型文件：

1. 在本地计算机上创建名为 models 的文件夹。

1. 右键单击 [TemperatureController.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) 并将 JSON 文件保存到 models 文件夹中。

1. 右键单击 [Thermostat.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) 并将 JSON 文件保存到 models 文件夹中。

## <a name="install-the-azure-iot-explorer"></a>安装 Azure IoT 资源管理器

快速入门和教程使用 Azure IoT 资源管理器工具。 请转到 [Azure IoT 资源管理器版本](https://github.com/Azure/azure-iot-explorer/releases)，展开最新版本的资产列表。 下载并安装适合你的操作系统的最新版应用程序。

首次运行该工具时，系统将提示你输入 IoT 中心连接字符串。 使用你之前记下的连接字符串。

将工具配置为使用你之前下载的模型文件。 在工具的主页中，选择“IoT 即插即用设置”，然后选择“+添加”>“本地文件夹” 。 选择你之前创建的模型文件夹。 然后，选择“保存”来保存设置。

若要了解详细信息，请参阅[安装并使用 Azure IoT 资源管理器](../iot-fundamentals/howto-use-iot-explorer.md)。

## <a name="clean-up-resources"></a>清理资源

可对所有 IoT 即插即用快速入门和教程使用 IoT 中心和 DPS 实例，因此你只需完成本文中所述步骤一次即可。 完成后，可使用以下命令从订阅中删除它们：

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>后续步骤

你现已设置好环境，接下来可尝试一个快速入门或教程，例如：

> [!div class="nextstepaction"]
> [将示例 IoT 即插即用设备应用程序连接到 IoT 中心](tutorial-connect-device.md)
