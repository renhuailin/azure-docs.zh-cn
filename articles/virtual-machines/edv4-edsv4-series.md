---
title: Edv4 和 Edsv4 系列
description: 适用于 Ev4、Edv4、Esv4 和 Edsv4 系列 VM 的规格。
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: f00188c93bc664395ddb9b72fc5150ede31bf670
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105403"
---
# <a name="edv4-and-edsv4-series"></a>Edv4 和 Edsv4 系列

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

Edv4 和 Edsv4 系列在超线程配置的 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行，非常适合于各种占用大量内存的企业应用程序，配备高达 504 GiB 的 RAM、[Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; 超线程技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)和 [Intel&reg; 高级矢量扩展 512 (intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)。 它们还支持[英特尔&reg; 深度学习加速技术](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)。 与 [Gen2 VM](./generation-2.md) 的 [Ev3/Esv3](./ev3-esv3-series.md) 大小相比，这些新的 VM 大小的本地存储将增加 50%，而且本地磁盘的读写 IOPS 更佳。 它的全核睿频时钟速度达到 3.4 GHz。 

## <a name="edv4-series"></a>Edv4 系列

Edv4 系列尺寸在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行。 除了快速和大型本地 SSD 存储（最高为 2400 GiB）外，Edv4 虚拟机大小还配备高达 504 GiB 的 RAM。 这些虚拟机非常适合于内存密集型企业应用程序和受益于低延迟、高速本地存储的应用程序。 可以将标准 SSD 和标准 HDD 磁盘存储附加到 Edv4 VM。 

[ACU](acu.md)：195 - 210<br>
[高级存储](premium-storage-performance.md)：不支持<br>
[高级存储缓存](premium-storage-performance.md)：不支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<sup>1</sup> <br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | <sup>**</sup> 临时存储的最大吞吐量：IOPS/MBps | 最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4<sup>1</sup>  | 2 | 16 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8|10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8|30000 |

<sup>1</sup> 加速网络只能应用于单个 NIC。 <br>
<sup>**</sup> 这些 IOPS 值可以通过使用[第 2 代 VM](generation-2.md) 来实现

## <a name="edsv4-series"></a>Edsv4 系列

Edsv4 系列尺寸在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行。 除了快速和大型本地 SSD 存储（最高为 2400 GiB）外，Edsv4 虚拟机大小还配备高达 504 GiB 的 RAM。 这些虚拟机非常适合于内存密集型企业应用程序和受益于低延迟、高速本地存储的应用程序。

[ACU](acu.md)：195-210<br>
[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持 <br>
[临时 OS 磁盘](ephemeral-os-disks.md)：支持 <br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | <sup>**</sup> 临时存储的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大突发非缓存磁盘吞吐量：IOPS/MBps<sup>1</sup> | 最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4<sup>4</sup>  | 2 | 16 | 75 | 4 | 19000/120(50) | 3200/48 | 4000/200 | 2|1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242(100) | 6400/96 | 8000/200 | 2|2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485(200) | 12800/192 | 16000/400 | 4|4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968(400) | 25600/384 | 32000/800 | 8|8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211(500)  | 32000/480 | 40000/1000 | 8|10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936(800) | 51200/768  | 64000/1600 | 8|16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 80000/2000 | 8|24000 |
| Standard_E64ds_v4 <sup>2</sup> | 64 | 504 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 80000/2000 | 8|30000 |
| Standard_E80ids_v4 <sup>3</sup> | 80 | 504 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 80000/2000 | 8|30000 |

<sup>**</sup>这些 IOP 值可以通过使用 [Gen2 VM](generation-2.md) 来保证<br>
<sup>1</sup>  Edsv4 系列 VM 可通过[突发方式](./disk-bursting.md)提高其磁盘性能，且最大突发的持续时间一次长达 30 分钟。<br>
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
