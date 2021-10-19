---
title: 包含文件
description: include 文件
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 10/07/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: a0082eb488c05d71409606e1048b0a94fd6155a4
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129855062"
---
[![浏览代码](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples)

本快速入门介绍一个基本的 Azure IoT 应用程序开发工作流。 使用 Azure CLI 和 IoT 资源管理器创建 Azure IoT 中心和设备。 然后，使用 Azure IoT 设备 SDK 示例来运行模拟的温度控制器，将此控制器安全连接到中心，并发送遥测数据。

## <a name="prerequisites"></a>先决条件

本快速入门在 Windows、Linux 和 Raspberry Pi 上运行。 它已在以下 OS 和设备版本上进行了测试：

- Windows 10
- 在适用于 Linux 的 Windows 子系统 (WSL) 上运行的 Ubuntu 20.04 LTS
- 在 Raspberry Pi 3 Model B+ 上运行的 Raspberry Pi OS 版本 10 (Raspian)

在开发计算机上安装以下必备组件（对 Raspberry Pi 另有注明的情况除外）：

- 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Git](https://git-scm.com/downloads)。
- .NET Core SDK 3.1。 请务必安装 .NET SDK，而不只是安装运行时。 若要检查计算机上安装的 .NET SDK 和运行时的版本，请运行 `dotnet --info`。

  - 对于 Windows 和 Linux（Raspberry Pi 除外），请按照说明在平台上[安装 .NET Core SDK 3.1](/dotnet/core/install/)。
  - 对于 Raspberry Pi，需要按照说明[手动安装 SDK](/dotnet/core/install/linux-scripted-manual#manual-install)。 这是因为在 Debian 上，.NET SDK 的包管理器安装仅支持 x64 体系结构。

- [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases)：用于监视和管理 Azure IoT 的基于 GUI 的跨平台实用工具。 如果使用 Raspberry Pi 作为开发平台，建议在另一台计算机上安装 IoT 资源管理器。 如果不想安装 IoT 资源管理器，可使用 Azure CLI 执行相同的步骤。 
- Azure CLI。 在本快速入门中，有两个选项可用于运行 Azure CLI 命令：
    - 使用 Azure Cloud Shell，这是一个交互式 Shell，可在浏览器中运行 CLI 命令。 建议使用此选项，因为无需安装任何插件。 如果是首次使用 Cloud Shell，请登录到 [Azure 门户](https://portal.azure.com)。 按照 [Cloud Shell 快速入门](../articles/cloud-shell/quickstart.md)中的步骤启动 Cloud Shell 并选择 Bash 环境。
    - （可选）在本地计算机上运行 Azure CLI。 如果已安装 Azure CLI，请运行 `az upgrade` 以将 CLI 和扩展升级到当前版本。 要安装 Azure CLI，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 如果使用 Raspberry Pi 作为开发平台，建议使用 Azure Cloud Shell 或在另一台计算机上安装 Azure CLI。

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-a-simulated-device"></a>运行模拟设备

在本部分，你将使用 C# SDK 将消息从模拟设备发送到 IoT 中心。 你将运行一个示例，该示例实现了具有两个恒温器传感器的温度控制器。

1. 打开一个新的控制台，如 Windows CMD、PowerShell 或 Bash。 在下面的步骤中，使用此控制台来安装 Node.js SDK 并处理 Node.js 示例代码。

    > [!NOTE]
    > 如果使用的是 Azure CLI 的本地安装，则现在可能会打开两个控制台窗口。 请确保在刚刚打开的控制台中输入此部分中的命令，而不是用于 CLI 的命令。

1. 将[适用于 C# 的 Microsoft Azure IoT 示例 (.NET) ](https://github.com/Azure-Samples/azure-iot-samples-csharp) 克隆到本地计算机：

    ```console
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

1. 导航到示例目录：

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

    此命令会安装在 TemperatureController.csproj 文件中指定的适当依赖项。

1. 设置以下两个环境变量，使模拟设备能够连接到 Azure IoT。
    * 设置名为 `IOTHUB_DEVICE_CONNECTION_STRING` 的环境变量。 对于变量值，请使用上一部分保存的设备连接字符串。
    * 设置名为 `IOTHUB_DEVICE_SECURITY_TYPE` 的环境变量。 对于变量，请使用文本字符串值 `connectionString`。

    CMD (Windows)

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > 对于 Windows CMD，每个变量的字符串值的两边没有引号。

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```
1. 运行代码示例：

    ```console
    dotnet run
    ```
    > [!NOTE]
    > 此代码示例使用 Azure IoT 即插即用，这使你无需任何手动配置即可将智能设备集成到你的解决方案中。  默认情况下，本文档中的大多数示例都使用 IoT 即插即用。 若要详细了解 IoT PnP 的优点以及使用或不使用它的案例，请参阅[什么是 IoT 即插即用？](../articles/iot-develop/overview-iot-plug-and-play.md)。

示例作为注册的设备安全地连接到 IoT 中心，并开始发送遥测消息。 示例输出将显示在控制台中。

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
