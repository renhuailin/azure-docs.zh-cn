---
title: include 文件
description: include 文件
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 29b0785f1656620be4e497b80c855d109cf522cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909408"
---
[![浏览代码](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp)

本快速入门介绍一个基本的 Azure IoT 应用程序开发工作流。 首先，创建适用于托管设备的 Azure IoT Central 应用程序。 然后，使用 Azure IoT 设备 SDK 示例来运行模拟的温度控制器，将此控制器安全连接到 IoT Central，并发送遥测数据。

## <a name="prerequisites"></a>必备条件
本快速入门在 Windows、Linux 和 Raspberry Pi 上运行。 它已在以下 OS 和设备版本上进行了测试：

- Windows 10
- 在适用于 Linux 的 Windows 子系统 (WSL) 上运行的 Ubuntu 20.04 LTS
- 在 Raspberry Pi 3 Model B+ 上运行的 Raspberry Pi OS 版本 10 (buster)

在开发计算机上安装以下必备组件：

- [Python](https://www.python.org/downloads/) 3.7 或更高版本。 若要检查 Python 版本，请运行 `python3 --version`。
- [Git](https://git-scm.com/downloads)。

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>运行模拟设备
在本部分，你将配置本地环境，安装 Azure IoT Python 设备 SDK，并运行创建模拟温度控制器的示例。

### <a name="configure-your-environment"></a>配置环境

1. 打开一个控制台，如 Windows CMD、PowerShell 或 Bash。

1. 使用适用于你的控制台的适当命令设置以下环境变量。 模拟设备使用这些值连接到 IoT Central。 对于 `IOTHUB_DEVICE_DPS_ID_SCOPE`、`IOTHUB_DEVICE_DPS_DEVICE_KEY` 和 `IOTHUB_DEVICE_DPS_DEVICE_ID`，请使用之前保存的设备连接值。

    CMD (Windows)

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > 在 Windows CMD 中，请不要在变量值的两边加上引号。

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    $env:IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    $env:IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net'
    ```

    **Bash**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="install-the-sdk-and-samples"></a>安装 SDK 和示例

1. 将 Azure IoT Python 设备 SDK 复制到本地计算机。

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. 导航到示例目录。

    **Windows**
    ```console
    cd azure-iot-sdk-python\azure-iot-device\samples\pnp
    ```

    Linux 或 Raspberry Pi OS
    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```

1. 安装 Azure IoT Python SDK。
    ```console
    pip3 install azure-iot-device
    ```

### <a name="run-the-code"></a>运行代码

1. 在控制台中，从 SDK 运行以下代码示例。 此示例使用恒温器传感器创建模拟温度控制器。
    ```console
    python3 temp_controller_with_thermostats.py
    ```

    模拟设备连接到 IoT Central 应用程序后，它会连接到在该应用程序中创建的设备实例，并开始发送遥测数据。 控制台中显示了连接详细信息和遥测输出： 
    
    ```output
    Device was assigned
    iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azure-devices.net
    my-sdk-device
    Updating pnp properties for root interface
    {'serialNumber': 'alohomora'}
    Updating pnp properties for thermostat1
    {'thermostat1': {'maxTempSinceLastReboot': 98.34, '__t': 'c'}}
    Updating pnp properties for thermostat2
    {'thermostat2': {'maxTempSinceLastReboot': 48.92, '__t': 'c'}}
    Updating pnp properties for deviceInformation
    {'deviceInformation': {'swVersion': '5.5', 'manufacturer': 'Contoso Device Corporation', 'model': 'Contoso 4762B-turbo', 'osName': 'Mac Os', 'processorArchitecture': 'x86-64', 'processorManufacturer': 'Intel', 'totalStorage': 1024, 'totalMemory': 32, '__t': 'c'}}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry from various components
    Sent message
    {"temperature": 33}
    ```