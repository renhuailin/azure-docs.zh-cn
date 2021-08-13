---
title: 包含文件
description: include 文件
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: 324e1d3c9d86f8c17581665507ebe52b946bd006
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114407699"
---
## <a name="create-an-iot-hub"></a>创建 IoT 中心
在本部分中，使用 Azure CLI 创建 IoT 中心和资源组。  Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 IoT 中心充当中央消息中心，用于 IoT 应用程序与设备之间的双向通信。

创建 IoT 中心和资源组：

1. 启动 CLI 应用程序。  若要在本快速入门的其余部分运行 CLI 命令，请复制命令语法，将其粘贴到 CLI 应用程序中，编辑变量值，然后按 Enter 键。
    - 如果使用 Cloud Shell，请在 CLI 命令上选择 **试用** 按钮，以在拆分浏览器窗口中启动 Cloud Shell。 或者，你可以在单独的浏览器选项卡中打开 [Cloud Shell](https://shell.azure.com/bash) 。
    - 如果要在本地使用 Azure CLI，请启动 CLI 控制台应用程序并登录到 Azure CLI。

1. 运行 [az extension add](/cli/azure/extension#az_extension_add)，安装 *azure-iot* 扩展或将其升级到当前版本。

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. 在 CLI 应用程序中，运行 [az group create](/cli/azure/group#az_group_create) 命令来创建资源组。 以下命令在“eastus”  位置创建名为“MyResourceGroup”的资源组  。 
    >[!NOTE]
    > 可以选择设置备用位置。 若要查看可用位置，请运行 `az account list-locations`。 本教程使用示例命令中所示的 eastus。 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```

1. 运行 [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) 命令创建 IoT 中心。 创建 IoT 中心可能需要数分钟的时间。 

    *YourIotHubName*。 使用你为 IoT 中心选择的名称，在以下命令中替换此占位符和周围的大括号。 IoT 中心名称必须在 Azure 中全局唯一。 无论你在何处看到占位符，都请使用本快速入门的其余部分中的 IoT 中心名称。

    ```azurecli-interactive
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```
    > [!TIP]
    > 创建 IoT 中心后，在本快速入门的其余部分，你可以继续使用 CLI 命令与中心交互。 或者，可以使用 Azure IoT 资源管理器替代 CLI 命令。 IoT 资源管理器是一种 GUI 应用程序，让你可以连接到现有的 IoT 中心并添加、管理和监视设备。 若要了解详细信息，请参阅[安装并使用 Azure IoT 资源管理器](../articles/iot-fundamentals/howto-use-iot-explorer.md)。

## <a name="create-a-simulated-device"></a>创建模拟设备
在本部分中，你将创建一个连接到 IoT 中心的模拟 IoT 设备。 

要创建模拟设备：
1. 在 CLI shell 中运行 [az iot hub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) 命令。 这会创建模拟设备标识。 

    *YourIotHubName*。 将下面的占位符替换为你为 IoT 中心选择的名称。 

    myDevice。 在本文的其余部分中，可以直接将此名称用于模拟设备 ID。 或者，也可使用其他名称。 

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  运行 [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string#az_iot_hub_device_identity_connection_string_show) 命令。 

    ```azurecli-interactive
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    连接字符串输出采用以下格式：

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. 在安全位置保存连接字符串。 

> [!NOTE]
> 让你的 CLI 应用程序保持开启。 因为在稍后的步骤中需要用到。