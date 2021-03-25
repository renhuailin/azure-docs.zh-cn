---
title: Msv2 系列（预览版）- Azure 虚拟机
description: Msv2 系列 VM 的规格。
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: a7f4757467523837423d52998eb6b8204090e627
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562565"
---
# <a name="msv2-and-mdsv2-series-medium-memory-preview"></a>Msv2 和 Mdsv2 系列中型内存（预览版）


> [!IMPORTANT]
> 通过以下链接填写表单加入预览版： https://aka.ms/Mv2MedMemoryPreview。  

Msv2 和 Mdsv2 中型内存 VM 系列具有 Intel® Xeon® Platinum 8280 (Cascade Lake) 处理器，所有核心基础频率为 2.7 GHz，单核睿频为 4.0 GHz。 借助这些 VM，客户可以通过本地磁盘和无磁盘选项获得更大的灵活性。 客户还可以访问一组新的独立 VM 大小，这些 VM 具有更多 CPU 和内存，vCPU 最多可以达到 192 个，内存最多可以达到 4 TiB。 


Msv2 和 Mdsv2 系列 VM 仅为第 2 代 VM，并支持第 2 代支持的部分映像。 有关 Msv2 和 Mdsv2 系列支持的映像的完整列表，请参阅下文。  

- Windows Server 2019 或更高版本
- SUSE Linux Enterprise Server 12 SP4 及更高版本或 SUSE Linux Enterprise Server 15 SP1 及更高版本
- Red Hat Enterprise Linux 7.6、7.7、8.1 或更高版本 
- Oracle Enterprise Linux 7.7 或更高版本

有关第 2 代虚拟机的详细信息，请参阅[对 Azure 上第 2 代 VM 的支持](./generation-2.md)。



[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 代系支持](generation-2.md)：第 2 代<br>
[写入加速器](./how-to-enable-write-accelerator.md)：支持<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 中等内存无磁盘 

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数 | 预期的网络带宽 (Mbps) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192is_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>具有磁盘的 Mdsv2 中型内存  

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘 | 最大缓存和临时存储吞吐量：IOPS/MBps | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数 | 预期的网络带宽 (Mbps) | 
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
