---
title: HBv2 系列 - Azure 虚拟机
description: HBv2 系列 VM 的规格。
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 8b177e1a4da4f1d2cd2b336f5929d9027c3b07cb
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803886"
---
# <a name="hbv2-series"></a>HBv2 系列

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

HBv2 系列 VM 针对内存带宽驱动的应用程序（例如流体动力学、有限元分析和储层模拟）进行了优化。 HBv2 VM 搭载 120 个 AMD EPYC 7742 处理器核心，为每个 CPU 核心提供 4 GB RAM，但不支持同步多线程。 每个 HBv2 VM 提供高达 340 GB/秒的内存带宽，以及高达 4 兆次的 FP64 浮点运算能力。

HBv2 系列 VM 具有 200 Gb/秒 Mellanox EDR InfiniBand。 这些 VM 以非阻塞胖树结构进行连接，以实现优化且一致的 RDMA 性能。 这些 VM 支持自适应路由和动态互联传输 (DCT)（标准 RC 和 UD 传输除外）。 这些功能可增强应用程序性能、可伸缩性和一致性，建议使用。

[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[超级磁盘](disks-types.md#ultra-disk)：支持（[详细了解](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)可用性、使用情况和性能） <br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持（[详细了解](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965)性能和潜在问题） <br>
[临时 OS 磁盘](ephemeral-os-disks.md)：支持（[预览版](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks)）<br>
<br>

| 大小 | vCPU | 处理器 | 内存 (GiB) | 内存带宽 GB/秒 | 基本 CPU 频率 (GHz) | 所有核心频率（GHz，峰值） | 单一核心频率（GHz，峰值） | RDMA 性能（Gb/秒） | MPI 支持 | 临时存储 (GiB) | 最大数据磁盘数 | 最大以太网 vNIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 456 | 350 | 2.45 | 3.1 | 3.3 | 200 | 全部 | 480 + 960 | 8 | 8 |

了解详细信息：
- [基础架构和 VM 拓扑](./workloads/hpc/hbv2-series-overview.md)
- 支持的[软件堆栈](./workloads/hpc/hbv2-series-overview.md#software-specifications)，包括受支持的操作系统
- HBv2 系列 VM 的预期[性能](./workloads/hpc/hbv2-performance.md)。

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和信息

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

定价计算器：[定价计算器](https://azure.microsoft.com/pricing/calculator/)

有关磁盘类型的详细信息，请参阅 [Azure 有哪些可用的磁盘类型？](disks-types.md)


## <a name="next-steps"></a>后续步骤

- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告、HPC 工作负载示例和性能结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
