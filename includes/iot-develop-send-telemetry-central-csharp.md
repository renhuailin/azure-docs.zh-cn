---
title: include 文件
description: include 文件
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: b4ff1f14ac628f28e67f4b619983760d06f60d8b
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129516739"
---
[![浏览代码](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples)

本快速入门介绍一个基本的 Azure IoT 应用程序开发工作流。 首先，创建适用于托管设备的 Azure IoT Central 应用程序。 然后，使用 Azure IoT 设备 SDK 示例来运行模拟的温度控制器，将此控制器安全连接到 IoT Central，并发送遥测数据。

## <a name="prerequisites"></a>先决条件
- [Visual Studio（Community、Professional 或 Enterprise 版）2019](https://visualstudio.microsoft.com/downloads/)。
- [适用于 C# (.NET) 的 Microsoft Azure IoT 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存储库的本地副本。 下载存储库的副本并提取它：[下载 ZIP](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip)。

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>运行模拟设备
在本部分，你将配置本地环境，并运行创建模拟温度控制器的示例。

如要在 Visual Studio 中运行示例应用程序：

1. 在解压缩了适用于 C# 的 Azure IoT 示例的文件夹中，打开 Visual Studio 中的“azure-iot-samples-csharp-main\iot-hub\Samples\device\IoTHubDeviceSamples.sln”解决方案文件。 

1. 在“解决方案资源管理器”中，选择“PnpDeviceSamples > TemperatureController”项目文件，右键单击它，然后选择“设为启动项目”  。

1. 右键单击“TemperatureController”项目，依次选择“属性”、调试”选项卡，然后将以下环境变量添加到该项目中  ：

    | 名称 | 值 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | 之前记下的 ID 范围值。 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | sample-device-01 |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | 之前记下的生成的设备密钥值。 |

1. 保存更新后的“TemperatureController”项目文件。

1. 按 Ctrl+F5 运行示例。

    在模拟设备连接到 IoT Central 应用程序后，它便会开始发送遥测。 连接详细信息和遥测输出显示在控制台中： 
    
    ```output
        [05/04/2021 11:53:50]info: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Press Control+C to quit the sample.
        [05/04/2021 11:53:50]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Set up the device client.
        [05/04/2021 11:53:50]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Initializing via DPS
        [05/04/2021 11:53:56]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Set handler for 'reboot' command.
        [05/04/2021 11:53:57]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Connection status change registered - status=Connected, reason=Connection_Ok.
        [05/04/2021 11:53:57]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Set handler for "getMaxMinReport" command.
        [05/04/2021 11:53:57]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Set handler to receive 'targetTemperature' updates.
        [05/04/2021 11:53:57]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Property: Update - component = 'deviceInformation', properties update is complete.
        [05/04/2021 11:53:58]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Property: Update - { "serialNumber": "SR-123456" } is complete.
        [05/04/2021 11:53:58]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Telemetry: Sent - component="thermostat1", { "temperature": 44.9 } in °C.
        [05/04/2021 11:53:58]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Property: Update - component="thermostat1", { "maxTempSinceLastReboot": 44.9 } in °C is complete.
        [05/04/2021 11:53:58]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Telemetry: Sent - component="thermostat2", { "temperature": 40.8 } in °C.
    ```