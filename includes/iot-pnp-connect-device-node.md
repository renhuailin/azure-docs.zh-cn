---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 114def65376e40fed81b4857e8d2f124ad665c70
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114403721"
---
本快速入门介绍如何生成示例 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送的遥测数据。 该示例应用程序采用 Node.js 编写，包含在用于 Node.js 的 Azure IoT 设备 SDK 中。 解决方案构建者可以使用 Azure IoT 资源管理器工具来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

[![浏览代码](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

若要完成本快速入门，需要在开发计算机上使用 Node.js。 可以从 [nodejs.org](https://nodejs.org) 下载适用于多个平台的最新推荐版本。

可以使用以下命令验证开发计算机上 Node.js 当前的版本：

```cmd/sh
node --version
```

## <a name="download-the-code"></a>下载代码

在本快速入门中，你需要准备一个用于克隆和生成用于 Node.js 的 Azure IoT 中心设备 SDK 的开发环境。

在所选目录中打开命令提示符。 执行以下命令，将[用于 Node.js 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 存储库克隆到此位置：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>安装所需的库

使用设备 SDK 生成包含的示例代码。 生成的应用程序将模拟连接到 IoT 中心的设备。 应用程序将发送遥测数据和属性，并接收命令。

1. 在本地终端窗口中，前往克隆存储库的文件夹，导航到 /azure-iot-sdk-node/device/samples/pnp 文件夹。 然后运行以下命令以安装所需的库：

    ```cmd/sh
    npm install
    ```

1. 使用之前记下的设备连接字符串配置环境变量：

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>运行示例设备

此示例实现了一个简单的 IoT 即插即用恒温器设备。 此示例实现的模型不使用 IoT 即插即用[组件](../articles/iot-develop/concepts-modeling-guide.md)。 [设备的 DTDL 模型文件](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)定义了设备实现的遥测、属性和命令。

打开“simple_thermostat .js”文件。 在此文件中，可以了解如何：

1. 导入所需的接口。
1. 编写属性更新处理程序和命令处理程序。
1. 处理所需的属性修补程序并发送遥测数据。
1. 选择性地使用 Azure 设备预配服务 (DPS) 预配设备。

在 main 函数中，可以了解如何逐步执行以下步骤：

1. 通过连接字符串创建设备，或使用 DPS 对其进行预配。
1. 使用 modelID 选项指定 IoT 即插即用设备型号。
1. 启用命令处理程序。
1. 将遥测数据从设备发送到中心。
1. 获取设备孪生，并更新已报告的属性。
1. 启用所需的属性更新处理程序。

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

若要详细了解示例配置，请参阅[示例自述文件](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md)。

运行示例应用程序，以模拟将遥测发送到 IoT 中心的 IoT 即插即用设备。 要运行示例应用程序，请使用以下命令：

```cmd\sh
node simple_thermostat.js
```

将显示以下输出，这表明设备已开始向中心发送遥测数据，现在可以接收命令和属性更新。

![设备确认消息](media/iot-pnp-connect-device-node/device-confirmation-node.png)

在执行后续步骤时，保持示例处于运行状态。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

设备客户端示例启动后，使用 Azure IoT 资源管理器工具验证它是否正常工作。

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
