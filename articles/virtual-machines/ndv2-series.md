---
title: NDv2 系列
description: NDv2 系列 VM 的规格。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: c0a13010be851e42ed1c50dc95330d497ab29c20
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780958"
---
# <a name="updated-ndv2-series"></a>已更新 NDv2 系列

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

NDv2 系列虚拟机是 GPU 系列中新增的系列，旨在满足最严苛 GPU 加速 AI、机器学习、模拟和 HPC 工作负载的需求。

NDv2 由 8 个 NVIDIA Tesla V100 NVLINK 连接的 GPU 提供支持，每个 GPU 都有 32 GB 的 GPU 内存。 每个 NDv2 VM 还具有 40 个非超线程的 Intel Xeon Platinum 8168 (Skylake) 核心和 672 GiB 的系统内存。

NDv2 实例使用 CUDA GPU 优化计算内核、众多 AI、ML、支持 GPU 加速的现成分析工具（如 TensorFlow、Pytorch、Caffe、RAPIDS）以及其他框架，为 HPC 和 AI 工作负载提供卓越的性能。

最重要的是，NDv2 是为计算密集型纵向扩展（每个 VM 利用 8 个 GPU）和横向扩展（利用多个协同工作的 VM）工作负载而构建的。 NDv2 系列现在支持 100 GB InfiniBand EDR 后端网络（类似于 HB 系列 HPC VM 上提供的网络），可对并行场景执行高性能聚类分析，包括针对 AI 和 ML 的分布式训练。 此后端网络支持所有主要的 InfiniBand 协议（包括 NVIDIA 的 NCCL2 库使用的协议），可实现 GPU 的无缝聚类分析。

> [!IMPORTANT]
> 在 ND40rs_v2 VM 上[启用 InfiniBand](./workloads/hpc/enable-infiniband.md) 时，请使用 4.7-1.0.0.1 Mellanox OFED 驱动程序。
>
> 由于 GPU 内存增加，新的 ND40rs_v2 VM 需要使用[第 2 代 VM](./generation-2.md) 和市场映像。 
>
> 请注意：每 GPU 内存为 16 GB 的 ND40s_v2 不再提供预览版，已被更新的 ND40rs_v2 取代。

<br>

[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[超级磁盘](disks-types.md#ultra-disk)：支持（[详细了解](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)可用性、使用情况和性能） <br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 代系支持](generation-2.md)：第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：支持（[预览版](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks)）<br>
InfiniBand：支持<br>
Nvidia NVLink 互连：支持<br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD)：GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大网络带宽 | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000 / 800 | 24000 Mbps | 8 |


## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

若要利用 Azure N 系列 VM 的 GPU 功能，必须安装 NVIDIA GPU 驱动程序。

[NVIDIA GPU 驱动程序扩展](./extensions/hpccompute-gpu-linux.md)可在 N 系列 VM 上安装适当的 NVIDIA CUDA 或 GRID 驱动程序。 请使用 Azure 门户或工具（例如 Azure PowerShell 或 Azure 资源管理器模板）安装或管理该扩展。 有关 VM 扩展的常规信息，请参阅 [Azure 虚拟机扩展和功能](./extensions/overview.md)。

如果选择手动安装 NVIDIA GPU 驱动程序，请参阅[适用于 Linux 的 N 系列 GPU 驱动程序安装](./linux/n-series-driver-setup.md)。

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

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
