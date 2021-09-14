---
title: Azure Defender for IoT 试用版设置
description: 在本教程中，你将了解如何使用虚拟传感器在虚拟机上通过 Azure Defender for IoT 试用版订阅加入 Azure Defender for IoT。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/06/2021
ms.custom: template-tutorial
ms.openlocfilehash: 66d95f5700cb3445aa5e2facabe12ea7c70ef92e
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544559"
---
# <a name="tutorial-azure-defender-for-iot-trial-setup"></a>教程：Azure Defender for IoT 试用版设置

本教程将帮助你了解如何使用虚拟传感器在虚拟机上通过 Azure Defender for IoT 试用版订阅加入 Azure Defender for IoT。 本教程将为想要在注册之前测试 Azure Defender for IoT 并将其整合到环境中的用户介绍最佳设置方式。

通过使用虚拟环境以及创建传感器所需的软件，Defender for IoT 让你可以：

- 使用被动、无代理的网络监视来获取所有 IoT 和 OT 设备的完整清单、其详细信息以及它们的通信方式，而不影响 IoT 和 OT 网络。

- 识别 IoT 和 OT 环境中的风险和漏洞。 例如，确定未修补的设备、开放端口、未经授权的应用程序和未经授权的连接。 还可以确定对设备配置、PLC 代码和固件的更改。

- 通过专门的 IoT 和 OT 感知威胁情报和行为分析来检测异常或未经授权的活动。 甚至可以检测到静态 IOC 错过的高级威胁，例如零时差恶意软件、无文件恶意软件和离地攻击策略。

- 集成到 Azure Sentinel 中以鸟瞰整个组织。 通过集成到现有工作流（包括 Splunk、IBM QRadar 和 ServiceNow 等第三方工具）实现统一的 IoT 和 OT 安全治理。

在本教程中，你将了解如何：

> [!div class="checklist"]
> * 加入 Azure Defender for IoT
> * 下载虚拟传感器的 ISO
> * 为传感器创建虚拟机
> * 安装虚拟传感器软件
> * 配置 SPAN 端口
> * 加入并激活虚拟传感器

## <a name="prerequisites"></a>先决条件

- 权限：Azure“订阅所有者”级别或“订阅参与者”级别 。

- 至少有一个要监视的设备连接到交换机上的 SPAN 端口。

- 已安装 VMware（ESXi 5.5 或更高版本）或 Hyper-V 虚拟机监控程序（Windows 10 专业版或企业版）且它们可正常运行。

- 一个 Azure 帐户。 如果还没有 Azure 帐户，可以[立即创建 Azure 免费帐户](https://azure.microsoft.com/free/)。

## <a name="onboard-with-azure-defender-for-iot"></a>加入 Azure Defender for IoT

若要使用 Azure Defender for IoT，你必须先订阅 Microsoft Azure。 如果还没有订阅，可以[立即创建 Azure 免费帐户](https://azure.microsoft.com/free/)。

若要评估 Defender for IoT，可以使用试用版订阅。 试用版的有效期为 30 天，且支持多达 1000 台已提交设备。 你可以使用试用版在网络中部署虚拟传感器。 使用传感器可以监视流量、分析数据、生成警报、了解网络风险和漏洞等。 使用试用版，你还可以部署虚拟的本地管理控制台，以查看传感器生成的汇总信息。

**加入 Azure Defender for IoT 订阅**：

1. 导航到 [Azure 门户](https://ms.portal.azure.com/)。

1. 搜索并选择“Azure Defender for IoT”。

1. 选择“加入订阅”。

    :::image type="content" source="media/tutorial-onboarding/onboard-subscription.png" alt-text="从“入门”页选择“加入订阅”按钮的屏幕截图。":::

1. 在“定价”页中，选择“开始使用试用版”。

   :::image type="content" source="media/tutorial-onboarding/start-with-trial.png" alt-text="用于打开试用版窗口的“开始使用试用版”按钮的屏幕截图。":::

1. 从“加入试用版订阅”窗格中选择一个订阅，然后选择“评估”。

1. 确认评估。

## <a name="download-the-iso-for-the-virtual-sensor"></a>下载虚拟传感器的 ISO

虚拟设备具有传感器和管理控制台所需的最低规格。 下表显示了根据你的环境，传感器所需的规格。

### <a name="virtual-sensor"></a>虚拟传感器

| 类型 | 企业 | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| 内存 | 32 GB | 32 GB | 8 GB |
| 存储 | 5.6 TB | 1.8 TB | 500 GB |

**下载虚拟传感器的 ISO 文件**：

1. 导航到 [Azure 门户](https://ms.portal.azure.com/)。

1. 搜索并选择“Azure Defender for IoT”。

1. 在“开始”页上选择“传感器”选项卡。

1. 选择“下载”  。

    :::image type="content" source="media/tutorial-onboarding/sensor-download.png" alt-text="“传感器”选项卡的屏幕截图，选择了“下载”以下载虚拟传感器的 ISO 文件。":::

## <a name="create-a-virtual-machine-for-the-sensor"></a>为传感器创建虚拟机

虚拟传感器支持 VMware 和 Hyper-V 部署选项。 在开始安装之前，请确保你具有以下项：

- 已安装并可正常运行的 VMware（ESXi 5.5 或更高版本）或 Hyper-V 虚拟机监控程序（Windows 10 专业版或企业版）。

- 虚拟机可用的硬件资源。

- Azure Defender for IoT 传感器的 ISO 安装文件。

- 请确保虚拟机监控程序正在运行。

### <a name="create-the-virtual-machine-for-the-sensor-with-esxi"></a>使用 ESXi 为传感器创建虚拟机

**为传感器创建虚拟机 (ESXi)** ：

1. 登录到 ESXi，选择相关的 **数据存储**，然后选择“数据存储浏览器”。

1. **上传** 映像，然后选择“关闭”。

1. 转到“虚拟机”，然后选择“创建/注册 VM”。

1. 选择“新建虚拟机”，然后选择“下一步”。 

1. 添加传感器名称并选择以下项：

   - 兼容性： **&lt;最新 ESXi 版本&gt;**

   - 来宾 OS 系列：**Linux**

   - 来宾 OS 版本：**Ubuntu Linux (64 位)**

1. 选择“**下一步**”。

1. 选择相关的数据存储，然后选择“下一步”。

1. 根据所需的[体系结构](#download-the-iso-for-the-virtual-sensor)更改虚拟硬件参数。

1. 对于“CD/DVD 驱动器 1”，请选择“数据存储 ISO 文件”，然后选择之前上传的 ISO 文件。

1. 选择“下一步” > “完成”。

1. 打开 VM，然后打开控制台。

### <a name="create-a-virtual-machine-for-the-sensor-with-hyper-v"></a>使用 Hyper-V 为传感器创建虚拟机

此过程介绍如何使用 Hyper-V 创建虚拟机。

**使用 Hyper-V 创建虚拟机**：

1. 在 Hyper-V 管理器中创建虚拟磁盘。

1. 选择“格式 = VHDX”。

1. 选择“类型 = 动态扩展”。

1. 输入 VHD 的名称和位置。

1. 输入所需的大小（具体取决于[体系结构](#download-the-iso-for-the-virtual-sensor)）。

1. 查看摘要并选择“完成”。

1. 在“操作”菜单上，创建新的虚拟机。

1. 输入虚拟机的名称。

1. 选择“指定代系” > “第 1 代”。 

1. 指定内存分配（具体取决于[体系结构](#download-the-iso-for-the-virtual-sensor)），然后选中动态内存的对应复选框。

1. 根据服务器网络拓扑配置网络适配器。

1. 将之前创建的 VHDX 连接到虚拟机。

1. 查看摘要并选择“完成”。

1. 右键单击新虚拟机，然后选择“设置”。

1. 选择“添加硬件”，添加新的网络适配器。

1. 选择将连接到传感器管理网络的虚拟交换机。

1. 分配 CPU 资源（具体取决于[体系结构](#download-the-iso-for-the-virtual-sensor)）。

1. 将管理控制台的 ISO 映像连接到虚拟 DVD 驱动器。

1. 启动虚拟机。

1. 在“操作”菜单上选择“连接”，继续进行软件安装。

## <a name="install-the-virtual-sensor-software-with-esxi-or-hyper-v"></a>使用 ESXi 或 Hyper-V 安装虚拟传感器软件

可以使用 ESXi 或 Hyper-V 为虚拟传感器安装软件。

**在虚拟传感器上安装软件**：

1. 打开虚拟机控制台。

1. 将会从 ISO 映像启动 VM，并会显示语言选择屏幕。 选择“英语”。

1. 选择所需[体系结构](#download-the-iso-for-the-virtual-sensor)。

1. 定义设备配置文件和网络属性：

    | 参数 | 配置 |
    | ----------| ------------- |
    | **硬件配置文件** | 基于所需的[体系结构](#download-the-iso-for-the-virtual-sensor)。 |
    | **管理界面** | **ens192** |
    | **网络参数（由客户提供）** | **管理网络 IP 地址：** <br/>**子网掩码：** <br>**设备主机名：** <br/>**DNS：** <br/>**默认网关：** <br/>**输入接口：**|
    | **桥接接口：** | 无需配置桥接接口。 此选项仅用于特殊用例。 |

1. 输入 **Y** 接受设置。

1. 登录凭据会自动生成并显示。 将用户名和密码复制到安全位置，因为登录和管理设备时需要用户名和密码。 用户名和密码将不会再次显示。

    - **Support**：进行用户管理的管理用户。

    - **CyberX**：root 的等效项，用于访问设备。

1. 设备重启。

1. 通过以前配置的 IP 地址访问传感器：`https://ip_address`。

### <a name="post-installation-validation"></a>安装后验证

若要验证物理设备的安装，需要执行多个测试。

支持部门和 CyberX 用户都可以进行验证 。

**访问安装后验证工具**：

1. 登录传感器。

1. 选择左侧窗格中的“系统设置”。

1. 选择 :::image type="icon" source="media/tutorial-onboarding/system-statistics-icon.png" border="false"::: 按钮。

    :::image type="content" source="media/tutorial-onboarding/system-health-check-screen.png" alt-text="系统运行状况检查的屏幕截图。" lightbox="media/tutorial-onboarding/system-health-check-screen-expanded.png":::

对于安装后验证，必须进行测试以确保系统正在运行、拥有的版本正确，并验证安装过程中配置的所有输入接口是否正在运行。

**验证系统是否正在运行**：

1. 选择“设备”，并确保每个行项都显示 `Running`，且底部行显示 `System is up`。

1. 选择“版本”，并确保显示正确的版本。

1. 选择“ifconfig”以显示设备的物理接口的参数，并确保其正确无误。

## <a name="configure-a-span-port"></a>配置 SPAN 端口

vSwitch 没有镜像功能，但你可以使用一种解决方法来实现 SPAN 端口。 可以通过 ESXi 或 Hyper-V 实现此变通方法。

### <a name="configure-a-span-port-with-esxi"></a>使用 ESXi 配置 SPAN 端口

**使用 ESXi 配置 SPAN 端口**：

1. 打开 vSwitch 属性。

1. 选择“添加”。

1. 选择“虚拟机” > “下一步”。 

1. 插入网络标签“SPAN 网络”，选择“VLAN ID” > “所有”，然后选择“下一步”。

1. 选择“完成”  。

1. 选择“SPAN 网络”> *“编辑”。

1. 选择“安全性”，验证是否已将“混杂模式”策略设置为“接受”模式。 

1. 选择“确定”，然后选择“关闭”以关闭 vSwitch 属性。

1. 打开 **XSense VM** 属性。

1. 对于“网络适配器 2”，请选择“SPAN”网络。

1. 选择“确定”。

1. 连接到传感器，验证镜像功能是否正常。

### <a name="configure-a-span-port-with-hyper-v"></a>使用 Hyper-V 配置 SPAN 端口

开始之前，你需要：

- 确保没有正在运行的 ClearPass VA 实例。

- 确保在数据端口而不是管理端口启用 SPAN。

- 确保数据端口 SPAN 的配置未配置 IP 地址。

**使用 Hyper-V 配置 SPAN 端口**：

1. 打开虚拟交换机管理器。

1. 在“虚拟交换机”列表中，选择“新建虚拟网络交换机” > “外部”作为专用跨区网络适配器类型 。

    :::image type="content" source="media/tutorial-onboarding/new-virtual-network.png" alt-text="创建虚拟交换机之前选择新的虚拟网络和外部的屏幕截图。":::

1. 选择“创建虚拟交换机”。

1. 在连接类型下，选择“外部网络”。

1. 确保选中“允许管理操作系统共享此网络适配器”复选框。

   :::image type="content" source="media/tutorial-onboarding/external-network.png" alt-text="选择外部网络，并允许管理操作系统共享网络适配器的屏幕截图。":::

1. 选择“确定”。

#### <a name="attach-a-clearpass-span-virtual-interface-to-the-virtual-switch"></a>将 ClearPass SPAN 虚拟接口附加到虚拟交换机

可通过 Windows PowerShell 或 Hyper-V 管理器将 ClearPass SPAN 虚拟接口附加到虚拟交换机。

**通过 PowerShell 将 ClearPass SPAN 虚拟接口附加到虚拟交换机**：

1. 选择新添加的 SPAN 虚拟交换机，并使用以下命令添加新的网络适配器：

    ```bash
    ADD-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 -Name Monitor -SwitchName vSwitch_Span
    ```

1. 使用以下命令，为所选接口启用端口镜像作为跨度目标：

    ```bash
    Get-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 | ? Name -eq Monitor | Set-VMNetworkAdapter -PortMirroring Destination
    ```

    | 参数 | 说明 |
    |--|--|
    | VK-C1000V-LongRunning-650 | CPPM VA 名称 |
    |vSwitch_Span |新添加的 SPAN 虚拟交换机名称 |
    |监视 |新添加的适配器名称 |

1. 选择“确定”。

这些命令将新添加的适配器硬件的名称设置为 `Monitor`。 如果使用 Hyper-V 管理器，新添加的适配器硬件的名称将设置为 `Network Adapter`。

**通过 Hyper-V 管理器将 ClearPass SPAN 虚拟接口附加到虚拟交换机**：

1. 在“硬件”列表下，选择“网络适配器”。

1. 在“虚拟交换机”字段中，选择“vSwitch_Span”。

    :::image type="content" source="media/tutorial-onboarding/vswitch-span.png" alt-text="在虚拟交换机屏幕上选择以下选项的屏幕截图。":::

1. 在“硬件”列表中的“网络适配器”下拉列表下，选择“高级功能”。

1. 在“端口镜像”部分，选择“目标”作为新虚拟接口的镜像模式。

    :::image type="content" source="media/tutorial-onboarding/destination.png" alt-text="配置镜像模式所需的选项的屏幕截图。":::

1. 选择“确定”。

#### <a name="enable-microsoft-ndis-capture-extensions-for-the-virtual-switch"></a>为虚拟交换机启用 Microsoft NDIS 捕获扩展

需要为新的虚拟交换机启用 Microsoft NDIS 捕获扩展。

**为新添加的虚拟交换机启用 Microsoft NDIS 捕获扩展**：

1. 在 Hyper-V 主机上打开虚拟交换机管理器。

1. 在“虚拟交换机”列表中，展开虚拟交换机名称 `vSwitch_Span` 并选择“扩展”。

1. 在“交换机扩展”字段中，选择“Microsoft NDIS 捕获”。

    :::image type="content" source="media/tutorial-onboarding/microsoft-ndis.png" alt-text="通过从交换机扩展菜单中选择 Microsoft NDIS 来启用它的屏幕截图。":::

1. 选择“确定”。

#### <a name="set-the-mirroring-mode-on-the-external-port"></a>在外部端口上设置镜像模式

需要在新虚拟交换机的外部端口上将镜像模式设置为源。

需要配置 Hyper-V 虚拟交换机 (vSwitch_Span)，以将任何传入外部源端口的流量转发到配置为目标的虚拟网络适配器。

使用以下 PowerShell 命令将外部虚拟交换机端口设置为源镜像模式：

```bash
$ExtPortFeature=Get-VMSystemSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings"
$ExtPortFeature.SettingData.MonitorMode=2
Add-VMSwitchExtensionPortFeature -ExternalPort -SwitchName vSwitch_Span -VMSwitchExtensionFeature $ExtPortFeature
```

| 参数 | 说明 |
|--|--|
| vSwitch_Span | 新添加的 SPAN 虚拟交换机名称。 |
| MonitorMode=2 | 源 |
| MonitorMode=1 | 目标 |
| MonitorMode=0 | 无 |

使用以下 PowerShell 命令验证监视模式状态：

```bash
Get-VMSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings" -SwitchName vSwitch_Span -ExternalPort | select -ExpandProperty SettingData
```

| 参数 | 说明 |
|--|--|
| vSwitch_Span | 新添加的 SPAN 虚拟交换机名称 |
## <a name="onboard-and-activate-the-virtual-sensor"></a>加入并激活虚拟传感器

开始使用 Defender for IoT 传感器之前，需要将创建的虚拟传感器加入 Azure 订阅，并下载虚拟传感器的激活文件以激活传感器。

### <a name="onboard-the-virtual-sensor"></a>加入虚拟传感器

**加入虚拟传感器**：

1. 转到 [Defender for IoT 门户](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)中的“欢迎”页。

1. 选择“加入传感器”。

   :::image type="content" source="media/tutorial-onboarding/onboard-a-sensor.png" alt-text="选择加入传感器以启动传感器加入过程的屏幕截图。":::

1. 输入传感器的名称。

   建议在名称中包含传感器的 IP 地址，或使用易于识别的名称。 以这种方式命名传感器有助于更轻松地进行跟踪。

1. 从下拉列表中选择订阅。

    :::image type="content" source="media/tutorial-onboarding/name-subscription.png" alt-text="输入有意义的名称并将传感器连接到订阅的屏幕截图。":::

1. 使用“连接到云”开关选择传感器连接模式。 如果打开此开关，则传感器将连接到云。 如果关闭此开关，则传感器将在本地进行管理。

   - 连接到云的传感器：传感器检测到的信息显示在传感器控制台中。 警报信息通过 IoT 中心进行传递，并且可以与其他 Azure 服务（例如 Azure Sentinel）共享。 此外，威胁智能包可以从 Azure Defender for IoT 门户推送到传感器。 相反，当传感器未连接到云时，你必须下载威胁情报包，然后将其上传到企业传感器。 若要允许 Defender for IoT 将包推送到传感器，请启用“自动威胁情报更新”开关。 有关详细信息，请参阅[威胁情报研究和包](how-to-work-with-threat-intelligence-packages.md)。

      对于连接到云的传感器，在加入过程中定义的名称就是在传感器控制台中显示的名称。 无法直接从控制台更改此名称。 对于在本地管理的传感器，在加入期间应用的名称会存储在 Azure 中，但可以在传感器控制台中进行更新。

   - 本地管理的传感器：传感器检测到的信息会在传感器控制台中显示。 如果使用气隙网络，并且想要统一查看由多个本地托管的传感器检测到的所有信息，请使用本地管理控制台。

1. 在 IoT 中心内选择要将传感器关联到的站点。 IoT 中心将作为此传感器与 Azure Defender for IoT 之间的网关。 定义显示名称和区域。 还可以添加说明性标记。 显示名称、区域和标记是[查看加入的传感器](how-to-manage-sensors-on-the-cloud.md#view-onboarded-sensors)上的说明性条目。

1. 选择“注册”。

### <a name="download-the-sensor-activation-file"></a>下载传感器激活文件

完成传感器注册后，便可以下载传感器的激活文件。 传感器激活文件包含有关传感器管理模式的说明。 下载的激活文件对于你部署的每个传感器都是唯一的。 首次登录传感器控制台的用户需要将激活文件上传到传感器。

**若要下载激活文件，请执行以下操作：**

1. 在“加入传感器”页上，选择“注册”

1. 选择“下载激活文件”。

1. 使文件可供首次登录到传感器控制台的用户访问。

### <a name="sign-in-and-activate-the-sensor"></a>登录并激活传感器

**若要登录并激活，请执行以下操作：**

1. 使用安装过程中定义的 IP 从浏览器转到传感器控制台。

    :::image type="content" source="media/tutorial-onboarding/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Azure Defender for IoT 传感器的屏幕截图。":::

1. 输入在传感器安装过程中定义的凭据。

1. 登录后，将打开“激活”对话框。 选择“上传”，并转到在传感器启动过程中下载的激活文件。

   :::image type="content" source="media/tutorial-onboarding/activation-upload-screen-with-upload-button.png" alt-text="选择上载并转到激活文件的屏幕截图。":::

1. 接受条款和条件。

1. 选择“激活”  。 此时将打开“SSL/TLS 证书”对话框。

1. 定义证书名称。

1. 上传 CRT 和密钥文件。

1. 输入密码并上传 PEM 文件（如有必要）。

1. 选择“下一步”。 此时将打开“验证”屏幕。 默认情况下，将启用管理控制台与已连接的传感器之间的验证。

1. 关闭“启用系统范围的验证”切换以禁用验证。 建议启用验证。

1. 选择“保存”。  

上传 CA 签名的证书后，你可能需要刷新屏幕。

## <a name="next-steps"></a>后续步骤

了解如何设置[其他设备](how-to-install-software.md#about-defender-for-iot-appliances)。
阅读有关[无代理体系结构](architecture.md)的信息。
