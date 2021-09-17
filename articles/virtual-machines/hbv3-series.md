---
title: HBv3 系列 - Azure 虚拟机
description: HBv3 系列 VM 的规格。
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 28198fe22bb20e3bb1e4a08715686eca62f1fbba
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689053"
---
# <a name="hbv3-series"></a>HBv3 系列

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

HBv3 系列 VM 针对 HPC 应用程序进行了优化，例如流体动力学、显式和隐式有限元素分析、天气建模、地震处理、容器模拟和 RTL 模拟。 HBv3 VM 最多具有 120 个 AMD EPYC™ 7003 系列（米兰）CPU 内核、448 GB RAM，无超线程。 HBv3 系列 VM 还提供 350 GB/秒的内存带宽，每个内核最多可达 32 MB 的 L3 缓存，最高可达 7 GB/秒的块设备 SSD 性能和最高为 3.675 GHz 的时钟频率。 

所有 HBv3 系列 VM 均具有 NVIDIA 网络提供的 200 Gb/秒 HDR InfiniBand，可启用超级计算机规模的 MPI 工作负载。 这些 VM 以非阻塞胖树结构进行连接，以实现优化且一致的 RDMA 性能。 HDR InfiniBand 结构支持自适应路由和动态互联传输（DCT，对标准 RC 和 UD 传输的补充）。 这些功能可增强应用程序性能、可伸缩性和一致性，强烈建议使用。

[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[超级磁盘](disks-types.md#ultra-disk)：支持（[详细了解](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)可用性、使用情况和性能） <br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：即将推出<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：支持（[预览版](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks)）<br>
<br>

|大小 |vCPU |处理器 |内存 (GiB) |内存带宽 GB/秒 |基本 CPU 频率 (GHz) |所有核心频率（GHz，峰值） |单一核心频率（GHz，峰值） |RDMA 性能（Gb/秒） |MPI 支持 |临时存储 (GiB) |最大数据磁盘数 |最大以太网 vNIC 数 |
|----|----|----|----|----|----|----|----|----|----|----|----|----|
|Standard_HB120rs_v3    |120 |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |全部 |2 * 960 |32 |8 |
|Standard_HB120-96rs_v3 |96  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |全部 |2 * 960 |32 |8 |
|Standard_HB120-64rs_v3 |64  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |全部 |2 * 960 |32 |8 |
|Standard_HB120-32rs_v3 |32  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |全部 |2 * 960 |32 |8 |
|Standard_HB120-16rs_v3 |16  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |全部 |2 * 960 |32 |8 |

了解详细信息：
- [基础架构和 VM 拓扑](./workloads/hpc/hbv3-series-overview.md)
- 受支持的[软件堆栈](./workloads/hpc/hbv3-series-overview.md#software-specifications)，包括受支持的 OS
- HBv3 系列 VM 的预期[性能](./workloads/hpc/hbv3-performance.md)

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
