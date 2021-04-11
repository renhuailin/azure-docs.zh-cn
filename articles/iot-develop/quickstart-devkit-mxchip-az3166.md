---
title: 将 MXCHIP AZ3166 连接到 Azure IoT Central 快速入门
description: 使用 Azure RTOS 嵌入软件将 MXCHIP AZ3166 设备连接到 Azure IoT 并发送遥测数据。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 03/17/2021
ms.openlocfilehash: 160797367e2daf0cb6fe708d626cbf217c9992c8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448588"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-central"></a>快速入门：将 MXCHIP AZ3166 Devkit 连接到 IoT Central

**适用于**：[嵌入式设备开发](about-iot-develop.md#embedded-device-development)<br>
**总完成时间**：30 分钟

[![浏览代码](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

在本教程中，将使用 Azure RTOS 将 MXCHIP AZ3166 IoT DevKit（后称 MXCHIP DevKit）连接到 Azure IoT。 本文是 [Azure IoT 嵌入式设备开发入门](quickstart-device-development.md)系列的一部分。 本系列文章向设备开发者介绍了 Azure RTOS，并演示了如何将多个设备评估工具包连接到 Azure IoT。

你将完成以下任务：

* 安装一组嵌入式开发工具，用于以 C 语言编写 MXCHIP DevKit 的程序
* 生成一个映像并将其刷入到 MXCHIP DevKit
* 使用 Azure IoT Central 创建云组件、查看属性、查看设备遥测和调用直接命令

## <a name="prerequisites"></a>先决条件

* 一台运行 Microsoft Windows 10 的 PC
* 用于克隆存储库的 [Git](https://git-scm.com/downloads)
* 硬件

    > * [MXCHIP AZ3166 IoT DevKit](https://aka.ms/iot-devkit)（简称 MXCHIP DevKit）
    > * Wi-Fi 2.4 GHz
    > * USB 2.0 A 公头转 Micro USB 公头电缆

## <a name="prepare-the-development-environment"></a>准备开发环境

要设置开发环境，首先要克隆 GitHub 存储库，其中包含本教程所需的所有资产。 然后安装一组编程工具。

### <a name="clone-the-repo-for-the-tutorial"></a>克隆本教程所需的存储库

克隆以下存储库以下载所有示例设备代码、安装脚本和文档的脱机版本。 如果以前在另一个教程中克隆过这个存储库，则不需要再次执行此操作。

要克隆存储库，请运行以下命令：

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>安装工具

克隆的存储库包含用于安装和配置所需工具的安装脚本。 如果在入门指南的其他教程中安装了这些工具，则不需要再次执行此操作。

> [!NOTE]
> 安装脚本将安装以下工具：
> * [CMake](https://cmake.org)：生成
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm)：编译
> * [Termite](https://www.compuphase.com/software_termite.htm)：监视已连接设备的串行端口输出

若要安装工具：

1. 在文件资源管理器中，导航到存储库中的以下路径，并运行名为 *get-toolchain.bat* 的安装脚本：

    > *getting-started\tools\get-toolchain.bat*

1. 安装后，打开一个新的控制台窗口来识别安装脚本所做的配置更改。 使用这个控制台完成本教程中的其余编程任务。 可以使用 Windows CMD、PowerShell 或 Git Bash for Windows。
1. 运行以下代码，确认已安装 CMake 版本3.14 或更高版本。

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>创建云组件

### <a name="create-the-iot-central-application"></a>创建 IoT Central 应用程序

可以通过多种方式将设备连接到 Azure IoT。 本部分介绍如何使用 Azure IoT Central 连接设备。 IoT Central 是一个 IoT 应用程序平台，可降低创建和管理 IoT 解决方案的成本和复杂性。

要创建新的应用程序：
1. 从 [Azure IoT Central 门户](https://apps.azureiotcentral.com/)的侧面导航菜单中选择“我的应用”。
1. 选择“+ 新建应用程序”。
1. 选择“自定义应用”。
1. 添加应用程序名称和 URL。
1. 选择“免费”定价计划以激活 7 天试用版。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-custom.png" alt-text="在 Azure IoT Central 中创建自定义应用":::

1. 选择“创建”  。

    IoT Central 在预配应用程序后，会自动将你重定向到新的应用程序仪表板。

    > [!NOTE]
    > 如果有现成的 IoT Central 应用程序，则可以用来完成本文中的步骤，而不必创建新的应用程序。

### <a name="create-a-new-device"></a>创建新设备

在本部分中，将使用 IoT Central 应用程序仪表板创建新设备。 在后面的部分中，将使用新创建的设备的连接信息安全连接到你的物理设备。

创建设备：
1. 在应用程序仪表板中，选择侧面导航菜单中的“设备”。
1. 选择“+ 新建”以打开“创建新设备”窗口。
1. 将“设备模板”保持设为“未分配”。
1. 填写所需的设备名称和设备 ID。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-device.png" alt-text="在 Azure IoT Central 中创建设备":::

1. 选择“创建”按钮。
1. 新创建的设备显示在“所有设备”列表中。  选择设备名称以显示详细信息。
1. 在右上方的菜单栏中选择“连接”可显示连接信息（用于在下一节中配置设备）。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-connection-info.png" alt-text="查看设备连接详细信息":::

1. 请注意，“连接”对话框中显示的以下连接字符串参数的连接值。 将在下一步中将这些值添加到配置文件中：

    > * `ID scope`
    > * `Device ID`
    > * `Primary key`

## <a name="prepare-the-device"></a>准备设备

要将 MXCHIP DevKit 连接到 Azure，需要修改 Wi-Fi 和 Azure IoT 设置的配置文件，重建映像，并将映像刷写到设备。

### <a name="add-configuration"></a>添加配置

1. 在文本编辑器中打开以下文件：

    > *getting-started\MXChip\AZ3166\app\azure_config.h*

1. 在本地环境中，将 Wi-Fi 常量设置为以下值。

    |常量名称|值|
    |-------------|-----|
    |`WIFI_SSID` |{*Wi-Fi SSID*}|
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

在控制台或文件资源管理器中，在以下路径中运行脚本 *rebuild.bat* 以生成映像：

> *getting-started\MXChip\AZ3166\tools\rebuild.bat*

生成完成后，请确认已在以下路径中创建了二进制文件：

> *getting-started\MXChip\AZ3166\build\app\mxchip_azure_iot.bin*

### <a name="flash-the-image"></a>刷写映像

1. 在 MXCHIP DevKit 上，找到“重置”按钮和 Micro USB 端口。 可在以下步骤中使用这两个组件。 下图突出显示了这两个组件：

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/mxchip-iot-devkit.png" alt-text="在 MXChip devkit 板上查找关键组件":::

1. 将 Micro USB 电缆连接到 MXCHIP DevKit 上的 Micro USB 端口，然后将电缆连接到计算机。
1. 在文件资源管理器中，找到在上一节中创建的二进制文件。
1. 复制二进制文件 *mxchip_azure_iot.bin*。
1. 在文件资源管理器中，找到连接到你的计算机的 MXCHIP DevKit 设备。 设备在系统中显示为驱动器，驱动器标签为 **AZ3166**。
1. 将二进制文件粘贴到 MXCHIP Devkit 的根文件夹中。 刷写会自动启动，并在几秒钟内完成。

    > [!NOTE]
    > 在刷写过程中，MXCHIP DevKit 上的绿色 LED 闪烁。

### <a name="confirm-device-connection-details"></a>确认设备连接详细信息

可以使用 **Termite** 应用来监视通信，并确认设备已正确设置。

1. 启动 **Termite**。
    > [!TIP]
    > 如果无法将 Termite 连接到 devkit，请安装 [ST-LINK 驱动程序](https://my.st.com/content/ccc/resource/technical/software/driver/files/stsw-link009.zip)，然后重试。 有关其他步骤，请参阅[故障排除](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md)。
1. 选择“设置”。
1. 在“串行端口设置”对话框中，检查以下设置并根据需要进行更新：
    * **波特率**：115,200
    * **端口**：你的 MXCHIP DevKit 连接到的端口。 如果下拉列表中有多个端口选项，则可以找到要使用的正确端口。 打开 Windows **设备管理器**，查看“端口”以确定要使用的端口。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/termite-settings.png" alt-text="在 Termite 应用中确认设置":::

1. 选择“确定”。
1. 按设备上的“复位”按钮。 该按钮在设备上进行了标记，位于 Micro USB 连接器附近。
1. 在 **Termite** 应用中，检查以下检查点值，以确认设备已初始化并连接到 Azure IoT。

    ```output
    Starting Azure thread

    Initializing WiFi
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 10.0.0.123
        Mask: 255.255.255.0
        Gateway: 10.0.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 10.0.0.1
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 185.242.56.3
        SNTP time update: Nov 16, 2020 23:47:35.385 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: ***
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***
        Device id: ***
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoTHub
    SUCCESS: Azure IoT Hub client initialized

    Starting Main loop
    ```

在以下步骤中，保持 Termite 处于打开状态以监视设备输出。

## <a name="verify-the-device-status"></a>查看设备状态

要在 IoT Central 门户中查看设备状态，请执行以下操作：
1. 在应用程序仪表板中，选择侧面导航菜单中的“设备”。
1. 确认“设备状态”已更新为“已预配”。
1. 确认“设备模板”已更新为“入门指南”。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-view-status.png" alt-text="在 IoT Central 中查看设备状态":::

## <a name="view-telemetry"></a>查看遥测数据

通过 IoT Central，可以查看从设备到云的遥测数据流。

在 IoT Central 门户中查看遥测数据：

1. 在应用程序仪表板中，选择侧面导航菜单中的“设备”。
1. 从设备列表中选择设备。
1. 在“概述”选项卡中，查看设备向云发送消息的实时遥测数据。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-telemetry.png" alt-text="在 IoT Central 中查看设备遥测数据":::

    > [!NOTE]
    > 还可以使用 Termite 应用从设备监视遥测数据。

## <a name="call-a-direct-method-on-the-device"></a>在设备上调用直接方法

还可以使用 IoT Central 调用在设备上实现的直接方法。 直接方法有一个名称，可以选择包含 JSON 有效负载、可配置的连接和方法超时。 在本节中，将调用一个方法让你可以打开或关闭 LED。

要在 IoT Central 门户中调用方法，请执行以下操作：

1. 从设备页中选择“命令”选项卡。
1. 选择“状态”，然后选择“运行”。  LED 指示灯应亮起。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-invoke-method.png" alt-text="在设备上调用直接方法":::

1. 取消选择“状态”，然后选择“运行”。 LED 指示灯应关闭。

## <a name="view-device-information"></a>查看设备信息

可以从 IoT Central 查看设备信息。

从设备页中选择“关于”选项卡。

:::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-about.png" alt-text="在 IoT Central 中查看有关设备的信息":::

## <a name="debugging"></a>调试

要调试应用程序，请参阅[使用 Visual Studio Code 进行调试](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md)。

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中创建的 Azure 资源，可以从 IoT Central 门户将其删除。 （可选）如果继续学习本入门指南中的其他教程，则可以保留已创建的资源并重复使用。

要删除整个 Azure IoT Central 示例应用程序及其所有设备和资源，请执行以下操作：
1. 选择“管理” > “你的应用程序”。 
1. 选择“删除”。 

## <a name="next-steps"></a>后续步骤

在本教程中，泥生成了一个包含 Azure RTOS 示例代码的自定义映像，并随后将该映像刷入 MXCHIP DevKit 设备。 还使用 IoT Central 门户创建了 Azure 资源，将 MXCHIP DevKit 安全地连接到 Azure，查看遥测数据以及发送消息。

* 对于设备开发人员，建议下一步是查看 [Azure IoT 嵌入式设备开发入门](quickstart-device-development.md)系列文章中的其他教程。
* 如果在执行本指南中的步骤后，设备进行初始化或连接时遇到问题，请参阅[故障排除](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md)。
* 要详细了解如何在本教程的示例代码中使用 Azure RTOS 组件，请参阅[在入门指南中使用 Azure RTOS](https://github.com/azure-rtos/getting-started/blob/master/docs/using-azure-rtos.md)。

    > [!IMPORTANT]
    > Azure RTOS 为 OEM 提供了一些组件，通过这些组件可以保护通信以及使用底层 MCU/MPU 硬件保护机制创建代码和数据隔离。 但是，每个 OEM 最终负责确保其设备满足不断变化的安全要求。

