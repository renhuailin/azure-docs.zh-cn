---
title: Mv2 系列 - Azure 虚拟机
description: Mv2 系列 VM 的规格。
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: eccc5389330fd43025dbb66835b82e45d0284df9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216092"
---
# <a name="mv2-series"></a>Mv2 系列

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

Mv2 系列采用高吞吐量、低延迟平台，在超线程 Intel® Xeon® Platinum 8180M 2.5GHz (Skylake) 处理器上运行，其所有核心基础频率为 2.5 GHz，最大超频为 3.8 GHz。 所有 Mv2 系列虚拟机大小均可使用标准和高级永久性磁盘。 Mv2 系列实例具有内存优化的 VM 大小，提供卓越的计算性能以支持大型内存中数据库和工作负载，其内存到 CPU 比率高，非常适用于关系数据库服务器、大型缓存和内存中分析。

Mv2 系列 VM 采用 Intel® 超线程技术

[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 代系支持](generation-2.md)：第 2 代<br>
[写入加速器](./how-to-enable-write-accelerator.md)：支持<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>

|大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大 NIC 数 | 预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 | 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 | 16000 |
| Standard_M416ms_v2<sup>1,2</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 | 32000 |
| Standard_M416s_v2<sup>1,2</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 | 32000 |

<sup>1</sup> Mv2 系列 VM 仅支持第 2 代，并支持第 2 代支持的部分映像。 有关 Mv2 系列支持的映像的完整列表，请参阅下文。 如果你使用的是 Linux，请参阅 [Azure 对第 2 代 VM 的支持](./generation-2.md)，了解如何查找并选择映像。 如果你使用的是 Windows，请参阅 [Azure 对第 2 代 VM 的支持](./generation-2.md)，了解如何查找并选择映像。 

- Windows Server 2019 或更高版本
- SUSE Linux Enterprise Server 12 SP4 及更高版本或 SUSE Linux Enterprise Server 15 SP1 及更高版本
- Red Hat Enterprise Linux 7.6 或更高版本，以及 8.1 或更高版本
- Oracle Enterprise Linux 7.7 或更高版本，以及 8.1 或更高版本
- Ubuntu 18.04，带有 5.4.0-azure 内核或更高版本

<sup>2</sup> [受约束的可用核心大小](./constrained-vcpu.md)。


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和信息

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

定价计算器：[定价计算器](https://azure.microsoft.com/pricing/calculator/)

有关磁盘类型的详细信息：[磁盘类型](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
