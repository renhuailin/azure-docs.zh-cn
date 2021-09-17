---
title: HC 系列 VM 概述 - Azure 虚拟机 | Microsoft Docs
description: 了解 Azure 中 HC 系列 VM 大小的预览支持。
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 0db18bf162ee91ddccc070d7250a2ee35b05f5a9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689203"
---
# <a name="hc-series-virtual-machine-overview"></a>HC 系列虚拟机概述

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

要在 Intel Xeon 可扩展处理器上最大限度提高 HPC 应用程序性能，需要一种周全的方法来处理这一新体系结构上的放置。 下文概述了如何在适用于 HPC 应用程序的 Azure HC 系列 VM 上实现它。 我们将使用术语“pNUMA”指代物理 NUMA 域，使用“vNUMA”指代虚拟化 NUMA 域。 同样，我们将使用术语“pCore”指代物理 CPU 核心，使用“vCore”指代虚拟化 CPU 核心。

从物理上讲，[HC 系列](../../hc-series.md)服务器是 2 * 24 核 Intel Xeon Platinum 8168 CPU，总共 48 个物理核心。 每个 CPU 都是一个 pNUMA 域，它们对六个 DRAM 通道的访问权限相同。 Intel Xeon Platinum CPU 的 L2 缓存是前几代的 4 倍大（256 KB/核 -> 1 MB/核），同时相比以前的 Intel CPU 还减少了 L3 缓存（2.5 MB/核 -> 1.375 MB/核）。

上面的拓扑结构也可继承到 HC 系列虚拟机监控程序配置中。 为了给 Azure 虚拟机监控程序提供运行空间而又不干扰 VM，我们预留了 pCore 0-1 和 24-25（即每个套接字上的前 2 个 pCore）。 然后，我们会将 pNUMA 域所有剩余的核心分配给 VM。 这样，VM 就会看到：

每个 VM `(2 vNUMA domains) * (22 cores/vNUMA) = 44` 个核心

VM 并不知道自己没有 pCore 0-1 和 24-25。 因此，它公开每个 vNUMA，就像它本身有 22 个核心一样。

Intel Xeon Platinum、Gold 和 Silver CPU 还引入了片上 2D 网格网络，用于在 CPU 插槽内部和外部进行通信。 强烈建议使用进程固定来实现最佳性能和一致性。 进程固定适用于 HC 系列 VM，因为底层硅按原样公开给来宾 VM。

下图显示了为 Azure 虚拟机监控程序和 HC 系列 VM 保留的核心的隔离。

![为 Azure 虚拟机监控程序和 HC 系列 VM 保留的核心的隔离](./media/architecture/hc-segregation-cores.png)

## <a name="hardware-specifications"></a>硬件规格

| 硬件规格          | HC 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心数                            | 44（已禁用 HT）                 |
| CPU                              | Intel Xeon Platinum 8168         |
| CPU 频率（非 AVX）          | 3.7 GHz（单核），2.7-3.4 GHz（所有核心） |
| 内存                           | 8 GB/核心（共 352）            |
| 本地磁盘                       | 700 GB SSD                       |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5   |
| 网络                          | 50 Gb 以太网（40 Gb 可用）Azure 第二代 SmartNIC    |

## <a name="software-specifications"></a>软件规格

| 软件规格     |HC 系列 VM           |
|-----------------------------|-----------------------|
| 最大 MPI 作业大小            | 13200 个核心（单个虚拟机规模中 300 个 VM，且 singlePlacementGroup=true）  |
| MPI 支持                 | HPC-X、Intel MPI、OpenMPI、MVAPICH2、MPICH、Platform MPI  |
| 其他框架       | UCX、libfabric、PGAS |
| Azure 存储支持       | 标准磁盘和高级磁盘（最多 4 个磁盘） |
| SRIOV RDMA 的操作系统支持   | CentOS/RHEL 7.6+、Ubuntu 16.04+、SLES 12 SP4+、WinServer 2016+  |
| Orchestrator 支持        | CycleCloud、Batch、AKS；[群集配置选项](../../sizes-hpc.md#cluster-configuration-options)  |

## <a name="next-steps"></a>后续步骤

- 详细了解 [Intel Xeon SP 体系结构](https://software.intel.com/content/www/us/en/develop/articles/intel-xeon-processor-scalable-family-technical-overview.html)。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告、HPC 工作负载示例和性能结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
