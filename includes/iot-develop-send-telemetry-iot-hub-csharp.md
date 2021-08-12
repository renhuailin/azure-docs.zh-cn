---
title: 包含文件
description: 包含文件
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/05/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 4874da0fc3f4ee6265725c0af509d2664ec79a63
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114712784"
---
[![浏览代码](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples)

本快速入门介绍一个基本的 Azure IoT 应用程序开发工作流。 你将使用 Azure CLI 创建 Azure IoT 中心和设备。 然后，使用 Azure IoT 设备 SDK 示例来运行模拟的温度控制器，将此控制器安全连接到中心，并发送遥测数据。

## <a name="prerequisites"></a>先决条件
- 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Visual Studio（Community、Professional 或 Enterprise 版）2019](https://visualstudio.microsoft.com/downloads/)。
- [适用于 C# (.NET) 的 Microsoft Azure IoT 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存储库的本地副本。 下载存储库的副本并提取它：[下载 ZIP](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)。
- Azure CLI。 在本快速入门中，有两个选项可用于运行 Azure CLI 命令：
    - 使用 Azure Cloud Shell，这是一个交互式 Shell，可在浏览器中运行 CLI 命令。 建议使用此选项，因为无需安装任何插件。 如果是首次使用 Cloud Shell，请登录到 [Azure 门户](https://portal.azure.com)。 按照 [Cloud Shell 快速入门](../articles/cloud-shell/quickstart.md)中的步骤启动 Cloud Shell 并选择 Bash 环境。
    - （可选）在本地计算机上运行 Azure CLI。 如果已安装 Azure CLI，请运行 `az upgrade` 以将 CLI 和扩展升级到当前版本。 要安装 Azure CLI，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

[!INCLUDE [iot-hub-include-create-hub-cli](iot-hub-include-create-hub-cli.md)]

## <a name="run-a-simulated-device"></a>运行模拟设备
在本部分，你将配置本地环境，并运行创建模拟温度控制器的示例。

如要在 Visual Studio 中运行示例应用程序：

1. 在解压缩适用于 C# 的 Azure IoT 示例的文件夹中，打开 Visual Studio 中的“azure-iot-samples-csharp-master\iot-hub\Samples\device\IoTHubDeviceSamples.sln”解决方案文件。 

1. 在“解决方案资源管理器”中，选择“PnpDeviceSamples > TemperatureController”项目文件，右键单击它，然后选择“设为启动项目”  。

1. 右键单击“TemperatureController”项目，依次选择“属性”、调试”选项卡，然后将以下环境变量添加到该项目中  ：

    | 名称 | 值 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | *connectionString* |
    | IOTHUB_DEVICE_CONNECTION_STRING | 以前保存的连接字符串。 |

1. 保存更新后的“TemperatureController”项目文件。

1. 在 CLI 应用中，运行 [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) 命令以开始监视模拟 IoT 设备上的事件。  事件消息到达时会在终端中输出。

    ```azurecli-interactive
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. 在 Visual Studio 中，按 Ctrl + F5 以运行示例。

    在模拟设备连接到 IoT Central 应用程序后，它便会开始发送遥测。 连接详细信息和遥测输出显示在控制台中： 
    
    ```output
    Starting event monitor, use ctrl-c to stop...
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: myDevice
      payload:
        temperature: 39.8
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: myDevice
      payload:
        temperature: 36.7
    ```