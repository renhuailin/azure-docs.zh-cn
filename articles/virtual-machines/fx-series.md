---
title: FX 系列
description: FX 系列 VM 的规格。
author: brbell
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: jushiman
ms.openlocfilehash: eb8e094b4a78e718efc86314ca74c0df92426248
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122229012"
---
# <a name="fx-series"></a>FX 系列

FX 系列在 Intel® Xeon® Gold 6246R (Cascade Lake) 处理器上运行。 该系列具有 4.0GHz 的全核 Turbo 频率、RAM 容量为 21GB 的 vCPU、高达 1TB 的总 RAM，并支持本地临时存储。 FX 系列将使需要高 CPU 时钟速度和高内存与 CPU 比的工作负荷、每个核心许可成本高的工作负荷以及需要较高单核性能的应用程序受益。 FX 系列的一个典型用法是电子设计自动化 (EDA) 工作负荷。

FX 系列 VM 具备 [Intel® 睿频加速技术 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel® 超线程技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)，并支持 [Intel® 高级矢量扩展 512 (intel® AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)。

[ACU](acu.md)：310 - 340<br>
[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：支持<br>
[VM 代系支持](generation-2.md)：第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：支持 <br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_FX4mds  | 4   | 84   | 168  | 8   | 40000/343     | 6700/104   | 2 | 4000  |
| Standard_FX12mds | 12  | 252  | 504  | 24  | 100000/1029   | 20000/314  | 4 | 8000  |
| Standard_FX24mds | 24  | 504  | 1008 | 32  | 200000/2057   | 40000/629  | 4 | 16000 |
| Standard_FX36mds | 36  | 756  | 1512 | 32  | 300000/3086   | 60000/944  | 8 | 24000 |
| Standard_FX48mds | 48  | 1008 | 2016 | 32  | 400000/3871   | 80000/1258 | 8 | 32000 |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和信息

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

定价计算器：[定价计算器](https://azure.microsoft.com/pricing/calculator/)

有关磁盘类型的详细信息，请参阅[磁盘类型](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
