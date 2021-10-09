---
title: include 文件
description: include 文件
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: bade266f219acfbd1636ba6650b2554383b5a4c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909121"
---
[![浏览代码](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample)

本快速入门介绍一个基本的 Azure IoT 应用程序开发工作流。 首先，创建适用于托管设备的 Azure IoT Central 应用程序。 然后，使用 Azure IoT 设备 SDK 示例来运行模拟的温度控制器，将此控制器安全连接到 IoT Central，并发送遥测数据。

## <a name="prerequisites"></a>先决条件
- 具有 Java SE 开发工具包 8 或更高版本的开发计算机。 你可以从[下载 OpenJDK 的 Zulu 版本](https://www.azul.com/downloads/zulu-community/)为多个平台下载 Java 8 (LTS) JDK。 在安装程序中，选择“添加到路径”选项。
- [Apache Maven 3](https://maven.apache.org/download.cgi)。 将下载内容提取到本地文件夹后，将 Maven /bin 文件夹的完整路径添加到 Windows PATH 变量中。
- [适用于 Java 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-java) GitHub 存储库的本地副本。 下载存储库的副本并提取它：[下载 ZIP](https://github.com/Azure/azure-iot-sdk-java/archive/refs/heads/master.zip)。

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>运行模拟设备
在本部分中，将配置本地环境，安装 Azure IoT Java 设备 SDK，并运行创建模拟温度控制器的示例。

### <a name="configure-your-environment"></a>配置环境

1. 打开控制台，如 Windows CMD、PowerShell 或 Bash。

1. 使用适用于你的控制台的适当命令设置以下环境变量。 模拟设备使用这些值连接到 IoT Central。 对于 `IOTHUB_DEVICE_DPS_ID_SCOPE`、`IOTHUB_DEVICE_DPS_DEVICE_KEY` 和 `IOTHUB_DEVICE_DPS_DEVICE_ID`，请使用之前保存的设备连接值。

    **Windows CMD**

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

### <a name="build-and-run-the-code"></a>生成并运行代码

1. 在 Windows 上导航到下载的 Azure SDK for Java 的根文件夹，然后运行以下命令来生成示例。

    ```console
    cd azure-iot-sdk-java
    mvn install -T 2C -DskipTests
    ```

1. 导航到示例目录。
    ```console
    cd device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample
    ```

1. 在控制台中运行以下代码示例。 此示例使用恒温器传感器创建模拟温度控制器。
    ```console
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
    ```

    模拟设备连接到 IoT Central 应用程序后，它会连接到在该应用程序中创建的设备实例，并开始发送遥测数据。 在发送一些初始预配详细信息后，控制台会开始输出温度控制器的遥测数据。
    
    ```output
    2021-05-13 15:39:26.411 DEBUG Mqtt:253 - Sending MQTT SUBSCRIBE packet for topic $iothub/twin/res/#
    2021-05-13 15:39:26.428 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Request Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Device Operation Type [DEVICE_OPERATION_TWIN_UPDATE_REPORTED_PROPERTIES_REQUEST] )
    2021-05-13 15:39:26.432 DEBUG TemperatureController:427 - Property: Update - component = "deviceInformation" is COMPLETED.
    2021-05-13 15:39:26.436 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 15:39:26.438 DEBUG TemperatureController:438 - Telemetry: Sent - {"workingSet": 1024.0KiB }
    2021-05-13 15:39:26.439 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Request Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Device Operation Type [DEVICE_OPERATION_TWIN_UPDATE_REPORTED_PROPERTIES_REQUEST] )
    2021-05-13 15:39:26.439 DEBUG TemperatureController:446 - Property: Update - {"serialNumber": SR-123456} is COMPLETED
    2021-05-13 15:39:26.447 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 15:39:26.447 DEBUG TemperatureController:465 - Telemetry: Sent - {"temperature": 44.4░C} with message Id xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
    ```