---
title: 有关将 NXP MIMXRT1050-EVKB 连接到 Azure IoT Central 的快速入门
description: 使用 Azure RTOS 嵌入软件将 NXP MIMXRT1050-EVKB 设备连接到 Azure IoT 并发送遥测数据。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/04/2021
ms.openlocfilehash: 7dcba5490d3f341e68b07aa798e9ca0ffa1666e7
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112122087"
---
# <a name="quickstart-connect-an-nxp-mimxrt1050-evkb-evaluation-kit-to-iot-central"></a>快速入门：将 NXP MIMXRT1050-EVKB 评估工具包连接到 IoT Central

**适用于**：[嵌入式设备开发](about-iot-develop.md#embedded-device-development)<br>
**总完成时间**：30 分钟

[![浏览代码](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1050-EVKB/)

本快速入门介绍如何使用 Azure RTOS 将 NXP MIMXRT1050-EVKB 评估工具包（以下称为 NXP EVK）连接到 Azure IoT。

你将完成以下任务：

* 安装一组嵌入式开发工具，用于以 C 语言编写 NXP EVK 的程序
* 生成一个映像并将其刷写到 NXP EVK
* 使用 Azure IoT Central 创建云组件、查看属性、查看设备遥测和调用直接命令

## <a name="prerequisites"></a>先决条件

* 一台运行 Microsoft Windows 10 的 PC
* 用于克隆存储库的 [Git](https://git-scm.com/downloads)
* 硬件

    * [NXP MIMXRT1050-EVKB](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/i-mx-rt1050-evaluation-kit:MIMXRT1050-EVK) (NXP EVK)
    * USB 2.0 A 公头转 Micro USB 公头电缆
    * 可访问有线以太网
    * 以太网数据线

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
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm)：编译
> * [Termite](https://www.compuphase.com/software_termite.htm)：监视已连接设备的串行端口输出

若要安装工具：

1. 在文件资源管理器中，导航到存储库中的以下路径，并运行名为 *get-toolchain.bat* 的安装脚本：

    *getting-started\tools\get-toolchain.bat*

1. 安装后，打开一个新的控制台窗口来识别安装脚本所做的配置更改。 使用此控制台完成本快速入门中的其余编程任务。 可以使用 Windows CMD、PowerShell 或 Git Bash for Windows。
1. 运行以下代码，确认已安装 CMake 版本3.14 或更高版本。

    ```shell
    cmake --version
    ```

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>准备设备

若要将 NXP EVK 连接到 Azure，需要修改 Wi-Fi 和 Azure IoT 设置的配置文件，重建映像，并将映像刷写到设备。

### <a name="add-configuration"></a>添加配置

1. 在文本编辑器中打开以下文件：

    *getting-started\NXP\MIMXRT1050-EVKB\app\azure_config.h*

1. 将 Azure IoT 设备信息常量设置为在创建 Azure 资源后保存的值。

    |常量名称|值|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*ID 范围值*}|
    |`IOT_DPS_REGISTRATION_ID` |{*设备 ID 值*}|
    |`IOT_DEVICE_SAS_KEY` |{*主密钥值*}|

1. 保存并关闭该文件。

### <a name="build-the-image"></a>生成映像

1. 在控制台或文件资源管理器中，在以下路径中运行脚本 *rebuild.bat* 以生成映像：

    *getting-started\NXP\MIMXRT1050-EVKB\tools\rebuild.bat*

2. 生成完成后，请确认已在以下路径中创建了二进制文件：

    *getting-started\NXP\MIMXRT1050-EVKB\build\app\mimxrt1050_azure_iot.bin*

### <a name="flash-the-image"></a>刷写映像

1. 在 NXP EVK 上，找到“Reset”（重置）按钮、Micro USB 端口和以太网端口。 可在以下步骤中使用这两个组件。 下图突出显示了所有这三个组件：

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1050-evkb/nxp-1050-evkb-board.png" alt-text="在 NXP EVK 板上找到关键组件":::

1. 将 Micro USB 数据线连接到 NXP EVK 上的 Micro USB 端口，然后将数据线连接到计算机。 设备通电后，绿色常亮 LED 指示电源状态。
1. 使用以太网数据线将 NXP EVK 连接到以太网端口。
1. 在文件资源管理器中，找到在上一节中创建的二进制文件。
1. 复制二进制文件 *mimxrt1050_azure_iot.bin*
1. 在文件资源管理器中，找到与计算机连接的 NXP EVK 设备。 该设备在系统中显示为驱动器，其驱动器标签为 **RT1050-EVK**。
1. 将二进制文件粘贴到 NXP EVK 的根文件夹中。 刷写会自动启动，并在几秒钟内完成。

    > [!NOTE]
    > 在刷写过程中，NXP EVK 上的红色 LED 会快速闪烁。

### <a name="confirm-device-connection-details"></a>确认设备连接详细信息

可以使用 **Termite** 应用来监视通信，并确认设备已正确设置。

1. 启动 **Termite**。
    > [!TIP]
    > 如果刷写后在设备进行初始化或连接时遇到问题，请参阅[故障排除](troubleshoot-embedded-device-quickstarts.md)。
1. 选择“设置”。
1. 在“串行端口设置”对话框中，检查以下设置并根据需要进行更新：
    * **波特率**：115,200
    * 端口：NXP EVK 连接到的端口。 如果下拉列表中有多个端口选项，则可以找到要使用的正确端口。 打开 Windows **设备管理器**，查看“端口”以确定要使用的端口。

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1050-evkb/termite-settings.png" alt-text="Termite 应用中串行端口设置的屏幕截图":::

1. 选择“确定”。
1. 按设备上的“复位”按钮。 该按钮在设备上进行了标记，位于 Micro USB 连接器附近。
1. 在 **Termite** 应用中，检查以下检查点值，以确认设备已初始化并连接到 Azure IoT。

    ```output
    Starting Azure thread

    Initializing DHCP
        IP address: 10.0.0.77
        Mask: 255.255.255.0
        Gateway: 10.0.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 10.0.0.1
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 142.147.92.5
        SNTP time update: May 28, 2021 17:36:33.325 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: mydevice
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***.azure-devices.net
        Device id: mydevice
        Model id: dtmi:azurertos:devkit:gsg;1
    Connected to IoT Hub
    SUCCESS: Azure IoT Hub client initialized
    ```

在以下步骤中，保持 Termite 处于打开状态以监视设备输出。

## <a name="verify-the-device-status"></a>查看设备状态

要在 IoT Central 门户中查看设备状态，请执行以下操作：
1. 在应用程序仪表板中，选择侧面导航菜单中的“设备”。
1. 确认“设备状态”已更新为“已预配”。
1. 确认“设备模板”已更新为“入门指南”。

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1050-evkb/iot-central-device-view-status.png" alt-text="IoT Central 中设备状态的屏幕截图":::

## <a name="view-telemetry"></a>查看遥测数据

通过 IoT Central，可以查看从设备到云的遥测数据流。

在 IoT Central 门户中查看遥测数据：

1. 在应用程序仪表板中，选择侧面导航菜单中的“设备”。
1. 从设备列表中选择设备。
1. 在“概述”选项卡中，查看设备向云发送消息的实时遥测数据。
1. 温度是从 MCU 晶圆测得的。

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1050-evkb/iot-central-device-telemetry.png" alt-text="IoT Central 中设备遥测数据的屏幕截图":::

    > [!NOTE]
    > 还可以使用 Termite 应用从设备监视遥测数据。

## <a name="call-a-direct-method-on-the-device"></a>在设备上调用直接方法

还可以使用 IoT Central 来调用已在设备上实现的直接方法。 直接方法有一个名称，可以选择包含 JSON 有效负载、可配置的连接和方法超时。 在本节中，将调用一个方法让你可以打开或关闭 LED。

要在 IoT Central 门户中调用方法，请执行以下操作：

1. 从设备页中选择“命令”选项卡。
1. 在 **状态** 下拉列表中选择 **True**，然后选择 **运行**。  设备上不会有任何变化，因为没有可用于切换的 LED。 可以在 Termite 中查看输出，以监视这些方法的状态。

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1050-evkb/iot-central-invoke-method.png" alt-text="在 IoT Central 中调用设备上的直接方法的屏幕截图":::

1. 在“状态”下拉列表中选择“False”，然后选择“运行”  。

## <a name="view-device-information"></a>查看设备信息

可以从 IoT Central 查看设备信息。

从设备页中选择“关于”选项卡。

:::image type="content" source="media/quickstart-devkit-nxp-mimxrt1050-evkb/iot-central-device-about.png" alt-text="IoT Central 中设备信息的屏幕截图":::

## <a name="troubleshoot-and-debug"></a>故障排除和调试

如果在生成设备代码、刷写设备或进行连接时遇到问题，请参阅[故障排除](troubleshoot-embedded-device-quickstarts.md)。

要调试应用程序，请参阅[使用 Visual Studio Code 进行调试](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md)。

## <a name="clean-up-resources"></a>清理资源

如果不再需要本快速入门中创建的 Azure 资源，可以从 IoT Central 门户将其删除。

要删除整个 Azure IoT Central 示例应用程序及其所有设备和资源，请执行以下操作：
1. 选择“管理” > “你的应用程序”。 
1. 选择“删除”。 

## <a name="next-steps"></a>后续步骤

在本快速入门中，你生成了一个包含 Azure RTOS 示例代码的自定义映像，然后将该映像刷写到了 NXP EVK 设备。 你还使用 IoT Central 门户创建了 Azure 资源，将 NXP EVK 安全地连接到了 Azure，查看了遥测数据，并发送了消息。

接下来请浏览以下文章，以详细了解如何使用 IoT 设备 SDK 将设备连接到 Azure IoT。 

> [!div class="nextstepaction"]
> [将模拟设备连接到 IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [将模拟设备连接到 IoT 中心](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Azure RTOS 为 OEM 提供了一些组件，通过这些组件可以保护通信以及使用底层 MCU/MPU 硬件保护机制创建代码和数据隔离。 但是，每个 OEM 最终负责确保其设备满足不断变化的安全要求。

