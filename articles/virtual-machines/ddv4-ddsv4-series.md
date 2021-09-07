---
title: Ddv4 和 Ddsv4 系列
description: Dv4、Ddv4、Dsv4 和 Ddsv4 系列 VM 的规格。
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: b29fb382e51a351291868e4417a819e969a1d249
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123097546"
---
# <a name="ddv4-and-ddsv4-series"></a>Ddv4 和 Ddsv4 系列

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

Ddv4 和 Ddsv4 系列采用英特尔&reg; 至强&reg; 铂金 8272CL (Cascade Lake) 处理器，具有超线程配置，为大多数通用工作负载提供了更好的价值主张。 它的全核睿频时钟速度为 3.4 GHz，采用 [Intel&reg; 睿频加速技术 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; 超线程技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)和 [Intel&reg; 高级矢量扩展 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)。 它们还支持 [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)。 与 [Gen2 VM](./generation-2.md) 的 [Dv3/Dsv3](./dv3-dsv3-series.md) 大小相比，这些新的 VM 大小的本地存储将增加 50%，而且本地磁盘的读写 IOPS 更佳。

D 系列用例包括企业级应用程序、关系数据库、内存中缓存和分析。

## <a name="ddv4-series"></a>Ddv4 系列

Ddv4 系列大小采用英特尔&reg; 至强&reg; 铂金 8272CL (Cascade Lake) 处理器。 Ddv4 系列提供 vCPU、内存和临时磁盘的组合，适用于大多数生产工作负载。

新的 Ddv4 VM 大小包括一个快速、大型本地 SSD 存储（多达 2,400 GiB），适用于需要低延迟、高速本地存储的应用程序，例如，适用于需要快速读取/写入临时存储或需要临时存储才能存储缓存或临时文件的应用程序。 可以将标准 SSD 和标准 HDD 存储附加到 Ddv4 VM。 远程数据磁盘存储与虚拟机分开计费。

[ACU](acu.md)：195-210<br>
[高级存储](premium-storage-performance.md)：不支持<br>
[高级存储缓存](premium-storage-performance.md)：不支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：支持 <br>
<br> 

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | <sup>**</sup> 临时存储的最大吞吐量：IOPS/MBps | 最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v4<sup>1</sup> | 2 | 8 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8|30000 |

<sup>**</sup> 这些 IOPS 值可以通过使用[第 2 代 VM](generation-2.md) 来实现<br>
<sup>1</sup> 加速网络只能应用于单个 NIC。 

## <a name="ddsv4-series"></a>Ddsv4 系列

Ddsv4 系列采用英特尔&reg; 至强&reg; 铂金 8272CL (Cascade Lake) 处理器。 Ddsv4 系列提供 vCPU、内存和临时磁盘的组合，适用于大多数生产工作负载。

新的 Ddsv4 VM 大小包括一个快速、大型本地 SSD 存储（多达 2,400 GiB），适用于需要低延迟、高速本地存储的应用程序，例如，适用于需要快速读取/写入临时存储或需要临时存储才能存储缓存或临时文件的应用程序。 

 > [!NOTE]
 >Ddsv4 大小的定价和计费标准与 Ddv4 系列相同。

[ACU](acu.md)：195-210<br>
[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：支持 <br>
<br> 

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | <sup>**</sup> 临时存储的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/MBps |  最大突发非缓存磁盘吞吐量：IOPS/MBps<sup>1</sup> | 最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4<sup>2</sup> | 2 | 8 | 75 | 4 | 19000/120(50) | 3200/48 | 4000/200 | 2|1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242(100) | 6400/96 | 8000/200 | 2|2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485(200) | 12800/192 | 16000/400 | 4|4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968(400) | 25600/384 | 32000/800 | 8|8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936(800) | 51200/768 | 64000/1600 | 8|16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 80000/2000 | 8|24000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 80000/2000 | 8|30000 |

<sup>**</sup> 这些 IOPS 值可以通过使用[第 2 代 VM](generation-2.md) 来实现<br>
<sup>1</sup> Ddsv4 系列 VM 可通过[突发方式](./disk-bursting.md)提高其磁盘性能，最大突发的持续时间一次长达 30 分钟。<br>
<sup>2</sup> 加速网络只能应用于单个 NIC。 

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
