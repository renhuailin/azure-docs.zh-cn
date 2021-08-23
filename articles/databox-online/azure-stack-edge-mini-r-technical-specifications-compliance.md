---
title: Microsoft Azure Stack Edge Mini R 技术规格和合规性 | Microsoft Docs
description: 了解 Azure Stack Edge Mini R 设备的技术规格和合规性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: alkohli
ms.openlocfilehash: 1f57e7f25a9e34a88c9ae279083a2cf1d9be2ff1
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110099417"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Azure Stack Edge Mini R 技术规格

Microsoft Azure Stack Edge Mini R 设备的硬件组件遵循本文中概述的技术规格和法规标准。 该技术规格介绍了 CPU、内存、电源装置 (PSU)、存储容量、机箱尺寸和重量。


## <a name="compute-memory"></a>计算、内存

Azure Stack Edge Mini R 设备的计算和内存规格如下：

| 规格           | 值                           |
|-------------------------|---------------------------------|
| CPU 类型                | Intel Xeon-D 1577               |
| CPU：原始                | 共 16 个核心，共 32 个 vCPU  |
| CPU：可用             | 24 个 vCPU                        |
| 内存类型             | 16 GB（2400 MT/秒）SODIMM          |
| 内存：原始             | 48 GB RAM (3 x 16 GB)           |
| 内存：可用          | 32 GB RAM                       |


## <a name="compute-acceleration"></a>计算加速

每个 Azure Stack Edge Mini R 设备上都包含一个视觉处理单元 (VPU)，支持 Kubernetes、深度神经网络和基于计算机视觉的应用程序。

| 规格             | 值                  |
|---------------------------|------------------------|
| 计算加速卡 | Intel Movidius Myriad X VPU <br> 有关详细信息，请参阅 [Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX) |


## <a name="storage"></a>存储

Azure Stack Edge Mini R 设备包含 1 个数据磁盘和 1 个启动磁盘（用作操作系统存储）。 下表介绍了设备存储容量的详细信息。

|     规格                          |     值                                              |
|--------------------------------------------|--------------------------------------------------------|
|    固态硬盘 (SSD) 数量     |    2 个 1-TB 磁盘 <br> 一个数据磁盘和一个启动磁盘 |
|    单个 SSD 容量                     |    1 TB                                                |
|    总容量（仅数据）              |    1 TB                                                |
|    总可用容量*                  |    约 750 GB                                            |

*已保留一些空间供内部使用。*

## <a name="network"></a>网络

Azure Stack Edge Mini R 设备的网络规格如下：

|规格         |值                                                               |
|----------------------|--------------------------------------------------------------------|
|网络接口    |2 个 10 Gbps SFP+ <br> 在本地 UI 中显示为端口 3 和端口 4    |
|网络接口    |2 个 1 Gbps RJ45 <br> 在本地 UI 中显示为端口 1 和端口 2     |
|Wi-Fi                 |802.11ac                                                            |

## <a name="routers-and-switches"></a>路由器和交换机

以下路由器和交换机与 Azure Stack Edge Mini R 设备上的 10 Gbps SPF+ 网络接口（端口 3 和端口 4）兼容：

|路由器/交换机     |注释                         |
|------------------|------------------------------|
|[VoyagerESR 2.0](https://klastelecom.com/products/voyageresr2-0/)    |Cisco ESS3300 交换机组件   |
|[VoyagerSW26G](https://klastelecom.com/products/voyagersw26g/)       |                                 |
|[VoyagerVM 3.0](https://klastelecom.com/products/voyager-vm-3-0/)    |                                 |
|[TDC 交换机](https://klastelecom.com/voyager-tdc/)                   |                                 |
|[TRX R2](https://klastelecom.com/products/trx-r2/)（8 核）  <!--Better link: https://www.klasgroup.com/products/voyagersw12gg/? On current link target, an "R6" link opens this page.-->        |                              |
|[SW12GG](https://www.klasgroup.com/products/voyagersw12gg/)          |                                 |

## <a name="transceivers-cables"></a>收发器、电缆

强烈建议将 Azure Stack Edge Mini R 设备与以下铜质 SFP+ (10 Gbps) 收发器和电缆配合使用。 兼容的光纤电缆可与 SFP+ 网络接口（端口 3 和端口 4）配合使用，但尚未经过测试。

|SFP+ 收发器类型 |支持的电缆    | 注释 |
|----------------------|--------------------|-------|
|SFP+ 直连式铜缆 (10GSFP+Cu)| <ul><li>[FS SFP-10G-DAC](https://www.fs.com/c/fs-10g-sfp-dac-1115)（可针对 -40ºC 至 +85ºC 的工业温度进行定制）</li><br><li>[10Gtek CAB-10GSFP-P0.5M](http://www.10gtek.com/10G-SFP+-182)</li><br><li>[Cisco SFP-H10GB-CU1M](https://www.cisco.com/c/en/us/products/collateral/interfaces-modules/transceiver-modules/data_sheet_c78-455693.html)</li></ul> |<ul><li>也称为 SFP+ Twinax DAC 电缆。</li><br><li>推荐选项，因为它的耗电量最低并且最简单。</li><br><li>不支持自动协商。</li><br><li>不支持将 SFP 设备连接到 SFP+ 设备。</li></ul>|

## <a name="power-supply-unit"></a>电源设备

Azure Stack Edge Mini R 设备包含一个外部 85 W AC 适配器，用于提供电源并对板载电池充电。

下表介绍了电源设备规范：

| 规格           | 值                      |
|-------------------------|----------------------------|
| 最大输出功率    | 85 W                       |
| 频率               | 50/60 Hz                   |
| 电压范围选择 | 自动范围：100-240 V 交流 |

## <a name="included-battery"></a>包含电池

Azure Stack Edge Mini R 设备还包含一个可充电的板载电池。

板载电池可以与另外一个 [2590 型电池](https://www.bren-tronics.com/bt-70791ck.html)一起使用，以便延长设备在两次充电之间的使用时间。 此电池应符合所在国家/地区适用的所有安全、运输和环境法规。

| 规格            | 值      |
|--------------------------|------------|
| 板载电池容量 | 73 瓦时      |

## <a name="enclosure-dimensions-and-weight"></a>机箱尺寸和重量

下表列出了尺寸和重量的各种机箱规格。

### <a name="enclosure-dimensions"></a>机箱尺寸

下表列出了带加固型包装箱的设备和 UPS 的尺寸（以毫米和英寸为单位）。

|     机箱     |     毫米     |     英寸     |
|-------------------|---------------------|----------------|
|    高度         |    68               |    2.68        |
|    宽度          |    208              |    8.19        |
|    长度         |    259              |    10.20       |


### <a name="enclosure-weight"></a>机箱重量

下表列出了设备的重量（包含电池）。

|     机箱                     |     重量          |
|-----------------------------------|---------------------|
|    设备总重量     |     7 磅           |

## <a name="enclosure-environment"></a>机箱环境

这部分列出了与机箱环境有关的规格，如温度、湿度和海拔高度。

|     规范             |     说明                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     温度范围          |     0 – 43° C（可操作）                                              |
|     振动                  |     MIL-STD-810 方法 514.7*<br> 过程 I CAT 4，20                  |
|     撞击                      |     MIL-STD-810 方法 516.7*<br> 过程 IV，逻辑                 |
|     海拔高度                   |     可操作：10000 英尺<br> 不可操作：40000 英尺          |

**所有数据引自 MIL-STD-810G 第一次变更版 (2014)*

## <a name="next-steps"></a>后续步骤

- [部署 Azure Stack Edge Mini R](azure-stack-edge-placeholder.md)
