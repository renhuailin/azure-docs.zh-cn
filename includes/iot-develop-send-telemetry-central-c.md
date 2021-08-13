---
title: 包含文件
description: 包含文件
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/06/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 9ba8a90dcd2043fc0f54278a3f1b807add819c66
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114712919"
---
[![浏览代码](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp/pnp_temperature_controller)

本快速入门介绍一种基本的 Azure IoT 应用程序开发工作流。 首先，创建适用于托管设备的 Azure IoT Central 应用程序。 然后，使用 Azure IoT 设备 SDK 示例来运行模拟的温度控制器，将此控制器安全连接到 IoT Central，并发送遥测数据。

## <a name="prerequisites"></a>先决条件
- 可以在 Linux 或 Windows 上运行此快速入门。 Shell 命令使用标准 Linux 路径分隔符 `/`。 如果使用 Windows，请将这些分隔符替换为 Windows 路径分隔符 `\`。

为操作系统安装剩余必备组件。

### <a name="linux"></a>Linux
本教程中的步骤已使用 Ubuntu Linux 18.04 进行了测试。

要在 Linux 上完成本快速入门，需在本地 Linux 环境上安装以下软件：

使用 `apt-get` 命令安装“GCC”、“Git”、“cmake”和必要的依赖项  ：

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

验证 `cmake` 的版本是否高于 2.8.12，GCC 的版本是否高于 4.4.7  。

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows
若要在 Windows 上完成本快速入门，请安装 Visual Studio 2019 并添加 C 和 C++ 开发所需的组件。

1. 对于新用户，请安装 [Visual Studio（Community、Professional 或 Enterprise）2019。](https://visualstudio.microsoft.com/downloads/) 下载要安装的版本，然后启动安装程序。
    > [!NOTE]
    > 对于现有 Visual Studio 2019 用户，请选择 Windows“开始”，键入“Visual Studio 安装程序”，然后启动安装程序。
1. 在安装程序“工作负载”选项卡中，选择“使用 C++ 的桌面开发”工作负载 。
1. 在安装程序“单个组件”选项卡中，选择“适用于 Windows 的 Git” 。
1. 进行安装。

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>运行模拟设备
在本部分中，将配置你的本地环境，安装 Azure IoT C 设备 SDK，并运行创建模拟温度控制器的示例。

### <a name="configure-your-environment"></a>配置环境

1. 打开控制台以安装 Azure IoT C 设备 SDK，并运行代码示例。 对于 Windows，请选择“开始”，键入“适用于 VS 2019 的开发人员命令提示”，然后打开控制台。 对于 Linux，打开 Bash。 

1. 使用控制台的适当命令设置以下环境变量。 模拟设备使用这些值连接到 IoT Central。 对于 `IOTHUB_DEVICE_DPS_ID_SCOPE`、`IOTHUB_DEVICE_DPS_DEVICE_KEY` 和 `IOTHUB_DEVICE_DPS_DEVICE_ID`，请使用之前保存的设备连接值。

    **CMD**

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > 在 Windows CMD 中，请不要在变量值的两边加上引号。

    **Bash**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="install-the-sdk-and-samples"></a>安装 SDK 和示例

1. 将 Azure IoT C 设备 SDK 复制到本地计算机。

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-c.git
    ```

1. 导航到 SDK 的根文件夹，并运行以下命令以更新依赖项：
    ```console
    cd azure-iot-sdk-c
    git submodule update --init
    ```
    此操作需要几分钟才能完成。

1. 要生成 SDK 和示例，请运行以下命令：

    ```console
    cmake -Bcmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF
    cmake --build cmake
    ```

### <a name="run-the-code"></a>运行代码

1. 使用适用于控制台的命令运行示例代码：

    **CMD**
    ```console
    cmake\iothub_client\samples\pnp\pnp_temperature_controller\Debug\pnp_temperature_controller.exe
    ```

    **Bash**
    ```bash
    cmake/iothub_client/samples/pnp/pnp_temperature_controller/Debug/pnp_temperature_controller
    ```

    模拟设备连接到 IoT Central 应用程序后，它会连接到在该应用程序中创建的设备实例，并开始发送遥测数据。 控制台中显示了连接详细信息和遥测输出： 
    
    ```output
    Info: Initiating DPS client to retrieve IoT Hub connection information
    -> 17:03:08 CONNECT | VER: 4 | KEEPALIVE: 0 | FLAGS: 194 | USERNAME: xxxxxxxxxxxxxxx/registrations/my-sdk-device/api-version=2019-03-31&ClientVersion=1.6.0 | PWD: XXXX | CLEAN: 1
    <- 17:03:09 CONNACK | SESSION_PRESENT: false | RETURN_CODE: 0x0
    -> 17:03:10 SUBSCRIBE | PACKET_ID: 1 | TOPIC_NAME: $dps/registrations/res/# | QOS: 1
    <- 17:03:11 SUBACK | PACKET_ID: 1 | RETURN_CODE: 1
    Info: Provisioning callback indicates success.  iothubUri=iotc-xxxxxxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx.azure-devices.net, deviceId=my-sdk-device
    -> 17:03:27 DISCONNECT
    Info: DPS successfully registered.  Continuing on to creation of IoTHub device client handle.
    Info: Successfully created device client.  Hit Control-C to exit program
    
    Info: Sending serialNumber property to IoTHub
    Info: Sending device information property to IoTHub.  propertyName=swVersion, propertyValue="1.0.0.0"
    Info: Sending device information property to IoTHub.  propertyName=manufacturer, propertyValue="Sample-Manufacturer"
    Info: Sending device information property to IoTHub.  propertyName=model, propertyValue="sample-Model-123"
    Info: Sending device information property to IoTHub.  propertyName=osName, propertyValue="sample-OperatingSystem-name"
    Info: Sending device information property to IoTHub.  propertyName=processorArchitecture, propertyValue="Contoso-Arch-64bit"
    Info: Sending device information property to IoTHub.  propertyName=processorManufacturer, propertyValue="Processor Manufacturer(TM)"
    Info: Sending device information property to IoTHub.  propertyName=totalStorage, propertyValue=10000
    Info: Sending device information property to IoTHub.  propertyName=totalMemory, propertyValue=200
    Info: Sending maximumTemperatureSinceLastReboot property to IoTHub for component=thermostat1
    Info: Sending maximumTemperatureSinceLastReboot property to IoTHub for component=thermostat2
    ```