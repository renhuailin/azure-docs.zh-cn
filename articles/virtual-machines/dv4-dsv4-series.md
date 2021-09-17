---
title: Dv4 和 Dsv4 系列 - Azure 虚拟机
description: Dv4 和 Dsv4 系列 VM 的规范。
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: a32fdd1da872c45c0e2402cdddd0685ef4296c7a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691211"
---
# <a name="dv4-and-dsv4-series"></a>Dv4 和 Dsv4 系列

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

Dv4 和 Dsv4 系列以超线程配置在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行，为大多数常规用途工作负载带来价值提升。 它的全核睿频时钟速度达到 3.4 GHz。 

> [!NOTE]
> 有关常见问题，请参阅[无本地临时磁盘的 Azure VM 大小](azure-vms-no-temp-disk.yml)。

## <a name="dv4-series"></a>Dv4 系列

Dv4 系列规格在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上运行。 Dv4 系列规格为大多数生产工作负载提供 vCPU、内存和远程存储选项组合。 Dv4 系列 VM 采用 [Intel&reg; 超线程技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)。

远程数据磁盘存储与虚拟机分开计费。 若要使用高级存储磁盘，请使用 Dsv4 规格。 Dsv4 规格的定价和计费标准与 Dv4 系列的相同。

> [!NOTE]
> 重启后，驱动器 C（从 Azure 门户附加的第一个数据磁盘）旁边可能会出现一个名为“Data_loss_warning.txt”的文件。 在这种情况下，不管文件名如何，磁盘上都没有数据丢失。 Data_loss_warning.txt 文件通常复制到临时驱动器上。 如果使用的 VM 没有临时驱动器，WindowsAzureGuestAgent 会错误地将文件复制到第一个驱动器号。 在 v4 VM 中，第一个驱动器号是数据磁盘。
>
> VM 代理的最新版本（版本 2.7.41491.999）中应用了此问题的解决方案。

[ACU](acu.md)：195-210<br>
[高级存储](premium-storage-performance.md)：不支持<br>
[高级存储缓存](premium-storage-performance.md)：不支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持 <br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2_v4<sup>1</sup> | 2 | 8 | 仅限远程存储 | 4 | 2|5000 |
| Standard_D4_v4 | 4 | 16  | 仅限远程存储 | 8 | 2|10000 |
| Standard_D8_v4 | 8 | 32 | 仅限远程存储 | 16 | 4|12500 |
| Standard_D16_v4 | 16 | 64 | 仅限远程存储 | 32 | 8|12500 |
| Standard_D32_v4 | 32 | 128 | 仅限远程存储 | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | 仅限远程存储 | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | 仅限远程存储 | 32 | 8|30000 |

<sup>1</sup> 加速网络只能应用于单个 NIC。 


## <a name="dsv4-series"></a>Dsv4 系列

Dsv4 系列规格在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上运行。 Dv4 系列规格为大多数生产工作负载提供 vCPU、内存和远程存储选项组合。 Dsv4 系列 VM 采用 [Intel&reg; 超线程技术](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)。 远程数据磁盘存储与虚拟机分开计费。

[ACU](acu.md)：195-210<br>
[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大突发非缓存磁盘吞吐量：IOPS/MBps<sup>1</sup> | 最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v4<sup>2</sup> | 2 | 8  | 仅限远程存储 | 4 | 3200/48 | 4000/200 |2|5000 |
| Standard_D4s_v4 | 4 | 16 | 仅限远程存储 | 8 | 6400/96 | 8000/200 |2|10000 |
| Standard_D8s_v4 | 8 | 32 | 仅限远程存储 | 16 | 12800/192 | 16000/400 |4|12500 |
| Standard_D16s_v4 | 16 | 64  | 仅限远程存储 | 32 | 25600/384 | 32000/800 |8|12500 |
| Standard_D32s_v4 | 32 | 128 | 仅限远程存储 | 32 | 51200/768 | 64000/1600 |8|16000 |
| Standard_D48s_v4 | 48 | 192 | 仅限远程存储 | 32 | 76800/1152 | 80000/2000 |8|24000 |
| Standard_D64s_v4 | 64 | 256 | 仅限远程存储 | 32 | 80000/1200 | 80000/2000 |8|30000 |

<sup>1</sup> Dsv4 系列 VM 可通过[突发方式](./disk-bursting.md)提高其磁盘性能，最大突发的持续时间一次长达 30 分钟。<br>
<sup>2</sup> 加速网络只能应用于单个 NIC。 
