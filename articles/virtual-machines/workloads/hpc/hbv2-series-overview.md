---
title: HBv2 系列 VM 概述-Azure 虚拟机 |Microsoft Docs
description: 了解 Azure 中的 HBv2 系列 VM 大小。
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: be66f29881250843f70ba85b8ef7c80ae8b31aa6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966932"
---
# <a name="hbv2-series-virtual-machine-overview"></a>HBv2 系列虚拟机概述 

 
最大程度提高高性能计算 (HPC) 应用程序性能 下面概述了 AMD EPYC 体系结构，以及如何在 Azure 上实现 HPC 应用程序。 我们将使用术语 **pNUMA** 来引用物理 numa 域，并使用 **vNUMA** 引用虚拟化 numa 域。 

从物理上讲， [HBv2 系列](../../hbv2-series.md) 服务器是 2 * 64 核心 EPYC 7742 cpu，总共128个物理内核。 这些128核心分为32个 pNUMA 域 (每套接字) 16 个，其中每个都是4个核心，由 AMD 称为 **核心复杂** (或 **CCX**) 。 每个 CCX 都有其自己的 L3 缓存，这是操作系统将如何看到 pNUMA/vNUMA 边界的。 四个相邻 CCXs 共享对两个物理 DRAM 通道的访问。 

若要为 Azure 虚拟机监控程序提供空间来操作而不干扰 VM，我们保留了物理 pNUMA 域0和 16 (即每个 CPU 插槽) 的第一个 CCX。 所有剩余的30个 pNUMA 域将分配给 VM，此时它们将变为 vNUMA。 这样，VM 就会看到：

`(30 vNUMA domains) * (4 cores/vNUMA) = 120` 每个 VM 的内核数 

VM 本身不会意识到 pNUMA 0 和16已保留。 它将它看到的 vNUMA 枚举为0-29，并以 15-29 0-14 对称方式为每个套接字 vNUMA 15 个 在下一部分中，有关于如何最好地在此非对称 NUMA 布局上运行 MPI 应用程序的说明。 

进程固定适用于 HBv2 系列 Vm，因为我们会向来宾 VM 公开基础硅。 强烈建议通过处理固定来实现最佳性能和一致性。 


## <a name="hardware-specifications"></a>硬件规格 

| 硬件规范          | HBv2 系列 VM                   | 
|----------------------------------|----------------------------------|
| 核心数                            | 120 (SMT 禁用)                | 
| CPU                              | AMD EPYC 7742                    | 
|  (非 AVX) 的 CPU 频率          | ~ 3.1 GHz (单个 + 所有核心)     | 
| 内存                           | 4 GB/核心 (480 GB 总)          | 
| 本地磁盘                       | 960 GB NVMe (块) ，480 GB SSD (页面文件)  | 
| Infiniband                       | 200 Gb/秒 EDR Mellanox ConnectX-6 | 
| 网络                          | 50 gb/s 以太网 (40 Gb/s 可用) Azure 第二代 SmartNIC | 


## <a name="software-specifications"></a>软件规范 

| 软件规范     | HBv2 系列 VM                                            | 
|-----------------------------|-----------------------------------------------------------|
| 最大 MPI 作业大小            | 36000核心 (使用 singlePlacementGroup = true 的单个虚拟机规模集中的 300 Vm)  |
| MPI 支持                 | HPC-X，Intel MPI，OpenMPI，MVAPICH2，MPICH，平台 MPI  |
| 其他框架       | 统一通信 X、libfabric、PGAS                  |
| Azure 存储支持       | 标准磁盘和高级磁盘 (最多8个磁盘)               |
| SRIOV RDMA 的操作系统支持   | CentOS/RHEL 7.6 +、SLES 12 SP4 +、WinServer 2016 +           |
| Orchestrator 支持        | CycleCloud，Batch                                         | 


## <a name="next-steps"></a>后续步骤

- 了解有关 [AMD EPYC 体系结构](https://bit.ly/2Epv3kC) 和 [多芯片体系](https://bit.ly/2GpQIMb)结构的详细信息。 有关更多详细信息，请参阅 [AMD EPYC 处理器的 HPC 优化指南](https://bit.ly/2T3AWZ9)。
- 阅读有关 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)中的最新公告和一些 HPC 示例。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。