---
title: Azure NetApp 文件的 Azure 虚拟机 SKU 最佳做法 | Microsoft Docs
description: 介绍了有关 Azure 虚拟机 SKU 的 Azure NetApp 文件最佳做法，包括 SKU 内差异和 SKU 间差异。
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
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: 6644a52ee094ce0076293b37fa79db28568c9c67
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233454"
---
# <a name="azure-virtual-machine-skus-best-practices-for-azure-netapp-files"></a>Azure NetApp 文件的 Azure 虚拟机 SKU 最佳做法

本文介绍了有关 Azure 虚拟机 SKU 的 Azure NetApp 文件最佳做法，包括 SKU 内差异和 SKU 间差异。   

## <a name="sku-selection-considerations"></a>SKU 选择注意事项

存储性能涉及的不仅仅是存储本身的速度。 处理器速度和体系结构对任何特定计算节点的总体体验有很大影响。 在给定 SKU 的选择过程中，你应当考虑以下因素：

* AMD 或 Intel：例如，SAS 使用专为 Intel 处理器设计的数学内核库。  在这种情况下，Intel SKU 优于 AMD SKU。
* F2、E_v3 和 D_v3 计算机类型都基于多个芯片组。  在使用 Azure 专用主机时，你可以选择具体的型号（例如，在选择 E 类型时选择 Broadwell、Cascade Lake 或 Skylake）。 其他情况下，芯片组选择是不确定的。  如果你在部署 HPC 群集，而在整个库存中保持一致的体验非常重要，则你可以考虑使用单个 Azure 专用主机或使用单个芯片组 SKU（例如 E_v4 或 D_v4）。
* 在使用基于 Intel Broadwell 的 SKU 和基于 AMD EPYC™ 7551 的 SKU 进行测试时，观察到网络连接存储 (NAS) 的性能有所变化。 观察到两个问题：
    * 当加速网络接口不恰当地映射到次优 NUMA 节点时，读取性能显著降低。   虽然在较新的 SKU 上将加速网络接口映射到特定的 NUMA 节点是有利的，但在使用特定芯片组 (Lv2|E_v3|D_v3) 的 SKU 上则不尽然，必须将它视为一种要求。
    * 与在其他 SKU 上运行相比，在 Lv2 上运行的虚拟机或在 Broadwell 芯片组上运行的 E_v3 或 D_v3 更容易发生资源争用。  使用在单个 Azure 专用主机中运行的多个虚拟机进行测试时，会发现从一个虚拟机运行基于网络的存储工作负荷会降低从另一个虚拟机运行的基于网络的存储工作负荷的性能。 当节点上有任何虚拟机未以最佳方式映射其加速网络接口/NUMA 节点时，性能降低更明显。  请记住，E_v3 和 D_V3 可能驻留在二者之间的 Haswell、Broadwell、Cascade Lake 或 Skylake 上。 

在选择虚拟机时，为了获得最一致的性能，请从具有单一类型芯片组的 SKU 中进行选择 - 如果可行，请优先选择较新的 SKU 而非较旧的型号。  请记住，除了使用专用主机之外，不太可能正确预测 E_v3 或 D_v3 虚拟机所使用的硬件类型。  当使用 E_v3 或 D_v3 SKU 时：

* 如果虚拟机被关闭、解除分配，然后重新打开，该虚拟机可能会更改主机以及硬件型号。
* 如果应用程序是跨多个虚拟机部署的，虚拟机将在异构硬件上运行。

## <a name="differences-within-and-between-skus"></a>SKU 内差异和 SKU 间差异
 
下表重点介绍了 SKU 内差异和 SKU 间差异。  请注意，举例来说，基础 E_v3 的芯片组在 Broadwell、Cascade Lake 和 Skylake 之间有所不同，D_v3 也是如此。  

|     系列    |     版本    |   说明     |     频率 (GHz)    |
|-|-|-|-|
|     E    |     V3    |     Intel® Xeon® E5-2673 v4 (Broadwell)    |     2.3 (3.6)    |
|     E    |     V3    |     Intel® Xeon® Platinum 8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     E    |     V3    |     Intel® Xeon® Platinum 8171M (Skylake)    |     2.1 (3.8)    |
|     E    |     V4    |     Intel® Xeon® Platinum 8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     Ea    |     V4    |     AMD EPYC™ 7452    |     2.35 (3.35)    |
|     D    |     V3    |     Intel® Xeon® E5-2673 v4 (Broadwell)    |     2.3 (3.6)    |
|     D    |     V3    |     Intel® Xeon® E5-2673 v3 (Haswell)    |     2.3 (2.3)    |
|     D    |     V3    |     Intel® Xeon® Platinum 8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     D    |     V3    |     Intel® Xeon® Platinum 8171M (Skylake)    |     2.1 (3.8)    |
|     D    |     V4    |     Intel® Xeon® Platinum 8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     Da    |     V4    |     AMD EPYC™ 7452    |     2.35 (3.35)    |
|     L    |     V2    |     AMD EPYC™ 7551    |     2.0 (3.2)    |
|     F    |     1    |     Intel Xeon® E5-2673 v3 (Haswell)     |     2.3 (2.3)    |
|     F    |     2    |     Intel® Xeon® Platinum 8168M (Cascade Lake)    |     2.7 (3.7)    |
|     F    |     2    |     Gen 2 Intel® Xeon® Platinum 8272CL (Skylake)    |     2.1 (3.8)   |

在为生产准备多节点 SAS 网格环境时，你可能会注意到在两次分析运行之间存在一小时十五分钟的可重复差异，除了底层硬件之外没有其他区别。  

|     SKU 和硬件平台    |     作业运行时间    |
|-|-|
|     E32-8_v3 (Broadwell)    |     5.5 小时    |
|     E32-8_v3 (Cascade Lake)    |     4.25 小时    |

在这两组测试中，都选择了 E32-8_v3 SKU，同时使用了 RHEL 8.3 和 `nconnect=8` 装载选项。

## <a name="best-practices"></a>最佳实践 

* 请尽可能选择 E_v4、D_v4 或更新的 SKU，而非选择 E_v3 或 D_v3 SKU。  
* 请尽可能选择 Ed_v4、Dd_v4 或更新的 SKU，而非选择 L2 SKU。

## <a name="next-steps"></a>后续步骤  

* [适用于 Azure NetApp 文件的 Linux 直接 I/O 最佳做法](performance-linux-direct-io.md)
* [适用于 Azure NetApp 文件的 Linux 文件系统缓存最佳做法](performance-linux-filesystem-cache.md)
* [适用于 Azure NetApp 文件的 Linux NFS 装载选项最佳做法](performance-linux-mount-options.md)
* [Linux 并发最佳做法](performance-linux-concurrency-session-slots.md)
* [Linux NFS 文件预读最佳做法](performance-linux-nfs-read-ahead.md)
* [Linux 性能基准](performance-benchmarks-linux.md) 
