---
title: NCas T4 v3 系列
description: NCas T4 v3 系列 VM 的规格。
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
author: vikancha-MSFT
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: vikancha
ms.openlocfilehash: 55799d15ef0ebe8af0f4a79b583143394540f48b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697964"
---
# <a name="ncast4_v3-series"></a>NCasT4_v3 系列 

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

NCasT4_v3 系列虚拟机采用 [Nvidia Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU 和 AMD EPYC 7V12(Rome) CPU。 VM 最多具有 4 个 NVIDIA T4 GPU，其中每个 GPU 具有 16 GB 内存，多达 64 个非多线程 AMD EPYC 7V12 (Rome) 处理器内核（基础频率为 2.45 GHz，全核峰值频率为 3.1 GHz，单核峰值频率为 3.3 GHz）和 440 GiB 系统内存。 这些虚拟机非常适合用于部署 AI 服务（例如对用户生成的请求的实时推断）或使用 NVIDIA 的 GRID 驱动程序和虚拟 GPU 技术的交互式图形和可视化工作负载。 基于 CUDA、TensorRT、Caffe、ONNX 和其他框架的标准 GPU 计算工作负载，或基于 OpenGL 和 DirectX 的 GPU 加速图形应用程序，可以在 NCasT4_v3 系列上以经济实惠的方式部署，并与用户非常接近。

<br>

[ACU](acu.md)：230-260<br>
[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[超级磁盘](disks-types.md#ultra-disk)：受支持（[详细了解](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)可用性、使用情况和性能） <br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：支持（[预览版](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks)）<br>
Nvidia NVLink 互连：不支持<br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数/预期网络带宽 (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2/8000 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4 / 8000  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8 / 8000  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8 / 32000  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

若要利用运行 Windows 或 Linux 的 Azure NCasT4_v3 系列 VM 的 GPU 功能，必须安装 Nvidia GPU 驱动程序。

若要手动安装 Nvidia GPU 驱动程序，请参阅[适用于 Windows 的 N 系列 GPU 驱动程序安装](./windows/n-series-driver-setup.md)，了解受支持的操作系统、驱动程序、安装和验证步骤。

Azure Nvidia GPU 驱动程序扩展将在 NCasT4_v3 系列 VM 上部署 CUDA 驱动程序。 对于图形和可视化工作负荷，手动安装 Azure 支持的 GRID 驱动程序。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
