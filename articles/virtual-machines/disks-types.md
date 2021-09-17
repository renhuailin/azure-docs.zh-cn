---
title: 选择 Azure IaaS VM 的磁盘类型 - 托管磁盘
description: 了解虚拟机的可用 Azure 磁盘类型，包括超级磁盘、高级 SSD、标准 SSD 和标准 HDD。
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ea55522714beea9274f27bec36232d9e50c90332
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688046"
---
# <a name="what-disk-types-are-available-in-azure"></a>Azure 有哪些可用的磁盘类型？

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

Azure 托管磁盘目前提供四种磁盘类型，每种类型都针对特定的客户方案。

## <a name="disk-comparison"></a>磁盘比较

下表对托管磁盘的超级磁盘、高级固态硬盘 (SSD)、标准 SSD 和标准硬盘驱动器 (HDD) 进行了比较，帮助你确定使用哪一种。

| 详细信息 | 超级磁盘 | 高级 SSD | 标准 SSD | 标准 HDD |
| ------ | ---------- | ----------- | ------------ | ------------ |
|磁盘类型   |SSD   |SSD   |SSD   |HDD   |
|方案   |[SAP HANA](workloads/sap/hana-vm-operations-storage.md)、顶层数据库（例如 SQL、Oracle）等 IO 密集型工作负载，以及其他事务密集型工作负载。   |生产和性能敏感型工作负荷   |Web 服务器、不常使用的企业应用程序和开发/测试   |备份、非关键、不常访问   |
|最大磁盘大小   |65,536 GB (GiB)    |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|最大吞吐量   |2,000 MB/秒    |900 MB/秒   |750 MB/秒   |500 MB/秒   |
|最大 IOPS   |160,000    |20,000   |6,000   |2,000   |

## <a name="ultra-disk"></a>超级磁盘

Azure 超级磁盘为 Azure IaaS VM 提供高吞吐量、高 IOPS 和一贯低延迟的磁盘存储。 超级磁盘的某些优势包括能够动态改变工作负载以及磁盘的性能，而无需重启虚拟机 (VM)。 超级磁盘适用于 SAP HANA、顶层数据库等数据密集型工作负荷，以及事务密集型工作负荷。 超级磁盘只可用作数据磁盘。 建议将高级 SSD 用作 OS 磁盘。

### <a name="performance"></a>性能

预配超级磁盘时，可以单独配置磁盘的容量和性能。 超级磁盘提供从 4 GiB 到 64 TiB 这个范围的多种固定大小，并提供一个灵活的性能配置模型，用于单独配置 IOPS 和吞吐量。

超级磁盘的一些重要功能包括：

- 磁盘容量：超级磁盘容量范围为 4 GiB 至 64 TiB。
- 磁盘 IOPS：超级磁盘支持 300 IOPS/GiB 的 IOPS 限制，每个磁盘最高可达 160K IOPS。 若要实现预配的 IOPS，请确保选择的磁盘 IOPS 小于 VM IOPS 限制。 每个磁盘的最低保证 IOPS 为 1 IOPS/GiB，总体基线最低为 100 IOPS。 例如，如果你有一个 4 GiB 的超级磁盘，则你至少有 100 IOPS，而不是 8 IOPS。
- 磁盘吞吐量：使用超级磁盘时，单个磁盘对应于每个预配 IOPS 的吞吐量限制为 256 KiB/秒，每个磁盘的最大吞吐量为 2000 MBps（其中 MBps = 每秒 10^6 字节）。 对于每个预配的 IOPS，每个磁盘的最低保证吞吐量为 4KiB/s，总体基线最低为 1 MBps。
- 超级磁盘支持在运行时调整磁盘性能属性（IOPS 和吞吐量），而无需从虚拟机拆离磁盘。 在对磁盘上发出磁盘性能调整大小操作后，更改实际上可能最多需要一个小时才能生效。 在 24 小时内，最多只能进行 4 次性能大小调整操作。 性能大小调整操作可能会因为性能带宽容量不足而失败。

### <a name="disk-size"></a>磁盘大小

|磁盘大小 (GiB)  |IOPS 上限  |吞吐量上限 (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |153,600         |2,000         |
|1,024 - 65,536（此范围内的大小以 1 TiB 为增量递增）     |160,000         |2,000         |

超级磁盘的设计目的是在 99.99% 的时间内提供亚毫秒级延迟以及上表所述的目标 IOPS 和吞吐量。

### <a name="ga-scope-and-limitations"></a>正式发布版的范围和限制

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]


如果想要开始使用超级磁盘，请参阅[使用 Azure 超级磁盘](disks-enable-ultra-ssd.md)这一主题的文章。

## <a name="premium-ssd"></a>高级·SSD

Azure 高级 SSD 为运行输入/输出 (IO) 密集型工作负荷的虚拟机 (VM) 提供高性能、低延迟的磁盘支持。 若要利用高级存储磁盘的速度和性能优势，可将现有的 VM 磁盘迁移到高级 SSD。 高级 SSD 适用于任务关键型生产应用程序。 高级 SSD 只能用于与高级存储兼容的 VM 系列。

若要详细了解 Azure 中适用于 Windows 或 Linux 的各个 VM 类型和大小（包括哪些大小与高级存储兼容），请参阅 [Azure 中虚拟机的大小](sizes.md)。 在本文中，需要检查每个 VM 大小的文章，以确定其是否与高级存储兼容。

### <a name="disk-size"></a>磁盘大小
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

预配高级存储磁盘时，可以获得该磁盘的容量、IOPS 和吞吐量保证，这与标准存储不同。 例如，如果创建 P50 磁盘，Azure 将为此磁盘预配 4,095-GB 存储容量、7,500 IOPS 和 250-MB/秒的吞吐量。 应用程序可以使用全部或部分容量与性能。 高级 SSD 的设计目的是在 99.9% 的时间内提供较低的个位数毫秒延迟以及上表所述的目标 IOPS 和吞吐量。

## <a name="bursting"></a>突发

高级 SSD 提供磁盘突发。 磁盘突发能更好地容许 IO 模式出现不可预测的变化情况。 可以最大程度地利用磁盘突发来应对 OS 磁盘启动时和应用程序的流量高峰。 若要详细了解 Azure 磁盘的突发工作原理，请参阅[磁盘级突发](disk-bursting.md#disk-level-bursting)。

### <a name="transactions"></a>事务

对于高级 SSD，每个小于或等于 256 KiB 吞吐量的 I/O 操作被视为单个 I/O 操作。 大于 256 KiB 吞吐量的 I/O 操作被视为大小为 256 KiB 的多个 I/O。

## <a name="standard-ssd"></a>标准 SSD

Azure 标准 SSD 是经济高效的存储选项，已针对需要一致性能和较低 IOPS 级别的工作负荷进行优化。 对于想要迁移到云的用户而言，标准 SSD 提供良好的入门级体验，尤其是在本地 HDD 解决方案中运行各种工作负荷遇到问题时。 与标准 HDD 相比，标准 SSD 提供更好的可用性、一致性、可靠性和延迟。 标准 SSD 适用于 Web 服务器、低 IOPS 应用程序服务器、较少使用的企业应用程序和开发/测试工作负荷。 与标准 HDD 一样，标准 SSD 也可以在所有 Azure VM 上使用。

### <a name="disk-size"></a>磁盘大小
[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

标准 SSD 的设计目的是在 99% 的时间内提供个位数毫秒延迟，以及不超过上表中所述限制的 IOPS 和吞吐量。 实际 IOPS 和吞吐量有时根据流量模式而异。 相比 HDD 磁盘，标准 SSD 提供更加一致的性能，并且延迟更低。

### <a name="transactions"></a>事务

对于标准 SSD，每个小于或等于 256 KiB 吞吐量的 I/O 操作被视为单个 I/O 操作。 大于 256 KiB 吞吐量的 I/O 操作被视为大小为 256 KiB 的多个 I/O。 这些事务对计费有影响。

### <a name="bursting"></a>突发

标准 SSD 提供磁盘突发。 磁盘突发能更好地容许 IO 模式出现不可预测的变化情况。 可以最大程度地利用磁盘突发来应对 OS 磁盘启动时和应用程序的流量高峰。 若要详细了解 Azure 磁盘的突发工作原理，请参阅[磁盘级突发](disk-bursting.md#disk-level-bursting)。

## <a name="standard-hdd"></a>标准 HDD

Azure 标准 HDD 为运行不区分延迟的工作负荷提供可靠、低成本的磁盘支持。 使用标准存储，将数据存储在硬盘驱动器 (HDD)。 与基于 SSD 的磁盘相比，标准 HDD 磁盘的延迟、IOPS 和吞吐量可能变化更大。 标准 HDD 磁盘旨在为大多数 IO 操作提供低于 10 毫秒的写入延迟和低于 20 毫秒的读取延迟，但是实际性能可能会因 IO 大小和工作负荷模式而异。 使用 VM 时，可将标准 HDD 磁盘用于开发/测试方案和不太重要的工作负荷。 标准 HDD 可在所有 Azure 区域中使用，并可与所有 Azure VM 一起使用。

### <a name="disk-size"></a>磁盘大小
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>事务

对于标准 HDD，每个 IO 操作会被视为单个事务，无论 I/O 大小如何。 这些事务对计费有影响。

## <a name="billing"></a>计费

使用托管磁盘时，将考虑以下计费事项：

- 磁盘类型
- 托管磁盘大小
- 快照
- 出站数据传输
- 事务数

**托管磁盘大小**：托管磁盘按预配大小计费。 Azure 将预配大小映射（向上舍入）到所提供的最接近的磁盘大小。 有关所提供的磁盘大小的详细信息，请参阅前面的表。 每个磁盘将映射到一种受支持的预配磁盘大小套餐并相应地计费。 例如，如果预配了 200 GiB 的标准 SSD，它会映射到 E15 的磁盘大小 (256 GiB)。 任何预配的磁盘根据每月的存储优惠价格按小时计费。 例如，如果在预配 E10 磁盘的 20 小时后删除它，则会以 20 小时计算 E10 产品/服务的费用。 这与写入磁盘的实际数据量无关。

**快照**：基于已使用大小对快照计费。 例如，如果创建预配容量为 64 GiB 且实际使用数据大小为 10 GiB 的托管磁盘的快照，则仅针对已用数据大小 10 GiB 对该快照计费。

有关快照的详细信息，请参阅[托管磁盘概述](managed-disks-overview.md)中有关快照的部分。

**出站数据传输**：[出站数据传输](https://azure.microsoft.com/pricing/details/bandwidth/)（Azure 数据中心送出的数据）会产生带宽使用费。

**事务**：会根据你对标准托管磁盘执行的事务数向你收费。 对于标准 SSD，每个小于或等于 256 KiB 吞吐量的 I/O 操作被视为单个 I/O 操作。 大于 256 KiB 吞吐量的 I/O 操作被视为大小为 256 KiB 的多个 I/O。 对于标准 HDD，每个 IO 操作会被视为单个事务，无论 I/O 大小如何。

有关托管磁盘定价的详细信息（包括事务成本），请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks)。

### <a name="ultra-disk-vm-reservation-fee"></a>超级磁盘 VM 预留费用

Azure VM 能够表明其是否兼容超级磁盘。 与超级磁盘兼容的 VM 在计算 VM 实例与块存储缩放单元之间分配专用的带宽容量，以优化性能并降低延迟。 在 VM 上添加此功能会导致预留费用，不过，这笔费用是仅当在 VM 上启用了超级磁盘功能但未将超级磁盘附加到 VM 时才产生的。 如果将超级磁盘附加到与超级磁盘兼容的 VM，则不会收取此费用。 此费用根据 VM 上预配的每个 vCPU 计收。 

> [!Note]
> 对于[受约束的核心 VM 大小](constrained-vcpu.md)，预留费用将基于 vCPU 的实际数量，而不是受约束的核心数。 对于 Standard_E32-8s_v3，预留费用将基于 32 个核心。 

如需了解超级磁盘的定价详情，请参阅 [Azure 磁盘定价页](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="azure-disk-reservation"></a>Azure 磁盘预留

磁盘预留是以一定折扣提前购买一年磁盘存储的选项，可降低总成本。 购买磁盘预留时，请在目标区域中选择特定的磁盘 SKU，例如在美国东部 2 区域选择 10 个一年期的 P30 (1TiB) 高级 SSD。 预留体验与预留虚拟机 (VM) 实例类似。 你可以捆绑 VM 和磁盘预留，以最大程度节省费用。 目前，Azure 磁盘预留为所有生产区域中的 P30 (1TiB) - P80 (32 TiB) 高级 SSD SKU 提供一年期承诺计划。 如需详细了解预留磁盘的定价，请参阅 [Azure 磁盘定价页](https://azure.microsoft.com/pricing/details/managed-disks/)。

## <a name="next-steps"></a>后续步骤

请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)以开始使用。