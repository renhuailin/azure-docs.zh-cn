---
title: 使用 Azure NetApp 文件实现电子设计自动化的好处 | Microsoft Docs
description: 介绍 Azure NetApp 文件为满足半导体和芯片设计行业需求而提供的解决方案。 演示使用 Azure NetApp 文件为电子设计自动化 (EDA) 运行标准行业基准的测试方案。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: b-juche
ms.openlocfilehash: fcede16619e8488796adc6f4c60af30643c1aadf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "82160147"
---
# <a name="benefits-of-using-azure-netapp-files-for-electronic-design-automation"></a>使用 Azure NetApp 文件实现电子设计自动化的好处

面市时间 (TTM) 是半导体和芯片设计行业的重要考虑因素。 该行业对于存储具有高带宽和低延迟的需求。 本文将介绍 Azure NetApp 文件为满足行业需求而提供的解决方案。 其中演示了使用 Azure NetApp 文件为电子设计自动化 (EDA) 运行标准行业基准的测试方案。 

## <a name="test-scenario-configurations"></a>测试方案配置

测试涉及到采用以下配置的三个方案。 

|    场景    |    卷    |    客户端<br> SLES15 D16s_v3  |
|----------------|---------------|--------------------------------|
|    一个         |    1          |    1                           |
|    两个         |    6          |    24                          |
|    三级       |    12         |    24                          |

第一个方案测试单个卷的驱动效果。  

第二和第三个方案评估单个 Azure NetApp 文件终结点的限制。 它们将调查潜在的 I/O 上限和延迟优势。

## <a name="test-scenario-results"></a>测试方案结果

下表汇总了测试方案结果。

|    场景       |    I/O 速率<br>  （2 毫秒）     |    I/O 速率<br>  （边缘处）     |    吞吐量<br>  （2 毫秒）     |    吞吐量<br>  （边缘处）     |
|-------------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|
|    1 个卷       |    39,601                 |    49,502                      |    692 MiB/秒                 |    866 MiB/秒                      |
|    6 个卷      |    255,613                |    317,000                     |    4,577 MiB/秒               |    5,568 MiB/秒                    |
|    12 个卷     |    256,612                |    319,196                     |    4,577 MiB/秒               |    5,709 MiB/秒                    |

单卷方案代表了基本的应用程序配置。 它是后续测试方案的基线方案。  

6 卷方案展示了相对于单卷工作负载的线性提升 (600%)。  通过单个 IP 地址访问单个虚拟网络中的所有卷。  

12 卷方案展示了延迟相比 6 卷方案的一般性下降。 但是，在可实现的吞吐量方面，它没有展示出相应的提升。   

下图演示了 Azure NetApp 文件中 EDA 工作负载的延迟和操作速率。  

![Azure NetApp 文件中 EDA 工作负载的延迟和操作速率](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-operation-rate.png)   

下图演示了 Azure NetApp 文件中 EDA 工作负载的延迟和吞吐量。  

![Azure NetApp 文件中 EDA 工作负载的延迟和吞吐量](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-throughput.png) 

## <a name="layout-of-test-scenarios"></a>测试方案的布局 

下表汇总了测试方案的布局。

|    测试方案     |    目录总数     |    文件总数     |
|----------------------|------------------------------------|------------------------------|
|    1 个卷          |    88,000                          |    880,000                   |
|    6 个卷         |    568,000                         |    5,680,000                 |
|    12 个卷        |    568,000                         |    5,680,000                 |

完整工作负载是并发运行功能阶段和物理阶段的混合形式。 它代表了从一组 EDA 工具过渡到另一组工具的典型流。   

功能阶段包括初始规范和逻辑设计。 物理阶段在将逻辑设计转换为物理芯片时发生。 在签收和流片试产阶段，将完成最终检查，并将设计交付到晶圆代工厂进行制造。  

功能阶段包括顺序和随机读写 I/O 的混合形式。 功能阶段是元数据密集阶段，使用文件统计信息和访问调用等大量元数据。 尽管元数据操作实际上没有大小的概念，但读写操作的大小介于 1 K 和 16 K 之间。大多数读取操作大小介于 4 K 和 16 K 之间。大多数写入操作大小为 4 K 或更小。 物理阶段完全由顺序读写操作组成，其中混合了 32 K 和 64 K 的操作大小。  

在上面的图表中，大部分吞吐量都来自工作负载的顺序物理阶段。 I/O 来自较小的随机和元数据密集型功能阶段。 这两个阶段是并行发生的。 

总而言之，可以将 Azure 计算与 Azure NetApp 文件搭配使用进行 EDA 设计，以获得可缩放的带宽。 

## <a name="next-steps"></a>后续步骤

- [使用 Azure NetApp 文件的解决方案体系结构](azure-netapp-files-solution-architectures.md)
