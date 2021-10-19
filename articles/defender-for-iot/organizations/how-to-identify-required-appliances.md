---
title: 确定所需设备
description: 了解用于经认证的 Defender for IoT 传感器和本地管理控制台的硬件和虚拟设备。
ms.date: 06/21/2021
ms.topic: how-to
ms.openlocfilehash: c124ccd69257b9a0b7a57c53b6fa6b0817d95fb6
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754342"
---
# <a name="identify-required-appliances"></a>确定所需设备

本文介绍经认证的 Defender for IoT 传感器设备相关信息。 可以在物理和虚拟设备上部署 Defender for IoT。

这包括经认证的预配置设备（已在其上安装软件），以及经认证的未配置设备（可在其上下载和安装所需软件）。

本文还提供了本地管理控制台设备的规范。 本地管理控制台不可作为预配置的设备。

- 如果要购买预配置的传感器，请查看[传感器设备](#sensor-appliances)部分中提供的型号，然后继续购买。

- 如果要购买自己的设备，请查看[传感器设备](#sensor-appliances)部分和[其他认证设备](#additional-certified-appliances)部分中提供的型号。 获取设备后，可以下载并安装软件。

- 如果要购买本地管理控制台，请查看[本地管理控制台设备](#on-premises-management-console-appliance)部分中的信息。 获取设备后，可以下载并安装软件。

完成此处的任务后，可以安装软件并设置网络。

## <a name="sensor-appliances"></a>传感器设备

Defender for IoT 支持物理和虚拟部署。

### <a name="physical-sensors"></a>物理传感器

本部分提供可用的物理传感器型号的概述。 可以购买预配置了软件的传感器，或购买未进行预配置的传感器。

| 部署类型 | 企业 | Enterprise | SMB 机架装载| SMB 加固型 |
|--|--|--|--|--|
| 映像 | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="公司级别型号。"::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="企业级别型号。"::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="SMB 级别型号。"::: | :::image type="content" source="media/how-to-prepare-your-network/office-ruggedized.png" alt-text="SMB 加固型级别型号。"::: |
| 建模 | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 | HPE EL300 |
| 监视端口 | 多达 15 个 RJ45 或 8 个 OPT | 多达 8 个 RJ45 或 6 个 OPT | 最多 4 个 RJ45 | 最多 5 个 RJ45 |
| 最大带宽 [1](#anchortext) | 3 Gb/秒 | 1 Gb/秒 | 200 Mb/秒 | 100 Mb/秒 |
| 最大受保护设备数 | 10,000 | 10,000 | 1,000 | 800 |

有关供应商的详细信息，请参阅[设备规格](#appliance-specifications)。

关于预配置的传感器：Microsoft 已与 Arrow 合作以提供预配置的传感器。 若要购买预配置的传感器，请通过以下地址与 Arrow 联系：<hardware.sales@arrow.com>

关于携带自己的设备：请查看此处所述的支持型号。 购买设备后，请转到“Defender for IoT” > “网络传感器 ISO” > “安装”以下载软件  。

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="网络传感器 ISO。":::

<a id="anchortext">1</a> 根据协议分发情况，带宽容量可能有所不同。

### <a name="virtual-sensors"></a>虚拟传感器

本部分介绍可用的虚拟传感器。

| 部署类型 | 企业 | Enterprise | SMB |
|--|--|--|--|
| 最大带宽 | 2.5 Gb/秒 | 800 Mb/秒 | 160 Mb/秒 |
| 最大受保护设备数 | 10,000 | 10,000 | 800 |

## <a name="on-premises-management-console-appliance"></a>本地管理控制台设备

管理控制台作为虚拟部署提供。

| 部署类型 | Enterprise |
|--|--|
| 设备类型 | HPE DL20, VM |
| 托管传感器数 | 最大 300 |

获取本地管理控制台后，请转到“Defender for IoT” > “本地管理控制台” > “ISO 安装”以下载 ISO  。

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="本地管理控制台。":::

## <a name="appliance-specifications"></a>设备规范

本部分介绍以下设备的硬件规范：

- 公司部署：HPE ProLiant DL360

- 企业部署：HPE ProLiant DL20

- SMB 部署：HPE ProLiant DL20

- 虚拟设备规范

## <a name="corporate-deployment-hpe-proliant-dl360"></a>公司部署：HPE ProLiant DL360

| 组件 | 技术规范 |
|--|--|
| 底盘 | 1U 机架服务器 |
| 维度 | 42.9 x 43.46 x 70.7 (cm)/1.69" x 17.11" x 27.83" (in) |
| 重量 | 最大 16.27 kg (35.86 lb) |
| 处理器 | Intel Xeon Silver 4215 R 3.2 GHz，11M 缓存，8c/16T，130 W |
| 芯片组 | Intel C621 |
| 内存 | 32 GB = 2 x 16-GB 2666MT/s DDR4 ECC UDIMM |
| 存储 | 热插播硬盘驱动器 - RAID 5：6 x 1.2-TB SAS 12G Enterprise 10K SFF (2.5 in) |
| 网络控制器 | 机载：2 x 1-Gb Broadcom BCM5720<br>机载 LOM：iDRAC 端口卡 1-Gb Broadcom BCM5720<br><br>外部：1 x Intel 以太网 i350 QP 1-Gb 服务器适配器，低矮 |
| 管理 | HPE iLO（高级） |
| 设备访问 | 两个后端 USB 3.0<br>一个前端 USB 2.0<br>一个内部 USB 3.0 |
| 强力 | 2 x HPE 500 W Flex 插槽铂金热插拔低卤素电源套件 |
| 机架支撑架 | HPE 1U Gen10 SFF 易安装导轨套件 |

### <a name="appliance-bom"></a>设备 BOM

| PN | 说明 | 数量 |
|--|--|--|
| P19766-B21 | HPE DL360 Gen10 8SFF NC CTO 服务器 | 1 |
| P19766-B21 | 欧洲 - 多语言本地化 | 1 |
| P24479-L21 | 适用于 DL360 G10 的 Intel Xeon-S 4215 R FIO 套件 | 1 |
| P24479-B21 | 适用于 DL360 Gen10 的 Intel Xeon-S 4215 R 套件 | 1 |
| P00922-B21 | HPE 16-GB 2Rx8 PC4-2933Y-R 智能套件 | 2 |
| 872479-B21 | HPE 1.2-TB SAS 10K SFF SC DS HDD | 6 |
| 811546-B21 | HPE 1-GbE 4-p BASE-T I350 适配器 | 1 |
| P02377-B21 | HPE 智能混合电容器 \_ 145 mm 电缆 | 1 |
| 804331-B21 | HPE 智能阵列 P408i-a SR Gen10 控制器 | 1 |
| 665240-B21 | HPE 1-GbE 4-p FLR-T I350 适配器 | 1 |
| 871244-B21 | HPE DL360 Gen10 高性能风扇套件 | 1 |
| 865408-B21 | HPE 500-W FS Plat 热插拔 LH 电源套件 | 2 |
| 512485-B21 | HPE iLO Adv 1-Server 许可证 1 年支持 | 1 |
| 874543-B21 | HPE 1U Gen10 SFF 易安装导轨套件 | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>企业部署：HPE ProLiant DL20

| 组件 | 技术规范 |
|--|--|
| 底盘 | 1U 机架服务器 |
| 尺寸（高 x 宽 x 深） | 4.32 x 43.46 x 38.22 cm/1.70 x 17.11 x 15.05 in |
| 重量 | 7.9 kg/17.41 lb |
| 处理器 | Intel Xeon E-2234, 3.6 GHz, 4C/8T, 71 W |
| 芯片组 | Intel C242 |
| 内存 | 2 x 16 GB 双面颗粒 x8 DDR4-2666 |
| 存储 | 3 x 1-TB SATA 6G Midline 7.2 K SFF (2.5 in) - RAID 5，含智能阵列 P408i-a SR 控制器 |
| 网络控制器 | 机载：2 x 1 Gb <br>机载：iLO 端口卡 1 Gb <br>外部：1 x HPE 以太网 1-Gb 4 端口 366FLR 适配器 |
| 管理 | HPE iLO（高级） |
| 设备访问 | 前面：1 x USB 3.0，1 x USB iLO 服务端口 <br>后面：2 x USB 3.0 <br>内部：1 x USB 3.0 |
| 强力 | 双热插拔电源 500 W |
| 机架支撑架 | HPE 1U 短摩擦导轨套件 |

### <a name="appliance-bom"></a>设备 BOM

| PN | 说明：高端 | 数量 |
|--|--|--|
| P06963-B21 | HPE DL20 Gen10 4SFF CTO 服务器 | 1 |
| P06963-B21 | HPE DL20 Gen10 4SFF CTO 服务器 | 1 |
| P17104-L21 | HPE DL20 Gen10 E-2234 FIO 套件 | 1 |
| 879507-B21 | HPE 16-GB 2Rx8 PC4-2666V-E STND 套件 | 2 |
| 655710-B21 | HPE 1-TB SATA 7.2 K SFF SC DS HDD | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM Riser 套件 | 1 |
| 665240-B21 | HPE 以太网 1-Gb 4 端口 366FLR 适配器 | 1 |
| 782961-B21 | HPE 12-W 智能蓄电池 | 1 |
| 869081-B21 | HPE 智能阵列 P408i-a SR G10 LH 控制器 | 1 |
| 865408-B21 | HPE 500-W FS Plat 热插拔 LH 电源套件 | 2 |
| 512485-B21 | HPE iLO Adv 1-Server 许可证 1 年支持 | 1 |
| P06722-B21 | HPE DL20 Gen10 RPS Enablement FIO 套件 | 1 |
| 775612-B21 | HPE 1U 短摩擦导轨套件 | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>SMB 部署：HPE ProLiant DL20

| 组件 | 技术规范 |
|--|--|
| 底盘 | 1U 机架服务器 |
| 尺寸（高 x 宽 x 深） | 4.32 x 43.46 x 38.22 cm/1.70 x 17.11 x 15.05 in |
| 重量 | 7.88 kg/17.37 lb |
| 处理器 | Intel Xeon E-2224, 3.4 GHz, 4C, 71 W |
| 芯片组 | Intel C242 |
| 内存 | 1 x 8-GB 双面颗粒 x8 DDR4-2666 |
| 存储 | 2 x 1-TB SATA 6G Midline 7.2 K SFF (2.5 in) - RAID 1，含智能阵列 P208i-a |
| 网络控制器 | 机载：2 x 1 Gb <br>机载：iLO 端口卡 1 Gb <br>外部：1 x HPE 以太网 1-Gb 4 端口 366FLR 适配器 |
| 管理 | HPE iLO（高级） |
| 设备访问 | 前面：1 x USB 3.0，1 x USB iLO 服务端口 <br>后面：2 x USB 3.0 <br>内部：1 x USB 3.0 |
| 强力 | 热插拔电源 290 W |
| 机架支撑架 | HPE 1U 短摩擦导轨套件 |

### <a name="appliance-bom"></a>设备 BOM

| PN | 说明 | 数量 |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO 服务器 | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO 服务器 | 1 |
| P17102-L21 | HPE DL20 Gen10 E-2224 FIO 套件 | 1 |
| 879505-B21 | HPE 8-GB 1Rx8 PC4-2666V-E STND 套件 | 1 |
| 801882-B21 | HPE 1-TB SATA 7.2 K LFF RW HDD | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM Riser 套件 | 1 |
| 665240-B21 | HPE 以太网 1-Gb 4 端口 366FLR 适配器 | 1 |
| 869079-B21 | HPE 智能阵列 E208i-a SR G10 LH 控制器 | 1 |
| P21649-B21 | HPE DL20 Gen10 Plat 290 W FIO PSU 套件 | 1 |
| P06683-B21 | HPE DL20 Gen10 M.2 SATA/LFF AROC 电缆套件 | 1 |
| 512485-B21 | HPE iLO Adv 1-Server 许可证 1 年支持 | 1 |
| 775612-B21 | HPE 1U 短摩擦导轨套件 | 1 |

## <a name="smb-rugged-hpe-edgeline-el300"></a>SMB 加固型：HPE Edgeline EL300

| 组件 | 技术规范 |
|--|--|
| 建筑 | 铝质，无风扇和防灰尘设计 |
| 尺寸（高 x 宽 x 深） | 200.5 毫米（7.9 英寸）高 x 232 毫米（9.14 英寸）宽 x 100 毫米（3.9 英寸）深 |
| 重量 | 4.91 KG (10.83 lbs.) |
| CPU | Intel Core i7-8650U (1.9GHz/4-core/15W) |
| 芯片组 | Intel® Q170 平台控制器中心 |
| 内存 | 8 GB DDR4 2133 MHz 宽温 SODIMM |
| 存储 | 128 GB 3ME3 宽温 mSATA SSD |
| 网络控制器 | Intel® I219 的 6x Gigabit 以太网端口 |
| 设备访问  | 4 USB：前面 2 个，后面 2 个，内部 1 个 |
| 电源适配器 | 250V/10A |
| 安装 | 安装套件，德标导轨 |
| 工作温度 | 0C 到 +70C  |
| 湿度 | 10%~90%，非冷凝 |
| 振动 | 0.3 克 10 Hz 至 300 Hz，每轴 15 分钟 - 德标导轨   |
| 撞击 | 10G 10 毫秒，半正弦（每个轴三个）。 （正脉冲和负脉冲）- 德标导轨 |

### <a name="appliance-bom"></a>设备 BOM
| 产品 | 说明 |
|--|--|
| P25828-B21 | HPE Edgeline EL300 v2 聚合边缘系统 |
| P25828-B21 B19 | HPE EL300 v2 聚合边缘系统 |
| P25833-B21 | 适用于 HPE Edgeline EL300 的 Intel Core i7-8650U（1.9 GHz/4 核/15W）FIO 基本处理器套件 |
| P09176-B21 | HPE Edgeline 8GB (1x8GB) 双面颗粒 x8 DDR4-2666 SODIMM WT CAS-19-19-19 寄存器内存 FIO 套件 |
| P09188-B21 | HPE Edgeline 256GB SATA 6G 读取密集型 M.2 2242 3yr Wty 宽温 SSD |
| P04054-B21 | HPE Edgeline EL300 SFF to M.2 Enablement 套件 |
| P08120-B21 | HPE Edgeline EL300 12VDC FIO 转接板 |
| P08641-B21 | HPE Edgeline EL300 80W 12VDC 电源 |
| AF564A | HPE C13-32 IL 250V 10Amp 1.83 m 电源线 |
| P25835-B21 | HPE EL300 v2 FIO 承接板 |
| R1P49AAE | HPE EL300 iSM Adv 3yr 24x7 Sup_Upd E-LTU |
| P08018-B21（可选） | HPE Edgeline EL300 低矮支架套件  |
| P08019-B21（可选） | HPE Edgeline EL300 德标导轨安装套件 |
| P08020-B21（可选） | HPE Edgeline EL300 壁挂式套件 |
| P03456-B21（可选） | HPE Edgeline 1GbE 4-port TSN FIO 子卡 |

## <a name="virtual-appliance-specifications"></a>虚拟设备规范

### <a name="sensors"></a>传感器

| 类型 | 企业 | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| 内存 | 32 GB | 32 GB | 8 GB |
| 存储 | 5.6 TB | 1.8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>本地管理控制台设备

| 类型 | Enterprise |
|--|--|
| 说明 | 企业部署类型的虚拟设备 |
| vCPU | 8 |
| 内存 | 32 GB |
| 存储 | 1.8 TB |

受支持的虚拟机监控程序：VMware ESXi 版本 5.0 及更高版本、Hyper-v

## <a name="additional-certified-appliances"></a>其他认证设备

本部分详细介绍了由 Microsoft 认证但未作为预配置设备提供的其他设备。

| 部署类型 | Enterprise |
|--|--|
| 映像 | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="企业部署类型。"::: |
| 建模 | Dell PowerEdge R340 XL |
| 监视端口 | 多达九个 RJ45 或六个 OPT |
| 最大带宽[1](#anchortext2)| 1 Gb/秒 |
| 最大受保护设备数 | 10,000 |

<a id="anchortext2">一</a> 根据协议分发情况，带宽容量可能有所不同。

购买设备后，请转到“Defender for IoT” > “网络传感器 ISO” > “安装”以下载软件  。

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="网络传感器 ISO。":::

## <a name="next-steps"></a>后续步骤

[关于 Azure Defender for IoT 安装](how-to-install-software.md)

[关于 Azure Defender for IoT 网络设置](how-to-set-up-your-network.md)

