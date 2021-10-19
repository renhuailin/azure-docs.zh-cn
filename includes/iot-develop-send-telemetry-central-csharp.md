---
title: 包含文件
description: include 文件
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 10/08/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 9d4dc20f2cebc25032f0d5ca07dd06169b2715ef
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129855060"
---
[![浏览代码](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples)

本快速入门介绍一个基本的 Azure IoT 应用程序开发工作流。 首先，创建适用于托管设备的 Azure IoT Central 应用程序。 然后，使用 Azure IoT 设备 SDK 示例来运行模拟的温度控制器，将此控制器安全连接到 IoT Central，并发送遥测数据。

## <a name="prerequisites"></a>先决条件

本快速入门在 Windows、Linux 和 Raspberry Pi 上运行。 它已在以下 OS 和设备版本上进行了测试：

- Windows 10
- 在适用于 Linux 的 Windows 子系统 (WSL) 上运行的 Ubuntu 20.04 LTS
- 在 Raspberry Pi 3 Model B+ 上运行的 Raspberry Pi OS 版本 10 (Raspian)

在开发计算机上安装以下必备组件：

- 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Git](https://git-scm.com/downloads)。
- .NET Core SDK 3.1+。 请务必安装 .NET SDK，而不只是安装运行时。 若要检查计算机上安装的 .NET SDK 和运行时的版本，请运行 `dotnet --info`。

  - 对于 Windows 和 Linux（除了 Raspberry Pi），请按照说明在平台上[安装 .NET Core SDK 3.1](/dotnet/core/install/)。
  - 对于 Raspberry Pi，你需要按照说明[手动安装 SDK](/dotnet/core/install/linux-scripted-manual#manual-install)。 这是因为在 Debian 上，.NET SDK 的包管理器安装仅支持 x64 体系结构。

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>运行模拟设备
在本部分中，你将配置本地环境，安装 Azure IoT C# 示例，并运行创建模拟温度控制器的示例。

### <a name="configure-your-environment"></a>配置环境

1. 打开控制台，如 Windows CMD、PowerShell 或 Bash。

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

1. 将[适用于 C# (.NET) 的 Microsoft Azure IoT 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp)克隆到本地计算机。

    ```console
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

1. 导航到示例目录。

    **Windows**

    ```console
    cd azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController
    ```

    Linux 或 Raspberry Pi OS

    ```console
    cd azure-iot-samples-csharp/iot-hub/Samples/device/PnpDeviceSamples/TemperatureController
    ```

1. 安装 Azure IoT C# SDK 和所需的依赖项：

    ```console
    dotnet restore
    ```

    此命令安装在 TemperatureController.csproj 文件中指定的适当依赖项。

### <a name="run-the-code"></a>运行代码

1. 在控制台中，运行代码示例。 此示例使用恒温器传感器创建模拟温度控制器。

    ```console
    dotnet run
    ```

    模拟设备连接到 IoT Central 应用程序后，它会连接到在该应用程序中创建的设备实例，并开始发送遥测数据。 控制台中显示了连接详细信息和遥测输出：

    ```output
    [10/09/2021 00:29:18]info: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Press Control+C to quit the sample.
    [10/09/2021 00:29:18]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set up the device client.
    [10/09/2021 00:29:18]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Initializing via DPS
    [10/09/2021 00:29:38]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set handler for 'reboot' command.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Connection status change registered - status=Connected, reason=Connection_Ok.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set handler for "getMaxMinReport" command.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set handler to receive 'targetTemperature' updates.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Property: Update - component = 'deviceInformation', properties update is complete.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Property: Update - { "serialNumber": "SR-123456" } is complete.
    [10/09/2021 00:29:40]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Telemetry: Sent - component="thermostat1", { "temperature": 23.7 } in °C.
    [10/09/2021 00:29:40]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Property: Update - component="thermostat1", { "maxTempSinceLastReboot": 23.7 } in °C is complete.
    [10/09/2021 00:29:40]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Telemetry: Sent - component="thermostat2", { "temperature": 25.8 } in °C.
    ```