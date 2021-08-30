---
title: HBv2 系列 VM 概述 - Azure 虚拟机 | Microsoft Docs
description: 了解 Azure 中的 HBv2 系列 VM 大小。
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: ee09191069c05ae67eb84ee15d739cd101de8ef0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460463"
---
# <a name="hbv2-series-virtual-machine-overview"></a>HBv2 系列虚拟机概述 

 
要在 AMD EPYC 上最大程度地提高高性能计算 (HPC) 应用程序性能，需要考虑周全的方法内存位置和进程放置。 下面概述了 AMD EPYC 体系结构，以及如何在 Azure 上为 HPC 应用程序实现它。 我们将使用术语“pNUMA”指代物理 NUMA 域，使用“vNUMA”指代虚拟化 NUMA 域 。 

从物理上讲，[HBv2 系列](../../hbv2-series.md)服务器是 2 * 64 核 EPYC 7742 CPU，总共 128 个物理内核。 这 128 个核心分为 32 个 pNUMA 域（每套接字 16 个），其中每个都是 4 个核心，被 AMD 称为“CPU Complex”（或“CCX”） 。 每个 CCX 都有自己的 L3 缓存，这是操作系统对 pNUMA/vNUMA 边界的理解。 四个相邻 CCX 共享对 2 个通道的物理 DRAM 的访问。 

为了给 Azure 虚拟机监控程序提供运行空间而又不干扰 VM，我们预留了物理 pNUMA 域 0 和 16（即，每个 CPU 套接字的第一个 CCX）。 所有剩余的 30 个 pNUMA 域将分配给 VM，此时它们会变为 vNUMA。 这样，VM 就会看到：

每个 VM `(30 vNUMA domains) * (4 cores/vNUMA) = 120` 个核心 

VM 本身不会意识到 pNUMA 0 和 16 已保留。 它将看到的 vNUMA 枚举为 0-29，每个套接字对称地有 15 个 vNUMA，vSocket 0上有 vNUMA 0-14，而 vSocket 1 上有 vNUMA 15-29。 下一部分介绍了如何以最佳方式在此非对称 NUMA 布局上运行 MPI 应用程序。 

进程固定适用于 HBv2 系列 VM，因为我们会将底层硅按原样公开给来宾 VM。 强烈建议使用进程固定来实现最佳性能和一致性。 


## <a name="hardware-specifications"></a>硬件规格 

| 硬件规格          | HBv2 系列 VM                   | 
|----------------------------------|----------------------------------|
| 核心数                            | 120（已禁用 SMT）               | 
| CPU                              | AMD EPYC 7742                    | 
| CPU 频率（非 AVX）          | ~3.1 GHz（单个 + 所有核心）    | 
| 内存                           | 4 GB/核心（总共 480 GB）         | 
| 本地磁盘                       | 960 GB NVMe（块）、480 GB SSD（页面文件） | 
| Infiniband                       | 200 Gb/s EDR Mellanox ConnectX-6 | 
| 网络                          | 50 Gb/秒以太网（40 Gb/秒可用）Azure 第二代 SmartNIC | 


## <a name="software-specifications"></a>软件规格 

| 软件规格     | HBv2 系列 VM                                            | 
|-----------------------------|-----------------------------------------------------------|
| 最大 MPI 作业大小            | 36,000 个核心（单个虚拟机规模中 300 个 VM，且 singlePlacementGroup=true） |
| MPI 支持                 | HPC-X、Intel MPI、OpenMPI、MVAPICH2、MPICH、Platform MPI  |
| 其他框架       | UCX、libfabric、PGAS |
| Azure 存储支持       | 标准磁盘和高级磁盘（最多 8 个磁盘） |
| SRIOV RDMA 的操作系统支持   | CentOS/RHEL 7.6+、Ubuntu 16.04+、SLES 12 SP4+、WinServer 2016+  |
| Orchestrator 支持        | CycleCloud、Batch、AKS；[群集配置选项](../../sizes-hpc.md#cluster-configuration-options)  |

> [!NOTE] 
> 在 HBv2 和大于 64（虚拟或物理）核的其他 VM 上，不支持 Windows Server 2012 R2。 有关详细信息，请参阅 [Windows Server 上 Hyper-V 支持的 Windows 来宾操作系统](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [AMD EPYC 体系结构](https://bit.ly/2Epv3kC)和[多芯片体系结构](https://bit.ly/2GpQIMb)。 有关详细信息，请参阅 [AMD EPYC 处理器的 HPC 优化指南](https://bit.ly/2T3AWZ9)。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告、HPC 工作负载示例和性能结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
