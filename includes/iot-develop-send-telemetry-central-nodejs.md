---
title: include 文件
description: include 文件
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/17/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 85e75839d204360847162b470d149e580fac80bc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910220"
---
[![浏览代码](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp)

本快速入门介绍一个基本的 Azure IoT 应用程序开发工作流。 首先，创建适用于托管设备的 Azure IoT Central 应用程序。 然后，使用 Azure IoT 设备 SDK 示例来运行模拟的温度控制器，将此控制器安全连接到 IoT Central，并发送遥测数据。

## <a name="prerequisites"></a>必备条件
本快速入门在 Windows、Linux 和 Raspberry Pi 上运行。 它已在以下 OS 和设备版本上进行了测试：

- Windows 10
- 在适用于 Linux 的 Windows 子系统 (WSL) 上运行的 Ubuntu 20.04 LTS
- 在 Raspberry Pi 3 Model B+ 上运行的 Raspberry Pi OS 版本 10 (buster)

在开发计算机上安装以下必备组件：

- [Node.js](https://nodejs.org/) 版本 6 或更高版本。 若要检查版本，请在控制台应用中运行 `node --version`。
- [Git](https://git-scm.com/downloads)。

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>运行模拟设备
在本部分，你将配置本地环境，安装 Azure IoT Node.js 设备 SDK，并运行创建模拟温度控制器的示例。

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

1. 将 Azure IoT Node.js 设备 SDK 复制到本地计算机。

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. 导航到示例目录。

    **Windows**
    ```console
    cd azure-iot-sdk-node\device\samples\pnp
    ```

    Linux 或 Raspberry Pi OS
    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. 安装 Azure IoT Node.js SDK 和所需的依赖项：
    ```console
    npm install
    ```

### <a name="run-the-code"></a>运行代码

1. 在控制台中，从 SDK 运行以下代码示例。 此示例使用恒温器传感器创建模拟温度控制器。
    ```console
    node pnpTemperatureController.js
    ```

    模拟设备连接到 IoT Central 应用程序后，它会连接到在该应用程序中创建的设备实例，并开始发送遥测数据。 控制台中显示了连接详细信息和遥测输出： 
    
    ```output
    registration succeeded
    assigned hub=iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx.azure-devices.net
    deviceId=my-sdk-device
    payload=undefined
    Connecting using connection string: HostName=iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx.azure-devices.net;DeviceId=my-sdk-device;SharedAccessKey=xxxxxxxxxxxxxxxxxxxxxxxxxxx
    Enabling the commands on the client
    Please enter q or Q to exit sample.
    The following properties will be updated for root interface.
    { serialNumber: 'alwinexlepaho8329' }
    The following properties will be updated for component: thermostat1
    {
      thermostat1: { maxTempSinceLastReboot: 40.53506261527863, __t: 'c' }
    }
    The following properties will be updated for component: thermostat2
    {
      thermostat2: { maxTempSinceLastReboot: 89.55136974144273, __t: 'c' }
    }
    The following properties will be updated for component: deviceInformation
    {
      deviceInformation: {
        manufacturer: 'Contoso Device Corporation',
        model: 'Contoso 47-turbo',
        swVersion: '10.89',
        osName: 'Contoso_OS',
        processorArchitecture: 'Contoso_x86',
        processorManufacturer: 'Contoso Industries',
        totalStorage: 65000,
        totalMemory: 640,
        __t: 'c'
      }
    }
    executed sample
    Received an update for device with value: {"$version":1}
    Properties have been reported for root interface.
    Properties have been reported for component: thermostat1
    Properties have been reported for component: thermostat2
    Properties have been reported for component: deviceInformation
    Sending telemetry message 0 from component: thermostat1
    Sending telemetry message 0 from component: thermostat2
    ```