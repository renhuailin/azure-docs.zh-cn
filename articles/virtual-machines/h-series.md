---
title: H 系列 - Azure 虚拟机
description: H 系列 VM 的规格。
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 8f48e6bd94566922066b277553f64b25f5d55a45
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696335"
---
# <a name="h-series"></a>H 系列

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

H 系列 VM 针对由高 CPU 频率或每核心大内存要求驱动的应用程序进行了优化。 H 系列 VM 具有 8 或 16 个 Intel Xeon E5 2667 v3 处理器核心，每个 CPU 核心最多 14 GB RAM，无超线程。 H 系列提供 56 Gb/秒的 Mellanox FDR InfiniBand，具有无阻塞的丰富树配置，可实现一致的 RDMA 性能。 H 系列 VM 目前不支持 SR-IOV，支持 Intel MPI 5.x 和 MS-MPI。

[ACU](acu.md)：290-300<br>
[高级存储](premium-storage-performance.md)：不支持<br>
[高级存储缓存](premium-storage-performance.md)：不支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 代系支持](generation-2.md)：第 1 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：不支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>

| 大小 | vCPU | 处理器 | 内存 (GiB) | 内存带宽 GB/秒 | 基本 CPU 频率 (GHz) | 所有核心频率（GHz，峰值） | 单一核心频率（GHz，峰值） | RDMA 性能（Gb/秒） | MPI 支持 | 临时存储 (GiB) | 最大数据磁盘数 | 磁盘最大吞吐量：IOPS | 最大以太网 vNIC 数 |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x、MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x、MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> 对于 MPI 应用程序来说，专用 RDMA 后端网络是通过 FDR InfiniBand 网络启用的。

> [!NOTE]
> [支持 RDMA 的 VM](sizes-hpc.md#rdma-capable-instances) 中的 H 系列不支持 SR-IOV。 因此，支持的 [VM 映像](./workloads/hpc/configure.md#vm-images)、[InfiniBand 驱动程序](./workloads/hpc/enable-infiniband.md)的要求和支持的 [MPI 库](./workloads/hpc/setup-mpi.md)与支持 SR-IOV 的 VM 不同。

## <a name="software-specifications"></a>软件规格

| 软件规格     |HC 系列 VM           |
|-----------------------------|-----------------------|
| 最大 MPI 作业大小            | 4800 个内核（单个虚拟机规模中的 300 个虚拟机，且 singlePlacementGroup = true）  |
| MPI 支持                 | Intel MPI 5.x、MS-MPI  |
| 非 SRIOV RDMA 的操作系统支持   | CentOS/RHEL 6.5 - 7.4、SLES 12 SP4+、WinServer 2012 - 2016  |
| Orchestrator 支持        | CycleCloud、Batch、AKS  |

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告、HPC 工作负载示例和性能结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
