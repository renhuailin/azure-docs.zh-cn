---
title: include 文件
description: include 文件
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 08/03/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 6f8cf902303dec652c8b1217815ccd9698e2f21e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610206"
---
[![浏览代码](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples)

本快速入门介绍一个基本的 Azure IoT 应用程序开发工作流。 使用 Azure CLI 和 IoT 资源管理器创建 Azure IoT 中心和设备。 然后，使用 Azure IoT 设备 SDK 示例来运行模拟的温度控制器，将此控制器安全连接到中心，并发送遥测数据。

## <a name="prerequisites"></a>先决条件
- 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Visual Studio（Community、Professional 或 Enterprise 版）2019](https://visualstudio.microsoft.com/downloads/)。
- [适用于 C# (.NET) 的 Microsoft Azure IoT 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存储库的本地副本。 下载存储库的副本并提取它：[下载 ZIP](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)。
- [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases)：用于监视和管理 Azure IoT 的基于 GUI 的跨平台实用工具。 
- Azure CLI。 在本快速入门中，有两个选项可用于运行 Azure CLI 命令：
    - 使用 Azure Cloud Shell，这是一个交互式 Shell，可在浏览器中运行 CLI 命令。 建议使用此选项，因为无需安装任何插件。 如果是首次使用 Cloud Shell，请登录到 [Azure 门户](https://portal.azure.com)。 按照 [Cloud Shell 快速入门](../articles/cloud-shell/quickstart.md)中的步骤启动 Cloud Shell 并选择 Bash 环境。
    - （可选）在本地计算机上运行 Azure CLI。 如果已安装 Azure CLI，请运行 `az upgrade` 以将 CLI 和扩展升级到当前版本。 要安装 Azure CLI，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-a-simulated-device"></a>运行模拟设备
在本部分，你将使用 C# SDK 将消息从模拟设备发送到 IoT 中心。 你将运行一个示例，该示例实现了具有两个恒温器传感器的温度控制器。

如要在 Visual Studio 中运行示例应用程序：

1. 在解压缩适用于 C# 的 Azure IoT 示例的文件夹中，打开 Visual Studio 中的“azure-iot-samples-csharp-master\iot-hub\Samples\device\IoTHubDeviceSamples.sln”解决方案文件。 

1. 在“解决方案资源管理器”中，选择“PnpDeviceSamples > TemperatureController”项目文件，右键单击它，然后选择“设为启动项目”  。

1. 右键单击“TemperatureController”项目，依次选择“属性”、调试”选项卡，然后将以下环境变量添加到该项目中  ：

    | 名称 | 值 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | *connectionString* |
    | IOTHUB_DEVICE_CONNECTION_STRING | 以前保存的连接字符串。 |

1. 保存更新后的“TemperatureController”项目文件。

1. 在 Visual Studio 中，按 Ctrl + F5 以运行示例。

控制台窗口即会打开。 示例作为注册的设备安全地连接到 IoT 中心，并开始发送遥测消息。 示例输出将显示在控制台中。

## <a name="view-telemetry"></a>查看遥测数据

可以通过 IoT Explorer 查看设备遥测数据。 或者，可以使用 Azure CLI 查看遥测数据。

若要在 Azure IoT Explorer 中查看遥测数据：

1. 从 IoT Explorer 的 IoT 中心中，选择“查看此中心的设备”，然后从列表中选择自己的设备。 
1. 在设备的左侧菜单中，选择“遥测”。
1. 确认“使用内置事件中心”设置为“是”，然后选择“开始”。
1. 查看设备向云发送消息的遥测数据。

    :::image type="content" source="media/iot-develop-send-telemetry-iot-hub-csharp/iot-explorer-device-telemetry.png" alt-text="IoT Explorer 中设备遥测数据的屏幕截图":::

1. 选择“停止”以结束接收事件。

若要读取单个设备组件发送的遥测数据，可以在 IoT Explorer 中使用即插即用功能。 例如，本快速入门中的温度控制器具有两个恒温器：thermostat1 和 thermostat2。 若要查看 thermostat1 报告的温度： 

1. 在 IoT Explorer 中的设备上，从左侧菜单中选择“IoT 即插即用组件”。 然后从组件列表中选择“thermostat1”。

1. 在“thermostat1”组件窗格中，从顶部菜单中选择“遥测” 。

1. 在“遥测”窗格中，按照之前执行的相同步骤进行操作。 确保“使用内置事件中心”设置为“是”，然后选择“开始”。

若要使用 Azure CLI 查看设备遥测数据：

1. 运行 [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) 命令以监视从模拟设备发送到 IoT 中心的事件。 使用之前在 Azure IoT 中为设备和 IoT 中心创建的名称。

    ```azurecli
    az iot hub monitor-events --output table --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. 在控制台中查看连接详细信息和遥测输出。

    ```output
    Starting event monitor, filtering on device: mydevice, use ctrl-c to stop...
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 39.8
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 36.7
    ```

1. 选择 CTRL+C 结束监视。
