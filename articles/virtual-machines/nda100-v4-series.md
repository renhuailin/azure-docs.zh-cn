---
title: ND A100 v4 系列
description: ND A100 v4 系列 VM 的规格。
author: iafinder
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: iafinder
ms.openlocfilehash: d323338ba5b323db5ea1a144fe1010962407f3b9
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123423603"
---
# <a name="nd-a100-v4-series"></a>ND A100 v4 系列

ND A100 v4 系列虚拟机是 Azure GPU 系列的新增旗舰，专为高端深度学习训练以及紧密耦合的纵向扩展和横向扩展 HPC 工作负荷而设计。 

ND A100 v4 系列的起步配置是一个虚拟机 (VM) 和八个 NVIDIA Ampere A100 Tensor Core GPU。 基于 ND A100 v4 的部署可以纵向扩展到成千上万个 GPU，每个 VM 的互连带宽为 1.6 Tb/秒。 VM 内的每个 GPU 都具备其自己专用的、与拓扑无关的 200 Gb/秒 NVIDIA Mellanox HDR InfiniBand 连接。 这些连接在占用同一虚拟机规模集的 VM 之间自动配置，并且支持 GPUDirect RDMA。

每个 GPU 都配备用于 VM 内通信的 NVLINK 3.0 连接，并且实例也由 96 个物理第 2 代 AMD Epyc™ CPU 核心提供支持。

这些实例为众多 AI、ML、支持 GPU 加速的现成分析工具（如 TensorFlow、Pytorch、Caffe、RAPIDS）以及其他框架提供卓越的性能。 此外，一大组基于 NVIDIA 的 NCCL2 通信库构建的现有 AI 和 HPC 工具都支持横向扩展 InfiniBand 互连，以便实现无缝的 GPU 群集化。

> [!IMPORTANT]
> 若要开始使用 ND A100 v4 VM，请参阅 [HPC 工作负荷配置和优化](./workloads/hpc/configure.md)，了解包括驱动程序和网络配置在内的步骤。
> 由于 GPU 内存 I/O 占用量增加，ND A100 v4 需要使用[第 2 代 VM](./generation-2.md) 和市场映像。 强烈建议使用 [ Azure HPC 映像](./workloads/hpc/configure.md)。 支持 Azure HPC Ubuntu 18.04、20.04 和 Azure HPC CentOS 7.9 映像。
> 

<br>

[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[超级磁盘](disks-types.md#ultra-disk)：受支持（[详细了解](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)可用性、使用情况和性能） <br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 代系支持](generation-2.md)：第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：不支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：支持（[预览版](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks)）<br>
InfiniBand：受支持，GPUDirect RDMA，8 x 200 千兆位 HDR<br>
Nvidia NVLink 互连：支持<br>
<br>
ND A100 v4 系列支持以下内核版本： <br>
CentOS 7.9 HPC: 3.10.0-1160.24.1.el7.x86_64 <br>
Ubuntu 18.04：5.4.0-1043-azure <br>
Ubuntu 20.04：5.4.0-1046-azure <br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD)：GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大网络带宽 | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND96asr_v4 | 96 | 900 | 6000 | 8 个 A100 40 GB GPU (NVLink 3.0) | 40 | 32 | 80,000 / 800 | 24000 Mbps | 8 |




[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)] <br>

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
