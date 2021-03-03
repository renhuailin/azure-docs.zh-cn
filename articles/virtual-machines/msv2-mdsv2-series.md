---
title: Msv2 系列 (预览版) -Azure 虚拟机
description: Msv2 系列 Vm 的规格。
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: 04d6daac6644bd8d29479ac527bbdd8a5bdbd116
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679141"
---
# <a name="msv2-and-mdsv2-series-medium-memory-preview"></a>Msv2 和 Mdsv2 中型内存 (预览) 


> [!IMPORTANT]
> 通过在处填写表单来加入预览 **https://aka.ms/Mv2MedMemoryPreview** 。  

Msv2 和 Mdsv2 中型内存 VM 系列具有 Intel®强®白金 8280 (级联 Lake) 处理器，其所有核心基本频率为 2.7 GHz，4.0 GHz 单核 turbo 频率。 利用这些 Vm，客户能够通过本地磁盘和无光盘选项获得更大的灵活性。 客户还可以访问一组新的隔离 VM 大小，其中包含更多的 CPU 和内存，最高可达 192 vCPU 4 TiB 内存。 


Msv2 和 Mdsv2 系列 Vm 仅适用于第2代，支持第2代支持映像的子集。 有关 Msv2 和 Mdsv2 的支持映像的完整列表，请参阅下文。  

- Windows Server 2019 或更高版本
- SUSE Linux Enterprise Server 12 SP4 及更高版本或 SUSE Linux Enterprise Server 15 SP1 及更高版本
- Red Hat Enterprise Linux 7.6、7.7、8.1 或更高版本 
- Oracle Enterprise Linux 7.7 或更高版本

有关第2代虚拟机的详细信息，请参阅 [Azure 上的第2代 Vm 支持](./generation-2.md)。



[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 生成支持](generation-2.md)：第2代<br>
[写入加速器](./how-to-enable-write-accelerator.md)：支持<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 中等内存无磁盘 

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数 | 预期的网络带宽 (Mbps) | 
|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128dms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ids_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192idms_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Mdsv2 Medium Memory with Disk  

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘 | 缓存和临时存储的最大吞吐量： IOPS/MBps | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数 | 预期的网络带宽 (Mbps) | 
|---|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 1024 | 32 | 40000/400 | 20000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1024 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1792 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2048 | 4096 | 64 |160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M128dms_v2 | 128 | 3892 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192ids_v2 | 192 | 2048 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192idms_v2 | 192 | 4096 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和信息

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

定价计算器：[定价计算器](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
