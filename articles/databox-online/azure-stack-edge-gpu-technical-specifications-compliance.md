---
title: 配备 GPU 的 Microsoft Azure Stack Edge Pro 的技术规格和符合性 | Microsoft Docs
description: 了解配备 GPU 的 Azure Stack Edge Pro 设备的技术规格和符合性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: alkohli
ms.openlocfilehash: e0eb976f655308082671afe2dc1923f082a3373b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303162"
---
# <a name="technical-specifications-and-compliance-for-azure-stack-edge-pro-with-gpu"></a>配备 GPU 的 Azure Stack Edge Pro 的技术规格和符合性 

配备板载图形处理单元 (GPU) 的 Azure Stack Edge Pro 的硬件组件遵循本文中列出的技术规格和监管标准。 这些技术规格描述了硬件、电源设备 (PSU)、存储容量、机箱和环境等方面的标准。

## <a name="compute-and-memory-specifications"></a>计算和内存规格

Azure Stack Edge Pro 设备的计算和内存规格如下：

| 规格  | 值                                                                       |
|----------------|-----------------------------------------------------------------------------|
| CPU 类型       | 双 Intel Xeon Silver 4214 (Cascade Lake) CPU                              |
| CPU：原始       | 共 24 个核心，共 48 个 vCPU                                              |
| CPU：可用    | 40 个 vCPU                                                                    |
| 内存类型    | Dell 兼容 16 GB PC4-23400 DDR4-2933Mhz 2Rx8 1.2v ECC 注册 RDIMM |
| 内存：原始    | 128 GB RAM (8 x 16 GB)                                                      |
| 内存：可用 | 102 GB RAM                                                                  |


## <a name="compute-acceleration-specifications"></a>计算加速规格

每台 Azure Stack Edge Pro 设备上都包含图形处理单元 (GPU)，可实现 Kubernetes、深度学习和机器学习方案。

| 规格           | 值                  |
|-------------------------|----------------------------|
| GPU   | 1 个或 2 个 nVidia T4 GPU <br> 有关详细信息，请参阅 [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)。| 


## <a name="power-supply-unit-specifications"></a>电源装置规格

Azure Stack Edge Pro 设备具有两个配备高性能风扇的 100-240 V 电源设备 (PSU)。 这两个 PSU 提供了冗余电源配置。 如果 PSU 发生故障，设备将继续在另一个 PSU 上正常运行，直至发生故障的模块得到更换。 下表列出了 PSU 的技术规格。

| 规格           | 750 W PSU                  |
|-------------------------|----------------------------|
| 最大输出功率    | 750 W                      |
| 频率               | 50/60 Hz                   |
| 电压范围选择 | 自动范围：100-240 V 交流 |
| 可热插拔           | 是                        |


## <a name="network-interface-specifications"></a>网络接口规格

Azure Stack Edge Pro 设备具有 6 个网络接口 (PORT1 - PORT6)。

| 规格           | 说明                 |
|-------------------------|----------------------------|
|  网络接口    | 2 个 1 GbE 接口 – 1 个管理接口（端口 1）用于初始设置，在默认情况下为静态。 在初始设置完成后，可将该接口用于那些使用任何 IP 地址的数据。 但是，在重置后，该接口会还原为静态 IP。 <br>另一个接口（端口 2）可由用户配置，可用于数据传输，在默认情况下为 DHCP。 <br>4 个 25 GbE 接口 – 用户可将这些数据接口（端口 3 至端口 6）配置为 DHCP（默认）或静态。 它们还可以用作 10 GbE 接口。  | 

Azure Stack Edge Pro 设备具有以下网络硬件：

* **自定义 Microsoft `Qlogic` Cavium 25G NDC 适配器** - 端口 1 到端口 4。
* **Mellanox 双端口 25G ConnectX-4 通道网络适配器** - 端口 5 和端口 6。

下面是 Mellanox 卡的详细信息：

| 参数           | 说明                 |
|-------------------------|----------------------------|
| 模型    | ConnectX®-4 Lx EN 网络接口卡                      |
| 模型说明               | 25 GbE 双端口 SFP28；PCIe3.0 x8；ROHS R6                    |
| 设备部件号 (R640) | MCX4121A-ACAT  |
| PSID (R640)           | MT_2420110034                         |

若要查看这些网卡支持的线缆、交换机和收发器的完整列表，请参阅：

- [`Qlogic` Cavium 25G NDC 适配器互操作性矩阵](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)。
- [Mellanox 双端口 25G ConnectX-4 通道网络适配器兼容产品](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)。  

## <a name="storage-specifications"></a>存储器规格

Azure Stack Edge Pro 设备具有 5 块 2.5 英寸的 NVMe DC P4610 SSD，每块的容量为 1.6 TB。 启动驱动器是一块 240 GB 的 SATA SSD。 设备的总可用容量约为 4.19 TB。 下表列出了设备的存储容量。

|     规格                          |     值             |
|--------------------------------------------|-----------------------|
|    NVMe SSD 的数量                     |    5                  |
|    单块 NVMe SSD 的容量                |    1.6 TB             |
|    启动 SATA 固态硬盘 (SSD)      |    1                  |
|    启动 SSD 容量                       |    240 GB             |
|    总容量                          |    8.0 TB             |
|    总可用容量                   |    ~ 4.19 TB          |
|    RAID 配置                      |    具有镜像和奇偶校验的组合的存储空间直通  |
|    SAS 控制器                          |    HBA330 12 Gbps     |

<!--Remove based on feedback from Ravi
## Other hardware specifications

Your Azure Stack Edge Pro device also contains the following hardware:

* iDRAC baseboard management
* Performance fans
* Custom ID module-->

## <a name="enclosure-dimensions-and-weight-specifications"></a>机箱尺寸和重量规格

下表列出了尺寸和重量的各种机箱规格。

### <a name="enclosure-dimensions"></a>机箱尺寸

下表列出了 1U 设备机箱的尺寸（以毫米和英寸为单位）。

|     机箱     |     毫米     |     英寸     |
|-------------------|---------------------|----------------|
|    高度         |    44.45            |    1.75"       |
|    宽度          |    434.1            |    17.09"      |
|    长度         |    740.4            |    29.15"      |

下表列出了以毫米和英寸为单位的发货包尺寸。

|     程序包     |     毫米     |     英寸     |
|-------------------|---------------------|----------------|
|    高度         |    311.2            |    12.25"          |
|    宽度          |    642.8            |    25.31"          |
|    长度         |    1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>机箱重量

设备包的重量为 66 磅。 需要两个人来操作。 设备重量取决于机箱配置。

|     机箱                                 |     重量          |
|-----------------------------------------------|---------------------|
|    总重量（包括打包）       |    61 磅。          |
|    设备重量                       |    35 磅。          |

## <a name="enclosure-environment-specifications"></a>机箱环境规格

本部分列出了与机箱环境有关的规格，如温度、湿度和海拔高度。

### <a name="temperature-and-humidity"></a>温度和湿度

|     机箱         |     环境温度范围     |     环境相对湿度     |     最大露点温度     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    可运行        |    10°C - 35°C (50°F - 86°F)         |    10% - 80% 无冷凝。         |    29°C (84°F)            |
|    不可运行    |    -40°C 到 65°C (-40°F - 149°F)     |    5% - 95% 无冷凝。          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>气流、海拔高度、撞击、震动、方向、安全和 EMC

|     机箱                           |     操作规范                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    气流                              |    系统气流的方向是从前到后。 系统的操作环境必须是低压、后排气的安装方式。 <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
| 进入保护 (IP)                 |    此类用于室内的架装设备通常不会进行进入保护（电气机箱防止固体和液体进入）测试。 制造商的安全评估显示 IPXO（无进入保护）。  |
|    最大海拔高度，可运行        |    3048 米（10,000 英尺），最高额定操作温度的降级由[额定操作温度降级规格](#operating-temperature-de-rating-specifications)确定。                                                                                |
|    最大海拔高度，不可运行    |    12,000 米（39,370 英尺）                                                                                                                                                                                         |
|    撞击，可运行                   |    6 G/11 毫秒（6 个方向）                                                                                                                                                                         |
|    撞击，不可运行               |    71 G/2 毫秒（6 个方向）                                                                                                                                                                           |
|    震动，可运行               |    0.26 G<sub>RMS</sub> 5 Hz 到 350 Hz 随机                                                                                                                                                                                     |
|    震动，不可运行           |    1.88 G<sub>RMS</sub> 10 Hz 到 500 Hz 测试 15 分钟（对六个面都进行测试。）                                                                                                                                                  |
|    方向和安装             |    标准 19 英寸架装 (1U)                                                                                                                                                                                       |
|    安全和审批                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A、ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014（D 类）   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    能源             |    Commission Regulation (EU) No. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>额定操作温度降级规格

|     额定操作温度降级     |     环境温度范围                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    上限为 35°C (95°F)                       |    950 米（3,117 英尺）以上，每升高 300 米，最高温度降低 1°C（每升高 547 英尺降低 1°F）。    |
|    35°C 到 40°C（95°F 到 104°F）            |    950 米（3,117 英尺）以上，每升高 175 米，最高温度降低 1°C（每升高 319 英尺降低 1°F）。    |
|    40°C 到 45°C（104°F 到 113°F）           |    950 米（3,117 英尺）以上，每升高 125 米，最高温度降低 1°C（每升高 228 英尺降低 1°F）。    |

## <a name="next-steps"></a>后续步骤

[部署 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)
