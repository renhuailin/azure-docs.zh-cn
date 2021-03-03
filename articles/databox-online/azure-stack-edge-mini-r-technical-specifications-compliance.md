---
title: Microsoft Azure Stack 边缘迷你 R 技术规格和符合性 |Microsoft Docs
description: 了解 Azure Stack Edge 迷你 R 设备的技术规格和符合性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 3a0b87f04e60fd56d543c7c7a752cd788e087c78
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727475"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Azure Stack 边缘迷你 R 技术规范

Microsoft Azure Stack Edge 迷你 R 设备的硬件组件遵循本文中所述的技术规格和法规标准。 技术规范描述了 CPU、内存、电源单位 (通电 psu) 、存储容量、机箱尺寸和重量。


## <a name="compute-memory-specifications"></a>计算和内存规格

Azure Stack Edge 迷你 R 设备具有以下计算和内存规范：

| 规格           | 值                  |
|-------------------------|------------------------|
| CPU    | 16核 CPU，Intel 至强-D 1577 |
| 内存              | 48 GB RAM (2400 MT/秒)                   |


## <a name="compute-acceleration-specifications"></a>计算加速规范

每个 Azure Stack 边缘迷你 R 设备上都包含一个 (VPU) 的远景处理单元，可实现 Kubernetes、深度神经网络和基于计算机视觉的应用程序。

| 规格           | 值                  |
|-------------------------|------------------------|
| 计算加速卡         | Intel Movidius 无数 X VPU <br> 有关详细信息，请参阅 [Intel Movidius 无数 X VPU](https://www.movidius.com/MyriadX) |


## <a name="storage-specifications"></a>存储器规格

Azure Stack Edge 迷你 R 设备有1个数据磁盘，1个启动磁盘 (用作操作系统存储) 。 下表显示了设备的存储容量的详细信息。

|     规格                          |     值             |
|--------------------------------------------|-----------------------|
|    固态硬盘 (SSD) 数量     |    2 X 1 TB 磁盘 <br> 一个数据磁盘和一个启动磁盘                  |
|    单个 SSD 容量                     |    1 TB               |
|    仅 (数据的总容量)               |    1 TB              |
|    总可用容量*                  |    约 750 GB        |

**已保留一些空间供内部使用。*

## <a name="network-specifications"></a>网络规格

Azure Stack Edge 迷你 R 设备的网络规格如下：


|规格  |值  |
|---------|---------|
|网络接口    |2 x 10 Gbe SFP + <br> 在本地 UI 中显示为端口3和端口4           |
|网络接口    |2 x 1 Gbe RJ45 <br> 在本地 UI 中显示为端口1和端口2          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>电源装置规格

Azure Stack Edge 迷你 R 设备包含一个外部 85 W AC 适配器，用于提供电源并对板载电池充电。

下表显示了电源单元规范：

| 规格           | 值                      |
|-------------------------|----------------------------|
| 最大输出功率    | 85 W                       |
| 频率               | 50/60 Hz                   |
| 电压范围选择 | 自动范围：100-240 V 交流 |



## <a name="included-battery"></a>已包含电池

Azure Stack 边缘迷你 R 设备还包含按电源付费的板载电池。

附加 [类型2590电池](https://www.bren-tronics.com/bt-70791ck.html) 可与板载电池结合使用，以在费用之间扩展设备的使用情况。 此电池应符合使用国家/地区的所有安全、运输和环境法规。


| 规格           | 值                      |
|-------------------------|----------------------------|
| 板载电池容量 | 73瓦                    |

## <a name="enclosure-dimensions-and-weight-specifications"></a>机箱尺寸和重量规格

下表列出了尺寸和重量的各种机箱规格。

### <a name="enclosure-dimensions"></a>机箱尺寸

下表列出了设备的尺寸和 USP，其大小写大小为毫米和英寸。

|     机箱     |     毫米     |     英寸     |
|-------------------|---------------------|----------------|
|    高度         |    68            |    2.68          |
|    宽度          |    208          |      8.19          |
|    长度          |   259           |    10.20          |


### <a name="enclosure-weight"></a>机箱重量

下表列出了包括电池的设备的权重。

|     机箱                                 |     重量          |
|-----------------------------------------------|---------------------|
|    设备的总重量     |    7磅。          |

## <a name="enclosure-environment-specifications"></a>机箱环境规格


本部分列出了与机箱环境有关的规格，如温度、湿度和海拔高度。


|     规范             |     说明                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     温度范围          |     0–43摄氏度 (操作)                                               |
|     振动                  |     MIL-810 方法 514.7 *<br> 过程 I CAT 4，20                  |
|     撞击                      |     MIL-810 方法 516.7 *<br> 步骤 IV，逻辑                 |
|     海拔高度                   |     操作：10000英尺<br> 不可操作：40000英尺          |

**所有引用都将 MIL-810G Change 1 (2014)*


## <a name="next-steps"></a>后续步骤

- [部署 Azure Stack Edge 迷你 R](azure-stack-edge-placeholder.md)
