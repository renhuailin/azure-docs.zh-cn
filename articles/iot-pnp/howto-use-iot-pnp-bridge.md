---
title: 如何将 Linux 或 Windows 上运行的 IoT 即插即用桥接示例连接到 IoT 中心 | Microsoft Docs
description: 在 Linux 或 Windows 上构建并运行可连接到 IoT 中心的 IoT 即插即用桥接。 使用 Azure IoT 资源管理器工具查看由设备发送到中心的信息。
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 28feaf266e2d5bed14077a686e6eb936e430a695
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066749"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>如何将 Linux 或 Windows 上运行的 IoT 即插即用桥接示例连接到 IoT 中心

本快速入门介绍如何构建 IoT 即插即用桥接的示例环境适配器，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送的遥测数据。 IoT 即插即用桥接用 C 编写，包括适用于 C 的 Azure IoT 设备 SDK。本教程结束时，你应该能够运行 IoT 即插即用桥接，并在 Azure IoT 资源管理器中查看它报告遥测：

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="显示 Azure IoT 资源管理器的屏幕截图，其中包含一个来自 IoT 即插即用桥接的报告的遥测（湿度、温度）。":::

## <a name="prerequisites"></a>先决条件

你可以在 Windows 或 Linux 中运行本文的示例。 本操作指南中的 shell 命令遵循适用于路径分隔符“`\`”的 Windows 约定，如果你是在 Linux 上操作，请务必将这些分隔符替换为“`/`”。

### <a name="azure-iot-explorer"></a>Azure IoT 资源管理器

若要与本文第二部分中的示例设备进行交互，请使用“Azure IoT 资源管理器”工具。 为操作系统[下载并安装最新版本的 Azure IoT 资源管理器](./howto-use-iot-explorer.md)。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

运行以下命令，获取中心的 IoT 中心连接字符串。 记下此连接字符串，稍后将在本文中使用它：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

运行以下命令，获取已添加到中心的设备的设备连接字符串。 记下此连接字符串，稍后将在本文中使用它：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>下载并运行桥接

本文介绍了两个运行桥接的选项。 方法：

- 下载预生成的可执行文件，并按本部分所述运行它。
- 下载源代码，然后[构建并运行桥接](#build-and-run-the-bridge)，如以下部分所述。

若要下载并运行桥接：

1. 转到 IoT 即插即用[版本页](https://github.com/Azure/iot-plug-and-play-bridge/releases)。
1. 下载适用于你的操作系统的预生成的可执行文件：适用于 Windows 的“pnpbridge_bin.exe”或适用于 Linux 的“pnpbridge_bin” 。
1. 下载环境传感器示例的示例 [config.json](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) 配置文件。 请确保配置文件与可执行文件位于同一文件夹中。
1. 编辑“config.json”文件：

    - 添加 `connection-string` 值，它是你之前记下的设备连接字符串。
    - 添加 `symmetric_key`，它是来自“设备连接字符串”的共享访问密钥值。
    - 将 `root_interface_model_id` 的值替换为 `dtmi:com:example:PnpBridgeEnvironmentalSensor;1`。

    config.json 文件的第一部分现在类似于以下代码片段：

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. 在命令行环境中运行可执行文件。 桥接生成如下所示的输出：

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>构建并运行桥接

如果你希望自行生成可执行文件，则可以下载源代码和生成脚本。

在所选文件夹中打开命令提示符。 运行以下命令，将 [IoT 即插即用桥接](https://github.com/Azure/iot-plug-and-play-bridge) GitHub 存储库克隆到此位置：

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

克隆存储库后，请更新子模块。 子模块包括适用于 C 的 Azure IoT SDK：

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

此操作需要几分钟才能完成。

> [!TIP]
> Git 克隆子模块更新失败的问题是 Windows 文件路径的已知问题。 可以尝试以下命令来解决此问题：`git config --system core.longpaths true`

构建桥接的先决条件因操作系统而异：

### <a name="windows"></a>Windows

若要在 Windows 上构建 IoT 即插即用桥接，请安装以下软件：

* [Visual Studio（Community、Professional 或 Enterprise 版）](https://visualstudio.microsoft.com/downloads/)- [安装](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio 时，请确保包括“使用 C++ 的桌面开发”工作负荷。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

### <a name="linux"></a>Linux

本文假设使用 Ubuntu Linux。 本文中的步骤已使用 Ubuntu 18.04 进行了测试。

若要在 Linux 上构建 IoT 即插即用桥接，可使用 `apt-get` 命令安装 GCC、Git、cmake 以及所有必需的依赖项  ：

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

验证 `cmake` 的版本是否高于 2.8.12，GCC 的版本是否高于 4.4.7  。

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>构建 IoT 即插即用桥接

导航到 GitHub 存储库中的 pnpbridge 文件夹。

对于 Windows，请在[适用于 Visual Studio 的开发人员命令提示](/dotnet/framework/tools/developer-command-prompt-for-vs)中运行以下命令：

```cmd
cd scripts\windows
build.cmd
```

同样，对于 Linux，运行以下命令：

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>在 Windows 上，可以在 Visual Studio 2019 中打开 cmake 命令生成的解决方案。 打开 cmake 目录中的“azure_iot_pnp_bridge.sln”项目文件 ，并在解决方案中将“pnpbridge_bin”项目设置为“启动项目”。 现在可以在 Visual Studio 中生成示例，并在调试模式下运行它。

### <a name="edit-the-configuration-file"></a>编辑配置文件

可以在 [IoT 即插即用桥接概念文档](concepts-iot-pnp-bridge.md)中详细了解配置文件。

在文本编辑器中打开“iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.json”文件。

- 添加 `connection-string` 值，它是你之前记下的设备连接字符串。
- 添加 `symmetric_key`，它是来自“设备连接字符串”的共享访问密钥值。
- 将 `root_interface_model_id` 的值替换为 `dtmi:com:example:PnpBridgeEnvironmentalSensor;1`。

config.json 文件的第一部分现在类似于以下代码片段：

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>运行 IoT 即插即用桥接

启动 IoT 即插即用桥接环境传感器示例。 参数是在上一节中编辑的 `config.json` 文件的路径：

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

桥接生成如下所示的输出：

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

使用以下命令在 Linux 上运行桥接：

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>下载模型文件

稍后当设备连接到 IoT 中心时，使用 Azure IoT 资源管理器查看设备。 Azure IoT 资源管理器需要与设备发送的模型 ID 匹配的模型文件的本地副本。 此模型文件允许 IoT 资源管理器显示设备实现的遥测、属性和命令。

若要下载 Azure IoT 资源管理器的模型：

1. 在本地计算机上创建名为 models 的文件夹。
1. 将 [EnvironmentalSensor.json](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json) 保存到上一步中创建的“models”文件夹中。
1. 如果在文本编辑器中打开此模型文件，则可以看到模型定义了一个组件，其 ID 为 `dtmi:com:example:PnpBridgeEnvironmentalSensor;1`。 这是在 config.json 文件中使用的同一 ID。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

桥接启动后，使用 Azure IoT 资源管理器工具验证它是否正常工作。 你可以看到在 `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` 模型中定义的遥测、属性和命令。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本文已介绍如何将 IoT 即插即用设备连接到 IoT 中心。 若要详细了解如何生成可与 IoT 即插即用设备交互的解决方案，请参阅：

* [什么是 IoT 即插即用桥接](./concepts-iot-pnp-bridge.md)
* [构建、部署并扩展 IoT 即插即用桥接](howto-build-deploy-extend-pnp-bridge.md)
* [GitHub 上的 IoT 即插即用桥接](https://github.com/Azure/iot-plug-and-play-bridge)
