---
title: 将 MXCHIP AZ3166 连接到 Azure IoT 中心快速入门
description: 使用 Azure RTOS 嵌入软件将 MXCHIP AZ3166 设备连接到 Azure IoT 中心并发送遥测数据。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/09/2021
ms.openlocfilehash: aea20a9f8a6f0725220a60e425b48e22c6aa8794
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862117"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-hub"></a>快速入门：将 MXCHIP AZ3166 devkit 连接到 IoT 中心

**适用于**：[嵌入式设备开发](about-iot-develop.md#embedded-device-development)<br>
**总完成时间**：30 分钟

[![浏览代码](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

本快速入门介绍如何使用 Azure RTOS 将 MXCHIP AZ3166 IoT DevKit（随后将 MXCHIP DevKit）连接到 Azure IoT。 

还将使用 IoT Explorer 和 IoT 即插即用来管理 MXCHIP DevKit。 IoT 即插即用提供一个开放设备模型，允许应用程序以编程方式查询设备的功能并与之进行交互。 设备使用该模型将其功能广播到启用 IoT 即插即用的应用程序。 通过使用此模型，可以简化和增强添加、配置和管理设备的任务。 有关详细信息，请参阅 [IoT 即插即用文档](../iot-develop/index.yml)。

将完成以下任务：

* 安装一组嵌入式开发工具，用于以 C 语言编写 MXChip DevKit 的程序
* 生成一个映像并将其刷入到 MXCHIP DevKit
* 使用 Azure CLI 创建并管理 MXCHIP DevKit 将安全连接到的 Azure IoT 中心
* 使用 Azure IoT Explorer 向 IoT 中心注册设备，查看设备属性，查看设备遥测数据，并在设备上调用直接命令

## <a name="prerequisites"></a>先决条件

* 一台运行 Microsoft Windows 10 的 PC
* 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于克隆存储库的 [Git](https://git-scm.com/downloads)
* Azure CLI。 在本快速入门中，有两个选项可用于运行 Azure CLI 命令：
    * 使用 Azure Cloud Shell，这是一个交互式 Shell，可在浏览器中运行 CLI 命令。 建议使用此选项，因为无需安装任何插件。 如果是首次使用 Cloud Shell，请登录到 [Azure 门户](https://portal.azure.com)。 按照 [Cloud Shell 快速入门](../cloud-shell/quickstart.md)中的步骤启动 Cloud Shell 并选择 Bash 环境。
    * （可选）在本地计算机上运行 Azure CLI。 如果已安装 Azure CLI，请运行 `az upgrade` 以将 CLI 和扩展升级到当前版本。 若要安装 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

* [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases)：用于监视和管理 Azure IoT 的跨平台实用工具 
* 硬件

    * [MXCHIP AZ3166 IoT DevKit](https://www.seeedstudio.com/AZ3166-IOT-Developer-Kit.html)（简称 MXCHIP DevKit）
    * Wi-Fi 2.4 GHz
    * USB 2.0 A 公头转 Micro USB 公头电缆

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
> * [Termite](https://www.compuphase.com/software_termite.htm)：监视已连接设备资源的串行端口输出

若要安装工具：

1. 在文件资源管理器中，导航到存储库中的以下路径，并运行名为 *get-toolchain.bat* 的安装脚本：

    *getting-started\tools\get-toolchain.bat*

1. 安装后，打开一个新的控制台窗口来识别安装脚本所做的配置更改。 使用此控制台完成本快速入门中的其余编程任务。 可以使用 Windows CMD、PowerShell 或 Git Bash for Windows。
1. 运行以下代码，确认已安装 CMake 版本3.14 或更高版本。

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>创建云组件

### <a name="create-an-iot-hub"></a>创建 IoT 中心

可使用 Azure CLI 来创建 IoT 中心，用于处理设备的事件和消息。

若要创建 IoT 中心：

1. 启动 CLI 应用。 若要在本快速入门的其余部分运行 CLI 命令，请复制命令语法，将其粘贴到 CLI 应用中，编辑变量值，然后按 Enter。
    - 如果首选使用 Cloud Shell，请右键单击 [Cloud Shell](https://shell.azure.com/bash) 的链接，然后选择要在新选项卡中打开的选项。
    - 如果要在本地使用 Azure CLI，请启动 CLI 控制台应用并登录到 Azure CLI。

1. 运行 [az extension add](/cli/azure/extension?view=azure-cli-latest#az_extension_add&preserve-view=true)，将“azure-iot”扩展安装或升级到当前版本。

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. 运行 [az group create](/cli/azure/group#az-group-create) 命令创建资源组。 以下命令在“centralus”区域创建名为“myResourceGroup”的资源组。 

    > [!NOTE] 
    > 可以选择设置备用 `location`。 若要查看可用位置，请运行 [az account list-locations](/cli/azure/account#az-account-list-locations)。

    ```azurecli
    az group create --name MyResourceGroup --location centralus
    ```

1. 运行 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 命令创建 IoT 中心。 创建 IoT 中心可能需要数分钟的时间。

    *YourIotHubName*。 将下面的占位符替换为你为 IoT 中心选择的名称。 IoT 中心名称必须在 Azure 中全局唯一。 此占位符在本快速入门的其余部分中用于表示唯一的 IoT 中心名称。

    `--sku F1` 参数在免费层中创建 IoT 中心。 免费层中心具有有限的功能集，用于概念证明应用程序。 有关 IoT 中心层、功能和定价的详细信息，请参阅 [Azure IoT 中心定价](https://azure.microsoft.com/pricing/details/iot-hub)。 

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName} --sku F1 --partition-count 2
    ```

1. 创建 IoT 中心后，在控制台中查看 JSON 输出，并复制 `hostName` 值用于后面的步骤。 `hostName` 值如以下示例所示：

    `{Your IoT hub name}.azure-devices.net`

### <a name="configure-iot-explorer"></a>配置 IoT Explorer

在本快速入门的其余部分中，将使用 IoT Explorer 将设备注册到 IoT 中心，查看设备属性和遥测数据，并将命令发送到设备。 在本部分中，将配置 IoT Explorer 以连接到刚创建的 IoT 中心，并从公共模型存储库中读取即插即用模型。 

若要添加与 IoT 中心的连接：

1. 在 CLI 应用中，运行 [az iot hub connection-string show](/cli/azure/iot/hub/connection-string#az_iot_hub_connection_string_show) 命令以获取用于 IoT 中心的连接字符串。

    ```azurecli
    az iot hub connection-string  show --hub-name {YourIoTHubName}
    ```

1. 复制不带两旁引号字符的连接字符串。
1. 在 Azure IoT Explorer 的左侧菜单中，选择“IoT 中心”，然后选择“+ 添加连接”。
1. 将连接字符串粘贴到“连接字符串”框中。
1. 选择“保存”。 

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-add-connection.png" alt-text="在 IoT Explorer 中添加连接的屏幕截图":::

1. 如果连接成功，IoT Explorer 将切换到“设备”视图。

若要添加公共模型存储库：

1. 在 IoT Explorer 中，选择“主页”以返回到主页视图。
1. 在左侧菜单中，选择“IoT 即插即用设置”，然后从下拉菜单中依次选择“+添加”和“公共存储库”。
1. 此时会在 `https://devicemodels.azure.com` 中显示公共模型存储库的条目。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-add-public-repository.png" alt-text="在 IoT Explorer 中添加公共模型存储库的屏幕截图":::

1. 选择“保存”。 

### <a name="register-a-device"></a>注册设备

在本部分中，将创建一个新设备实例，并将其注册到所创建的 IoT 中心。 在后面的部分中，将使用新注册的设备的连接信息安全连接物理设备。

若要注册设备：

1. 从 IoT Explorer 的主页视图中，选择“IoT 中心”。
1. 此时应出现之前添加的连接。 在“连接属性”下面选择“查看此中心的设备”。
1. 选择“+ 新建”，然后输入设备的设备 ID；例如，mydevice。 将所有其它属性保持不变。
1. 选择“创建”  。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-device-created.png" alt-text="Azure IoT Explorer 设备标识的屏幕截图":::

1. 使用复制按钮复制并记下“设备 ID”和“主键”字段。

在继续下一部分之前，请确认已复制以下值：

* `hostName`
* `deviceId`
* `primaryKey`

## <a name="prepare-the-device"></a>准备设备

要将 MXCHIP DevKit 连接到 Azure，需要修改 Wi-Fi 和 Azure IoT 设置的配置文件，重建映像，并将映像刷写到设备。

### <a name="add-configuration"></a>添加配置

1. 在文本编辑器中打开以下文件：

    *getting-started\MXChip\AZ3166\app\azure_config.h*

1. 如下所示，注释掉文件顶部附近的以下行：

    ```c
    // #define ENABLE_DPS
    ```

1. 在本地环境中，将 Wi-Fi 常量设置为以下值。

    |常量名称|值|
    |-------------|-----|
    |`WIFI_SSID` |{*Wi-Fi SSID*}|
    |`WIFI_PASSWORD` |{*Wi-Fi 密码*}|
    |`WIFI_MODE` |{*文件中枚举的 Wi-Fi 模式值之一*}|

1. 将 Azure IoT 设备信息常量设置为在创建 Azure 资源后保存的值。

    |常量名称|值|
    |-------------|-----|
    |`IOT_HUB_HOSTNAME` |{*IoT 中心主机名值*}|
    |`IOT_DPS_REGISTRATION_ID` |{*设备 ID 值*}|
    |`IOT_DEVICE_SAS_KEY` |{*主密钥值*}|

1. 保存并关闭该文件。

### <a name="build-the-image"></a>生成映像

1. 在控制台或文件资源管理器中，在以下路径中运行脚本 *rebuild.bat* 以生成映像：

    *getting-started\MXChip\AZ3166\tools\rebuild.bat*

2. 生成完成后，请确认已在以下路径中创建了二进制文件：

    *getting-started\MXChip\AZ3166\build\app\mxchip_azure_iot.bin*

### <a name="flash-the-image"></a>刷写映像

1. 在 MXCHIP DevKit 上，找到“重置”按钮和 Micro USB 端口。 可在以下步骤中使用这两个组件。 下图突出显示了这两个组件：

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/mxchip-iot-devkit.png" alt-text="在 MXChip devkit 板上查找关键组件":::

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
    > 如果无法将 Termite 连接到 devkit，请安装 [ST-LINK 驱动程序](https://www.st.com/en/development-tools/stsw-link009.html)，然后重试。 有关其他步骤，请参阅[故障排除](troubleshoot-embedded-device-quickstarts.md)。
1. 选择“设置”。
1. 在“串行端口设置”对话框中，检查以下设置并根据需要进行更新：
    * **波特率**：115,200
    * **端口**：你的 MXCHIP DevKit 连接到的端口。 如果下拉列表中有多个端口选项，则可以找到要使用的正确端口。 打开 Windows **设备管理器**，查看“端口”以确定要使用的端口。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/termite-settings.png" alt-text="Termite 应用中串行端口设置的屏幕截图":::

1. 选择“确定”。
1. 按设备上的“复位”按钮。 该按钮在设备上进行了标记，位于 Micro USB 连接器附近。
1. 在 **Termite** 应用中，检查以下检查点值，以确认设备已初始化并连接到 Azure IoT。

    ```output
    Starting Azure thread

    Initializing WiFi
        MAC address: C8:93:46:8A:4C:43
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 192.168.0.18
        Mask: 255.255.255.0
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 157.245.166.169
        SNTP time update: Jun 8, 2021 18:16:50.807 UTC
    SUCCESS: SNTP initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***.azure-devices.net
        Device id: mydevice
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoT Hub
    SUCCESS: Azure IoT Hub client initialized
    ```

在以下步骤中，保持 Termite 处于打开状态以监视设备输出。

## <a name="view-device-properties"></a>查看设备属性

可使用 Azure IoT Explorer 查看和管理设备的属性。 在本部分及后面的部分中，将使用 IoT Explorer 中显示的即插即用功能来管理 MXCHIP DevKit 并与之进行交互。 这些功能依赖于为公共模型存储库中的 MXCHIP DevKit 发布的设备模型。 在本快速入门的前面部分，已将 IoT Explorer 配置为在此存储库中搜索设备模型。 在很多情况下，无需使用即插即用即可执行相同的操作，只需从 IoT Explorer 中设备窗格的左侧菜单中选择此操作；但是，使用即插即用功能通常可以提供更好的体验。 这是因为 IoT Explorer 可以读取即插即用设备指定的设备模型，并显示特定于该设备的信息。  

若要在 IoT Explorer 中访问设备的 IoT 即插即用组件：

1. 从 IoT Explorer 的主页视图中，选择“IoT 中心”，然后选择“查看此中心的设备”。
1. 选择你的设备。
1. 选择“IoT 即插即用组件”。
1. 选择“默认组件”。 IoT Explorer 显示设备上实施的 IoT 即插即用组件。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-default-component-view.png" alt-text="IoT Explorer 中 MXCHIP DevKit 默认组件的屏幕截图":::

1. 在“接口”选项卡上，查看设备模型“说明”中的 JSON 内容。 JSON 包含设备模型中每个 IoT 即插即用组件的配置详细信息。

    IoT Explorer 中的每个选项卡都与设备模型中的一个 IoT 即插即用组件相对应。

    | Tab | 类型 | 名称 | 说明 |
    |---|---|---|---|
    | **Interface** | 接口 | `MXCHIP Getting Started Guide` | MXCHIP DevKit 的示例模型 |
    | **属性（只读）** | 属性 | -- | 模型当前没有任何只读属性 |
    | **属性（可写）** | 属性 | `telemetryInterval` | 设备发送遥测数据的间隔 |
    | 命令 | 命令 | `setLedState` | 打开或关闭 LED |
    | **遥测** | 遥测 | `temperature` | 温度（摄氏度） |

若要使用 Azure IoT Explorer 查看设备属性：

1. 选择“属性（只读）”选项卡。目前，设备模型未公开任何只读属性。
1. 选择“属性（可写）”选项卡。将显示发送遥测数据的间隔。
1. 将 `telemetryInterval` 更改为 *5*，然后选择“更新所需值”。 设备现在用此间隔发送遥测数据。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-set-telemetry-interval.png" alt-text="在 IoT Explorer 中设置 MXCHIP DevKit 上遥测间隔的屏幕截图":::

1. IoT Explorer 使用通知进行响应。 还可以在 Termite 中观察更新情况。
1. 将遥测间隔设置回 10。
 
若要使用 Azure CLI 查看设备属性：

1. 运行 [az iot hub device-identity show](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_show) 命令。

    ```azurecli
    az iot hub device-identity show --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. 在控制台输出中检查设备的属性。

## <a name="view-telemetry"></a>查看遥测数据

通过 Azure IoT Explorer，可以查看从设备到云的遥测数据流。 （可选）可以使用 Azure CLI 执行同一任务。

若要在 Azure IoT Explorer 中查看遥测数据：

1. 从 IoT Explorer 中设备的“IoT 即插即用组件”（默认组件）窗格中，选择“遥测”选项卡。确认“使用内置事件中心”设置为“是”。
1. 选择“开始”。
1. 查看设备向云发送消息的遥测数据。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-device-telemetry.png" alt-text="IoT Explorer 中设备遥测数据的屏幕截图":::

    > [!NOTE]
    > 还可以使用 Termite 应用从设备监视遥测数据。

1. 选中“显示建模事件”复选框，查看设备模型指定的数据格式的事件。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-show-modeled-events.png" alt-text="IoT Explorer 中已建模的遥测事件的屏幕截图":::

1. 选择“停止”以结束接收事件。

若要使用 Azure CLI 查看设备遥测数据：

1. 运行 [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) 命令。 使用之前在 Azure IoT 中为设备和 IoT 中心创建的名称。

    ```azurecli
    az iot hub monitor-events --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. 在控制台中查看 JSON 输出。

    ```json
    {
        "event": {
            "origin": "mydevice",
            "module": "",
            "interface": "dtmi:azurertos:devkit:gsgmxchip;1",
            "component": "",
            "payload": "{\"humidity\":41.21,\"temperature\":31.37,\"pressure\":1005.18}"
        }
    }
    ```

1. 选择 CTRL+C 结束监视。


## <a name="call-a-direct-method-on-the-device"></a>在设备上调用直接方法

还可以使用 Azure IoT Explorer 来调用已在设备上实现的直接方法。 直接方法有一个名称，可以选择包含 JSON 有效负载、可配置的连接和方法超时。 在本部分中，将调用一个方法用于打开或关闭 LED。 （可选）可以使用 Azure CLI 执行同一任务。

若要在 Azure IoT Explorer 中调用方法：

1. 从 IoT Explorer 中设备的“IoT 即插即用组件”（默认组件）窗格中，选择“命令”选项卡。
1. 对于“setLedState”命令，将“状态”设置为 “true”。
1. 选择“发送命令”。 此时应在 IoT Explorer 中看到一条通知，设备上的黄色用户 LED 灯应打开。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-invoke-method.png" alt-text="在 IoT Explorer 中调用 setLedState 方法的屏幕截图":::

1. 将“状态”设置为“false”，然后选择“发送命令”。 黄色用户 LED 灯应关闭。
1. （可选）可以在 Termite 中查看输出，以监视这些方法的状态。

若要使用 Azure CLI 调用方法：

1. 运行 [az iot hub invoke-device-method](/cli/azure/iot/hub#az_iot_hub_invoke_device_method) 命令，并指定方法名称和有效负载。 对于此方法，将 `method-payload` 设置为 `true` 将打开 LED，设置为 `false` 将关闭它。

    ```azurecli
    az iot hub invoke-device-method --device-id mydevice --method-name setLedState --method-payload true --hub-name {YourIoTHubName}
    ```

    CLI 控制台在设备上显示方法调用的状态，其中 `204` 指示成功。

    ```json
    {
        "payload": {},
        "status": 200
    }    
    ```

1. 检查设备以确认 LED 状态。

1. 查看 Termite 终端以确认输出消息：

    ```output
    Receive direct method: setLedState
        Payload: true
    LED is turned ON
    Device twin property sent: {"ledState":true}
    ```

## <a name="troubleshoot-and-debug"></a>故障排除和调试

如果在生成设备代码、刷写设备或进行连接时遇到问题，请参阅[故障排除](troubleshoot-embedded-device-quickstarts.md)。

要调试应用程序，请参阅[使用 Visual Studio Code 进行调试](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md)。

## <a name="clean-up-resources"></a>清理资源

如果不再需要本快速入门中创建的 Azure 资源，可以使用 Azure CLI 删除资源组及其所有资源。

> [!IMPORTANT] 
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。

若要按名称删除资源组，请执行以下操作：

1. 运行 [az group delete](/cli/azure/group#az-group-delete) 命令。 这会删除你创建的资源组、IoT 中心和设备注册。

    ```azurecli-interactive
    az group delete --name MyResourceGroup
    ```

1. 运行 [az group list](/cli/azure/group#az-group-list) 命令，确认资源组是否已删除。  

    ```azurecli-interactive
    az group list
    ```

## <a name="next-steps"></a>后续步骤

在本快速入门中，生成了一个包含 Azure RTOS 示例代码的自定义映像，然后将该映像刷写到了 MXCHIP DevKit 设备。 还使用 Azure CLI 和/或 IoT Explorer 创建了 Azure 资源，将 MXCHIP DevKit 安全地连接到 Azure，查看遥测数据以及发送消息。

接下来请浏览以下文章，以详细了解如何使用 IoT 设备 SDK 将设备连接到 Azure IoT。 

> [!div class="nextstepaction"]
> [将 MXCHIP AZ3166 Devkit 连接到 IoT Central](quickstart-devkit-mxchip-az3166.md)
> [!div class="nextstepaction"]
> [将模拟设备连接到 IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [将模拟设备连接到 IoT 中心](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Azure RTOS 为 OEM 提供了一些组件，通过这些组件可以保护通信以及使用底层 MCU/MPU 硬件保护机制创建代码和数据隔离。 但是，每个 OEM 最终负责确保其设备满足不断变化的安全要求。
