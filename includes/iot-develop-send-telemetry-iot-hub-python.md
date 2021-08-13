---
title: 包含文件
description: 包含文件
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/05/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: ddb798fe70d58a530210ea74d76f1b0bb4169477
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114712868"
---
[![浏览代码](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp)

本快速入门介绍一个基本的 Azure IoT 应用程序开发工作流。 你将使用 Azure CLI 创建 Azure IoT 中心和设备。 然后，使用 Azure IoT 设备 SDK 示例来运行模拟的温度控制器，将此控制器安全连接到中心，并发送遥测数据。

## <a name="prerequisites"></a>先决条件
- 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Git](https://git-scm.com/downloads)。
- [Python](https://www.python.org/downloads/) 3.7 或更高版本。 若要检查 Python 版本，请运行 `python --version`。
- Azure CLI。 在本快速入门中，有两个选项可用于运行 Azure CLI 命令：
    - 使用 Azure Cloud Shell，这是一个交互式 Shell，可在浏览器中运行 CLI 命令。 建议使用此选项，因为无需安装任何插件。 如果是首次使用 Cloud Shell，请登录到 [Azure 门户](https://portal.azure.com)。 按照 [Cloud Shell 快速入门](../articles/cloud-shell/quickstart.md)中的步骤启动 Cloud Shell 并选择 Bash 环境。
    - （可选）在本地计算机上运行 Azure CLI。 如果已安装 Azure CLI，请运行 `az upgrade` 以将 CLI 和扩展升级到当前版本。 要安装 Azure CLI，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

[!INCLUDE [iot-hub-include-create-hub-cli](iot-hub-include-create-hub-cli.md)]

## <a name="run-a-simulated-device"></a>运行模拟设备
在本部分，你要使用 Python SDK 将消息从模拟设备发送到 IoT 中心。

1. 打开新的控制台窗口。 将使用此控制台来安装 Python SDK 和处理 Python 示例代码。 现在应会打开两个控制台窗口：刚打开的控制台窗口，以及之前用于输入 CLI 命令的 Cloud Shell 或 CLI 控制台。

1. 在 Python 控制台中，将 [Azure IoT Python SDK 设备示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)克隆到本地计算机：

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. 导航到示例目录：

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. 安装 Azure IoT Python SDK：

    ```console
    pip3 install azure-iot-device
    ```
1. 设置以下环境变量，使模拟设备能够连接到 Azure IoT。
    * 设置名为 `IOTHUB_DEVICE_CONNECTION_STRING` 的环境变量。 对于变量值，请使用上一部分保存的设备连接字符串。
    * 设置名为 `IOTHUB_DEVICE_SECURITY_TYPE` 的环境变量。 对于变量，请使用文本字符串值 `connectionString`。

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```
    > [!NOTE]
    > 对于 Windows CMD，每个变量的字符串值的两边没有引号。

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash（Linux 或 Windows）**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

1. 在 CLI 应用中，运行 [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) 命令以开始监视模拟 IoT 设备上的事件。  事件消息到达时会在终端中输出。

    ```azurecli-interactive
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. 在 Python 控制台中，运行以下示例文件的代码。 此示例使用恒温器传感器创建模拟温度控制器。

    ```console
    python temp_controller_with_thermostats.py
    ```
    > [!NOTE]
    > 此代码示例使用 Azure IoT 即插即用，这使你无需任何手动配置即可将智能设备集成到你的解决方案中。  默认情况下，本文档中的大多数示例都使用 IoT 即插即用。 若要详细了解 IoT PnP 的优点以及使用或不使用它的案例，请参阅[什么是 IoT 即插即用？](../articles/iot-develop/overview-iot-plug-and-play.md)。

 当 Python 代码将消息从设备发送到 IoT 中心时，该消息将显示于正在监视事件的 CLI 应用中：

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: thermostat1
  interface: dtmi:com:example:TemperatureController;2
  module: ''
  origin: myDevice
  payload:
    temperature: 29

event:
  component: thermostat2
  interface: dtmi:com:example:TemperatureController;2
  module: ''
  origin: myDevice
  payload:
    temperature: 48
```

设备现已安全建立连接，并在向 Azure IoT 中心发送遥测数据。