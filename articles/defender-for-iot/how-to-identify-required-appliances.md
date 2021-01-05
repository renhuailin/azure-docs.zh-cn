---
title: 确定所需的设备
description: 了解用于 IoT 传感器和本地管理控制台的经过认证的 Defender 的硬件和虚拟设备。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/21/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b22f880eee1b691b6b50b8151a64a2d501b891b3
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838293"
---
# <a name="identify-required-appliances"></a>确定所需的设备

本文提供了有关 IoT 传感器设备的经过认证的 Defender 的信息。 可以在物理和虚拟设备上部署 Defender fort IoT。

这包括经过认证的 *预配置* 设备、已安装的软件，以及你可在其上下载和安装所需软件的未配置认证设备。

本文还提供了本地管理控制台设备的规范。 本地管理控制台不可作为预配置的设备。

- 如果要购买预配置的传感器，请查看 " [传感器设备](#sensor-appliances) " 部分中提供的型号，然后继续购买。

- 如果要购买自己的设备，请查看 " [传感器设备](#sensor-appliances) " 部分和 " [其他认证的设备](#additional-certified-appliances) " 部分中提供的模型。 获取设备后，你可以下载并安装软件。

- 如果要购买本地管理控制台，请查看 [本地管理控制台设备](#on-premises-management-console-appliance) 部分中的信息。 获取设备后，你可以下载并安装软件。

完成此处的任务后，可以安装软件并设置网络。

## <a name="sensor-appliances"></a>传感器设备

用于 IoT 的 Defender 同时支持物理部署和虚拟部署。

### <a name="physical-sensors"></a>物理传感器

本部分提供可用的物理传感器型号的概述。 你可以购买包含预配置软件的传感器，或购买未预先配置的传感器。

| 部署类型 | 企业 | Enterprise | SMB |
|--|--|--|--|
| 图像 | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="公司级别的模型。"::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="企业级模型。"::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="SMB 级别的模型。"::: |
| 型号 | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 |
| 监视端口 | 最多15个 RJ45 或8个 OPT | 最多8个 RJ45 或 6 OPT | 4 RJ45 |
| 最大带宽 [1](#anchortext) | 每秒 3 Gb | 每秒 1 Gb | 每秒 200 Mb |
| 受保护的最大设备 | 30,000 | 15,000 | 1,000 |

有关供应商的详细信息，请参阅 [设备规格](#appliance-specifications) 。

关于预配置传感器： Microsoft 已与箭头合作以提供预配置的传感器。 若要购买预配置的传感器，请在以下地址中使用联系人箭头： <hardware.sales@arrow.com>

关于携带你自己的设备：查看此处所述的支持模型。 获取设备后，请跳到用于 **IoT**  >  **网络传感器 ISO**  >  **安装** 的 Defender 以下载软件。

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="网络传感器 ISO。":::

<a id="anchortext">1</a> 根据协议分发情况，带宽容量可能会有所不同。

### <a name="virtual-sensors"></a>虚拟传感器

本部分提供可用虚拟传感器的概述。

| 部署类型 | 企业 | Enterprise | SMB |
|--|--|--|--|
| 最大带宽 | 2.5 Gb/秒 | 800 Mb/秒 | 160 Mb/秒 |
| 受保护的最大设备 | 30,000 | 10,000 | 2,500 |

## <a name="on-premises-management-console-appliance"></a>本地管理控制台设备

管理控制台作为虚拟部署提供。

| 部署类型 | Enterprise |
|--|--|
| 设备类型 | HPE DL20，VM |
| 托管传感器数量 | 最大 300 |

获取本地管理控制台后，请使用 "用于 **IoT**  >  **本地管理控制台** 的 Defender  >  "**iso 安装** 下载 iso。

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="本地管理控制台。":::

## <a name="appliance-specifications"></a>设备规范

本部分介绍以下设备的硬件规范：

- 企业部署： HPE ProLiant DL360

- 企业部署： HPE ProLiant DL20

- SMB 部署： HPE ProLiant DL20

- 虚拟设备规范

## <a name="corporate-deployment-hpe-proliant-dl360"></a>企业部署： HPE ProLiant DL360

| 组件 | 技术规范 |
|--|--|
| 底盘 | 1U 机架服务器 |
| 维度 | 42.9 x 43.46 x 70.7 (厘米) /1.69 "x 17.11" x 27.83 " ()  |
| 重量 | 最大16.27 千克 (35.86 lb)  |
| 处理器 | 3.2 4215 Intel 11 分钟 cache、8c/16T、130 W |
| 高速芯片组家族 | Intel C621 |
| 内存 | 32 GB = 2 x 16-GB 2666MT/s DDR4 ECC UDIMM |
| 存储 | 6 x 1.2-TB SAS 12G Enterprise 10K SFF (2.5 in) in Hot-Plug 硬盘驱动器-RAID 5 |
| 网络控制器 | 机载： 2 x 1-Gb Broadcom BCM5720<br>机载 LOM： iDRAC 端口卡 1-Gb Broadcom BCM5720<br><br>外部： 1 x Intel 以太网 i350 QP 1-Gb 服务器适配器，低配置文件 |
| 管理 | HPE iLO 高级 |
| 设备访问 | 两个后端 USB 3。0<br>一台前端 USB 2。0<br>一个内部 USB 3。0 |
| 强力 | 2 x HPE 500 W 弯曲槽顶级热插拔低 Halogen 电源套件 |
| 机架支持 | HPE 1U Gen10 SFF 轻松安装导轨套件 |

### <a name="appliance-bom"></a>设备 BOM

| PN | 说明 | 数量 |
|--|--|--|
| P19766-B21 | HPE DL360 Gen10 8SFF NC CTO 服务器 | 1 |
| P19766-B21 | 欧洲-多语言本地化 | 1 |
| P24479-L21 | 适用于 DL360 G10 的 Intel FIO-S 4215 R | 1 |
| P24479-B21 | 适用于 DL360 Gen10 的 Intel 强-S 4215 R 包 | 1 |
| P00922-B21 | HPE 16-GB 2Rx8 PC4-2933Y-R 智能套件 | 2 |
| 872479-B21 | HPE 1.2-TB SAS 10K SFF SC DS HDD | 6 |
| 811546-B21 | HPE 1-GbE 4-p I350 适配器 | 1 |
| P02377-B21 | HPE 智能混合电容器（ \_ 145 毫米） | 1 |
| 804331-B21 | HPE 智能阵列 P408i-a SR Gen10 控制器 | 1 |
| 665240-B21 | HPE 1-GbE 4-p FLR-T I350 适配器 | 1 |
| 871244-B21 | HPE DL360 Gen10 高性能风扇套件 | 1 |
| 865408-B21 | HPE 500-W FS X-plat 热插拔 LH 电源套件 | 2 |
| 512485-B21 | HPE iLO 高级 1-Server License 1 年支持 | 1 |
| 874543-B21 | HPE 1U Gen10 SFF 轻松安装导轨套件 | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>企业部署： HPE ProLiant DL20

| 组件 | 技术规范 |
|--|--|
| 底盘 | 1U 机架服务器 |
| 尺寸 (高度 x 宽度 x 深度)  | 4.32 x 43.46 x 38.22 厘米/1.70 x 17.11 x 15.05 英寸 |
| 重量 | 7.9 千克/17.41 lb |
| 处理器 | Intel 强 E-2234，3.6 GHz，4C/8T，71 W |
| 高速芯片组家族 | Intel C242 |
| 内存 | 2 x 16 GB 双级别 x8 DDR4-2666 |
| 存储 | 3 x 1 TB SATA 6G 中线 7.2 K SFF (2.5 in) – RAID 5 （含智能阵列 P408i）-a SR 控制器 |
| 网络控制器 | 机载： 2 x 1 Gb <br>机载： iLO 端口卡 1 Gb <br>外部： 1 x HPE 以太网 1-Gb 4 端口366FLR 适配器 |
| 管理 | HPE iLO 高级 |
| 设备访问 | 前端： 1 x USB 3.0、1 x USB iLO 服务端口 <br>背面： 2 x USB 3。0 <br>内部： 1 x USB 3。0 |
| 强力 | 双热插拔电源 500 W |
| 机架支持 | HPE 1U 小小小导轨套件 |

### <a name="appliance-bom"></a>设备 BOM

| PN | 说明：高端 | 数量 |
|--|--|--|
| P06963-B21 | HPE DL20 Gen10 4SFF CTO 服务器 | 1 |
| P06963-B21 | HPE DL20 Gen10 4SFF CTO 服务器 | 1 |
| P17104-L21 | HPE DL20 Gen10 2234 FIO 工具包 | 1 |
| 879507-B21 | HPE 16-GB 2Rx8 PC4-2666V-E STND 工具包 | 2 |
| 655710-B21 | HPE 1-TB SATA 7.2 K SFF SC DS HDD | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM 转接套件 | 1 |
| 665240-B21 | HPE 以太网 1 Gb 4 端口366FLR 适配器 | 1 |
| 782961-B21 | HPE 12-W 智能存储电池 | 1 |
| 869081-B21 | HPE 智能阵列 P408i-a SR G10 LH 控制器 | 1 |
| 865408-B21 | HPE 500-W FS X-plat 热插拔 LH 电源套件 | 2 |
| 512485-B21 | HPE iLO 高级 1-Server License 1 年支持 | 1 |
| P06722-B21 | HPE DL20 Gen10 RPS 启用 FIO 工具包 | 1 |
| 775612-B21 | HPE 1U 小小小导轨套件 | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>SMB 部署： HPE ProLiant DL20

| 组件 | 技术规范 |
|--|--|
| 底盘 | 1U 机架服务器 |
| 尺寸 (高度 x 宽度 x 深度)  | 4.32 x 43.46 x 38.22 厘米/1.70 x 17.11 x 15.05 英寸 |
| 重量 | 7.88 千克/17.37 lb |
| 处理器 | Intel 强 E-2224、3.4 GHz、4C、71 W |
| 高速芯片组家族 | Intel C242 |
| 内存 | 1 x 8 GB 双级别 x8 DDR4-2666 |
| 存储 | 2 x 1 TB SATA 6G 中线 7.2 K SFF (2.5 in) – RAID 1 （含智能阵列 P208i） |
| 网络控制器 | 机载： 2 x 1 Gb <br>机载： iLO 端口卡 1 Gb <br>外部： 1 x HPE 以太网 1-Gb 4 端口366FLR 适配器 |
| 管理 | HPE iLO 高级 |
| 设备访问 | 前端： 1 x USB 3.0、1 x USB iLO 服务端口 <br>背面： 2 x USB 3。0 <br>内部： 1 x USB 3。0 |
| 强力 | 热插拔电源 290 W |
| 机架支持 | HPE 1U 小小小导轨套件 |

### <a name="appliance-bom"></a>设备 BOM

| PN | 说明 | 数量 |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO 服务器 | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO 服务器 | 1 |
| P17102-L21 | HPE DL20 Gen10 2224 FIO 工具包 | 1 |
| 879505-B21 | HPE 8-GB 1Rx8 PC4-2666V-E STND 工具包 | 1 |
| 801882-B21 | HPE 1-TB SATA 7.2 K LFF RW HDD | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM 转接套件 | 1 |
| 665240-B21 | HPE 以太网 1 Gb 4 端口366FLR 适配器 | 1 |
| 869079-B21 | HPE 智能阵列 E208i-a SR G10 LH 控制器 | 1 |
| P21649-B21 | HPE DL20 Gen10 X-plat 290 W FIO PSU 套件 | 1 |
| P06683-B21 | HPE DL20 Gen10） | 1 |
| 512485-B21 | HPE iLO 高级 1-Server License 1 年支持 | 1 |
| 775612-B21 | HPE 1U 小小小导轨套件 | 1 |

## <a name="virtual-appliance-specifications"></a>虚拟设备规范

### <a name="sensors"></a>Sensors

| 类型 | 企业 | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| 内存 | 32 GB | 32 GB | 8 GB |
| 存储 | 5.6 TB | 1.8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>本地管理控制台设备

| 类型 | Enterprise |
|--|--|
| 说明 | 适用于企业部署类型的虚拟设备 |
| vCPU | 8 |
| 内存 | 32 GB |
| 存储 | 1.8 TB |

支持的虚拟机监控程序： VMware ESXi 版本5.0 及更高版本、Hyper-v

## <a name="additional-certified-appliances"></a>其他认证的设备

本部分详细介绍了由 Microsoft 认证但未作为预配置设备提供的其他设备。

| 部署类型 | Enterprise |
|--|--|
| 图像 | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="企业部署类型。"::: |
| 型号 | Dell PowerEdge R340 XL |
| 监视端口 | 最多9个 RJ45 或六个 OPT |
| 最大带宽 [1](#anchortext2)| 1G Mb/秒 |
| 最大受保护设备 | 10,000 |

<a id="anchortext2">一个</a> 带宽容量可能因协议分发而异。

购买设备后，请跳到用于 **IoT**  >  **网络传感器 ISO**  >  **安装** 的 Defender 以下载软件。

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="网络传感器 ISO。":::

## <a name="enterprise-deployment-dell-poweredge-r340-xl"></a>企业部署： Dell PowerEdge R340 XL

| 组件 | 技术规范 |
|--|--|
| 底盘 | 1U 机架服务器 |
| 维度 | 42.8 x 434.0 x 596 (mm) /1.67 "x 17.09" x 23.5 " ()  |
| 重量 | 最大29.98 磅/13.6 公斤 |
| 处理器 | Intel 2144G 3.6 GHz、8分钟缓存、4C/8T、turbo (71 W)  |
| 高速芯片组家族 | Intel C246 |
| 内存 | 32 GB = 2 x 16-GB 2666MT/s DDR4 ECC UDIMM |
| 存储 | 3 x 2-TB 7.2 K RPM SATA 6-Gbps 512n 3.5-in Hot-Plug 硬盘驱动器-RAID 5 |
| 网络控制器 | 机载： 2 x 1-Gb Broadcom BCM5720<br>机载 LOM： iDRAC 端口卡 1-Gb Broadcom BCM5720 <br><br>外部： 1 x Intel 以太网 i350 QP 1-Gb 服务器适配器，低配置文件 |
| 管理 | iDRAC 九点企业 |
| 设备访问 | 两个后端 USB 3。0 <br> 一台前端 USB 3。0 |
| 强力 | 双热插拔电源 350 W |
| 机架支持 | 用于无工具装载的 ReadyRails II 滑动滑轨，适用于带方形或 unthreaded 圆形孔的4后架，或 4-后架架架中的配置装载： |

## <a name="dell-r340-bom"></a>Dell R340 BOM

:::image type="content" source="media/how-to-prepare-your-network/enterprise-deployment-for-azure-defender-for-iot-dell-r340-bom.png" alt-text="Dell R340 BOM。":::

## <a name="smb-deployment-neousys-nuvo-5006lp"></a>SMB 部署： Neousys Nuvo-5006LP

| 组件 | 技术规范 |
|--|--|
| 建筑 | 铝、fanless 和灰尘考验设计 |
| 维度 | 240 mm (W) x 225 mm (D) x 77 mm (H)  |
| 重量 | 3.1 千克 (包括 CPU、内存和 HDD)  |
| CPU | Intel Core i5-6500TE (6 分钟缓存，最高 3.30 GHz) S1151 |
| 高速芯片组家族 | Intel Q170 Platform 控制器中心 |
| 内存 | 8 GB DDR4 2133 MHz 宽温度 SODIMM |
| 存储 | 128-GB 3ME3 宽温度 mSATA SSD |
| 网络控制器 | 6倍千兆以太网端口（由 Intel I219） |
| 设备访问 | 4 USBs：两个正面，两个 rears，一个内部 |
| 电源适配器 | 120/240VAC-20VDC/6A |
| 安装 | 安装套件，下导轨 |
| 操作温度 | \-25° C ~ 70 ° C |
| 存储温度 | \-40° C ~ 85 ° C |
| 湿度 | 10% ~ 90%，非冷凝 |
| 振动 | 操作，5 Grms，5-500 Hz，3个轴 <br>根据 IEC60068-2-64)  (w/SSD |
| 撞击 | 按照 IEC60068-2-27) 操作，50 Grms，半正弦 11 ms duration (w/SSD |
| EMC | CE/FCC 类 A，根据 EN 55022、EN 55024 和 EN 55032 |

## <a name="next-steps"></a>后续步骤

[关于用于 IoT 安装的 Azure Defender](how-to-install-software.md)

[关于用于 IoT 的 Azure Defender 网络设置](how-to-set-up-your-network.md)
