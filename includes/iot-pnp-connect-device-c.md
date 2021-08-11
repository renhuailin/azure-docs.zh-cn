---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7b7ac037522c52aa4713973d1a056a03de5918bd
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114403720"
---
本快速入门介绍如何生成示例 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送的遥测数据。 该示例应用程序以 C 编写，包含在适用于 C 的 Azure IoT 设备 SDK 中。解决方案构建者可以使用 Azure IoT 资源管理器工具来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

[![浏览代码](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

可以在 Linux 或 Windows 上运行此快速入门。 本快速入门中的 shell 命令遵循适用于路径分隔符“`/`”的 Linux 约定，如果你是在 Windows 上操作，请务必将这些分隔符替换为“`\`”。

先决条件根据操作系统而异：

### <a name="linux"></a>Linux

本快速入门假设你使用 Ubuntu Linux。 本快速入门中的步骤已使用 Ubuntu 18.04 进行了测试。

要在 Linux 上完成本快速入门，需在本地 Linux 环境上安装以下软件：

使用 `apt-get` 命令安装“GCC”、“Git”、“cmake”和所有必要的依赖项：

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

要在 Windows 上完成本快速入门，需在本地 Windows 环境上安装以下软件：

* [Visual Studio（Community、Professional 或 Enterprise 版）](https://visualstudio.microsoft.com/downloads/)- [安装](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio 时，请确保包括“使用 C++ 的桌面开发”工作负荷。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

## <a name="download-the-code"></a>下载代码

在本快速入门中，你将准备一个用于克隆和生成 Azure IoT 中心设备 C SDK 的开发环境。

在所选目录中打开命令提示符。 执行以下命令将 [Azure IoT C SDK 和库](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库克隆到此位置：

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

此操作需要几分钟才能完成。

## <a name="build-the-code"></a>生成代码

使用设备 SDK 生成包含的示例代码：

1. 在设备 SDK 根文件夹中创建一个 cmake 子目录，并导航到该文件夹：

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 运行以下命令以生成 SDK 和示例：

    ```cmd\bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF ..
    cmake --build .
    ```

> [!TIP]
> 在 Windows 上，可以在 Visual Studio 2019 中打开 `cmake` 命令生成的解决方案。 打开 cmake 目录中的 azure_iot_sdks .sln 项目文件，并在解决方案中将 pnp_simple_thermostat 项目设置为“启动项目”。 现在可以在 Visual Studio 中生成示例，并在调试模式下运行它。

## <a name="run-the-device-sample"></a>运行设备示例

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

若要详细了解示例配置，请参阅[示例自述文件](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md)。

在 SDK 中运行示例应用程序，以模拟将遥测发送到 IoT 中心的 IoT 即插即用设备：

在 cmake 文件夹中，导航到包含可执行文件的文件夹，并运行该文件：

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_simple_thermostat\Debug
.\pnp_simple_thermostat.exe
```

> [!TIP]
> 若要在 Windows 上的 Visual Studio 中跟踪代码执行，请在 pnp_simple_thermostat.c 文件中向 `main` 函数添加一个断点。

设备现在可以接收命令和属性更新，并已开始向中心发送遥测数据。 在执行后续步骤时，保持示例处于运行状态。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

设备客户端示例启动后，使用 Azure IoT 资源管理器工具验证它是否正常工作。

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>查看代码

此示例实现了一个简单的 IoT 即插即用恒温器设备。 此示例实现的模型不使用 IoT 即插即用[组件](../articles/iot-develop/concepts-modeling-guide.md)。 [设备的 DTDL 模型文件](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)定义了设备实现的遥测、属性和命令。

设备代码使用标准函数连接到 IoT 中心：

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

设备发送在连接请求中实现的 DTDL 模型的模型 ID。 发送模型 ID 的设备是 IoT 即插即用设备：

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

更新属性、处理命令和发送遥测数据的代码与不使用 IoT 即插即用约定的设备的代码相同。

该代码使用 Parson 库来分析从 IoT 中心发送的有效负载中的 JSON 对象：

```c
// JSON parser
#include "parson.h"
```
