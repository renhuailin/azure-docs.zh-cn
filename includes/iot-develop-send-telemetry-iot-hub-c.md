---
title: include 文件
description: include 文件
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 08/03/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: cf398acc35f309218d123a0c8fc60e7d6f4a3a81
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744295"
---
[![浏览代码](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp)

本快速入门介绍一个基本的 Azure IoT 应用程序开发工作流。 你将使用 Azure CLI 创建 Azure IoT 中心和设备。 然后，使用 Azure IoT 设备 SDK 示例来运行模拟的温度控制器，将此控制器安全连接到中心，并发送遥测数据。

## <a name="prerequisites"></a>先决条件
- 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Git](https://git-scm.com/downloads)。
- [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases)：用于监视和管理 Azure IoT 的跨平台实用工具 
- Azure CLI。 在本快速入门中，有两个选项可用于运行 Azure CLI 命令：
    - 使用 Azure Cloud Shell，这是一个交互式 Shell，可在浏览器中运行 CLI 命令。 建议使用此选项，因为无需安装任何插件。 如果是首次使用 Cloud Shell，请登录到 [Azure 门户](https://portal.azure.com)。 按照 [Cloud Shell 快速入门](../articles/cloud-shell/quickstart.md)中的步骤启动 Cloud Shell 并选择 Bash 环境。
    - （可选）在本地计算机上运行 Azure CLI。 如果已安装 Azure CLI，请运行 `az upgrade` 以将 CLI 和扩展升级到当前版本。 若要安装 Azure CLI，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

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
1. 在安装程序 **工作负载** 选项卡中，选择 **使用 C++ 的桌面开发** 工作负载。
1. 进行安装。

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-a-simulated-device"></a>运行模拟设备
在本部分，你将使用 C SDK 将消息从模拟设备发送到 IoT 中心。 你将运行一个示例，该示例实现了具有两个恒温器传感器的温度控制器。

### <a name="build-the-sample"></a>生成示例
1. 打开控制台以安装 Azure IoT C 设备 SDK，并运行代码示例。 对于 Windows，请选择“开始”，键入“适用于 VS 2019 的开发人员命令提示”，然后打开控制台。 对于 Linux，打开 Bash。

    > [!NOTE]
    > 如果使用的是 Azure CLI 的本地安装，则现在可能会打开两个控制台窗口。 请确保在刚刚打开的控制台中输入此部分中的命令，而不是用于 CLI 的命令。

1. 在 C 控制台中，将 Azure IoT C 设备 SDK 克隆到本地计算机：
    ```console
    git clone https://github.com/Azure/azure-iot-sdk-c.git
    ```
1. 导航到 SDK 的根文件夹，并运行以下命令以更新依赖项：
    ```console
    cd azure-iot-sdk-c
    git submodule update --init
    ```
    此操作需要几分钟才能完成。
1. 运行以下命令以生成 SDK 和示例：

    ```console
    cmake -Bcmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF
    cmake --build cmake
    ```
1. 设置以下环境变量，使模拟设备能够连接到 Azure IoT。
    * 设置名为 `IOTHUB_DEVICE_CONNECTION_STRING` 的环境变量。 对于变量值，请使用上一部分保存的设备连接字符串。
    * 设置名为 `IOTHUB_DEVICE_SECURITY_TYPE` 的环境变量。 对于变量，请使用文本字符串值 `connectionString`。

    **CMD**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```
    > [!NOTE]
    > 对于 Windows CMD，每个变量的字符串值的两边没有引号。

    **Bash**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

### <a name="run-the-code"></a>运行代码
1. 使用适用于控制台的命令运行示例代码。

    **CMD**
    ```console
    cmake\iothub_client\samples\pnp\pnp_temperature_controller\Debug\pnp_temperature_controller.exe
    ```

    **Bash**
    ```bash
    cmake/iothub_client/samples/pnp/pnp_temperature_controller/Debug/pnp_temperature_controller
    ```
    > [!NOTE]
    > 此代码示例使用 Azure IoT 即插即用，这使你无需任何手动配置即可将智能设备集成到你的解决方案中。  默认情况下，本文档中的大多数示例都使用 IoT 即插即用。 若要详细了解 IoT PnP 的优点以及使用或不使用它的案例，请参阅[什么是 IoT 即插即用？](../articles/iot-develop/overview-iot-plug-and-play.md)。

示例作为注册的设备安全地连接到 IoT 中心，并开始发送遥测消息。 示例输出将显示在控制台中。 

## <a name="view-telemetry"></a>查看遥测数据

可通过 IoT Explorer 查看设备遥测数据。 也可使用 Azure CLI 查看遥测数据。

若要在 Azure IoT Explorer 中查看遥测数据：

1. 从 IoT Explorer 的 IoT 中心，选择“查看此中心的设备”，然后从列表中选择自己的设备。 
1. 在设备的左侧菜单中，选择“遥测”。
1. 确认“使用内置事件中心”设置为“是”，然后选择“开始”。
1. 查看设备向云发送消息的遥测数据。

    :::image type="content" source="media/iot-develop-send-telemetry-iot-hub-c/iot-explorer-device-telemetry.png" alt-text="IoT Explorer 中设备遥测数据的屏幕截图":::

1. 选择“停止”以结束接收事件。

若要读取由单个设备组件发送的遥测数据，可以在 IoT Explorer 中使用即插即用功能。 例如，本快速入门中的温度控制器具有两个恒温器：thermostat1 和 thermostat2。 查看 thermostat1 报告的温度： 

1. 在 IoT Explorer 中的设备上，从左侧菜单中选择“IoT 即插即用组件”。 然后从组件列表中选择“thermostat1”。

1. 在“thermostat1”组件窗格中，从顶部菜单中选择“遥测” 。

1. 在“遥测”窗格中，按照之前执行的相同步骤进行操作。 确保“使用内置事件中心”设置为“是”，然后选择“开始”。

使用 Azure CLI 查看设备遥测数据：

1. 在 CLI 应用中，运行 [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) 命令以监视从模拟设备发送到 IoT 中心的事件。 使用之前在 Azure IoT 中为设备和 IoT 中心创建的名称。

    ```azurecli
    az iot hub monitor-events --output table --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. 在控制台中查看连接详细信息和遥测输出。

    ```output
    Starting event monitor, filtering on device: mydevice, use ctrl-c to stop...
    event:
      component: ''
      interface: dtmi:com:example:TemperatureController;1
      module: ''
      origin: mydevice
      payload: '{"workingSet":1251}'
    
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;1
      module: ''
      origin: mydevice
      payload: '{"temperature":22.00}'
    ```

