---
title: HC 系列 - Azure 虚拟机
description: HC 系列 VM 的规格。
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: adc52df3a393abb5579a7e03015548808722b197
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697112"
---
# <a name="hc-series"></a>HC 系列

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

HC 系列 VM 针对由密集计算驱动的应用程序（如隐式有限元素分析、分子动力学和计算化学）进行了优化。 HC VM 搭载 44 个 Intel Xeon Platinum 8168 处理器核心，为每个 CPU 核心提供 8 GB RAM，但不支持超线程。 Intel Xeon Platinum 平台支持 Intel 丰富的软件工具（如 Intel 数学内核库）生态系统，和高级矢量处理功能（如 AVX-512）。

HC 系列 VM 具有 100 Gb/秒 Mellanox EDR InfiniBand。 这些 VM 以非阻塞胖树结构进行连接，以实现优化且一致的 RDMA 性能。 这些 VM 支持自适应路由和动态互联传输（DCT，对标准 RC 和 UD 传输的补充）。 这些功能可增强应用程序性能、可伸缩性和一致性，建议使用。 

[ACU](acu.md)：297-315<br>
[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[超级磁盘](disks-types.md#ultra-disk)：支持（[详细了解](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)可用性、使用情况和性能） <br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持（[详细了解](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965)性能和潜在问题）<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：支持（[预览版](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks)）<br>
<br>

| 大小 | vCPU | 处理器 | 内存 (GiB) | 内存带宽 GB/秒 | 基本 CPU 频率 (GHz) | 所有核心频率（GHz，峰值） | 单一核心频率（GHz，峰值） | RDMA 性能（Gb/秒） | MPI 支持 | 临时存储 (GiB) | 最大数据磁盘数 | 最大以太网 vNIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | All | 700 | 4 | 8 |

了解详细信息：
- [基础架构和 VM 拓扑](./workloads/hpc/hc-series-overview.md)
- 支持的[软件堆栈](./workloads/hpc/hc-series-overview.md#software-specifications)，包括受支持的操作系统
- HC 系列 VM 的预期[性能](./workloads/hpc/hc-series-performance.md)

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
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负载，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
