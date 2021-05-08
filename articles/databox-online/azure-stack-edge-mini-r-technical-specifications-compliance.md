---
title: Microsoft Azure Stack Edge Mini R 技术规格和合规性 | Microsoft Docs
description: 了解 Azure Stack Edge Mini R 设备的技术规格和合规性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 3a0b87f04e60fd56d543c7c7a752cd788e087c78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727475"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Azure Stack Edge Mini R 技术规格

Microsoft Azure Stack Edge Mini R 设备的硬件组件遵循本文中概述的技术规格和法规标准。 该技术规格介绍了 CPU、内存、电源装置 (PSU)、存储容量、机箱尺寸和重量。


## <a name="compute-memory-specifications"></a>计算和内存规格

Azure Stack Edge Mini R 设备的计算和内存规格如下：

| 规格           | 值                  |
|-------------------------|------------------------|
| CPU    | 16 核 CPU，Intel Xeon-D 1577 |
| 内存              | 48 GB RAM（2400 MT/秒）                  |


## <a name="compute-acceleration-specifications"></a>计算加速规范

每个 Azure Stack Edge Mini R 设备上都包含一个视觉处理单元 (VPU)，支持 Kubernetes、深度神经网络和基于计算机视觉的应用程序。

| 规格           | 值                  |
|-------------------------|------------------------|
| 计算加速卡         | Intel Movidius Myriad X VPU <br> 有关详细信息，请参阅 [Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX) |


## <a name="storage-specifications"></a>存储器规格

Azure Stack Edge Mini R 设备包含 1 个数据磁盘和 1 个启动磁盘（用作操作系统存储）。 下表介绍了设备存储容量的详细信息。

|     规格                          |     值             |
|--------------------------------------------|-----------------------|
|    固态硬盘 (SSD) 数量     |    2 个 1-TB 磁盘 <br> 一个数据磁盘和一个启动磁盘                  |
|    单个 SSD 容量                     |    1 TB               |
|    总容量（仅数据）              |    1 TB              |
|    总可用容量*                  |    约 750 GB        |

**已保留一些空间供内部使用。*

## <a name="network-specifications"></a>网络规格

Azure Stack Edge Mini R 设备的网络规格如下：


|规格  |值  |
|---------|---------|
|网络接口    |2 个 10 Gbe SFP+ <br> 在本地 UI 中显示为端口 3 和端口 4           |
|网络接口    |2 个 1 Gbe RJ45 <br> 在本地 UI 中显示为端口 1 和端口 2          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>电源装置规格

Azure Stack Edge Mini R 设备包含一个外部 85 W AC 适配器，用于提供电源并对板载电池充电。

下表介绍了电源设备规范：

| 规格           | 值                      |
|-------------------------|----------------------------|
| 最大输出功率    | 85 W                       |
| 频率               | 50/60 Hz                   |
| 电压范围选择 | 自动范围：100-240 V 交流 |



## <a name="included-battery"></a>包含电池

Azure Stack Edge Mini R 设备还包含一个可充电的板载电池。

该电池可以额外连接一个 [2590 电池](https://www.bren-tronics.com/bt-70791ck.html)，在两次充电之间延长设备使用时间。 此电池应符合所在国家/地区适用的所有安全、运输和环境法规。


| 规格           | 值                      |
|-------------------------|----------------------------|
| 板载电池容量 | 73 WHr                    |

## <a name="enclosure-dimensions-and-weight-specifications"></a>机箱尺寸和重量规格

下表列出了尺寸和重量的各种机箱规格。

### <a name="enclosure-dimensions"></a>机箱尺寸

下表列出了带加固型包装箱的设备和 UPS 的尺寸（以毫米和英寸为单位）。

|     机箱     |     毫米     |     英寸     |
|-------------------|---------------------|----------------|
|    高度         |    68            |    2.68          |
|    宽度          |    208          |      8.19          |
|    长度          |   259           |    10.20          |


### <a name="enclosure-weight"></a>机箱重量

下表列出了设备的重量（包含电池）。

|     机箱                                 |     重量          |
|-----------------------------------------------|---------------------|
|    设备总重量     |    7 磅          |

## <a name="enclosure-environment-specifications"></a>机箱环境规格


本部分列出了与机箱环境有关的规格，如温度、湿度和海拔高度。


|     规范             |     说明                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     温度范围          |     0 – 43° C（可操作）                                              |
|     振动                  |     MIL-STD-810 方法 514.7*<br> 过程 I CAT 4，20                  |
|     撞击                      |     MIL-STD-810 方法 516.7*<br> 过程 IV，逻辑                 |
|     海拔高度                   |     可操作：10000 英尺<br> 不可操作：40000 英尺          |

**所有数据引自 MIL-STD-810G 第一次变更版 (2014)*


## <a name="next-steps"></a>后续步骤

- [部署 Azure Stack Edge Mini R](azure-stack-edge-placeholder.md)
