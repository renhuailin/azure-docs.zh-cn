---
title: HBv2 系列 - Azure 虚拟机
description: HBv2 系列 VM 的规格。
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: edc0e7cb0c7e2f9deb6e8af785fb98be1a1d9968
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565999"
---
# <a name="hbv2-series"></a>HBv2 系列

HBv2 系列 VM 针对由内存带宽驱动的应用程序（如流体动态、有限元素分析和容器模拟）进行了优化。 HBv2 VM 搭载 120 个 AMD EPYC 7742 处理器核心，为每个 CPU 核心提供 4 GB RAM，但不支持同步多线程。 每个 HBv2 VM 提供高达 340 GB/秒的内存带宽，以及高达 4 兆次的 FP64 浮点运算能力。

HBv2 系列 VM 具有 200 Gb/秒 Mellanox EDR InfiniBand。 这些 VM 以非阻塞胖树结构进行连接，以实现优化且一致的 RDMA 性能。 这些 VM 支持自适应路由和动态互联传输（DCT，对标准 RC 和 UD 传输的补充）。 这些功能增强了应用性能、可伸缩性和一致性，我们强烈建议使用。

[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 代系支持](generation-2.md)：第 1 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持（[详细了解](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965)性能和潜在问题） <br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>

| 大小 | vCPU | 处理器 | 内存 (GiB) | 内存带宽 GB/秒 | 基本 CPU 频率 (GHz) | 所有核心频率（GHz，峰值） | 单一核心频率（GHz，峰值） | RDMA 性能（Gb/秒） | MPI 支持 | 临时存储 (GiB) | 最大数据磁盘数 | 最大以太网 vNIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | 全部 | 480 + 960 | 8 | 8 |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

- 在 [HPC 工作负载](./workloads/hpc/overview.md)中详细了解如何[配置 VM](./workloads/hpc/configure.md)、[启用 InfiniBand](./workloads/hpc/enable-infiniband.md)、[设置 MPI](./workloads/hpc/setup-mpi.md) 以及如何为 Azure 优化 HPC 应用程序。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告以及一些 HPC 示例和结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
