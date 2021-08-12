---
title: 有关将 Renesas RX65N 云工具包连接到 Azure IoT Central 的快速入门
description: 使用 Azure RTOS 嵌入式软件将 Renesas RX65N 云工具包设备连接到 Azure IoT 并发送遥测数据。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/04/2021
ms.openlocfilehash: 38227e47154e280fc34624b4f92a4f75261b7004
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121959"
---
# <a name="quickstart-connect-a-renesas-rx65n-cloud-kit-to-iot-central"></a>快速入门：将 Renesas RX65N 云工具包连接到 IoT Central

**适用于**：[嵌入式设备开发](about-iot-develop.md#embedded-device-development)<br>
**总完成时间**：30 分钟

[![浏览代码](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/Renesas/RX65N_Cloud_Kit)

本快速入门介绍如何使用 Azure RTOS 将 Renesas RX65N 云工具包（随后，将 Renesas RX65N）连接到 Azure IoT。

你将完成以下任务：

* 安装一组嵌入式开发工具，用于以 C 语言编写 Renesas RX65N 程序
* 生成一个映像并将其刷写到 Renesas RX65N
* 使用 Azure IoT Central 创建云组件、查看属性、查看设备遥测和调用直接命令

## <a name="prerequisites"></a>先决条件

* 一台运行 Microsoft Windows 10 的 PC
* 用于克隆存储库的 [Git](https://git-scm.com/downloads)
* 硬件

    * [Renesas RX65N 云工具包](https://www.renesas.com/products/microcontrollers-microprocessors/rx-32-bit-performance-efficiency-mcus/rx65n-cloud-kit-renesas-rx65n-cloud-kit) (Renesas RX65N)
    * 2 根 USB 2.0 A 公头转 Mini USB 公头的数据线
    * WiFi 2.4 GHz

## <a name="prepare-the-development-environment"></a>准备开发环境

若要设置开发环境，请先克隆一个 GitHub 存储库，其中包含本快速入门所需的所有资产。 然后安装一组编程工具。

### <a name="clone-the-repo-for-the-quickstart"></a>克隆本快速入门所需的存储库

克隆以下存储库以下载所有示例设备代码、安装脚本和文档的脱机版本。 如果以前在另一篇快速入门中克隆过此存储库，则无需再次执行此操作。

要克隆存储库，请运行以下命令：

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>安装工具

克隆的存储库包含用于安装和配置所需工具的安装脚本。 如果在另一篇嵌入式设备快速入门中安装了这些工具，则无需再次执行此操作。

> [!NOTE]
> 安装脚本将安装以下工具：
> * [CMake](https://cmake.org)：生成
> * [RX GCC](http://gcc-renesas.com/downloads/get.php?f=rx/8.3.0.202004-gnurx/gcc-8.3.0.202004-GNURX-ELF.exe)：编译
> * [Termite](https://www.compuphase.com/software_termite.htm)：监视已连接设备的串行端口输出

若要安装工具：

1. 在文件资源管理器中，导航到存储库中的以下路径，并运行名为 get-toolchain-rx.bat 的安装脚本：

    getting-started\tools\get-toolchain-rx.bat

1. 将 RX 编译器添加到 Windows 路径：

   %USERPROFILE%\AppData\Roaming\GCC for Renesas RX 8.3.0.202004-GNURX-ELF\rx-elf\rx-elf\bin

1. 安装后，打开一个新的控制台窗口来识别安装脚本所做的配置更改。 使用此控制台完成本快速入门中的其余编程任务。 可以使用 Windows CMD、PowerShell 或 Git Bash for Windows。
1. 运行以下命令，确认已安装 CMake 3.14 或更高版本并已正确设置 RX 编译器路径。

    ```shell
    cmake --version
    rx-elf-gcc --version
    ```
若要安装其余工具，请执行以下操作：

* 安装 [Renesas Flash Programmer](https://www.renesas.com/software-tool/renesas-flash-programmer-programming-gui)。 Renesas Flash Programmer 包含通过 Renesas E2 Lite 刷写 Renesas RX65N 所需的驱动程序和工具。

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>准备设备

若要将 Renesas RX65N 连接到 Azure，需要修改 Wi-Fi 和 Azure IoT 设置的配置文件，重建映像，并将映像刷写到设备。

### <a name="add-configuration"></a>添加配置

1. 在文本编辑器中打开以下文件：

    getting-started\Renesas\RX65N_Cloud_Kit\app\azure_config.h

1. 在本地环境中，将 Wi-Fi 常量设置为以下值。

    |常量名称|值|
    |-------------|-----|
    |`WIFI_SSID` |{你的 Wi-Fi SSID}|
    |`WIFI_PASSWORD` |{*Wi-Fi 密码*}|
    |`WIFI_MODE` |{*文件中枚举的 Wi-Fi 模式值之一*}|

1. 将 Azure IoT 设备信息常量设置为在创建 Azure 资源后保存的值。

    |常量名称|值|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*ID 范围值*}|
    |`IOT_DPS_REGISTRATION_ID` |{*设备 ID 值*}|
    |`IOT_DEVICE_SAS_KEY` |{*主密钥值*}|

1. 保存并关闭该文件。

### <a name="build-the-image"></a>生成映像

1. 在控制台或文件资源管理器中，在以下路径中运行脚本 *rebuild.bat* 以生成映像：

    getting-started\Renesas\RX65N_Cloud_Kit\tools\rebuild.bat

2. 生成完成后，请确认已在以下路径中创建了二进制文件：

    getting-started\Renesas\RX65N_Cloud_Kit\build\app\rx65n_azure_iot.hex

### <a name="connect-the-device"></a>连接设备

> [!NOTE]
> 有关如何设置和开始使用 Renesas RX65N 的详细信息，请参阅 [Renesas RX65N 云工具包快速入门](https://www.renesas.com/document/man/quick-start-guide-renesas-rx65n-cloud-kit)。

1. 参考下图完成以下步骤。
    
    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/renesas-rx65n.jpg" alt-text="在 Renesas RX65N 板上找到重置按钮、USB 接口和 E1/E2Lite":::

1. 在板上断开 EJ2 链路以启用 E2 Lite 调试器。 该链路位于“USER SW”按钮下面。
    > [!WARNING] 
    > 不断开此链路会导致无法刷写设备。

1. 将 WiFi 模块连接到云选项板 

1. 使用第一根 Mini USB 数据线将 Renesas RX65N 上的 USB 串行接口连接到计算机。

1. 使用第二根 Mini USB 数据线将 Renesas RX65N 上的 USB E2 Lite 连接到计算机。

### <a name="flash-the-image"></a>刷写映像

1. 从“开始”菜单启动 Renesas Flash Programmer 应用程序。

2. 从“文件”菜单中选择“新建项目...”，然后输入以下设置 ：
    * 微控制器：RX65x
    * 项目名称：RX65N
    * 工具：E2 Emulator Lite
    * 接口：FINE

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/rfp-new.png" alt-text="Renesas Flash Programmer -“新建项目”的屏幕截图":::

3. 选择“工具详细信息”按钮，然后导航到“重置设置”选项卡 。

4. 选择“将引脚重置为 Hi-Z”并按“确定”按钮 。

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/rfp-reset.png" alt-text="Renesas Flash Programmer -“重置设置”的屏幕截图":::

5. 按“连接”按钮，出现提示时请选中“自动身份验证”复选框，然后按“确定”  。

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/rfp-auth.png" alt-text="Renesas Flash Programmer -“身份验证”的屏幕截图":::

6. 选择“浏览...”按钮，然后找到在上一部分创建的“rx65n_azure_iot.hex”文件 。

7. 按“开始”以开始刷写。 此过程大约需要 10 秒。

### <a name="confirm-device-connection-details"></a>确认设备连接详细信息

可以使用 **Termite** 应用来监视通信，并确认设备已正确设置。
> [!TIP]
> 如果刷写后在设备进行初始化或连接时遇到问题，请参阅[故障排除](troubleshoot-embedded-device-quickstarts.md)。

1. 启动 **Termite**。
1. 选择“设置”。
1. 在“串行端口设置”对话框中，检查以下设置并根据需要进行更新：
    * **波特率**：115,200
    * 端口：Renesas RX65N 连接到的端口。 如果下拉列表中有多个端口选项，则可以找到要使用的正确端口。 打开 Windows **设备管理器**，查看“端口”以确定要使用的端口。

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/termite-settings.png" alt-text="Termite 应用中串行端口设置的屏幕截图":::

1. 选择“确定”。
1. 按设备上的“复位”按钮。
1. 在 **Termite** 应用中，检查以下检查点值，以确认设备已初始化并连接到 Azure IoT。

    ```output
    Starting Azure thread

    Initializing WiFi
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 192.168.0.21
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.76.76
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 45.79.214.107
        SNTP time update: May 21, 2021 20:24:10.76 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: mydevice
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***.azure-devices.net
        Device id: mydevice
        Model id: dtmi:azurertos:devkit:gsgrx65ncloud;1
    Connected to IoT Hub
    SUCCESS: Azure IoT Hub client initialized
    ```

在以下步骤中，保持 Termite 处于打开状态以监视设备输出。

## <a name="verify-the-device-status"></a>查看设备状态

要在 IoT Central 门户中查看设备状态，请执行以下操作：
1. 在应用程序仪表板中，选择侧面导航菜单中的“设备”。
1. 确认“设备状态”已更新为“已预配”。
1. 确认“设备模板”已更新为“RX65N 云工具包入门指南” 。

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/iot-central-device-view-status.png" alt-text="IoT Central 中设备状态的屏幕截图":::

## <a name="view-telemetry"></a>查看遥测数据

通过 IoT Central，可以查看从设备到云的遥测数据流。

在 IoT Central 门户中查看遥测数据：

1. 在应用程序仪表板中，选择侧面导航菜单中的“设备”。
1. 从设备列表中选择设备。
1. 在“概述”选项卡中，查看设备向云发送消息的实时遥测数据。

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/iot-central-device-telemetry.png" alt-text="IoT Central 中设备遥测数据的屏幕截图":::

    > [!NOTE]
    > 还可以使用 Termite 应用从设备监视遥测数据。

## <a name="call-a-direct-method-on-the-device"></a>在设备上调用直接方法

还可以使用 IoT Central 来调用已在设备上实现的直接方法。 直接方法有一个名称，可以选择包含 JSON 有效负载、可配置的连接和方法超时。 在本节中，将调用一个方法让你可以打开或关闭 LED。

要在 IoT Central 门户中调用方法，请执行以下操作：

1. 从设备页中选择“命令”选项卡。
1. 在“状态”下拉列表中选择“True”，然后选择“运行”  。  LED 指示灯应亮起。

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/iot-central-invoke-method.png" alt-text="在 IoT Central 中调用设备上的直接方法的屏幕截图":::

1. 在“状态”下拉列表中选择“False”，然后选择“运行”  。 LED 指示灯应关闭。

## <a name="view-device-information"></a>查看设备信息

可以从 IoT Central 查看设备信息。

从设备页中选择“关于”选项卡。

:::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/iot-central-device-about.png" alt-text="IoT Central 中设备信息的屏幕截图":::

## <a name="troubleshoot"></a>疑难解答

如果在生成设备代码、刷写设备或进行连接时遇到问题，请参阅[故障排除](troubleshoot-embedded-device-quickstarts.md)。

## <a name="clean-up-resources"></a>清理资源

如果不再需要本快速入门中创建的 Azure 资源，可以从 IoT Central 门户将其删除。

要删除整个 Azure IoT Central 示例应用程序及其所有设备和资源，请执行以下操作：
1. 选择“管理” > “你的应用程序”。 
1. 选择“删除”。 

## <a name="next-steps"></a>后续步骤

在本快速入门中，你生成了一个包含 Azure RTOS 示例代码的自定义映像，然后将该映像刷写到了 Renesas RX65N 设备。 你还使用 IoT Central 门户创建了 Azure 资源，将 Renesas RX65N 安全地连接到了 Azure，查看了遥测数据，并发送了消息。

接下来请浏览以下文章，以详细了解如何使用 IoT 设备 SDK 将设备连接到 Azure IoT。 

> [!div class="nextstepaction"]
> [将模拟设备连接到 IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [将模拟设备连接到 IoT 中心](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Azure RTOS 为 OEM 提供了一些组件，通过这些组件可以保护通信以及使用底层 MCU/MPU 硬件保护机制创建代码和数据隔离。 但是，每个 OEM 最终负责确保其设备满足不断变化的安全要求。

