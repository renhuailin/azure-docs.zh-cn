---
title: NVv4 系列
description: NVv4 系列 VM 的规格。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 6f2a70e932c1b810e9fef2bca75f1ff9b6e92048
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113353964"
---
# <a name="nvv4-series"></a>NVv4 系列 

NVv4 系列虚拟机由 [Amd Radeon INSTINCT MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU 和 AMD EPYC 7V12(Rome) CPU 提供支持，基本频率为 2.45GHz，所有核心峰值频率为 3.1GHz，单核峰值频率为 3.3GHz。 Azure 通过 NVv4 系列引入使用部分 GPU 的虚拟机。 选取适当大小虚拟机来运行 GPU 加速图形应用程序和虚拟桌面，可选范围为具有 2 GiB 帧缓冲区的 1/8 个 GPU 到具有 16 GiB 帧缓冲区的完整 GPU。 NVv4 虚拟机目前只支持 Windows 来宾操作系统。

<br>

[ACU](acu.md)：230-260<br>
[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[超级磁盘](disks-types.md#ultra-disk)：支持（[详细了解](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)可用性、使用情况和性能） <br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：支持（[预览版](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks)）<br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数/预期网络带宽 (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 / 1000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 / 2000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 / 4000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 / 8000 |

<sup>1</sup> NVv4 系列 VM 采用 AMD 同步多线程处理技术

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

若要利用运行 Windows 的 Azure NVv4 系列 VM 的 GPU 功能，必须安装 AMD GPU 驱动程序。

若要手动安装 AMD GPU 驱动程序，请参阅[适用于 Windows 的 N 系列 AMD GPU 驱动程序安装](./windows/n-series-amd-driver-setup.md)，了解受支持的操作系统、驱动程序、安装和验证步骤。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
