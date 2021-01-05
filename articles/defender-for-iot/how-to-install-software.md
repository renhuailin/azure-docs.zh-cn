---
title: 用于 IoT 安装的 Defender
description: 了解如何安装适用于 Azure Defender IoT 的传感器和本地管理控制台。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/2/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 5e33a7adc7b529df8c7c821cbfdcb0ad5709803b
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838199"
---
# <a name="defender-for-iot-installation"></a>用于 IoT 安装的 Defender

本文介绍如何安装用于 IoT 的 Azure Defender 的以下元素：

- **传感器**：用于 IoT 传感器的 Defender 使用被动 (无代理) 监视收集 ICS 网络流量。 被动和产生干扰，传感器对 OT 网络和设备的影响不会有任何影响。 传感器连接到 SPAN 端口或网络分流，并立即开始监视网络。 检测会出现在传感器控制台中。 在这里，你可以在网络映射、设备清单和范围广泛的报表中查看、调查和分析它们。 示例包括风险评估报表、数据挖掘查询和攻击向量。 有关详细信息，请参阅有关适用于 [IoT 的 Defender 传感器用户指南 (直接下载) ](https://aka.ms/AzureDefenderforIoTUserGuide)。

- **本地管理控制台**：本地管理控制台使你可以执行设备管理、风险管理和漏洞管理。 你还可以使用它在企业中执行威胁监视和事件响应。 它提供了一个统一的视图，显示了所有网络设备、关键 IoT，以及在部署传感器的设施中检测到的风险指标和警报。 使用本地管理控制台查看和管理有气流的网络中的传感器。

本文介绍以下安装信息：

  - **硬件：** Dell 和 HPE 物理设备详细信息。

  - **软件：** 传感器和本地管理控制台软件安装。

  - **虚拟设备：** 虚拟机详细信息和软件安装。

安装完成后，将传感器连接到网络。

## <a name="about-defender-for-iot-appliances"></a>关于用于 IoT 设备的 Defender 

以下部分提供有关用于 IoT 传感器设备的 Defender 的信息以及用于 IoT 本地管理控制台的 Defender 设备的信息。

### <a name="physical-appliances"></a>物理设备

用于 IoT 设备传感器的 Defender 连接到 SPAN 端口或网络分流点，并使用被动 (无代理) 监视立即开始收集 ICS 网络流量。 此过程对网络和设备不会有任何影响，因为它不会放置在数据路径中，也不会主动扫描 OT 设备。

以下机架安装设备可用：

| **部署类型** | **企业** | **企业** | **SMB** |  |
|--|--|--|--|--|
| **Model** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **监视端口** | 最多15个 RJ45 或8个 OPT | 最高 9 RJ45 或 6 OPT | 最多8个 RJ45 或 6 OPT | 4 RJ45 |
| **最大 \* 带宽* _ | 3 Gb/秒 | 1 Gb/秒 | 1 Gb/秒 | 100 Mb/秒 |
| _ *最大受保护设备** | 30,000 | 10,000 | 15,000 | 1,000 |

* 最大带宽容量可能因协议分发而异。

### <a name="virtual-appliances"></a>虚拟设备

提供以下虚拟设备：

| **部署类型** | **企业** | **SMB** | **Line** |
|--|--|--|--|
| **说明** | 适用于企业部署的虚拟设备 | 用于 SMB 部署的虚拟设备 | 用于行部署的虚拟设备 |
| **最大 \* 带宽* _ | 150 Mb/秒 | 每秒 15 Mb | 3 Mb/秒 |
| _ *最大受保护设备** | 3,000 | 300 | 100 |
| **部署类型** | Enterprise | SMB | 行 |
| **说明** | 适用于企业部署的虚拟设备 | 用于 SMB 部署的虚拟设备 | 用于行部署的虚拟设备 |

* 最大带宽容量可能因协议分发而异。

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>本地管理控制台的硬件规格

 | Item | 说明 |
 |----|--|
 **说明** | 在多层体系结构中，本地管理控制台跨地理上分散的站点提供可见性和控制能力。 它与 SOC 安全堆栈集成，其中包括 Siem、票证系统、下一代防火墙、安全远程访问平台和用于 IoT ICS 恶意软件沙箱的 Defender。 |
 **部署类型** | Enterprise |
 **设备类型**  | Dell R340，VM |
 **托管传感器数量** | 无限制 |

## <a name="prepare-for-the-installation"></a>准备安装

### <a name="access-the-iso-installation-image"></a>访问 ISO 安装映像

可从用于 IoT 的 Defender 门户访问安装映像。

访问该文件的步骤：

1. 登录到 IoT 帐户。

2. 请参阅 **网络传感器** 或 **本地管理控制台** 页面，并选择要下载的版本。

### <a name="install-from-dvd"></a>从 DVD 安装

安装之前，请确保：

- 带有 USB 连接器的便携式 DVD 驱动器。

- ISO 安装程序映像。

若要进行安装：

1. 将映像刻录到 DVD，或在某个密钥上准备磁盘。 将便携式 DVD 驱动器连接到计算机，右键单击 ISO 映像，然后选择 " **刻录到磁盘**"。

1. 连接密钥上的 DVD 或磁盘，并将设备配置为从 DVD 或磁盘上的密钥启动。

### <a name="install-from-disk-on-a-key"></a>从磁盘上的密钥安装

安装之前，请确保：

  - 已安装 Rufus。
  
  - 使用 USB 版本3.0 和更高版本的密钥磁盘。 最小大小为 4 GB。

  - ISO 安装程序映像文件。

在此过程中，将删除密钥上的磁盘。

在密钥上准备磁盘：

1. 运行 Rufus 并选择 " **传感器 ISO**"。

2. 将密钥上的磁盘连接到前面板。

3. 设置要从 USB 启动的服务器的 BIOS。

## <a name="dell-poweredger340xl-installation"></a>Dell PowerEdgeR340XL 安装

在 Dell 设备上安装软件之前，需调整设备的 BIOS 配置：

  - [Dell Poweredge R340 前面板](#dell-poweredge-r340-front-panel) 和 [Dell Poweredge R340 背面板](#dell-poweredge-r340-back-panel) 包含前面板和后面板的说明，以及安装所需的信息，如驱动程序和端口。

  - [DELL BIOS 配置](#dell-bios-configuration) 提供有关如何连接到 Dell 设备管理界面和配置 BIOS 的信息。

  - [软件安装 (Dell R340) ](#software-installation-dell-r340) 介绍安装用于 IoT 传感器软件的 Defender 所需的过程。

### <a name="dell-poweredge-r340xl-requirements"></a>Dell PowerEdge R340XL 要求 

若要安装 Dell PowerEdge R340XL 设备，需要：

- 适用于 Dell 远程访问控制器 (iDrac) 的企业许可证

- BIOS 配置 XML

- 服务器固件版本：

  - BIOS 版本2.1。6

  - iDrac 版本3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Dell PowerEdge R340 前面板

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Dell PowerEdge R340 前面板。":::

 1. 左侧控制面板 
 2. 光驱 (可选)  
 3. 右侧控制面板 
 4. 信息标记 
 5. 驱动器  

### <a name="dell-poweredge-r340-back-panel"></a>Dell PowerEdge R340 背面面板

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340 背面面板。":::

1. 串行端口 
2. NIC 端口 (Gb 1)  
3. NIC 端口 (Gb 1)  
4. 半高 PCIe 
5. 全高 PCIe 扩展卡插槽 
6. 电源单元1 
7. 电源单元2 
8. 系统标识 
9. 系统状态指示器电缆端口 (CMA) 按钮 
10. USB 3.0 端口 (2)  
11. iDRAC9 专用网络端口 
12. VGA 端口 

### <a name="dell-bios-configuration"></a>Dell BIOS 配置

需要 dell BIOS 配置才能调整 Dell 设备以使用软件。

BIOS 配置是通过预定义的配置执行的。 可从 [帮助中心](https://help.cyberx-labs.com/)访问该文件。

将配置文件导入到 Dell 设备。 在使用配置文件之前，需要在 Dell 设备与管理计算机之间建立通信。

Dell 设备由 (LC) 的集成 iDRAC 与生命周期控制器管理。 LC 嵌入在每个 Dell PowerEdge 服务器中，并提供可帮助你部署、更新、监视和维护 Dell PowerEdge 设备的功能。

若要建立 Dell 设备与管理计算机之间的通信，需要在同一子网上定义 iDRAC IP 地址和管理计算机的 IP 地址。

建立连接后，可以配置 BIOS。

若要配置 Dell BIOS：

1. [配置 iDRAC IP 地址](#configure-idrac-ip-address)

2. [导入 BIOS 配置文件](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>配置 iDRAC IP 地址

1. 开启传感器的电源。

2. 如果已安装操作系统，请选择 F2 键以输入 BIOS 配置。

3. 选择 **IDRAC 设置**。

4. 选择 " **网络**"。

   > [!NOTE]
   > 在安装过程中，必须配置以下步骤中所述的默认 iDRAC IP 地址和密码。 安装后，可以更改这些定义。

5. 将静态 IPv4 地址更改为 **10.100.100.250**。

6. 将静态子网掩码改为 **255.255.255.0**。

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="屏幕截图，显示静态子网掩码。":::

7. 选择 **"**  >  **完成**"。

#### <a name="import-the-bios-configuration-file"></a>导入 BIOS 配置文件

本文介绍如何使用配置文件配置 BIOS。

1. 将具有静态预配置 IP 地址的 PC 插入到 **iDRAC** 端口 **10.100.100.200** 。

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="预配置 IP 地址端口的屏幕截图。":::

2. 打开浏览器并输入 **10.100.100.250** 以连接到 iDRAC web 界面。

3. 用 Dell 默认的管理员特权登录：

   - 用户名： **root**

   - 密码： **《 calvin**

4. 设备的凭据如下：

   - 用户名： **cyberx**

   - 密码： **xhxvhttju， @4338**

     "导入服务器配置文件" 操作已启动。

     > [!NOTE]
     > 导入文件之前，请确保：
     > - 当前已连接到 iDRAC 的用户是当前连接的用户。
     > - 系统不在 BIOS 菜单中。

5. 请参阅 **配置**  >  **服务器配置文件**。 设置以下参数：

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="显示服务器配置文件配置的屏幕截图。":::

   | 参数 | 配置 |
   |--|--|
   | 位置类型 | 选择 " **本地**"。 |
   | 文件路径 | 选择 " **选择文件** " 并添加配置 XML 文件。 |
   | 导入组件 | 选择 " **BIOS、NIC、RAID"**。 |
   | 最长等待时间 | 选择 **20 分钟**。 |

6. 选择“导入”  。

7. 若要监视此过程，请参阅 **维护**  >  **作业队列**。

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="显示作业队列的屏幕截图。":::

#### <a name="manually-configuring-bios"></a>手动配置 BIOS 

如果需要，请手动配置设备 BIOS：

- 未从箭头购买设备。

- 你有一个设备，但没有访问 XML 配置文件的权限。

访问 BIOS 后，请访问 " **设备设置**"。

手动配置：

1. 使用键盘和屏幕直接访问设备 BIOS，或使用 iDRAC。

   - 如果设备不是 IoT 设备的 Defender，请打开浏览器并前往之前配置的 IP 地址。 用 Dell 默认的管理员特权登录。 使用 **root** 作为密码的用户名和 **《 calvin** 。

   - 如果设备是 IoT 设备的 Defender，请使用 **cyberx** 作为用户名和 **@4338 xhxvhttju** 的密码进行登录。

2. 访问 BIOS 后，请访问 " **设备设置**"。

3. 通过选择 " **集成 raid 控制器1： DELL PERC \<PERC H330 Adapter\> 配置实用工具**" 选择 RAID 控制的配置。

4. 选择 " **配置管理**"。

5. 选择 " **创建虚拟磁盘**"。

6. 在 " **选择 RAID 级别** " 字段中，选择 " **RAID5**"。 在 " **虚拟磁盘名称** " 字段中，输入 **ROOT** ，然后选择 " **物理磁盘**"。

7. 选择 "**全部选中**"，然后选择 "**应用更改**"

8. 选择“确定”。

9. 向下滚动并选择 " **创建虚拟磁盘**"。

10. 选中 " **确认** " 复选框，然后选择 **"是"**。

11. 选择“确定”  。

12. 返回到主屏幕并选择 " **系统 BIOS**"。

13. 选择 " **启动设置**"。

14. 对于 " **启动模式** " 选项，请选择 " **BIOS**"。

15. 选择 " **上一步**"，然后选择 " **完成** " 退出 BIOS 设置。

### <a name="software-installation-dell-r340"></a>软件安装 (Dell R340) 

安装过程大约需20分钟。 安装后，系统将重新启动多次。

若要进行安装：

1. 通过以下方式之一验证是否已将版本介质装载到设备：

   - 使用发布的密钥连接外部 CD 或磁盘。

   - 使用 iDRAC 装载 ISO 映像。 登录到 iDRAC 后，选择虚拟控制台，然后选择 " **虚拟媒体**"。

2. 在 " **地图 CD/DVD** " 部分中，选择 " **选择文件**"。

3. 从打开的对话框中选择此版本的 ISO 映像文件。

4. 选择 " **地图设备** " 按钮。

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="显示映射设备的屏幕截图。":::

5. 已装入介质。 选择“关闭”。

6. 启动设备。 如果使用的是 iDRAC，则可以通过选择 **Consul 控件** 按钮来重新启动服务器。 然后，在 **键盘宏** 上选择 " **应用** " 按钮，这将启动 Ctrl + Alt + Delete 序列。

7. 选择 " **英语**"。

8. 选择 " **传感器-发布- \<version\> 企业**"。

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="显示版本选择的屏幕截图。":::   

9. 定义设备配置文件和网络属性：

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="显示设备配置文件的屏幕截图。":::   

   | 参数 | 配置 |
   |--|--|
   | **硬件配置文件** | **版** |
   | **管理界面** | **eno1** |
   | **客户 (提供网络参数)** | - |
   |**管理网络 IP 地址：** | - |
   | **子网掩码：** | - |
   | **设备主机名：** | - |
   | **DNS** | - |
   | **默认网关 IP 地址：** | - |
   | **输入接口：** |  系统会为您生成输入接口列表。 若要镜像输入接口，请复制列表中以逗号分隔显示的所有项目。 请注意，无需配置桥接口。 此选项仅用于特殊用例。 |

10. 大约10分钟后，将显示两组凭据。 一个适用于 **CyberX** 用户，一个用于 **支持** 用户。  

11. 保存设备 ID 和密码。 首次使用时，需要使用这些凭据来访问平台。

12. 选择 **Enter** 以继续。

## <a name="hpe-proliant-dl20-installation"></a>HPE ProLiant DL20 安装

本文介绍 HPE ProLiant DL20 安装过程，其中包括以下步骤：

  - 启用远程访问并更新默认管理员密码。
  - 配置 BIOS 和 RAID 设置。
  - 安装软件。

### <a name="about-the-installation"></a>关于安装

  - 可以安装企业和 SMB 设备。 对于这两种设备类型，安装过程是相同的，但阵列配置除外。
  - 提供了默认的管理用户。 建议在网络配置过程中更改密码。
  - 在网络配置过程中，将在网络端口1上配置 iLO 端口。
  - 安装过程大约需20分钟。 安装后，系统将重新启动多次。

### <a name="hpe-proliant-dl20-front-panel"></a>HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE ProLiant DL20 前面板。":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE ProLiant DL20 后面板

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="HPE ProLiant DL20 的后面板。":::

### <a name="enable-remote-access-and-update-the-password"></a>启用远程访问和更新密码

使用以下过程设置网络选项并更新默认密码。

若要启用和更新密码：

1. 将屏幕和键盘连接到 HP 设备，启用设备，并按 **F9**。

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="显示 HPE ProLiant 窗口的屏幕截图。":::

2. 中转到 "**系统实用程序**"  >  **系统配置**  >  **iLO 5 配置实用工具**  >  **网络选项**。

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="显示 &quot;系统配置&quot; 窗口的屏幕截图。":::

    1.  从 "**网络接口适配器**" 字段中选择 "**共享网络端口-LOM** "。
    
    1.  禁用 DHCP。
    
    1.  输入 IP 地址、子网掩码和网关 IP 地址。

3. 选择 " **F10：保存**"。

4. 选择 **Esc** 返回到 **ILO 5 配置实用程序**，然后选择 " **用户管理**"。

5. 选择 " **编辑/删除用户**"。 管理员是唯一定义的默认用户。 

6. 更改默认密码，然后选择 " **F10：保存**"。

### <a name="configure-the-hpe-bios"></a>配置 HPE BIOS

以下过程介绍如何为企业和 SMB 设备配置 HPE BIOS。

配置 HPE BIOS：

1. 选择 **系统实用工具**  >  **系统配置**  >  **BIOS/平台配置 (RBSU)**。

2. 在 **BIOS/平台配置 (RBSU)** "窗体中，选择" **启动选项**"。

3. 将 **启动模式** 更改为 **旧的 BIOS 模式**，然后选择 " **F10：保存**"。

4. 选择 " **Esc** 两次" 以关闭 **系统配置** 窗体。

#### <a name="for-the-enterprise-appliance"></a>适用于企业设备

1. 选择 "**嵌入的 RAID 1： HPE 智能阵列 P408i-a SR Gen 10**  >  **阵列配置**  >  **创建数组**"。

2. 在 " **创建数组** " 窗体中，选择所有选项。 **企业** 设备可以使用三个选项。

#### <a name="for-the-smb-appliance"></a>对于 SMB 设备

1. 选择 "**嵌入的 RAID 1： HPE 智能阵列 P208i-a SR Gen 10**  >  **阵列配置**  >  **创建数组**"。

2. 选择 " **转到下一窗体**"。

3. 在 " **设置 Raid 级别** " 窗体中，将 "级别" 设置为 "适用于企业部署的 **raid 5** **"，将** "级别" 设置为 "

4. 选择 " **转到下一窗体**"。

5. 在 " **逻辑驱动器标签** " 窗体中，输入 **逻辑驱动器 1**。

6. 选择 " **提交更改**"。

7. 在 **提交** 窗体中，选择 " **返回到主菜单**"。

8. 选择 " **F10：保存** " 两次，然后按 **Esc** 。

9. 在 " **系统实用程序** " 窗口中，选择 " **一次性启动菜单**"。

10. 在 " **一次性启动" 菜单** 中，选择 " **旧的 BIOS One-Time 启动菜单**"。

11. 出现 "在旧版和 **启动替代** 窗口 **中启动**" 窗口。 选择启动替代选项;例如，到 cd-rom、USB、HDD 或 UEFI shell。

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="显示第一个启动替代窗口的屏幕截图。":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="显示第二个启动替代窗口的屏幕截图。":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>软件安装 (HPE ProLiant DL20 设备) 

安装过程大约需20分钟。 安装后，系统将重新启动多次。

安装软件：

1. 将屏幕和键盘连接到设备，然后连接到 CLI。

2. 将密钥上的外部 CD 或磁盘连接到从 IoT 门户的 " **更新** " 页中下载的 ISO 映像。

3. 启动设备。

4. 选择 " **英语**"。

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="在 CLI 窗口中选择英语。":::

5. 选择 " **传感器-发布- <version> 企业**"。

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="用于选择版本的屏幕屏幕截图。":::

6. 在安装向导中，定义设备配置文件和网络属性：

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="显示安装向导的屏幕截图。":::

    | 参数 | 配置 |
    | ----------| ------------- |
    | **硬件配置文件** | 为 SMB 部署选择 " **企业** " 或 " **Office** "。 |
    | **管理界面** | **eno2** |
    | **默认网络参数 (通常由客户提供参数)** | **管理网络 IP 地址：** <br/> <br/>**设备主机名：** <br/>**DNS** <br/>**默认网关 IP 地址：**|
    | **输入接口：** | 系统会为您生成输入接口列表。<br/><br/>若要镜像输入接口，请复制列表中以逗号分隔符显示的所有项： **eno5、eno3、eno1、eno6、eno4**<br/><br/>**对于 HPE DL20：不列出 eno1，enp1s0f4u4 (iLo 接口)**<br/><br/>**桥**：无需配置桥接接口。 此选项仅用于特殊用例。 按 Enter 继续。 |

7. 大约10分钟后，将显示两组凭据。 一个适用于 **CyberX** 用户，一个用于 **支持** 用户。

8. 保存设备的 ID 和密码。 首次访问平台时需要凭据。

9. 选择 **Enter** 以继续。

## <a name="hpe-proliant-dl360-installation"></a>HPE ProLiant DL360 安装

  - 提供了默认的管理用户。 建议在网络配置过程中更改密码。

  - 在网络配置过程中，你将配置 iLO 端口。

  - 安装过程大约需20分钟。 安装后，系统将重新启动多次。

### <a name="hpe-proliant-dl360-front-panel"></a>HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE ProLiant DL360 前面板。":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE ProLiant DL360 后面板

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE ProLiant DL360 背面面板。":::

### <a name="enable-remote-access-and-update-the-password"></a>启用远程访问和更新密码

有关 HPE ProLiant DL20 安装，请参阅前面的部分：

  - "启用远程访问和更新密码"

  - "配置 HPE BIOS"

企业配置是相同的。

> [!Note]
> 在数组窗体中，验证是否选择了所有选项。

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>从虚拟驱动器 (iLO 远程安装) 

此过程介绍了从虚拟驱动器进行的 iLO 安装。

若要进行安装：

1. 登录到 iLO 控制台，然后右键单击服务器屏幕。

2. 选择 " **HTML5 控制台**"。

3. 在控制台中，选择 CD 图标，然后选择 CD/DVD 选项。

4. 选择 " **本地 ISO 文件**"。

5. 在对话框中，选择相关的 ISO 文件。

6. 依次单击左侧图标、" **电源**" 和 " **重置**"。

7. 设备将重新启动并运行传感器安装过程。

### <a name="software-installation-hpe-dl360"></a>软件安装 (HPE DL360) 

安装过程大约需20分钟。 安装后，系统将重新启动多次。

若要进行安装：

1. 将屏幕和键盘连接到设备，然后连接到 CLI。

2. 使用从 IoT 门户 Defender 中的 **更新** 页面下载的 ISO 映像将密钥连接到密钥。

3. 启动设备。

4. 选择 " **英语**"。

5. 选择 " **传感器-发布- <version> 企业**"。

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="屏幕截图，显示选择版本。":::

6. 在安装向导中，定义设备配置文件和网络属性。

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="显示安装向导的屏幕截图。":::

    | 参数 | 配置 |
    | ----------| ------------- |
    | **硬件配置文件** | 选择 " **公司**"。 |
    | **管理界面** | **eno2** |
    | **(客户提供的默认网络参数)** | **管理网络 IP 地址：** <br/>**子网掩码：** <br/>**设备主机名：** <br/>**DNS** <br/>**默认网关 IP 地址：**|
    | **输入接口：**  | 系统会为您生成一个输入接口列表。<br/><br/>若要镜像输入接口，请复制列表中以逗号分隔显示的所有项目。<br/><br/>请注意，无需配置桥接口。 此选项仅用于特殊用例。 |

7. 大约10分钟后，将显示两组凭据。 一个适用于 **CyberX** 用户，一个用于 **支持** 用户。

8. 保存设备的 ID 和密码。 首次访问平台时，需要使用这些凭据。

9. 选择 **Enter** 以继续。

## <a name="sensor-installation-for-the-virtual-appliance"></a>虚拟设备的传感器安装

可以在以下体系结构中为用于 IoT 的 Defender 传感器部署虚拟机：


| 体系结构 | 规格 | 用法 | 注释 |
|---|---|---|---|
| **企业** | CPU：8<br/>内存： 32G RAM<br/>HDD： 1800 GB | 生产环境 | 默认值和最常见 |
| **小型企业** | CPU：4 <br/>内存： 8G RAM<br/>HDD： 500 GB | 测试或小型生产环境 | -  |
| **Office** | CPU：4<br/>内存： 8G RAM<br/>HDD： 100 GB | 小型测试环境 | -  |

### <a name="prerequisites"></a>先决条件

本地管理控制台支持 VMware 和 Hyper-v 部署选项。 在开始安装之前，请确保具有以下各项：

  - VMware (ESXi 5.5 或更高版本) 或 Hyper-v 虚拟机监控程序 (Windows 10 专业版或企业版) 已安装并正常运行

  - 虚拟机的可用硬件资源

  - 用于 IoT 传感器的 Azure Defender 的 ISO 安装文件

请确保虚拟机监控程序正在运行。

### <a name="create-the-virtual-machine-esxi"></a> (ESXi 创建虚拟机) 

1. 登录到 ESXi，选择相关的 **数据存储**，然后选择 " **数据存储浏览器**"。

2. **上载** 图像并选择 " **关闭**"。

3. 中转到 " **虚拟机**"，并选择 " **创建/注册 VM**"。

4. 选择 " **创建新虚拟机**"，并选择 " **下一步**"。

5. 添加传感器名称并选择：

   - 兼容性： **&lt; 最新 &gt; ESXi 版本**

   - 来宾操作系统系列： **Linux**

   - 来宾 OS 版本： **Ubuntu Linux (64 位)**

6. 选择“**下一页**”。

7. 选择相关数据存储，然后选择 " **下一步**"。

8. 根据所需的体系结构更改虚拟硬件参数。

9. 对于 **CD/DVD 驱动器 1**，选择 " **数据存储" "iso 文件** "，然后选择之前上传的 iso 文件。

10. 选择“下一步” > “完成”。

### <a name="create-the-virtual-machine-hyper-v"></a> (Hyper-v 创建虚拟机) 

此过程介绍如何使用 Hyper-v 创建虚拟机。

若要创建虚拟机：

1. 在 Hyper-v 管理器中创建虚拟磁盘。

2. 选择 " **格式 = VHDX**"。

3. 选择 " **类型 = 动态扩展**"。

4. 输入 VHD 的名称和位置。

5. 根据体系结构) 输入所需的大小 (。   

6. 查看摘要并选择“完成”。

7. 在 " **操作** " 菜单上，创建新的虚拟机。

8. 输入虚拟机的名称。

9. 选择 "**指定代** 第  >  **1** 代"。

10. 根据体系结构) 指定 (内存分配，并选中 "动态内存" 复选框。

11. 根据服务器网络拓扑配置网络适配器。

12. 将之前创建的 VHDX 连接到虚拟机。

13. 查看摘要并选择“完成”。

14. 右键单击新虚拟机，然后选择 " **设置**"。

15. 选择 " **添加硬件** " 并添加新的网络适配器。

16. 选择将连接到传感器管理网络的虚拟交换机。

17. 根据体系结构)  (分配 CPU 资源。

18. 将管理控制台的 ISO 映像连接到虚拟 DVD 驱动器。

19. 启动虚拟机。

20. 在 " **操作** " 菜单上，选择 " **连接** " 以继续软件安装。

### <a name="software-installation-esxi-and-hyper-v"></a>软件安装 (ESXi 和 Hyper-v) 

本部分介绍 ESXi 和 Hyper-v 软件安装。

若要进行安装：

1. 打开虚拟机控制台。

2. VM 将从 ISO 映像启动，并将显示 "语言选择" 屏幕。 选择 " **英语**"。

3. 选择所需的体系结构。

4. 定义设备配置文件和网络属性：

    | 参数 | 配置 |
    | ----------| ------------- |
    | **硬件配置文件** | &lt;所需体系结构&gt; |
    | **管理界面** | **ens192** |
    | **客户 (提供网络参数)** | **管理网络 IP 地址：** <br/>**子网掩码：** <br/>**设备主机名：** <br/>**DNS** <br/>**默认网关：** <br/>**输入接口：**|
    | **桥接口：** | 无需配置桥接口。 此选项仅适用于特殊用例。 |

5. 输入 **Y** 接受设置。

6. 自动生成并显示登录凭据。 将用户名和密码复制到安全的位置，因为登录和管理需要这些用户名和密码。

   - **支持**：用户管理的管理用户。

   - **CyberX**：用于访问设备的 root 的等效项。

7. 设备将重新启动。

8. 通过以前配置的 IP 地址访问管理控制台： `https://ip_address` 。

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="显示对管理控制台的访问的屏幕截图。":::

## <a name="virtual-appliance-on-premises-management-console-installation"></a>虚拟设备：本地管理控制台安装

本地管理控制台 VM 支持以下体系结构：

| 体系结构 | 规格 | 用法 | 
|--|--|--|
| Enterprise <br/> (默认值和最常见)  | CPU：8 <br/>内存： 32G RAM<br/> HDD： 1.8 TB | 大型生产环境 | 
| Enterprise | CPU：4 <br/> 内存： 8G RAM<br/> HDD： 500 GB | 大型生产环境 |
| Enterprise | CPU：4 <br/>内存： 8G RAM <br/> HDD： 100 GB | 小型测试环境 | 
   
### <a name="prerequisites"></a>先决条件

本地管理控制台支持 VMware 和 Hyper-v 部署选项。 在开始安装之前，请验证以下各项：

- VMware (ESXi 5.5 或更高版本) 或 Hyper-v 虚拟机监控程序 (Windows 10 专业版或企业版) 已安装并且可正常运行。

- 硬件资源可用于虚拟机。

- 你具有本地管理控制台的 ISO 安装文件。
    
- 虚拟机监控程序正在运行。

### <a name="create-the-virtual-machine-esxi"></a> (ESXi 创建虚拟机) 

 (ESXi) 的 "创建虚拟机"：

1. 登录到 ESXi，选择相关的 **数据存储**，然后选择 " **数据存储浏览器**"。

2. 上载图像并选择 " **关闭**"。

3. 中转到 " **虚拟机**"。

4. 选择 " **创建/注册 VM**"。

5. 选择 " **创建新虚拟机** "，并选择 " **下一步**"。

6. 添加传感器名称并选择：

   - 兼容性 \<latest ESXi version>

   - 来宾操作系统系列： Linux

   - 来宾 OS 版本： Ubuntu Linux (64 位) 

7. 选择“**下一页**”。

8. 选择相关数据存储，然后选择 " **下一步**"。

9. 根据所需的体系结构更改虚拟硬件参数。

10. 对于 **CD/DVD 驱动器 1**，选择 " **数据存储" "iso 文件** "，然后选择之前上传的 iso 文件。

11. 选择“下一步” > “完成”。

### <a name="create-the-virtual-machine-hyper-v"></a> (Hyper-v 创建虚拟机) 

使用 Hyper-v 创建虚拟机：

1. 在 Hyper-v 管理器中创建虚拟磁盘。

2. 选择 " **VHDX** 格式"。

3. 选择“**下一页**”。

4. 选择类型 " **动态扩展**"。

5. 选择“**下一页**”。

6. 输入 VHD 的名称和位置。

7. 选择“**下一页**”。

8. 根据体系结构) 输入所需的大小 (。

9. 选择“**下一页**”。

10. 查看摘要并选择“完成”。

11. 在 " **操作** " 菜单上，创建新的虚拟机。

12. 选择“**下一页**”。

13. 输入虚拟机的名称。

14. 选择“**下一页**”。

15. 选择 " **生成** " 并将其设置为 **第1代**。

16. 选择“**下一页**”。

17. 根据体系结构) 指定 (内存分配，并选中 "动态内存" 复选框。

18. 选择“**下一页**”。

19. 根据服务器网络拓扑配置网络适配器。

20. 选择“**下一页**”。

21. 将之前创建的 VHDX 连接到虚拟机。

22. 选择“**下一页**”。

23. 查看摘要并选择“完成”。

24. 右键单击新的虚拟机，然后选择 " **设置**"。

25. 选择 " **添加硬件** " 并添加 **网络适配器** 的新适配器。

26. 对于 " **虚拟交换机**"，选择将连接到传感器管理网络的交换机。

27. 根据体系结构)  (分配 CPU 资源。

28. 将管理控制台的 ISO 映像连接到虚拟 DVD 驱动器。

29. 启动虚拟机。

30. 在 " **操作** " 菜单上，选择 " **连接** " 以继续软件安装。

### <a name="software-installation-esxi-and-hyper-v"></a>软件安装 (ESXi 和 Hyper-v) 

启动虚拟机将从 ISO 映像启动安装过程。

安装软件：

1. 选择 " **英语**"。

2. 为部署选择所需的体系结构。

3. 定义传感器管理网络的网络接口：接口、IP、子网、DNS 服务器和默认网关。

4. 自动生成并显示登录凭据。 请将这些凭据保存在安全的位置，因为它们对于登录和管理是必需的。

  - **支持**：用户管理的管理用户。

  - **CyberX**：用于访问设备的 root 的等效项。

5. 设备将重新启动。

6. 通过以前配置的 IP 地址访问管理控制台： `<https://ip_address>` 。

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="屏幕截图，显示管理控制台的登录屏幕。":::

## <a name="post-installation-validation"></a>安装后验证

若要验证物理设备的安装，需要执行多个测试。 同一验证过程适用于所有设备类型。

使用 GUI 或 CLI 执行验证。 用户 **支持** 和用户 **CyberX** 可以使用验证。

安装后验证必须包括以下测试：

  - **稳定测试**：验证系统是否正在运行。

  - **版本**：验证版本是否正确。

  - **ifconfig**：验证安装过程中配置的所有输入接口是否正在运行。

### <a name="checking-system-health-by-using-the-gui"></a>使用 GUI 检查系统运行状况

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="显示系统运行状况检查的屏幕截图。":::

#### <a name="sanity"></a>健全

- **设备**：运行 "设备健全检查"。 可以使用 CLI 命令执行相同的检查 `system-sanity` 。

- **版本**：显示设备版本。

- **网络属性**：显示传感器网络参数。

#### <a name="redis"></a>Redis

- **内存**：提供内存使用量的整体图，如使用的内存量和保留的内存量。

- **最长密钥**：显示可能会导致内存使用的最长的密钥。

#### <a name="system"></a>系统

- **核心日志**：提供核心日志的最后500行，使你能够查看最近的日志行，而无需导出整个系统日志。

- **任务管理器**：将进程表中出现的任务转换为以下层： 
  
  - 永久性层 (Redis)  
  -  (SQL) 的现金层

- **网络统计信息**：显示网络统计信息。

- **TOP**：显示进程表。 它是一个 Linux 命令，提供正在运行的系统的动态实时视图。

- **备份内存检查**：提供备份内存的状态，并检查以下各项：
  - 备份文件夹的位置 
  - 备份文件夹的大小
  - 备份文件夹的限制
  - 上次备份发生的时间
  - 额外备份文件有多少空间

- **ifconfig**：显示设备的物理接口的参数。

- **CyberX 下载**：使用六秒测试显示网络流量和带宽。

- **Core** 日志中的错误：显示核心日志文件中的错误。

访问工具：

1. 用 **支持** 用户凭据登录到传感器。

2. 从 "**系统设置**" 窗口中选择 "**系统统计信息**"。

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>使用 CLI 检查系统运行状况

**测试1：稳定**

验证系统是否已启动并正在运行：

1. 通过 Linux 终端 (（例如，PuTTY) 和用户 **支持**）连接到 CLI。

2. 输入 `system sanity`。

3. 检查所有服务是否均为绿色 () 运行。

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="显示正在运行的服务的屏幕截图。":::

4. 验证 **系统是否正常运行！ (生产)** 显示在底部。

**测试2：版本检查**

验证是否使用了正确的版本：

1. 通过 Linux 终端 (（例如，PuTTY) 和用户 **支持**）连接到 CLI。

2. 输入 `system version`。

3. 检查是否显示了正确的版本。

**测试3：网络验证**

验证安装过程中配置的所有输入接口是否正在运行：

1. 通过 Linux 终端 (（例如，PuTTY) 和用户 **支持**）连接到 CLI。

2. 输入 `network list` (Linux 命令) 的等效项 `ifconfig` 。

3. 验证是否显示了所需的输入接口。 例如，如果安装了两个四核 Nic，则列表中应有10个接口。

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="显示接口列表的屏幕截图。":::

**测试4：对 UI 的管理访问权限**

验证是否可以访问控制台 web GUI：

1. 使用以太网电缆将便携式计算机连接到管理端口 (**Gb1**) 。

2. 将便携式计算机 NIC 地址定义为与设备相同的范围。

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="显示对 UI 的管理访问权限的屏幕截图。":::

3. 从笔记本电脑对设备的 IP 地址进行 Ping 操作，以验证连接 (默认值： 10.100.10.1) 。

4. 在便携式计算机上打开 Chrome 浏览器，然后输入设备的 IP 地址。

5. 在 " **你的连接不是专用** " 窗口中，选择 " **高级** " 并继续。

6. 当显示 "IoT 登录" 屏幕时，测试成功。

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="显示对管理控制台的访问的屏幕截图。":::

## <a name="troubleshooting"></a>疑难解答

### <a name="you-cant-connect-by-using-a-web-interface"></a>不能使用 web 界面进行连接

1. 验证你尝试连接的计算机是否与设备位于同一网络上。

2. 验证 GUI 网络是否已连接到管理端口。

3. 对设备的 IP 地址进行 Ping 操作。 如果没有 ping：

   1. 将监视器和键盘连接到设备。

   1. 使用 **支持** 用户和密码进行登录。

   1. 使用命令 `network list` 查看当前 IP 地址。

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="显示网络列表的屏幕截图。":::

4. 如果网络参数配置错误，请使用以下过程来更改它们：

   1. 使用命令 `network edit-settings` 。

   1. 若要更改管理网络 IP 地址，请选择 " **Y**"。

   1. 若要更改子网掩码，请选择 **Y**。

   1. 若要更改 DNS，请选择 **Y**。

   1. 若要更改默认网关 IP 地址，请选择 " **Y**"。

   1. 对于输入接口 (仅) 的传感器更改，请选择 " **N**"。

   1. 若要应用设置，请选择 " **Y**"。

5. 重新启动后，请使用支持用户凭据进行连接，并使用 `network list` 命令验证参数是否已更改。

6. 尝试 ping 并再次从 GUI 连接。

### <a name="the-appliance-isnt-responding"></a>设备未响应

1. 将监视器和键盘连接到设备，或使用 PuTTY 远程连接到 CLI。

2. 使用 **支持** 用户的凭据登录。

3. 使用 `system sanity` 命令并检查所有进程是否正在运行。

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="显示系统健全命令的屏幕截图。":::

对于任何其他问题，请联系 [Microsoft 支持部门](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>附录 A： vSwitch 上的镜像端口 (ESXi) 

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>在现有 vSwitch 上配置 SPAN 端口

VSwitch 没有镜像功能，但你可以使用一种简单的方法来实现 SPAN 端口。

配置 SPAN 端口：

1. 打开 vSwitch 属性。

2. 选择 **添加** 。

3. 选择 **"**  >  **下一步**"。

4. 插入网络标签 **SPAN 网络**，选择 "**所有 VLAN ID**  >  "，然后选择 "**下一步**"。

5. 选择“完成”。

6. 选择 " **跨网络** >" "*编辑*"。

7. 选择 " **安全性**"，并确认 " **混杂模式** " 策略设置为 " **接受** " 模式。

8. 选择 **"确定"**，然后选择 " **关闭** " 以关闭 vSwitch 属性。

9. 打开 **XSENSE VM** 属性。

10. 对于 " **网络适配器 2**"，请选择 " **SPAN** 网络"。

11. 选择“确定”  。

12. 连接到传感器，并验证镜像是否正常工作。

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>附录 B：从本地管理控制台访问传感器

可以通过阻止直接用户访问传感器来增强系统安全性。 请改用代理隧道，让用户使用单个防火墙规则从本地管理控制台访问传感器。 此方法可缩小对超出传感器的网络环境进行未经授权访问的可能性。 用户登录传感器时的体验保持不变。

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="显示对传感器的访问的屏幕截图。":::

启用隧道：

1. 通过 **CyberX** 登录到本地管理控制台的 CLI 或 **支持** 用户凭据。

2. 输入 `sudo cyberx-management-tunnel-enable`。

3. 选择 **Enter**。

4. 输入 `--port 10000`。

### <a name="next-steps"></a>后续步骤

[设置网络](how-to-set-up-your-network.md)
