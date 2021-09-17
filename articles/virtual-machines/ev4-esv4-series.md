---
title: Ev4 和 Esv4 系列 - Azure 虚拟机
description: Ev4 和 Esv4 系列 VM 的规范。
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: e4278cb662d4e3ee518a52651462ca04d634c556
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688295"
---
# <a name="ev4-and-esv4-series"></a>Ev4 和 Esv4 系列

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

Ev4 和 Esv4 系列在采用超线程配置的 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行，非常适合各种内存密集型企业应用程序，并且配备高达 504 GiB 的 RAM。 它的全核睿频时钟速度达到 3.4 GHz。

> [!NOTE]
> 有关常见问题解答，请参阅[无本地临时磁盘的 Azure VM 规格](azure-vms-no-temp-disk.yml)。

## <a name="ev4-series"></a>Ev4 系列

Ev4 系列规格在 Intel Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行。 Ev4 系列实例非常适合内存密集型企业应用程序。 Ev4 系列 VM 采用 Intel&reg; 超线程技术。

远程数据磁盘存储与虚拟机分开计费。 要使用高级存储磁盘，请使用 Esv4 规格。 Esv4 系列规格的定价和计费标准与 Ev4 系列相同。

[ACU](acu.md)：195 - 210<br>
[高级存储](premium-storage-performance.md)：不支持<br>
[高级存储缓存](premium-storage-performance.md)：不支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：支持<br>
[VM 代系支持](generation-2.md)：第 1 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持 <br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2_v4<sup>1</sup>  | 2 | 16   | 仅限远程存储 | 4 | 2|5000  |
| Standard_E4_v4  | 4 | 32  | 仅限远程存储 | 8 | 2|10000  |
| Standard_E8_v4  | 8 | 64 | 仅限远程存储 | 16 | 4|12500 |
| Standard_E16_v4 | 16 | 128 | 仅限远程存储 | 32 | 8|12500 |
| Standard_E20_v4 | 20 | 160 | 仅限远程存储 | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | 仅限远程存储 | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | 仅限远程存储 | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | 仅限远程存储 | 32| 8|30000 |

<sup>1</sup> 加速网络只能应用于单个 NIC。 


## <a name="esv4-series"></a>Esv4 系列

Esv4 系列规格在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行。 Esv4 系列实例非常适合于内存密集型企业应用程序。 Esv4 系列 VM 采用 Intel&reg; 超线程技术。 远程数据磁盘存储与虚拟机分开计费。

[ACU](acu.md)：195-210<br>
[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持 <br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>


| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大突发非缓存磁盘吞吐量：IOPS/MBps<sup>1</sup> |最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v4<sup>4</sup>  | 2 | 16  | 仅限远程存储 | 4 | 3200/48 | 4000/200 | 2|5000  |
| Standard_E4s_v4  | 4 | 32  | 仅限远程存储 | 8 | 6400/96 | 8000/200 | 2|10000  |
| Standard_E8s_v4  | 8 | 64  | 仅限远程存储 | 16 | 12800/192 | 16000/400 | 4|12500 |
| Standard_E16s_v4 | 16 | 128 | 仅限远程存储 | 32 | 25600/384 | 32000/800 | 8|12500 |
| Standard_E20s_v4 | 20 | 160 | 仅限远程存储 | 32 | 32000/480  | 40000/1000 | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | 仅限远程存储 | 32 | 51200/768  | 64000/1600 | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | 仅限远程存储 | 32 | 76800/1152 | 80000/2000 | 8|24000 |
| Standard_E64s_v4 <sup>2</sup> | 64 | 504| 仅限远程存储 | 32 | 80000/1200 | 80000/2000 | 8|30000 |
| Standard_E80is_v4 <sup>3</sup> | 80 | 504 | 仅限远程存储 | 32 | 80000/1200 | 80000/2000 | 8|30000 |

<sup>1</sup>  Esv4 系列 VM 可通过[突发方式](./disk-bursting.md)提高其磁盘性能，且最大突发的持续时间一次长达 30 分钟。<br>
<sup>2</sup> [受约束的可用核心大小](./constrained-vcpu.md)。<br>
<sup>3</sup> 实例与专用于单个客户的硬件隔离。<br>
<sup>4</sup> 加速网络只能应用于单个 NIC。 


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
