---
title: Microsoft Azure Stack Edge Pro R 技术规格和符合性 |Microsoft Docs
description: 了解 Azure Stack Edge Pro R 设备的技术规格和符合性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 56d301762fde41f727b0b425d6c41a423f08103c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466094"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Azure Stack Edge Pro R 技术规范

Azure Stack Edge Pro R 设备的硬件组件遵循本文中所述的技术规范。 该技术规格介绍了电源装置 (PSU)、存储容量、机箱和环境标准。


## <a name="compute-memory-specifications"></a>计算和内存规格

Azure Stack Edge Pro R 设备具有以下计算和内存规范：

| 规格       | 值                  |
|---------------------|------------------------|
| CPU    | 2 X 10 核心 CPU，Intel 至强银色4114 |
| 内存              | 256 GB RAM (2666 MT/秒)      |


## <a name="compute-acceleration-specifications"></a>计算加速规范

每台设备上都包含一个图形处理单元 (GPU) ，可实现 Kubernetes、深度学习和机器学习方案。

| 规格           | 值                  |
|-------------------------|----------------------------|
| GPU   | 一个 nVidia T4 GPU <br> 有关详细信息，请参阅 [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)。| 

## <a name="power-supply-unit-specifications"></a>电源装置规格

Azure Stack Edge Pro R 设备具有两个 100-240 V 电源单位， (通电 psu) 与高性能风扇一起提供。 这两个 PSU 提供了冗余电源配置。 如果 PSU 发生故障，设备将继续在另一个 PSU 上正常运行，直至发生故障的模块得到更换。 下表列出了 PSU 的技术规格。

| 规格           | 550 W PSU                  |
|-------------------------|----------------------------|
| 最大输出功率    | 550 W                      |
| 热量散发 (最大)                    | 2891 BTU/小时                |
| 频率               | 50/60 Hz                   |
| 电压范围选择 | 自动范围： 115-230 V AC |
| 可热插拔           | 是                        |

## <a name="network-specifications"></a>网络规格

Azure Stack Edge Pro R 设备具有四个网络接口 PORT1-PORT4。 


|规格  |说明                              |
|----------------------|----------------------------------|
|网络接口    |**2 x 1 Gbe RJ45** <br> 端口1用作初始设置的管理界面，默认情况下是静态的。 初始设置完成后，可以使用具有任何 IP 地址的数据的接口。 但是，在重置时，接口会恢复为静态 IP。 <br>其他接口端口2是用户可配置的，可用于数据传输，默认情况下为 DHCP。     |
|网络接口    |**2 x 25 Gbe SFP28** <br> 可以将这些数据接口端口3和端口4配置为 DHCP (默认) 或静态。            |

Azure Stack Edge Pro R 设备具有以下网络硬件：

* **Mellanox 双重端口 25G ConnectX-4 通道网络适配器** -端口3和端口4。 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

若要查看这些网卡支持的电缆、交换机和收发器的完整列表，请转到： [Mellanox 双重端口 25G ConnectX-4 通道网络适配器兼容产品](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)。

## <a name="storage-specifications"></a>存储器规格

Azure Stack Edge Pro R 设备有8个数据磁盘和2个为操作系统磁盘提供的2个 SATA 磁盘。 有关详细信息，请参阅 " [2. 2 SATA 磁盘](https://en.wikipedia.org/wiki/M.2)"。

#### <a name="storage-for-1-node-device"></a>1节点设备的存储

下表提供了1节点设备的存储容量的详细信息。

|     规格                          |     值             |
|--------------------------------------------|-----------------------|
|    固态硬盘 (SSD) 数量     |    8                  |
|    单个 SSD 容量                     |    8 TB               |
|    总容量                          |    64 TB              |
|    总可用容量*                  |    约 42 TB          |

**已保留一些空间供内部使用。*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>机箱尺寸和重量规格

下表列出了尺寸和重量的各种机箱规格。

### <a name="enclosure-dimensions"></a>机箱尺寸 

下表列出了设备的维度，以及以毫米和英寸为单位的耐用大小的 UPS。

|     机箱     |     毫米     |     英寸     |
|-------------------|---------------------|----------------|
|    高度         |    301.2            |    11.86       |
|    宽度          |    604.5            |    23.80       |
|    长度         |    740.4            |    35.50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>机箱重量 

设备重量取决于机箱配置。

|     机箱                                 |     重量          |
|-----------------------------------------------|---------------------|
|    1-节点设备的总重量和端帽的耐用情况     |    约114磅。          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>机箱环境规格

本部分列出了与机箱环境有关的规格，如温度、振动、冲击和海拔。


|     规格              |     值    |
|--------------------------------|-------------------------------------------------------------------|
|     温度范围          |     0–43摄氏度 (操作)     |
|     振动                  |     MIL-810 方法 514.7 *<br>过程 I CAT 4，20                  |
|     撞击                      |     MIL-810 方法 516.7 *<br>步骤 IV，逻辑                 |
|     海拔高度                   |     操作：10000英尺<br>不可操作：40000英尺          |

**所有引用都将 MIL-810G Change 1 (2014)*

## <a name="next-steps"></a>后续步骤

- [部署 Azure Stack Edge](azure-stack-edge-placeholder.md)
