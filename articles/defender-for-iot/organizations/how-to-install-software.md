---
title: Defender for IoT 安装
description: 了解如何安装适用于 Azure Defender for IoT 的传感器和本地管理控制台。
ms.date: 06/21/2021
ms.topic: how-to
ms.openlocfilehash: 5cd5bf569f1e27d66a5fb14d4f580d6842313419
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015394"
---
# <a name="defender-for-iot-installation"></a>Defender for IoT 安装

本文介绍如何安装 Azure Defender for IoT 的以下元素：

- **传感器**：Defender for IoT 传感器使用被动（无代理）监视收集 ICS 网络流量。 这些传感器属被动非侵入式传感器，对 OT 和 IoT 网络和设备不会有任何影响。 传感器在连接到 SPAN 端口或网络 TAP 后，会立即开始监视你的网络。 检测结果显示在传感器控制台中。 在那里，你可以在网络映射、设备清单和各种报表中查看、调查和分析它们。 例如，风险评估报表、数据挖掘查询、攻击途径。 有关传感器功能的详细信息，请阅读 [Defender for IoT 传感器用户指南（直接下载）](./getting-started.md)。

- **本地管理控制台**：可以通过本地管理控制台进行设备管理、风险管理和漏洞管理。 还可以使用它在企业中进行威胁监视和事件响应。 它提供了所有网络设备、关键 IoT 以及在部署传感器的设施中检测到的 OT 风险指标和警报的统一视图。 可以使用本地管理控制台查看和管理气隙网络中的传感器。

本文涵盖以下安装信息：

  - **硬件：** Dell 和 HPE 物理设备详细信息。

  - **软件：** 传感器和本地管理控制台软件安装。

  - **虚拟设备：** 虚拟机详细信息和软件安装。

安装完成后，将传感器连接到网络。

## <a name="about-defender-for-iot-appliances"></a>关于 Defender for IoT 设备 

以下部分介绍 Defender for IoT 传感器设备以及 Defender for IoT 本地管理控制台的设备。

### <a name="physical-appliances"></a>物理设备

Defender for IoT 设备传感器在连接到 SPAN 端口或网络 TAP 后，会立即开始通过被动（无代理）监视来收集 ICS 网络流量。 此过程不会对 OT 网络和设备造成任何影响，因为它不会放置在数据路径中，也不会主动扫描 OT 设备。

以下机架装载设备可供使用：

| **部署类型** | **企业** | **企业** | **SMB** |SMB 加固型 |
|--|--|--|--|--|
| **Model** | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 | HPE EL300 |
| **监视端口** | 最多 15 个 RJ45 或 8 个 OPT | 最多 8 个 RJ45 或 6 个 OPT | 最多 4 个 RJ45 | 最多 5 个 RJ45 |
| **最大带宽\*** | 3 Gb/秒 | 1 Gb/秒 | 200 Mb/秒 | 100 Mb/秒 |
| **受保护设备的最大数目** | 30,000 | 15,000 | 1,000 | 800 |

*最大带宽容量可能因协议分配而异。

### <a name="virtual-appliances"></a>虚拟设备

以下虚拟设备可供使用：

| **部署类型** | **企业** | **企业** | **SMB** |
|--|--|--|--|
| **说明** | 适用于企业部署的虚拟设备 | 适用于企业部署的虚拟设备 | 适用于 SMB 部署的虚拟设备 |
| **最大带宽\*** | 2.5 Gb/秒 | 800 Mb/秒 | 160 Mb/秒 |
| **受保护设备的最大数目** | 30,000 | 10,000 | 800 |
| **部署类型** | 企业 | Enterprise | SMB |

*最大带宽容量可能因协议分配而异。

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>本地管理控制台的硬件规格

 | 项 | 说明 |
 |----|--|
 **说明** | 在多层体系结构中，本地管理控制台可以提供对地理上分散的站点的可见性和控制能力。 它与 SOC 安全堆栈集成，后者包括 SIEM、票证系统、下一代防火墙、安全的远程访问平台，以及 Defender for IoT ICS 恶意软件沙盒。 |
 **部署类型** | Enterprise |
 **设备类型**  | Dell R340、VM |
 **托管传感器数** | 无限制 |

## <a name="prepare-for-the-installation"></a>准备安装

### <a name="access-the-iso-installation-image"></a>访问 ISO 安装映像

可从 Defender for IoT 门户访问安装映像。

若要访问该文件，请执行以下步骤：

1. 登录到 Defender for IoT 帐户。

1. 转到“网络传感器”或“本地管理控制台”页面，选择要下载的版本。

### <a name="install-from-dvd"></a>从 DVD 进行安装

安装之前，请确保：

- 你有一个带 USB 连接器的便携式 DVD 驱动器。

- 你有一个 ISO 安装程序映像。

若要进行安装：

1. 将映像刻录到 DVD，或准备一个基于密钥的磁盘。 将便携式 DVD 驱动器连接到计算机，右键单击 ISO 映像，然后选择“刻录到磁盘”。

1. 连接 DVD 或基于密钥的磁盘，将设备配置为从 DVD 或基于密钥的磁盘启动。

### <a name="install-from-disk-on-a-key"></a>从基于密钥的磁盘进行安装

安装之前，请确保：

  - 已安装了 Rufus。
  
  - 你有一个基于密钥的磁盘，其 USB 版本为 3.0 或更高版本。 最小大小为 4 GB。

  - 一个 ISO 安装程序映像文件。

在此过程中，将擦除基于密钥的磁盘。

若要准备基于密钥的磁盘，请执行以下操作：

1. 运行 Rufus 并选择“SENSOR ISO”（传感器 ISO）。

1. 将基于密钥的磁盘连接到前面板。

1. 设置要从 USB 启动的服务器的 BIOS。

## <a name="dell-poweredger340xl-installation"></a>Dell PowerEdgeR340XL 安装

在 Dell 设备上安装软件之前，需调整设备的 BIOS 配置：

  - [Dell PowerEdge R340 前面板](#dell-poweredge-r340-front-panel)和 [Dell PowerEdge R340 后面板](#dell-poweredge-r340-back-panel)包含前面板和后面板的说明，以及进行安装所需的信息，如驱动程序和端口。

  - [Dell BIOS 配置](#dell-bios-configuration)介绍了如何连接到 Dell 设备管理界面，以及如何配置 BIOS。

  - [软件安装 (Dell R340)](#software-installation-dell-r340) 介绍了安装 Defender for IoT 传感器软件所需的过程。

### <a name="dell-poweredge-r340xl-requirements"></a>Dell PowerEdge R340XL 要求 

若要安装 Dell PowerEdge R340XL 设备，你需要：

- 适用于 Dell 远程访问控制器 (iDrac) 的企业许可证

- BIOS 配置 XML

- 服务器固件版本：

  - BIOS 版本 2.1.6

  - iDrac 版本 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Dell PowerEdge R340 前面板

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Dell PowerEdge R340 前面板。":::

 1. 左侧控制面板 
 1. 光驱（可选） 
 1. 右侧控制面板 
 1. 信息标记 
 1. 驱动器  

### <a name="dell-poweredge-r340-back-panel"></a>Dell PowerEdge R340 后面板

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340 后面板。":::

1. 串行端口 
1. NIC 端口 (Gb 1) 
1. NIC 端口 (Gb 1) 
1. 半高 PCIe 
1. 全高 PCIe 扩展卡插槽 
1. 电源设备 1 
1. 电源设备 2 
1. 系统标识 
1. 系统状态指示器电缆端口 (CMA) 按钮 
1. USB 3.0 端口 (2) 
1. iDRAC9 专用网络端口 
1. VGA 端口 

### <a name="dell-bios-configuration"></a>Dell BIOS 配置

必须使用 Dell BIOS 配置来调整要与软件配合使用的 Dell 设备。

BIOS 配置是通过预定义的配置执行的。 可以从[帮助中心](https://cyberx-labs.zendesk.com/hc/)访问该文件。

请将配置文件导入到 Dell 设备。 在使用配置文件之前，需要在 Dell 设备与管理计算机之间建立通信。

Dell 设备由一个与生命周期控制器 (LC) 集成的 iDRAC 管理。 LC 嵌入到每个 Dell PowerEdge 服务器中，提供有助于部署、更新、监视和维护 Dell PowerEdge 设备的功能。

若要在 Dell 设备与管理计算机之间建立通信，需要在同一子网上定义 iDRAC IP 地址和管理计算机的 IP 地址。

建立连接后，即可配置 BIOS。

若要配置 Dell BIOS，请执行以下操作：

1. [配置 iDRAC IP 地址](#configure-idrac-ip-address)

1. [导入 BIOS 配置文件](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>配置 iDRAC IP 地址

1. 接通传感器的电源。

1. 如果已安装 OS，请选择 F2 键进入 BIOS 配置。

1. 选择“IDRAC 设置”。

1. 选择“网络”。

   > [!NOTE]
   > 在安装过程中，必须配置以下步骤所述的默认 iDRAC IP 地址和密码。 安装后，请更改这些定义。

1. 将静态 IPv4 地址更改为 **10.100.100.250**。

1. 将静态子网掩码更改为 **255.255.255.0**。

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="屏幕截图，显示静态子网掩码。":::

1. 选择“返回” > “完成”。 

#### <a name="import-the-bios-configuration-file"></a>导入 BIOS 配置文件

本部分介绍如何使用配置文件配置 BIOS。

1. 将具有静态预配置 IP 地址 **10.100.100.200** 的电脑连接到 **iDRAC** 端口。

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="预配置的 IP 地址端口的屏幕截图。":::

1. 打开浏览器并输入 **10.100.100.250** 以连接到 iDRAC Web 界面。

1. 使用 Dell 默认管理员特权登录：

   - 用户名：**root**

   - 密码：**calvin**

1. 设备的凭据如下：

   - 用户名：**XXX**

   - 密码：**XXX**

     导入服务器配置文件的操作已启动。

     > [!NOTE]
     > 导入文件之前，请确保：
     > - 你是当前连接到 iDRAC 的唯一用户。
     > - 系统不在 BIOS 菜单中。

1. 转到“配置” > “服务器配置文件”。 设置以下参数：

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="屏幕截图，显示服务器配置文件的配置。":::

   | 参数 | 配置 |
   |--|--|
   | 位置类型 | 选择“本地”。 |
   | 文件路径 | 选择“选择文件”，添加 XML 配置文件。 |
   | 导入组件 | 选择“BIOS、NIC、RAID”。 |
   | 最长等待时间 | 选择“20 分钟”。 |

1. 选择“导入”  。

1. 若要监视此过程，请转到“维护” > “作业队列”。

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="屏幕截图，显示“作业队列”。":::

#### <a name="manually-configuring-bios"></a>手动配置 BIOS 

如果出现以下情况，你需要手动配置设备 BIOS：

- 你未从 Arrow 购买设备。

- 你有一个设备，但没有对 XML 配置文件的访问权限。

访问 BIOS 后，请转到“设备设置”。

若要手动进行配置，请执行以下操作：

1. 使用键盘和屏幕直接访问设备 BIOS，或者使用 iDRAC。

   - 如果设备不是 Defender for IoT 设备，请打开浏览器并访问前面配置的 IP 地址。 使用 Dell 默认管理员特权登录。 使用 **root** 作为用户名，使用 **calvin** 作为密码。

   - 如果设备是 Defender for IoT 设备，请分别使用 **XXX** 和 **XXX** 作为用户名和密码登录。

1. 访问 BIOS 后，请转到“设备设置”。

1. 通过选择“集成 RAID 控制器1: Dell PERC\<PERC H330 Adapter\> 配置实用工具”来选择 RAID 控制的配置。

1. 选择“配置管理”。

1. 选择“创建虚拟磁盘”。

1. 在“选择 RAID 级别”字段中，选择“RAID5”。 在“虚拟磁盘名称”字段中输入 **ROOT**，然后选择“物理磁盘”。

1. 选择“全部选中”，然后选择“应用更改”

1. 选择“确定”。

1. 向下滚动并选择“创建虚拟磁盘”。

1. 选中“确认”复选框，然后选择“是” 。

1. 选择“确定”。

1. 返回到主屏幕并选择“系统 BIOS”。

1. 选择“启动设置”。

1. 对于“启动模式”选项，请选择“BIOS”。

1. 选择“返回”，然后选择“完成”，退出 BIOS 设置。

### <a name="software-installation-dell-r340"></a>软件安装 (Dell R340)

安装过程大约需要 20 分钟。 安装后，系统会重启多次。

若要进行安装：

1. 通过以下方式之一验证是否已将版本介质装载到设备：

   - 连接包含该版本的外部 CD 或基于密钥的磁盘。

   - 使用 iDRAC 装载 ISO 映像。 登录到 iDRAC 后，选择虚拟控制台，然后选择“虚拟介质”。

1. 在“映射 CD/DVD”部分，选择“选择文件”。

1. 从打开的对话框中选择此版本的版本 ISO 映像文件。

1. 选择“映射设备”按钮。

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="屏幕截图，显示一个映射的设备。":::

1. 介质已装载。 选择“关闭”。

1. 启动设备。 使用 iDRAC 时，可以通过选择“Consul 控制”按钮来重启服务器。 然后，在“键盘宏”上选择“应用”按钮，这将启动 Ctrl+Alt+Delete 按键顺序。

1. 选择“英语”。

1. 选择“SENSOR-RELEASE-\<version\> Enterprise”。

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="选择传感器版本和企业类型。":::   

1. 定义设备配置文件和网络属性：

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="显示设备配置文件和网络属性的屏幕截图。":::   

   | 参数 | 配置 |
   |--|--|
   | **硬件配置文件** | **enterprise** |
   | **管理界面** | **eno1** |
   | **网络参数（由客户提供）** | - |
   |**管理网络 IP 地址：** | - |
   | **子网掩码：** | - |
   | **设备主机名：** | - |
   | **DNS：** | - |
   | **默认网关 IP 地址：** | - |
   | **输入接口：** |  系统会为你生成输入接口列表。 若要镜像输入接口，请复制带逗号分隔符的列表中显示的所有项。 无需配置桥接接口。 此选项仅用于特殊用例。 |

1. 大约 10 分钟后，将显示两组凭据。 一组用于 **CyberX** 用户，一组用于 **Support** 用户。  

1. 保存设备 ID 和密码。 首次使用平台时，需要使用这些凭据来访问平台。

1. 选择 **Enter** 继续。

## <a name="hpe-proliant-dl20-installation"></a>HPE ProLiant DL20 安装

本部分介绍 HPE ProLiant DL20 安装过程，其中包括以下步骤：

  - 启用远程访问并更新默认的管理员密码。
  - 配置 BIOS 和 RAID 设置。
  - 安装软件。

### <a name="about-the-installation"></a>关于安装

  - 可以安装企业设备和 SMB 设备。 对于这两种设备类型而言，安装过程是相同的，但阵列配置除外。
  - 提供了默认的管理用户。 建议在网络配置过程中更改密码。
  - 在网络配置过程中，需在网络端口 1 上配置 iLO 端口。
  - 安装过程大约需要 20 分钟。 安装后，系统会重启多次。

### <a name="hpe-proliant-dl20-front-panel"></a>HPE ProLiant DL20 前面板

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE ProLiant DL20 前面板。":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE ProLiant DL20 后面板

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="HPE ProLiant DL20 的后面板。":::

### <a name="enable-remote-access-and-update-the-password"></a>启用远程访问并更新密码

请使用以下过程设置网络选项并更新默认密码。

若要启用远程访问并更新密码，请执行以下操作：

1. 将屏幕和键盘连接到 HP 设备，打开设备并按 **F9**。

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="屏幕截图，显示“HPE ProLiant”窗口。":::

1. 转到“系统实用工具” > “系统配置” > “iLO 5 配置实用工具” > “网络选项”。

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="屏幕截图，显示“系统配置”窗口。":::

    1.  从“网络接口适配器”字段中选择“共享网络端口-LOM”。
    
    1.  禁用 DHCP。
    
    1.  输入 IP 地址、子网掩码和网关 IP 地址。

1. 选择“F10: 保存”。

1. 选择“Esc”以返回到“ILO 5 配置实用工具”，然后选择“用户管理”。

1. 选择“编辑/删除用户”。 管理员是唯一的已定义默认用户。 

1. 更改默认密码，然后选择“F10: 保存”。

### <a name="configure-the-hpe-bios"></a>配置 HPE BIOS

以下过程介绍如何为企业设备和 SMB 设备配置 HPE BIOS。

若要配置 HPE BIOS，请执行以下操作：

1. 选择“系统实用工具” > “系统配置” > “BIOS/平台配置(RBSU)”。

1. 在“BIOS/平台配置(RBSU)”窗体中，选择“启动选项”。

1. 将“启动模式”更改为“旧 BIOS 模式”，然后选择“F10: 保存”。

1. 选择“Esc”两次以关闭“系统配置”窗体。

#### <a name="for-the-enterprise-appliance"></a>对于企业设备

1. 选择“嵌入式 RAID 1: HPE 智能阵列 P408i-a SR 第 10 代” > “阵列配置” > “创建阵列”。

1. 在“创建阵列”窗体中，选择所有选项。 对于 **企业** 设备，有三个选项可用。

#### <a name="for-the-smb-appliance"></a>对于 SMB 设备

1. 选择“嵌入式 RAID 1: HPE 智能阵列 P208i-a SR 第 10 代” > “阵列配置” > “创建阵列”。

1. 选择“转到下一窗体”。

1. 在“设置 RAID 级别”窗体中，将级别设置为“RAID 5”可进行企业部署，设置为“RAID 1”可进行 SMB 部署。 

1. 选择“转到下一窗体”。

1. 在“逻辑驱动器标签”窗体中，输入“逻辑驱动器 1”。

1. 选择“提交更改”。

1. 在“提交”窗体中，选择“返回到主菜单”。

1. 选择“F10: 保存”，然后按 **Esc** 两次。

1. 在“系统实用工具”窗口中，选择“一次性启动菜单”。

1. 在“一次性启动菜单”窗体中，选择“旧 BIOS 一次性启动菜单”。

1. 此时会显示“在旧版中启动”和“启动重写”窗口。  选择启动重写选项，例如，启动重写到 CD-ROM、USB、HDD 或 UEFI shell。

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="屏幕截图，显示第一个“启动重写”窗口。":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="屏幕截图，显示第二个“启动重写”窗口。":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>软件安装（HPE ProLiant DL20 设备）

安装过程大约需要 20 分钟。 安装后，系统会重启多次。

若要安装软件，请执行以下操作：

1. 将屏幕和键盘连接到设备，然后连接到 CLI。

1. 连接外部 CD 或基于密钥的磁盘，其中包含从 Defender for IoT 门户的“更新”页中下载的 ISO 映像。

1. 启动设备。

1. 选择“英语”。

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="在 CLI 窗口中选择“英语”。":::

1. 选择“SENSOR-RELEASE-<version> Enterprise”。

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="屏幕截图，显示用于选择版本的屏幕。":::

1. 在安装向导中，定义硬件配置文件和网络属性：

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="屏幕截图，显示安装向导。":::

    | 参数 | 配置 |
    | ----------| ------------- |
    | **硬件配置文件** | 选择“企业”或“Office”进行 SMB 部署。 |
    | **管理界面** | **eno2** |
    | **默认网络参数（通常由客户提供这些参数）** | **管理网络 IP 地址：** <br/> <br/>**设备主机名：** <br/>**DNS：** <br/>**默认网关 IP 地址：**|
    | **输入接口：** | 系统会为你生成输入接口列表。<br/><br/>若要镜像输入接口，请复制列表中显示的带逗号分隔符的所有项：**eno5, eno3, eno1, eno6, eno4**<br/><br/>**对于 HPE DL20：不要列出 eno1、enp1s0f4u4（iLo 接口）**<br/><br/>**BRIDGE**：无需配置桥接接口。 此选项仅用于特殊用例。 按 Enter 继续。 |

1. 大约 10 分钟后，将显示两组凭据。 一组用于 **CyberX** 用户，一组用于 **Support** 用户。

1. 保存设备的 ID 和密码。 首次访问平台时，需要使用这些凭据。

1. 选择 **Enter** 继续。

## <a name="hpe-proliant-dl360-installation"></a>HPE ProLiant DL360 安装

  - 提供了默认的管理用户。 建议在网络配置过程中更改密码。

  - 在网络配置过程中，需配置 iLO 端口。

  - 安装过程大约需要 20 分钟。 安装后，系统会重启多次。

### <a name="hpe-proliant-dl360-front-panel"></a>HPE ProLiant DL360 前面板

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE ProLiant DL360 前面板。":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE ProLiant DL360 后面板

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE ProLiant DL360 后面板。":::

### <a name="enable-remote-access-and-update-the-password"></a>启用远程访问并更新密码

有关 HPE ProLiant DL20 安装，请参阅前面的部分：

  - “启用远程访问并更新密码”

  - “配置 HPE BIOS”

企业配置是相同的。

> [!Note]
> 在“阵列”窗体中，验证是否选择了所有选项。

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>iLO 远程安装（从虚拟驱动器进行）

此过程介绍了从虚拟驱动器进行的 iLO 安装。

若要进行安装：

1. 登录到 iLO 控制台，然后右键单击服务器的屏幕。

1. 选择“HTML5 控制台”。

1. 在控制台中选择 CD 图标，然后选择 CD/DVD 选项。

1. 选择“本地 ISO 文件”。

1. 在对话框中，选择相关的 ISO 文件。

1. 转到左侧图标，选择“电源”，然后选择“重置”。

1. 设备将重启并运行传感器安装过程。

### <a name="software-installation-hpe-dl360"></a>软件安装 (HPE DL360)

安装过程大约需要 20 分钟。 安装后，系统会重启多次。

若要进行安装：

1. 将屏幕和键盘连接到设备，然后连接到 CLI。

1. 连接外部 CD 或基于密钥的磁盘，其中包含从 Defender for IoT 门户的“更新”页中下载的 ISO 映像。

1. 启动设备。

1. 选择“英语”。

1. 选择“SENSOR-RELEASE-<version> Enterprise”。

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="屏幕截图，显示如何选择版本。":::

1. 在安装向导中，定义设备配置文件和网络属性。

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="屏幕截图，显示安装向导。":::

    | 参数 | 配置 |
    | ----------| ------------- |
    | **硬件配置文件** | 选择“corporate”。 |
    | **管理界面** | **eno2** |
    | **默认网络参数（由客户提供）** | **管理网络 IP 地址：** <br/>**子网掩码：** <br/>**设备主机名：** <br/>**DNS：** <br/>**默认网关 IP 地址：**|
    | **输入接口：**  | 系统会为你生成输入接口列表。<br/><br/>若要镜像输入接口，请复制带逗号分隔符的列表中显示的所有项。<br/><br/> 无需配置桥接接口。 此选项仅用于特殊用例。 |

1. 大约 10 分钟后，将显示两组凭据。 一个用于 **CyberX** 用户，一个用于 **Support** 用户。

1. 保存设备的 ID 和密码。 首次访问平台时，需要使用这些凭据。

1. 选择 **Enter** 继续。

## <a name="hp-edgeline-300-installation"></a>HP EdgeLine 300 安装

•   提供了默认的管理用户。 建议在网络配置过程中更改密码。

•   安装过程大约需要 20 分钟。 安装后，系统会重启多次。

### <a name="hp-edgeline-300-back-panel"></a>HP EdgeLine 300 后面板

:::image type="content" source="media/tutorial-install-components/edgeline-el300-panel.png" alt-text="EL300 后面板视图":::

### <a name="enable-remote-access"></a>启用远程访问

1. 将 iSM IP 地址输入到 Web 浏览器中。

1. 使用在设备上找到的默认用户名和密码进行登录。

1. 导航到“有线和无线网络” > “IPV4” 

    :::image type="content" source="media/tutorial-install-components/wired-and-wireless.png" alt-text="导航到突出显示的部分。":::

1. 禁用“DHCP 切换”。

1. 按如下所示配置 IPv4 地址：
    - IPV4 地址：`192.168.1.125`
    - IPV4 子网掩码：`255.255.255.0`
    - IPV4 网关：`192.168.1.1`

1. 选择“应用”。 

1. 注销并重新启动设备。

### <a name="configure-the-bios"></a>配置 BIOS

以下过程介绍如何配置适用于 HP EL300 设备的 BIOS。

若要配置 BIOS，请执行以下操作：

1. 打开设备并按 F9 即可进入 BIOS。

1. 选择“高级”，然后向下滚动到“CSM 支持”。 

    :::image type="content" source="media/tutorial-install-components/csm-support.png" alt-text="启用“CSM 支持”以打开其他菜单。":::

1. 按 Enter 以启用“CSM 支持”。

1. 导航到“存储”，然后按 +/- 以将其更改为“旧版”。 

1. 导航到“视频”，然后按 +/- 以将其更改为“旧版”。 

    :::image type="content" source="media/tutorial-install-components/storage-and-video.png" alt-text="导航到“存储和视频”并将其更改为“旧版”。":::

1. 导航到“启动” > “启动模式选择”。 

1. 按 +/- 将其更改为“旧版”。

    :::image type="content" source="media/tutorial-install-components/boot-mode.png" alt-text="将“启动模式选择”更改为“旧版”。":::

1. 导航到“保存并退出”。

1. 选择“保存更改并退出”。

    :::image type="content" source="media/tutorial-install-components/save-and-exit.png" alt-text="保存更改并退出系统。":::

1. 选择“是”，设备将重新启动。

1. 按 F11 进入“启动菜单”。 

1. 选择包含传感器映像的设备。 DVD 或 USB。 

1. 选择语言。

1. 选择“sensor-10.0.3.12-62a2a3f724 Office：4 个 CPU、8GB RAM、100GB 存储空间”。

    :::image type="content" source="media/tutorial-install-components/sensor-select-screen.png" alt-text="如下所示选择传感器版本。":::

1. 在安装向导中，定义设备配置文件和网络属性：

    :::image type="content" source="media/tutorial-install-components/appliance-parameters.png" alt-text="使用以下参数定义设备的配置文件和网络配置。":::

    | 参数 | 配置 |
    |--|--|
    | 配置硬件配置文件 | office |
    | 配置管理网络接口 | enp3s0 <br />或 <br />可能的值 |
    | 配置网络 IP 地址： | 客户提供的 IP 地址 |
    | 配置子网掩码： | 客户提供的 IP 地址 |
    | 配置 DNS： | 客户提供的 IP 地址 |
    | 配置默认网关 IP 地址： | 客户提供的 IP 地址 |
    | 配置输入接口 | enp4s0 <br />或 <br />可能的值 |
    | 配置网桥接口 | 空值 |

1. 接受设置，然后输入 `Y` 继续。

## <a name="sensor-installation-for-the-virtual-appliance"></a>虚拟设备的传感器安装

可以在以下体系结构中为 Defender for IoT 传感器部署虚拟机：

| 体系结构 | 规范 | 使用情况 | 注释 |
|---|---|---|---|
| **企业** | CPU：8<br/>内存：32G RAM<br/>HDD：1800 GB | 生产环境 | 默认且最常用 |
| **小型企业** | CPU：4 <br/>内存：8G RAM<br/>HDD：500 GB | 测试环境或小型生产环境 | -  |
| **Office** | CPU：4<br/>内存：8G RAM<br/>HDD：100 GB | 小型测试环境 | -  |

### <a name="prerequisites"></a>先决条件

本地管理控制台支持 VMware 和 Hyper-V 部署选项。 在开始安装之前，请确保你具有以下项：

  - 已安装并正常运行的 VMware（ESXi 5.5 或更高版本）或 Hyper-V 虚拟机监控程序（Windows 10 专业版或企业版）

  - 虚拟机的可用硬件资源

  - Azure Defender for IoT 传感器的 ISO 安装文件

请确保虚拟机监控程序正在运行。

### <a name="create-the-virtual-machine-esxi"></a>创建虚拟机 (ESXi)

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

1. 根据所需的体系结构更改虚拟硬件参数。

1. 对于“CD/DVD 驱动器 1”，请选择“数据存储 ISO 文件”，然后选择之前上传的 ISO 文件。

1. 选择“下一步” > “完成”。

### <a name="create-the-virtual-machine-hyper-v"></a>创建虚拟机 (Hyper-V)

此过程介绍如何使用 Hyper-V 创建虚拟机。

若要创建虚拟机，请执行以下操作：

1. 在 Hyper-V 管理器中创建虚拟磁盘。

1. 选择“格式 = VHDX”。

1. 选择“类型 = 动态扩展”。

1. 输入 VHD 的名称和位置。

1. 输入所需的大小（具体取决于体系结构）。   

1. 查看摘要并选择“完成”。

1. 在“操作”菜单上，创建新的虚拟机。

1. 输入虚拟机的名称。

1. 选择“指定代系” > “第 1 代”。 

1. 指定内存分配（具体取决于体系结构），然后选中动态内存的对应复选框。

1. 根据服务器网络拓扑配置网络适配器。

1. 将之前创建的 VHDX 连接到虚拟机。

1. 查看摘要并选择“完成”。

1. 右键单击新虚拟机，然后选择“设置”。

1. 选择“添加硬件”，添加新的网络适配器。

1. 选择将连接到传感器管理网络的虚拟交换机。

1. 分配 CPU 资源（具体取决于体系结构）。

1. 将管理控制台的 ISO 映像连接到虚拟 DVD 驱动器。

1. 启动虚拟机。

2. 在“操作”菜单上选择“连接”，继续进行软件安装。

### <a name="software-installation-esxi-and-hyper-v"></a>软件安装（ESXi 和 Hyper-V）

此部分介绍 ESXi 和 Hyper-V 软件安装。

若要进行安装：

1. 打开虚拟机控制台。

1. 将会从 ISO 映像启动 VM，并会显示语言选择屏幕。 选择“英语”。

1. 选择所需体系结构。

1. 定义设备配置文件和网络属性：

    | 参数 | 配置 |
    | ----------| ------------- |
    | **硬件配置文件** | &lt;所需体系结构&gt; |
    | **管理界面** | **ens192** |
    | **网络参数（由客户提供）** | **管理网络 IP 地址：** <br/>**子网掩码：** <br/>**设备主机名：** <br/>**DNS：** <br/>**默认网关：** <br/>**输入接口：**|
    | **桥接接口：** | 无需配置桥接接口。 此选项仅用于特殊用例。 |

1. 输入 **Y** 接受设置。

1. 登录凭据会自动生成并显示。 将用户名和密码复制到安全位置，因为登录和管理需要用户名和密码。

      - **Support**：进行用户管理的管理用户。

      - **CyberX**：root 的等效项，用于访问设备。

1. 设备重启。

1. 通过以前配置的 IP 地址访问管理控制台：`https://ip_address`。

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="屏幕截图，显示对管理控制台的访问。":::

## <a name="on-premises-management-console-installation"></a>本地管理控制台安装

在设备上安装软件之前，需调整设备的 BIOS 配置：

### <a name="bios-configuration"></a>BIOS 配置

如需为设备配置 BIOS：

1. [启用远程访问并更新密码](#enable-remote-access-and-update-the-password)。

1. [配置 BIOS](#configure-the-hpe-bios)。

### <a name="software-installation"></a>软件安装

安装过程大约需要 20 分钟。 安装后，系统会重启多次。 

在安装过程中，你可以添加辅助 NIC。 如果选择在安装过程中不安装辅助 NIC，则可以稍后[添加辅助 NIC](#add-a-secondary-nic)。 

若要安装软件，请执行以下操作：

1. 为安装过程选择首选语言。

   :::image type="content" source="media/tutorial-install-components/on-prem-language-select.png" alt-text="为安装过程选择首选语言。":::     

1. 选择“管理-版本-\<version\>\<deployment type\>”。

   :::image type="content" source="media/tutorial-install-components/on-prem-install-screen.png" alt-text="选择你的版本。":::   

1. 在安装向导中，定义设备配置文件：

   :::image type="content" source="media/tutorial-install-components/on-prem-first-steps-install.png" alt-text="屏幕截图，显示设备配置文件。":::   

   | 参数 | 配置 |
   |--|--|
   | 配置管理网络接口 | 对于 Dell：eth0、eth1 <br /> 对于 HP：enu1、enu2 <br /> 或 <br />可能的值 |
   | 配置网络 IP 地址： | 客户提供的 IP 地址 |
   | 配置子网掩码： | 客户提供的 IP 地址 |
   | 配置 DNS： | 客户提供的 IP 地址 |
   | 配置默认网关 IP 地址： | 客户提供的 IP 地址 |
   
1. （可选）如需安装辅助网络接口卡 (NIC)，请定义以下设备配置文件和网络属性：

    :::image type="content" source="media/tutorial-install-components/on-prem-secondary-nic-install.png" alt-text="显示辅助 NIC 安装问题的屏幕截图。":::

   | 参数 | 配置 |
   |--|--|
   | 配置传感器监视接口（可选）： | eth1 或可能的值  |
   | 配置传感器监视接口的 IP 地址： | 客户提供的 IP 地址 |
   | 为传感器监视接口配置子网掩码： | 客户提供的 IP 地址 |

1. 接受设置并通过输入 `Y` 继续。 

1. 大约 10 分钟后，将显示两组凭据。 一组用于 **CyberX** 用户，一组用于 **Support** 用户。

   :::image type="content" source="media/tutorial-install-components/credentials-screen.png" alt-text="复制这些凭据，因为它们将不会再次显示。":::  

   保存用户名和密码，首次使用平台时，需要使用这些凭据来访问平台。

1. 选择 **Enter** 继续。

有关如何在设备上查找物理端口的信息，请参阅[查找端口](#find-your-port)。

### <a name="add-a-secondary-nic"></a>添加辅助 NIC。

可以通过添加辅助 NIC 提高本地管理控制台的安全性。 通过添加辅助 NIC，一个 NIC 可以专供用户使用，另外一个 NIC 将支持路由网络的网关配置。 第二个 NIC 专用于某个 IP 地址范围内的所有已连接的传感器。

:::image type="content" source="media/tutorial-install-components/secondary-nic.png" alt-text="辅助 NIC 的总体体系结构。":::

这两个 NIC 都为用户界面 (UI) 提供支持。 

如果选择不部署辅助 NIC，则所有功能都将通过主 NIC 提供。 

如已配置本地管理控制台，并希望将辅助 NIC 添加到本地管理控制台，请执行以下步骤：

1. 使用网络重新配置命令：

    ```bash
    sudo cyberx-management-network-reconfigure
    ```

1. 输入以下问题的以下响应：

    :::image type="content" source="media/tutorial-install-components/network-reconfig-command.png" alt-text="输入以下答案以配置设备。":::

    | 参数 | 要输入的响应 |
    |--|--|
    | 管理网络 IP 地址 | `N` |
    | **子网掩码** | `N` |
    | **DNS** | `N` |
    | 默认网关 IP 地址 | `N` |
    | 传感器监视接口（可选。当传感器位于不同网段时适用。有关详细信息，请参阅《安装说明》）| `Y`，选择一个可能的值 |
    | 传感器监视接口的 IP 地址（可供传感器访问） | `Y`，客户提供的 IP 地址|
    | 传感器监视接口的子网掩码（可供传感器访问） | `Y`，客户提供的 IP 地址 |
    | **主机名** | 由客户提供 |

1. 查看所有选项，并输入 `Y` 以接受更改。 系统将重新启动。

### <a name="find-your-port"></a>查找端口

如果在设备上查找物理端口时遇到问题，可以使用以下命令执行操作：

```bash
sudo ethtool -p <port value> <time-in-seconds>
```

此命令会导致端口上的指示灯在指定时间段内闪烁。 例如，输入 `sudo ethtool -p eno1 120` 将会使端口 eno1 闪烁 2 分钟，以便在设备背面查找端口。 

## <a name="virtual-appliance-on-premises-management-console-installation"></a>虚拟设备：本地管理控制台安装

本地管理控制台 VM 支持以下体系结构：

| 体系结构 | 规范 | 使用情况 | 
|--|--|--|
| Enterprise <br/>（默认且最常用） | CPU：8 <br/>内存：32G RAM<br/> HDD：1.8 TB | 大型生产环境 | 
| Small | CPU：4 <br/> 内存：8G RAM<br/> HDD：500 GB | 大型生产环境 |
| Office | CPU：4 <br/>内存：8G RAM <br/> HDD：100 GB | 小型测试环境 | 
   
### <a name="prerequisites"></a>先决条件

本地管理控制台支持 VMware 和 Hyper-V 部署选项。 在开始安装之前，请验证以下先决条件：

- VMware（ESXi 5.5 或更高版本）或 Hyper-V 虚拟机监控程序（Windows 10 专业版或企业版）已安装并正常运行。

- 虚拟机的硬件资源可用。

- 你有本地管理控制台的 ISO 安装文件。
    
- 虚拟机监控程序正在运行。

### <a name="create-the-virtual-machine-esxi"></a>创建虚拟机 (ESXi)

若要创建虚拟机 (ESXi)，请执行以下操作：

1. 登录到 ESXi，选择相关的 **数据存储**，然后选择“数据存储浏览器”。

1. 上传映像，然后选择“关闭”。

1. 转到“虚拟机”。

1. 选择“创建/注册 VM”。

1. 选择“新建虚拟机”，然后选择“下一步”。 

1. 添加传感器名称并选择以下项：

   - 兼容性：\<latest ESXi version>

   - 来宾 OS 系列：Linux

   - 来宾 OS 版本：Ubuntu Linux (64 位)

1. 选择“**下一步**”。

1. 选择相关的数据存储，然后选择“下一步”。

1. 根据所需的体系结构更改虚拟硬件参数。

1. 对于“CD/DVD 驱动器 1”，请选择“数据存储 ISO 文件”，然后选择之前上传的 ISO 文件。

1. 选择“下一步” > “完成”。

### <a name="create-the-virtual-machine-hyper-v"></a>创建虚拟机 (Hyper-V)

若要使用 Hyper-V 创建虚拟机，请执行以下操作：

1. 在 Hyper-V 管理器中创建虚拟磁盘。

1. 选择“VHDX”格式。

1. 选择“**下一步**”。

1. 选择“动态扩展”类型。

1. 选择“**下一步**”。

1. 输入 VHD 的名称和位置。

1. 选择“**下一步**”。

1. 输入所需的大小（具体取决于体系结构）。

1. 选择“**下一步**”。

1. 查看摘要并选择“完成”。

1. 在“操作”菜单上，创建新的虚拟机。

1. 选择“**下一步**”。

1. 输入虚拟机的名称。

1. 选择“**下一步**”。

1. 选择“代系”，将其设置为“第 1 代”。

1. 选择“**下一步**”。

1. 指定内存分配（具体取决于体系结构），然后选中动态内存的对应复选框。

1. 选择“**下一步**”。

1. 根据服务器网络拓扑配置网络适配器。

1. 选择“**下一步**”。

1. 将之前创建的 VHDX 连接到虚拟机。

1. 选择“**下一步**”。

1. 查看摘要并选择“完成”。

1. 右键单击新虚拟机，然后选择“设置”。

1. 选择“添加硬件”，添加新的适配器作为“网络适配器”。

1. 对于“虚拟交换机”，请选择将连接到传感器管理网络的交换机。

1. 分配 CPU 资源（具体取决于体系结构）。

1. 将管理控制台的 ISO 映像连接到虚拟 DVD 驱动器。

1. 启动虚拟机。

1. 在“操作”菜单上选择“连接”，继续进行软件安装。

### <a name="software-installation-esxi-and-hyper-v"></a>软件安装（ESXi 和 Hyper-V）

启动虚拟机时会从 ISO 映像启动安装过程。

若要安装软件，请执行以下操作：

1. 选择“英语”。

1. 选择部署所需的体系结构。

1. 定义传感器管理网络的网络接口：接口、IP、子网、DNS 服务器和默认网关。

1. 登录凭据会自动生成。 保存用户名和密码，首次使用平台时，需要使用这些凭据来访问平台。

   然后，设备将重新启动。

1. 通过以前配置的 IP 地址访问管理控制台：`<https://ip_address>`。

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="屏幕截图，显示管理控制台的登录屏幕。":::

## <a name="legacy-appliances"></a>旧设备

本部分只介绍旧设备的安装过程。 如果要购买新设备，请参阅[确定所需设备](how-to-identify-required-appliances.md#identify-required-appliances)。

### <a name="nuvo-5006lp-installation"></a>Nuvo 5006LP 安装

本部分提供 Nuvo 5006LP 安装过程。 在 Nuvo 5006LP 设备上安装软件之前，需要调整设备的 BIOS 配置。 

#### <a name="nuvo-5006lp-front-panel"></a>Nuvo 5006LP 前面板

:::image type="content" source="media/tutorial-install-components/nuvo5006lp_frontpanel.png" alt-text="Nuvo 5006LP 设备前面板的视图。":::

1. 电源按钮，电源指示灯
1. DVI 视频连接器
1. HDMI 视频连接器
1. VGA 视频连接器
1. 远程开关控制和状态 LED 输出
1. 重置按钮
1. 管理网络适配器
1. 用于接收镜像数据的端口

#### <a name="nuvo-back-panel"></a>Nuvo 后面板

:::image type="content" source="media/tutorial-install-components/nuvo5006lp_backpanel.png" alt-text="Nuvo 5006LP 后面板的视图。":::

1. SIM 卡槽
1. 麦克风和扬声器
1. COM 端口
1. USB 连接器
1. 直流电源端口 (DC IN)

#### <a name="configure-the-nuvo-5006lp-bios"></a>配置 Nuvo 5006LP BIOS

以下过程说明如何配置 Nuvo 5006LP BIOS。 请确保事先已在设备上安装操作系统。

若要配置 BIOS，请执行以下操作：

1. 打开设备电源。

1. 按 F2 进入 BIOS 配置。

1. 导航到“Power”（电源），将“Power On after Power Failure”（断电后开机）更改为“S0-Power On”（S0-开机）。

    :::image type="content" source="media/tutorial-install-components/nuvo-power-on.png" alt-text="将 Nuvo 5006 更改为断电后开机。":::

1. 导航到“Boot”（启动），确保“PXE Boot to LAN”（通过局域网进行 PXE 启动）设置为“Disabled”（已禁用）  。

1. 按 F10 保存设置，然后选择“Exit”（退出） 。 

#### <a name="software-installation-nuvo-5006lp"></a>软件安装 (Nuvo 5006LP)

完成过程大约需要 20 分钟。 安装后，系统会重启多次。

1. 连接包含 ISO 映像的外部 CD 或基于密钥的磁盘。

1. 启动设备。

1. 选择“英语”。

1. 选择“XSENSE-RELEASE-<version> Office...”。

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="选择要安装的传感器版本。":::

1. 定义设备体系结构和网络属性：

    :::image type="content" source="media/tutorial-install-components/nuvo-profile-appliance.png" alt-text="定义 Nuvo 的体系结构和网络属性。":::

    | 参数 | 配置 |
    | ----------| ------------- |
    | **硬件配置文件** | 选择“Office”。 |
    | **管理界面** | **eth0** |
    | 管理网络 IP 地址 | 客户提供的 IP 地址 | 
    | 管理子网掩码 | 客户提供的 IP 地址 | 
    | **DNS** | 客户提供的 IP 地址 |
    | 默认网关 IP 地址 | **0.0.0.0** | 
    | 输入接口 | 输入接口列表由系统生成。 <br />若要镜像输入接口，请复制带逗号分隔符的列表中显示的所有项。 |
    | 网桥接口 | - |

1. 接受设置，然后输入 `Y` 继续。

大约 10 分钟后，会自动生成登录凭据。 保存用户名和密码，首次使用平台时，需要使用这些凭据来访问平台。

### <a name="fitlet2-mini-sensor-installation"></a>Fitlet2 微型传感器安装

本部分提供 Fitlet2 安装过程。 在 Fitlet 设备上安装软件之前，需要调整设备的 BIOS 配置。

#### <a name="fitlet2-front-panel"></a>Fitlet2 前面板 

:::image type="content" source="media/tutorial-install-components/fitlet-front-panel.png" alt-text="Fitlet 2 前面板的视图。":::

#### <a name="fitlet2-back-panel"></a>Fitlet2 后面板

:::image type="content" source="media/tutorial-install-components/fitlet2-back-panel.png" alt-text="Fitlet 2 后面板的视图。":::

#### <a name="configure-the-fitlet2-bios"></a>配置 Fitlet2 BIOS

1. 打开设备电源。

1. 导航到“Main”（主要） > “OS Selection”（OS 选择） 。

1. 按 +/- 选择“Linux” 。

    :::image type="content" source="media/tutorial-install-components/fitlet-linux.png" alt-text="在 Fitlet2 上将 OS 设置为“Linux”。":::

1. 验证系统日期和时间是否更新为与安装日期和时间相同。

1. 导航到“Advanced”（高级），选择“ACPI Settings”（ACPI 设置） 。

1. 选择“Enable Hibernation”（启用休眠），然后按 +/- 选择“Disabled”（已禁用）  。

    :::image type="content" source="media/tutorial-install-components/disable-hibernation.png" alt-text="在 Fitlet2 上禁用休眠模式。":::

1. 按 Esc。

1. 导航到“Advanced”（高级） > “TPM Configuration”（TPM 配置） 。

1. 选择“fTPM”，然后按 +/- 选择“Disabled”（已禁用）  。

1. 按 Esc。

1. 导航到“CPU Configuration”（CPU 配置） > “VT-d” 。

1. 按 +/- 选择“Enabled”（已启用） 。

1. 导航到“CSM Configuration”（CSM 配置） > “CSM Support”（CSM 支持） 。

1. 按 +/- 选择“Enabled”（已启用） 。
1. 导航到“Advanced”（高级） > “Boot option filter [Legacy only]”（启动选项筛选器 [仅限旧版]），然后将以下字段中的设置更改为“Legacy”（旧版）  ：
    - 网络
    - 存储
    - 视频
    - 其他 PCI

    :::image type="content" source="media/tutorial-install-components/legacy-only.png" alt-text="将所有字段设置为“Legacy”。":::

1. 按 Esc。

1. 导航到“Security”（安全性） > “Secure Boot Customization”（安全启动自定义） 。

1. 按 +/- 选择“Disabled”（已禁用） 。

1. 按 Esc。

1. 导航到“Boot”（启动） > “Boot mode”（启动模式），选择“Legacy”（旧版）  。

1. 选择“Boot Option #1 – [USB CD/DVD]”（启动选项 #1 – [USB CD/DVD]）。
 
1. 选择“Save & Exit”（保存并退出）。

#### <a name="software-installation-fitlet2"></a>软件安装 (Fitlet2)

完成过程大约需要 20 分钟。 安装后，系统会重启多次。

1. 连接包含 ISO 映像的外部 CD 或基于密钥的磁盘。

1. 启动设备。

1. 选择“英语”。

1. 选择“XSENSE-RELEASE-<version> Office...”。

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="选择要安装的传感器版本。":::

    > [!Note]
    > 请不要选择“Ruggedized”。

1. 定义设备体系结构和网络属性：

    :::image type="content" source="media/tutorial-install-components/nuvo-profile-appliance.png" alt-text="定义 Nuvo 的体系结构和网络属性。":::

    | 参数 | 配置 |
    | ----------| ------------- |
    | **硬件配置文件** | 选择“Office”。 |
    | **管理界面** | em1 |
    | 管理网络 IP 地址 | 客户提供的 IP 地址 | 
    | 管理子网掩码 | 客户提供的 IP 地址 | 
    | **DNS** | 客户提供的 IP 地址 |
    | 默认网关 IP 地址 | **0.0.0.0** | 
    | 输入接口 | 输入接口列表由系统生成。 <br />若要镜像输入接口，请复制带逗号分隔符的列表中显示的所有项。 |
    | 网桥接口 | - |

1. 接受设置，然后输入 `Y` 继续。

大约 10 分钟后，会自动生成登录凭据。 保存用户名和密码，首次使用平台时，需要使用这些凭据来访问平台。

## <a name="post-installation-validation"></a>安装后验证

若要验证物理设备的安装，需要执行多个测试。 同一验证过程适用于所有设备类型。

使用 GUI 或 CLI 进行验证。 用户 **Support** 和用户 **CyberX** 可以使用验证。

安装后验证必须包括以下测试：

  - **健全性测试**：验证系统是否正在运行。

  - **版本**：验证版本是否正确。

  - **ifconfig**：验证安装过程中配置的所有输入接口是否正在运行。

### <a name="check-system-health-by-using-the-gui"></a>使用 GUI 检查系统运行状况

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="屏幕截图，显示系统运行状况检查。":::

#### <a name="sanity"></a>健全性

- **设备**：运行设备健全性检查。 可以使用 CLI 命令 `system-sanity` 执行同一检查。

- **版本**：显示设备版本。

- **网络属性**：显示传感器网络参数。

#### <a name="redis"></a>Redis

- **内存**：提供内存使用整体情况，例如使用的内存量和剩余的内存量。

- **最长密钥**：显示可能导致内存大量使用的最长密钥。

#### <a name="system"></a>系统

- **核心日志**：提供核心日志的最后 500 行，使你能够查看最新的日志行而无需导出整个系统日志。

- **任务管理器**：将进程表中出现的任务转换为以下层： 
  
  - 永久性层 (Redis) 
  - 缓存层 (SQL)

- **网络统计信息**：显示网络统计信息。

- **TOP**：显示进程表。 它是一个 Linux 命令，提供正在运行的系统的动态实时视图。

- **备份内存检查**：提供备份内存的状态，检查以下各项：
  - 备份文件夹的位置 
  - 备份文件夹的大小
  - 备份文件夹的限制
  - 上次备份的发生时间
  - 有多少空间可用于额外的备份文件

- **ifconfig**：显示设备的物理接口的参数。

- **CyberX nload**：使用六秒测试显示网络流量和带宽。

- **核心日志中的错误**：显示核心日志文件中的错误。

若要访问此工具，请执行以下操作：

1. 使用 **Support** 用户凭据登录到传感器。

1. 从“系统设置”窗口中选择“系统统计信息”。

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="check-system-health-by-using-the-cli"></a>使用 CLI 检查系统运行状况

**测试 1：健全性**

验证系统是否已启动并运行：

1. 通过 Linux 终端（例如 PuTTY）和用户 **Support** 连接到 CLI。

1. 输入 `system sanity`。

1. 检查所有服务是否均为绿色（正在运行）。

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="屏幕截图，显示正在运行的服务。":::

1. 验证“System is UP! (prod)”是否显示在底部。

**测试 2：版本检查**

验证是否使用了正确的版本：

1. 通过 Linux 终端（例如 PuTTY）和用户 **Support** 连接到 CLI。

1. 输入 `system version`。

1. 检查是否显示了正确的版本。

**测试 3：网络验证**

验证安装过程中配置的所有输入接口是否正在运行：

1. 通过 Linux 终端（例如 PuTTY）和用户 **Support** 连接到 CLI。

1. 输入 `network list`（Linux 命令 `ifconfig` 的等效项）。

1. 验证是否显示了所需的输入接口。 例如，如果安装了两个四端口铜缆 NIC，则列表中应有 10 个接口。

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="屏幕截图，显示接口列表。":::

**测试 4：对 UI 的管理访问权限**

验证是否可以访问控制台 Web GUI：

1. 使用以太网电缆将便携式计算机连接到管理端口 (**Gb1**)。

1. 将便携式计算机 NIC 地址的范围定义为与设备相同的范围。

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="屏幕截图，显示对 UI 的管理访问权限。":::

1. 从便携式计算机对设备的 IP 地址（默认值：10.100.10.1）进行 ping 操作，以验证连接性。

1. 在便携式计算机上打开 Chrome 浏览器，输入设备的 IP 地址。

1. 在“你的连接不是专用连接”窗口中选择“高级”，继续下一步。

1. 如果显示 Defender for IoT 登录屏幕，则表明测试成功。

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="屏幕截图，显示对管理控制台的访问。":::

## <a name="troubleshooting"></a>疑难解答

### <a name="you-cant-connect-by-using-a-web-interface"></a>不能使用 Web 界面进行连接

1. 验证你尝试连接的计算机是否位于设备所在的网络上。

1. 验证 GUI 网络是否连接到管理端口。

1. 对设备的 IP 地址进行 ping 操作。 如果没有 ping，请执行以下操作：

   1. 将监视器和键盘连接到设备。

   1. 使用 **Support** 用户和密码进行登录。

   1. 使用 `network list` 命令查看当前 IP 地址。

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="屏幕截图，显示网络列表。":::

1. 如果网络参数配置错误，请使用以下过程来更改它们：

   1. 使用 `network edit-settings` 命令。

   1. 若要更改管理网络 IP 地址，请选择“Y”。

   1. 若要更改子网掩码，请选择“Y”。

   1. 若要更改 DNS，请选择“Y”。

   1. 若要更改默认网关 IP 地址，请选择“Y”。

   1. 对于输入接口更改（仅限传感器），请选择“N”。

   1. 若要应用设置，请选择“Y”。

1. 重启后，请使用 Support 用户凭据进行连接，并使用 `network list` 命令验证参数是否已更改。

1. 尝试 ping 操作并再次从 GUI 进行连接。

### <a name="the-appliance-isnt-responding"></a>设备未响应

1. 将监视器和键盘连接到设备，或使用 PuTTY 以远程方式连接到 CLI。

1. 使用 **Support** 用户的凭据进行登录。

1. 使用 `system sanity` 命令，检查所有进程是否正在运行。

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="屏幕截图，显示 system sanity 命令。":::

对于任何其他问题，请联系 [Microsoft 支持部门](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>附录 A：vSwitch 上的镜像端口 (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>在现有 vSwitch 上配置 SPAN 端口

vSwitch 没有镜像功能，但你可以使用一种解决方法来实现 SPAN 端口。

若要配置 SPAN 端口，请执行以下操作：

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

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>附录 B：从本地管理控制台访问传感器

可以通过阻止用户直接访问传感器来增强系统安全性。 请改用代理隧道，通过单个防火墙规则让用户从本地管理控制台访问传感器。 此方法可降低对传感器之外的网络环境进行未经授权的访问的可能性。 用户登录传感器时的体验保持不变。

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="屏幕截图，显示对传感器的访问。":::

若要启用隧道，请执行以下操作：

1. 使用 CyberX 或 Support 用户凭据登录到本地管理控制台的 CLI 。

1. 输入 `sudo cyberx-management-tunnel-enable`。

1. 选择 **Enter**。

1. 输入 `--port 10000`。

### <a name="next-steps"></a>后续步骤

[设置网络](how-to-set-up-your-network.md)
