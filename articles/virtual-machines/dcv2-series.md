---
title: DCsv2 系列 - Azure 虚拟机
description: DCsv2 系列 VM 的规格。
author: mmcrey
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: 474478c03586866f28d02c4e1cb219bd6df65dea
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183465"
---
# <a name="dcsv2-series"></a>DCsv2 系列


DCsv2 系列虚拟机可以在公有云中处理数据和代码时帮助保护数据和代码的机密性和完整性。 DCsv2 系列利用 Intel® Software Guard Extensions，使客户能够使用安全 enclave 进行保护。

这些计算机由采用 SGX 技术的 3.7 GHz Intel® Xeon E-2288G (Coffee Lake) 提供支持。 利用 Intel® Turbo Boost Max 3.0 技术，这些计算机最多可达 5.0 GHz。 

示例用例包括：多方机密数据共享、欺诈检测、机密数据库、反洗钱、区块链、机密使用情况分析、情报分析和机密机器学习。

## <a name="configuration"></a>配置

[Turbo Boost Max 3.0](https://www.intel.com/content/www/us/en/gaming/resources/turbo-boost.html)：支持（租户 VM 将报告 3.7 GHz，但会达到 Turbo 速度）<br>
[超线程](https://www.intel.com/content/www/us/en/gaming/resources/hyper-threading.html)：不支持<br>
[高级存储](premium-storage-performance.md)：支持（不支持 Standard_DC8_v2）<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 代系支持](generation-2.md)：第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：不支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：支持 <br>

## <a name="technical-specifications"></a>技术规范

| 大小             | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大 NIC 数/预期网络带宽 (MBps) | EPC 内存 (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |


## <a name="get-started"></a>入门

- 使用 [Azure 门户](./linux/quick-create-portal.md)或 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)创建 DCsv2 VM
- DCsv2 系列 VM 是[第 2 代 VM](./generation-2.md#creating-a-generation-2-vm)，仅支持 `Gen2` 映像。
- 当前在 [Azure 产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)中列出的区域中可用。
- 下一代 DC 系列 VM：[加入预览计划](https://aka.ms/intelgen3)

## <a name="more-sizes-and-information"></a>更多大小和信息

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)
- [定价计算器](https://azure.microsoft.com/pricing/calculator/)
- [更多磁盘类型](./disks-types.md#ultra-disk)

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。