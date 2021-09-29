---
title: Ev3 系列和 Esv3 系列
description: Ev3 和 Esv3 系列 VM 的规范。
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: mimckitt
ms.openlocfilehash: 251d9d2afd541159ae48d5e769b24e0f925fd65d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776588"
---
# <a name="ev3-and-esv3-series"></a>Ev3 和 Esv3 系列

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

Ev3 和 Esv3 系列在采用超线程配置的 Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake) 或 Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 处理器上运行，为大多数常规用途工作负荷提供更好的价值定位，并使 Ev3 满足大多数其他云的常规用途 VM 的要求。  在磁盘和网络限制已基于核心进行了调整以适应超线程技术的同时，内存也得到了扩展（从 7 GiB/vCPU 到 8 GiB/vCPU）。 Ev3 是 D/Dv2 系列的高内存 VM 大小产品的后继产品。

## <a name="ev3-series"></a>Ev3 系列

Ev3 系列实例在 Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake) 或 Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 处理器上运行，并配置了 Intel Turbo Boost Technology 2.0。 Ev3 系列实例适用于内存密集型企业应用程序。

数据磁盘存储与虚拟机分开计费。 若要使用高级存储磁盘，请使用 ESv3 大小。 ESv3 系列大小的定价和计费标准与 Ev3 系列相同。

Ev3 系列 VM 的 Intel® 超线程技术功能。

[ACU](acu.md)：160 - 190<br>
[高级存储](premium-storage-performance.md)：不支持<br>
[高级存储缓存](premium-storage-performance.md)：不支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：支持<br>
[VM 代系支持](generation-2.md)：第 1 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大临时存储吞吐量：IOPS/读取 MBps/写入 MBps | 最大网卡数/网络带宽等级 |
|---|---|---|---|---|---|---|
| Standard_E2_v3<sup>1</sup>  | 2  | 16  | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8/10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |
| Standard_E64i_v3 <sup>2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

<sup>1</sup> 加速网络只能应用于单个 NIC。 
<sup>2</sup> 实例对于专用于单个客户的硬件独立。

## <a name="esv3-series"></a>Esv3 系列

Esv3 系列实例在 Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake) 或 Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 处理器上运行，配置了 Intel Turbo Boost Technology 2.0，并使用高级存储。 Esv3 系列实例最适用于内存密集型企业应用程序。

Esv3 系列 VM 的 Intel® 超线程技术功能。

[ACU](acu.md)：160-190<br>
[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：支持 <br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 突发缓存吞吐量和临时存储吞吐量：IOPS/MBps<sup>3</sup> | 最大非缓存磁盘吞吐量：IOPS/MBps |  突发非缓存磁盘吞吐量：IOPS/MBps<sup>3</sup>| 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v3<sup>4</sup>                | 2  | 16  | 32  | 4  | 4000/32 (50)       | 4000/100    | 3200/48    | 4000/100 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>   | 4  | 32  | 64  | 8  | 8000/64 (100)      | 8000/200    | 6400/96    | 8000/200 | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>   | 8  | 64  | 128 | 16 | 16000/128 (200)    | 16000/400   | 12800/192  | 16000/400 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup>  | 16 | 128 | 256 | 32 | 32000/256 (400)    | 32000/800   | 25600/384  | 32000/800 | 8/8000 |
| Standard_E20s_v3               | 20 | 160 | 320 | 32 | 40000/320 (400)    | 40000/1000  | 32000/480  | 40000/1000 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 64000/1600  | 51200/768  | 64000/1600 | 8/16000 |
| Standard_E48s_v3               | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 96000/2000  | 76800/1152 | 80000/2000 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 128000/2000 | 80000/1200 | 80000/2000 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 128000/2000 | 80000/1200 | 80000/2000 | 8/30000 |

<sup>1</sup> [受约束的可用核心规格](./constrained-vcpu.md)。<br>
<sup>2</sup> 实例对于专用于单个客户的硬件独立。<br>
<sup>3</sup> Esv3 系列 VM 可通过[突发](./disk-bursting.md)方式提高其磁盘性能，并达到其突发的最大值，一次长达 30 分钟。<br>
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

有关磁盘类型的详细信息，请参阅 [Azure 有哪些可用的磁盘类型？](disks-types.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
