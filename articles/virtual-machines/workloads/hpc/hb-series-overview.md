---
title: HB 系列 VM 概述 - Azure 虚拟机 | Microsoft Docs
description: 了解 Azure 中 HB 系列 VM 大小的预览支持。
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 7bcd49de1b92dd91ee1c643d1f48629d8b0a8c02
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694640"
---
# <a name="hb-series-virtual-machines-overview"></a>HB 系列虚拟机概述

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

要在 AMD EPYC 上最大程度地提高高性能计算 (HPC) 应用程序性能，需要考虑周全的方法内存位置和进程放置。 下面概述了 AMD EPYC 体系结构，以及如何在 Azure 上为 HPC 应用程序实现它。 我们将使用术语“pNUMA”指代物理 NUMA 域，使用“vNUMA”指代虚拟化 NUMA 域。

从物理上讲，[HB 系列](../../hb-series.md)服务器是 2 * 32 核 EPYC 7551 CPU，总共 64 个物理内核。 这 64 个内核分为 16 个 pNUMA 域（每套接字 8 个），其中每个都是四个内核，称为“CPU Complex”（或“CCX”）。 每个 CCX 都有其自己的 L3 缓存，这是操作系统将看到 pNUMA/vNUMA 边界的方式。 一对相邻的 CCX 共享对两个物理 DRAM（HB 系列服务器中为 32 GB 的 DRAM）通道的访问。

为了给 Azure 虚拟机监控程序提供运行空间而又不干扰 VM，我们预留了物理 pNUMA 域 0（第一个 CCX）。 然后，我们为 VM 分配 pNUMA 域 1-15（其余的 CCX 单元）。 VM 将会看到：

每个 VM `(15 vNUMA domains) * (4 cores/vNUMA) = 60` 个内核

VM 本身并不知道未向其提供 pNUMA 0。 VM 将 pNUMA 1-15 理解为 vNUMA 0-14，其中 vSocket 0 上有 7 个 vNUMA，vSocket 1 上有 8 个 vNUMA。 尽管这是不对称的，但你的操作系统仍应启动并正常运行。 稍后在本指南中，我们将指示如何最好地在此非对称 NUMA 布局上运行 MPI 应用程序。

进程固定将在 HB 系列 VM 上运行，因为我们会将底层硅按原样公开给来宾 VM。 强烈建议使用进程固定来实现最佳性能和一致性。

下图显示了为 Azure 虚拟机监控程序和 HB 系列 VM 保留的内核的隔离。

![为 Azure 虚拟机监控程序和 HB 系列 VM 保留的内核的隔离](./media/architecture/hb-segregation-cores.png)

## <a name="hardware-specifications"></a>硬件规格

| 硬件规格                | HB 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心数                            | 60（已禁用 SMT）                |
| CPU                              | AMD EPYC 7551                    |
| CPU 频率（非 AVX）          | ~ 2.55 GHz（单个 + 所有内核）   |
| 内存                           | 4 GB/内核（总共 240 GB）         |
| 本地磁盘                       | 700 GB SSD                       |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 |
| 网络                          | 50 Gb 以太网（40 Gb 可用）Azure 第二代 SmartNIC |

## <a name="software-specifications"></a>软件规格

| 软件规格           |HB 系列 VM           |
|-----------------------------|-----------------------|
| 最大 MPI 作业大小            | 18000 个内核（单个虚拟机规模中的 300 个虚拟机，且 singlePlacementGroup = true）  |
| MPI 支持                 | HPC-X、Intel MPI、OpenMPI、MVAPICH2、MPICH、Platform MPI  |
| 其他框架       | UCX、libfabric、PGAS |
| Azure 存储支持       | 标准磁盘和高级磁盘（最多 4 个磁盘） |
| SRIOV RDMA 的操作系统支持   | CentOS/RHEL 7.6+、Ubuntu 16.04+、SLES 12 SP4+、WinServer 2016+  |
| Orchestrator 支持        | CycleCloud、Batch、AKS；[群集配置选项](../../sizes-hpc.md#cluster-configuration-options) |

## <a name="next-steps"></a>后续步骤

- 详细了解 [AMD EPYC 体系结构](https://bit.ly/2Epv3kC)和[多芯片体系结构](https://bit.ly/2GpQIMb)。 有关详细信息，请参阅 [AMD EPYC 处理器的 HPC 优化指南](https://bit.ly/2T3AWZ9)。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告、HPC 工作负载示例和性能结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
