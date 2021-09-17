---
title: 存储：性能最佳做法和准则
description: 提供存储最佳做法和准则，以优化 Azure 虚拟机 (VM) 上的 SQL Server 的性能。
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 86db0ce090c68f1a610aae6c69ed74dcf303416a
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635200"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>存储：Azure VM 上的 SQL Server 的性能最佳做法
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文提供存储最佳做法和准则，以优化 Azure 虚拟机 (VM) 上的 SQL Server 的性能。

通常需要在针对成本优化和针对性能优化之间进行权衡。 这一系列性能最佳做法侧重于实现 Azure 虚拟机上 SQL Server 的最佳性能。 如果工作负荷要求较低，可能不需要每项建议的优化。 评估这些建议时应考虑性能需求、成本和工作负荷模式。

若要了解详细信息，请参阅本系列中的其他文章：[清单](performance-guidelines-best-practices-checklist.md)、[VM 大小](performance-guidelines-best-practices-vm-size.md)、[安全性](security-considerations-best-practices.md)、[HADR 配置](hadr-cluster-best-practices.md)和 [收集基线](performance-guidelines-best-practices-collect-baseline.md)。 

## <a name="checklist"></a>清单

查看以下清单，大致了解文章其余部分更详细介绍的存储最佳做法： 

- 在选择磁盘类型之前，监视应用程序并确定 SQL Server 数据、日志和 tempdb 文件的[存储带宽和延迟要求](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements)。 
- 为了优化存储性能，请规划可用的最高未缓存 IOPS，并使用数据缓存作为数据读取的性能功能，同时避免[虚拟机和磁盘上限](../../../virtual-machines/premium-storage-performance.md#throttling)。
- 将数据、日志和 tempdb 文件放在不同的驱动器上。
    - 对于数据驱动器，仅使用[高级 P30 和 P40 磁盘](../../../virtual-machines/disks-types.md#premium-ssd)以确保缓存支持可用性
    - 对于日志驱动器，规划容量并测试性能与成本，同时评估[高级 P30 - P80 磁盘](../../../virtual-machines/disks-types.md#premium-ssd)。
      - 如果需要亚毫秒存储延迟，请对事务日志使用 [Azure 超级磁盘](../../../virtual-machines/disks-types.md#ultra-disk)。 
      - 对于 M 系列虚拟机部署，请考虑[写入加速器](../../../virtual-machines/how-to-enable-write-accelerator.md)，而不是使用 Azure 超级磁盘。
    - 在选择最佳 VM 大小后，请将 [tempdb](/sql/relational-databases/databases/tempdb-database) 放置在大多数 SQL Server 工作负载的本地临时 SSD `D:\` 驱动器上。 
      - 如果本地驱动器的容量对 tempdb 来说不足够，请考虑增加 VM 的大小。 有关详细信息，请参阅[数据文件缓存策略](#data-file-caching-policies)。
- 使用[存储空间](/windows-server/storage/storage-spaces/overview)对多个 Azure 数据磁盘进行条纹化，以将 I/O 带宽增加到目标虚拟机的 IOPS 和吞吐量上限。
- 将数据文件磁盘的[主机缓存](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits)设置为只读。
- 将日志文件磁盘的[主机缓存](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits)设置为无。
    - 请不要在包含 SQL Server 文件的磁盘上启用读取/写入缓存。 
    - 更改磁盘的缓存设置之前，请始终停止 SQL Server 服务。
- 对于开发和测试工作负载和长期备份存档，请考虑使用标准存储。 建议不要将标准 HDD/SDD 用于生产工作负载。
- [基于额度的磁盘突发](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) 仅应考虑用于较小的开发/测试工作负载和部门系统。
- 将数据磁盘格式化，为临时 `D:\` 驱动器（默认为 4 KB）以外的驱动器上放置的所有数据文件使用 64 KB 的块大小（分配单元大小）。 通过 Azure 市场部署的 SQL Server VM 附带经过格式化的数据磁盘，其中块大小和存储池的交错设置为 64 KB。 

若要将存储清单与其他清单进行比较，请参阅全面的[性能最佳做法清单](performance-guidelines-best-practices-checklist.md)。 

## <a name="overview"></a>概述

若要查找对 Azure VM 上的 SQL Server 工作负载最有效的配置，请从[衡量业务应用程序的存储性能](performance-guidelines-best-practices-collect-baseline.md#storage)着手。 了解存储要求后，请选择支持所需 IOPS 和吞吐量并具有适当内存与 vCore 比的虚拟机。 

选择具有足够存储可缩放性的 VM 大小，以适应你的工作负载和磁盘（通常位于存储池中）组合，以便满足业务的容量和性能要求。 

磁盘类型取决于托管在磁盘上的文件类型和峰值性能要求。

> [!TIP]
> 通过 Azure 门户预配 SQL Server VM 有助于指导你完成存储配置流程，并实现大多数存储最佳做法，例如为数据和日志文件创建单独的存储池、将 tempdb 目标设定为 `D:\` 驱动器，以及实现最佳缓存策略。 有关预配和配置存储的详细信息，请参阅 [SQL VM 存储配置](storage-configuration.md)。 

## <a name="vm-disk-types"></a>VM 磁盘类型

可以选择磁盘的性能级别。 以基础存储形式提供的托管磁盘类型（通过提高性能功能列出）是标准硬盘驱动器 (HDD)、标准 SSD、高级固态硬盘 (SSD) 和超级磁盘。 

磁盘的性能随容量的增加而增加，按[高级磁盘标签](../../../virtual-machines/disks-types.md#premium-ssd)（如具有 4 GiB 空间和 120 IOPS 的 P1 到具有 32 TiB 存储和 20,000 IOPS 的 P80）分组。 高级存储支持存储缓存，有助于提高某些工作负载的读取和写入性能。 有关详细信息，请参阅[托管磁盘概述](../../../virtual-machines/managed-disks-overview.md)。 

对于 Azure VM 上的 SQL Server，还需要考虑三种主要[磁盘类型](../../../virtual-machines/managed-disks-overview.md#disk-roles) - OS 磁盘、临时磁盘和数据磁盘。 请仔细选择操作系统驱动器 `(C:\)` 和临时驱动器 `(D:\)` 上存储的内容。 

### <a name="operating-system-disk"></a>操作系统磁盘

操作系统磁盘是可以作为操作系统的运行版本来启动和装载的 VHD，标记为 `C:\` 驱动器。 创建 Azure 虚拟机时，该平台至少将一个磁盘附加到 VM 作为操作系统磁盘。 `C:\` 驱动器是应用程序安装和文件配置的默认位置。 

对于生产 SQL Server 环境，请不要将操作系统磁盘用于数据文件、日志文件和错误日志。 

### <a name="temporary-disk"></a>临时磁盘

许多 Azure 虚拟机包含另一个称为临时磁盘的磁盘类型（标记为 `D:\` 驱动器）。 根据虚拟机系列和大小，此磁盘的容量会有所不同。 临时磁盘是暂时的，这意味着当虚拟机重启或将其移动到其他主机（例如用于[服务修复](/troubleshoot/azure/virtual-machines/understand-vm-reboot)）时，便会重新创建磁盘（如解除分配并再次分配磁盘）。 

临时存储驱动器不会持久保存到远程存储，因此不应存储用户数据库文件、事务日志文件或任何必须保留的内容。 

将 tempdb 放置在 SQL Server 工作负载的本地临时 SSD `D:\` 驱动器上，除非需要考虑本地缓存。 如果使用的是[没有临时磁盘](../../../virtual-machines/azure-vms-no-temp-disk.yml)的虚拟机，则建议将 tempdb 放置在自己的独立磁盘或存储池上，并将缓存设置为只读。 若要了解详细信息，请参阅 [tempdb 数据缓存策略](performance-guidelines-best-practices-storage.md#data-file-caching-policies)。

### <a name="data-disks"></a>数据磁盘数

数据磁盘是远程存储磁盘，通常在[存储池](/windows-server/storage/storage-spaces/overview)中创建，以超过任何单个磁盘可以提供给虚拟机的容量和性能。

附加最少数量的磁盘来满足工作负载的 IOPS、吞吐量和容量要求。 不要超过你计划调整大小的最小虚拟机的最大磁盘数。

将数据和日志文件放在已预配为最适合性能要求的数据磁盘上。 

将数据磁盘格式化，为临时 `D:\` 驱动器（默认为 4 KB）以外的驱动器上放置的所有数据文件使用 64 KB 的分配单元大小。 通过 Azure 市场部署的 SQL Server VM 附带经过格式化的数据磁盘，其中分配单元大小和存储池的交错设置为 64 KB。 

## <a name="premium-disks"></a>高级磁盘

将高级 SSD 磁盘用于生产 SQL Server 工作负载的数据和日志文件。 高级 SSD IOPS 和带宽因[磁盘大小和类型](../../../virtual-machines/disks-types.md)而异。 

对于生产工作负载，请对 SQL Server 数据文件使用 P30 和/或 P40 磁盘，以确保缓存支持，并对 SQL Server 事务日志文件使用 P30 至 P80。  为获得最佳总拥有成本，对于数据和日志文件，请从 P30 (5000 IOPS/200 MBPS) 开始，并且在需要控制虚拟机磁盘计数时仅选择较高的容量。

对于 OLTP 工作负载，使用高峰时段的工作负载和 `Disk Reads/sec` + `Disk Writes/sec` 性能计数器，使每磁盘（或存储池）的目标 IOPS 满足性能要求。 对于数据仓库和报告工作负载，在高峰时段和 `Disk Read Bytes/sec` + `Disk Write Bytes/sec` 时使用工作负载匹配目标吞吐量。 

使用存储空间实现最佳性能，配置两个池，一个用于日志文件，另一个用于数据文件。 如果不使用磁盘条带化，请使用两个映射到不同驱动器的高级 SSD 磁盘，其中一个驱动器包含日志文件，另一个包含数据。

属于存储池一部分的每个磁盘的[预配 IOPS 和吞吐量](../../../virtual-machines/disks-types.md#premium-ssd)。 磁盘的合并 IOPS 和吞吐容量是达到虚拟机吞吐量限制的最大容量。

最佳做法是尽可能少地使用磁盘，同时满足 IOPS（和吞吐量）和容量的最低要求。 然而，对价格和性能进行权衡时，往往是大量小磁盘更有优势，而不是少量大磁盘。

### <a name="scaling-premium-disks"></a>缩放高级磁盘

首次部署 Azure 托管磁盘时，该磁盘的性能层基于预配的磁盘大小。 在部署时指定性能层或在之后对其进行更改，而无需更改磁盘大小。 如果需求增加，可以增加性能级别来满足业务需求。 

通过更改性能层，管理员可以在不依赖[磁盘突发](../../../virtual-machines/disk-bursting.md#credit-based-bursting)的情况下做好准备并满足更高需求。 

在计费旨在满足存储性能层的情况下，根据需要使用更高的性能。 升级层以匹配性能需求，而无需增加容量。 如果不再需要额外的性能，则返回原始层。

这种经济高效的临时性能扩展对于目标事件（如购物、性能测试、培训活动和其他仅在短期内需要更高性能的短暂时段）来说是一个极为典型的用例。 

有关详细信息，请参阅[托管磁盘的性能层](../../../virtual-machines/disks-change-performance.md)。 

## <a name="azure-ultra-disk"></a>Azure 超级磁盘

如果需要亚毫秒级的响应时间并减少延迟，请考虑将 [Azure 超级磁盘](../../../virtual-machines/disks-types.md#ultra-disk)用于 SQL Server 日志驱动器，甚至可用于对 I/O 延迟极其敏感的应用程序的数据驱动器。 

可以在容量和 IOPS 可独立缩放的情况下配置超级磁盘。 凭借超级磁盘，管理员可以根据应用程序需求，预配具有容量、IOPS 和吞吐量要求的磁盘。 

所有 VM 系列上都不支持超级磁盘，并且有其他限制，如区域可用性、冗余和 Azure 备份支持。 若要了解详细信息，请参阅[使用 Azure 超级磁盘](../../../virtual-machines/disks-enable-ultra-ssd.md)，获取限制的完整列表。 

## <a name="standard-hdds-and-ssds"></a>标准 HDD 和 SSD

[标准 HDD](../../../virtual-machines/disks-types.md#standard-hdd) 和 SSD 具有不同的延迟和带宽，建议仅用于开发/测试工作负载。 生产工作负荷应使用高级 SSD。 如果使用标准 SSD（开发/测试方案），建议添加 [VM 大小](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json)支持的最大数量的磁盘，并对存储空间使用磁盘条带化，以实现最佳性能。

## <a name="caching"></a>缓存

支持高级存储缓存的虚拟机可以利用称为 Azure BlobCache 的附加功能或主机缓存来扩展虚拟机的 IOPS 和吞吐容量。 同时支持高级存储和高级存储缓存的虚拟机具有这两种不同存储带宽限制，可可配合使用以提升存储性能。

不进行缓存的 IOPS 和 MBps 吞吐量对虚拟机未缓存的磁盘吞吐量限制不利。 最大缓存限制为读取提供了额外的缓冲，有助于解决增长和意外峰值问题。

每当支持该选项时，启用高级缓存，可显著提高针对数据驱动器的读取性能，而无需额外成本。 

读取和写入 Azure BlobCache（缓存 IOPS 和吞吐量）并不会对虚拟机未缓存的 IOPS 和吞吐量限制产生不利。

> [!NOTE]
> 4 TiB 及更大（P50 或更大）磁盘不支持磁盘缓存。 如果将多个磁盘附加到 VM，则每个小于 4 TiB 的磁盘都会支持缓存。 有关详细信息，请参阅[磁盘缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)。 

### <a name="uncached-throughput"></a>未缓存吞吐量

最大未缓存磁盘 IOPS 和吞吐量是虚拟机可以处理的最大远程存储限制。 此限制在虚拟机上定义，并不是基础磁盘存储的限制。 此限制仅适用于针对远程连接到 VM 的数据驱动器的 I/O，不适用于针对临时驱动器（`D:\` 驱动器）或 OS 驱动器的本地 I/O。

可以在虚拟机的文档中验证可用于 VM 的未缓存 IOPS 和吞吐量。

例如，[M 系列](../../../virtual-machines/m-series.md)文档显示，Standard_M8ms VM 的最大未缓存吞吐量为 5000 IOPS 和 125 MBps 的未缓存磁盘吞吐量。 

![显示 M 系列未缓存磁盘吞吐量文档的屏幕截图。](./media/performance-guidelines-best-practices/m-series-table.png)

同样，可以看到，Standard_M32ts 支持 20,000 未缓存的磁盘 IOPS 和 500 MBps 未缓存磁盘吞吐量。 无论基础高级磁盘存储如何，此限制都在虚拟机级别进行控制。

有关详细信息，请参阅[未缓存和已缓存限制](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits)。


### <a name="cached-and-temp-storage-throughput"></a>缓存吞吐量和临时存储吞吐量

最大缓存和临时存储吞吐量限制是虚拟机上未缓存吞吐量限制的单独限制。 Azure BlobCache 由虚拟机主机的随机存取内存和本地连接的 SSD 组合而成。 虚拟机中的临时驱动器 (`D:\` 驱动器) 也托管在此本地 SSD 上。

仅在启用主机缓存的情况下，最大缓存和临时存储吞吐量限制才会管理针对本地临时驱动器（`D:\` 驱动器）和 Azure BlobCache 的 I/O。 

在高级存储上启用缓存时，虚拟机可超出远程存储未缓存的 VM IOPS 和吞吐量上限的限制进行缩放。  

只有特定虚拟机同时支持高级存储和高级存储缓存（这需要在虚拟机文档中验证）。 例如，[M 系列](../../../virtual-machines/m-series.md)文档指示支持高级存储和高级存储缓存： 

![显示 M 系列高级存储支持的屏幕截图。](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

缓存限制会因虚拟机大小而异。 例如，Standard_M8ms VM 支持 10000 缓存的磁盘 IOPS 和 1000 MBps 缓存磁盘吞吐量，总缓存大小为 793 GiB。 同样，Standard_M32ts VM 支持 40000 缓存的磁盘 IOPS 和 400 MBps 缓存磁盘吞吐量，总缓存大小为 3174 GiB。 

![显示 M 系列缓存磁盘吞吐量文档的屏幕截图。](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

可以在现有 VM 上手动启用主机缓存。 在对虚拟机缓存策略进行任何更改前，停止所有应用程序工作负载和 SQL Server 服务。 更改任何虚拟机缓存设置会导致目标磁盘在应用设置后分离并重新附加。

### <a name="data-file-caching-policies"></a>数据文件缓存策略

存储缓存策略因驱动器上托管的 SQL Server 数据文件类型而异。 

下表根据 SQL Server 数据的类型提供了建议的缓存策略的摘要： 

|SQL Server 磁盘 |建议 |
|---------|---------|
| **数据磁盘** | 为托管 SQL Server 数据文件的磁盘启用 `Read-only` 缓存。 <br/> 从缓存中读取的速度要快于从数据磁盘进行的非缓存读取速度。 <br/> 未缓存的 IOPS 和吞吐量加上缓存 IOPS 和吞吐量将形成虚拟机在 VM 限制范围内可用的潜在总体性能，但实际性能将因工作负载使用缓存的能力（缓存命中率）而异。 <br/>|
|**事务日志磁盘**|将托管事务日志的磁盘的缓存策略设置为 `None`。  为事务日志磁盘启用缓存没有性能优势，事实上，在日志驱动器上启用 `Read-only` 或 `Read/Write` 缓存可能会降低对驱动器的写入的性能，并减少数据驱动器上可用于读取的缓存量。  |
|**操作 OS 磁盘** | 对于 OS 驱动器，默认缓存策略 `Read/write`。 <br/> 建议不要更改 OS 驱动器的缓存级别。  |
| **tempdb**| 如果由于容量原因，无法将 tempdb 放置在临时驱动器 `D:\` 上，则可以调整虚拟机大小以获得更大的临时驱动器，或者将 tempdb 放置在已配置 `Read-only` 缓存的独立数据驱动器上。 <br/> 虚拟机缓存和临时驱动器都使用本地 SSD，因此，请记住托管在临时驱动器上时，将大小调整为 tempdb I/O 会对缓存的 IOPS 和吞吐量虚拟机限制不利。| 
| | | 

> [!IMPORTANT]
> 更改 Azure 磁盘的缓存设置会分离并重新附加目标磁盘。 当更改托管 SQL Server 数据、日志或应用程序文件的磁盘缓存设置时，请确保停止 SQL Server 服务以及任何其他相关服务，以避免数据损坏。

若要了解详细信息，请参阅[磁盘缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)。 


## <a name="disk-striping"></a>磁盘条带化

分析 SQL 数据文件所需的吞吐量和带宽，以确定数据磁盘数，包括日志文件和 tempdb。 吞吐量和带宽限制因 VM 大小而异。 若要了解详细信息，请参阅 [VM 大小](../../../virtual-machines/sizes.md)

为提高吞吐量，可以添加更多的数据磁盘，并使用磁盘条带化。 例如，需要 12,000 IOPS 和 180 MB/秒吞吐量的应用程序可以使用三个条带化 P30 磁盘来提供 15,000 IOPS 和 600 MB/秒的吞吐量。 

若要配置磁盘条带化，请参阅[磁盘条带化](storage-configuration.md#disk-striping)。 

## <a name="disk-capping"></a>磁盘上限 

磁盘和虚拟机级别都有吞吐量限制。 每个 VM 和每个磁盘的最大 IOPS 限制不同，且互不影响。

超出这些限制消耗资源的应用程序将中止（也称为达到上限）。 在符合应用程序要求且不会面临上限限制的磁盘条带中选择虚拟机和磁盘大小。 若要解决上限问题，请使用缓存或微调应用程序，以便只需要较少的吞吐量。

例如，需要 12,000 IOPS 和 180 MB/s 的应用程序可以： 
- 使用 [Standard_M32ms](../../../virtual-machines/m-series.md)，其最大非缓存磁盘吞吐量为 20,000 IOPS 和 500 MBps。
- 条带化三个 P30 磁盘，以提供 15,000 IOPS 和 600-MB/s 的吞吐量。
- 使用 [Standard_M16ms](../../../virtual-machines/m-series.md) 虚拟机，并使用主机缓存来利用本地缓存，而不是使用吞吐量。 

在高利用率期间配置以进行纵向扩展的虚拟机应预配足够的 IOPS 和吞吐量存储，来支持最大 VM 大小，同时保持磁盘总数小于或等于所使用的最小 VM SKU 支持的最大磁盘数。

有关磁盘上限限制和使用缓存避免上限的详细信息，请参阅[磁盘 IO 上限](../../../virtual-machines/disks-performance.md)。

> [!NOTE] 
> 某些磁盘上限仍可能给用户带来满意的性能；微调和维护工作负载，而不是调整到更大的 VM，以实现业务的管理成本和性能平衡。 


## <a name="write-acceleration"></a>写入加速

写入加速是一项磁盘功能，仅适用于 [M 系列](../../../virtual-machines/m-series.md)虚拟机 (VM)。 写入加速的目的在于，当你因大量的任务关键型 OLTP 工作负载或数据仓库环境而需要个位数 I/O 延迟时，可以缩短对 Azure 高级存储进行写入的 I/O 延迟。 

使用写入加速缩短托管日志文件的驱动器的写入延迟。 请不要将写入加速用于 SQL Server 数据文件。 

写入加速器磁盘与虚拟机具有相同的 IOPS 限制。 附加的磁盘不能超过 VM 的写入加速器 IOPS 限制。  

下表概述了每个虚拟机支持的数据磁盘和 IOPS 的数量： 

| VM SKU  | # 写入加速器磁盘  | 每个 VM 的写入加速器磁盘 IOPS  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2, M208s_v2  | 8  | 10000  |
| M64ms、M64ls、M64s  |  8 | 10000 |
| M32ms、M32ls、M32ts、M32s  | 4  | 5000  |
| M16ms、M16s  | 2 | 2500 |
| M8ms、M8s  | 1 | 1250 |

使用写入加速时有许多限制。 若要了解详细信息，请参阅[使用写入加速器时的限制](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator)。


### <a name="comparing-to-azure-ultra-disk"></a>与 Azure 超级磁盘比较

写入加速和 Azure 超级磁盘的最大区别是，写入加速是一种虚拟机功能，仅适用于 M 系列，而 Azure 超级磁盘是一种存储选项。 写入加速是一种写入优化缓存，根据虚拟机大小具有自身的限制。 Azure 超级磁盘是 Azure 虚拟机的低延迟磁盘存储选择。 

如果可能，请在事务日志磁盘上使用写入加速而不是超级磁盘。 对于不支持写入加速但要求事务日志延迟低的虚拟机，请使用 Azure 超级磁盘。 

## <a name="monitor-storage-performance"></a>监视存储性能

为了评估存储需求，并确定存储的性能，你需要了解要测量的内容以及这些指标的含义。 

[IOPS（每秒输入/输出）](../../../virtual-machines/premium-storage-performance.md#iops)是应用程序每秒进行存储的请求数。 使用性能监视器计数器 `Disk Reads/sec` 和 `Disk Writes/sec` 测量 IOPS。 [OLTP（联机事务处理）](/azure/architecture/data-guide/relational-data/online-transaction-processing)应用程序需要驱动更高的 IOPS 才能实现最佳性能。 应用程序（如支付处理系统、在线购物和零售销售点系统）都是 OLTP 应用程序的例子。

[吞吐量](../../../virtual-machines/premium-storage-performance.md#throughput)是发送到基础存储的数据量，通常以每秒兆字节为单位衡量。 使用性能监视器计数器 `Disk Read Bytes/sec` 和 `Disk Write Bytes/sec` 来测量吞吐量。 [数据仓储](/azure/architecture/data-guide/relational-data/data-warehousing)通过最大限度地提高吞吐量（而非 IOPS）进行优化。 用于分析、报告、ETL 工作流和其他商业智能目标的数据存储等应用程序都是数据仓储应用程序的例子。

I/O 单位大小影响 IOPS 和吞吐容量，因为较小的 I/O 大小产生更高的 IOPS，较大的 I/O 大小产生更高的吞吐量。 SQL Server 自动选择最佳 I/O 大小。 有关详细信息，请参阅[优化应用程序的 IOPS、吞吐量和延迟](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance)。 

有特定的 Azure Monitor 指标，对于发现虚拟机和磁盘级别的上限以及 AzureBlob 缓存的消耗和运行状况是非常有用的。 若要识别要添加到监视解决方案和 Azure 门户仪表板的关键计数器，请参阅[存储使用率指标](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics)。 

> [!NOTE]
> Azure Monitor 当前不提供临时驱动器 `(D:\)` 的磁盘级指标。 VM 缓存 IOPS 消耗百分比和 VM 缓存带宽消耗百分比将反映临时驱动器 `(D:\)` 和主机缓存的 IOPS 和吞吐量。


## <a name="next-steps"></a>后续步骤

若要了解有关性能最佳做法的详细信息，请参阅本系列中的其他文章：
- [快速核对清单](performance-guidelines-best-practices-checklist.md)
- [VM 大小](performance-guidelines-best-practices-vm-size.md)
- [安全性](security-considerations-best-practices.md)
- [HADR 设置](hadr-cluster-best-practices.md)
- [收集基线](performance-guidelines-best-practices-collect-baseline.md)

有关安全最佳做法，请参阅 [Azure 虚拟机上 SQL Server 的安全注意事项](security-considerations-best-practices.md)。

有关采用 TPC-E 和 TPC_C 基准详细测试 Azure VM 上的 SQL Server 性能的信息，请参阅博客[优化 OLTP 性能](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794)。

查看 [Azure 虚拟机上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)中的其他 SQL Server 虚拟机文章。 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](frequently-asked-questions-faq.yml)。
