---
title: VM 大小：性能最佳做法和准则
description: 提供 VM 大小准则和最佳做法，以优化 Azure 虚拟机 (VM) 上 SQL Server 的性能。
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 14197b7974db7697b9718f13ef59ac3e34c22a9b
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397148"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>VM 大小：Azure VM 上 SQL Server 的性能最佳做法
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文针对 VM 大小提供一系列最佳做法和准则，以优化 Azure 虚拟机 (VM) 上 SQL Server 的性能。

通常需要在针对成本优化和针对性能优化之间进行权衡。 这一系列性能最佳做法侧重于实现 Azure 虚拟机上 SQL Server 的最佳性能。 如果工作负荷要求较低，可能不需要每项建议的优化。 评估这些建议时应考虑性能需求、成本和工作负荷模式。

如需了解更全面的详情，请参阅本系列中的其他文章：[清单](performance-guidelines-best-practices-checklist.md)、[存储](performance-guidelines-best-practices-storage.md)、[安全性](security-considerations-best-practices.md)、[HADR 配置](hadr-cluster-best-practices.md)和[收集基线](performance-guidelines-best-practices-collect-baseline.md)。 


## <a name="checklist"></a>清单

请查看以下清单，以大致了解本文其余部分详细介绍的 VM 大小最佳做法： 

- 使用具有 4 个或更多 vCPU 的 VM 大小，如 [Standard_M8-4ms](../../../virtual-machines/m-series.md)、[E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series) 或 [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15)，或使用更大的大小。 
- 使用[内存优化](../../../virtual-machines/sizes-memory.md)的虚拟机大小，以实现 SQL Server 工作负载的最佳性能。 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md)、[Edsv4](../../../virtual-machines/edv4-edsv4-series.md) 系列、[M-](../../../virtual-machines/m-series.md) 和 [Mv2-](../../../virtual-machines/mv2-series.md) 系列提供 OLTP 工作负载所需的最佳内存与 vCore 比率。 这两个 M 系列的 VM 都提供最高的内存与 vCore 比率，可满足任务关键型工作负载的需求，也非常适合用于数据仓库工作负载。 
- 对于任务关键型工作负载和数据仓库工作负载，考虑更高的内存与 vCore 比率。 
- 利用 Azure 虚拟机市场映像作为 SQL Server 设置，并配置存储选项以获得最佳 SQL Server 性能。 
- 收集目标工作负载的性能特征，并使用它们来确定适用于你的业务的 VM 大小。
- 使用[数据迁移助手](https://www.microsoft.com/download/details.aspx?id=53595) [SKU 建议](/sql/dma/dma-sku-recommend-sql-db)工具为现有 SQL Server 工作负载查找正确的 VM 大小。

若要将 VM 大小清单与其他清单进行比较，请参阅全面的[性能最佳做法清单](performance-guidelines-best-practices-checklist.md)。 

## <a name="overview"></a>概述

在 Azure VM 上创建 SQL Server 时，请仔细考虑所需工作负载的类型。 如果要迁移现有环境，请[收集性能基线](performance-guidelines-best-practices-collect-baseline.md)，以确定 SQL Server 对 Azure VM 的要求。 如果为新 VM，请根据供应商要求创建新的 SQL Server VM。 

如果要使用为云生成的新应用程序创建新的 SQL Server VM，则随着数据和使用情况要求的变化，你可以轻松地调整 SQL Server VM 的大小。
使用较低层 D 系列、B 系列或 Av2 系列开始开发环境，并随着时间的推移扩展你的环境。 

将 SQL Server VM 市场映像与门户中的存储配置结合使用。 这样便可更轻松地正确创建具有工作负载所需的大小、IOPS 和吞吐量的存储池。 必须选择支持高级存储和高级存储缓存的 SQL Server VM。 有关详细信息，请参阅[存储](performance-guidelines-best-practices-storage.md)一文。 

针对生产 OLTP 环境的建议最小值是 4 个 vCore、32 GB 内存，以及内存与 vCore 比率为 8。 对于新环境，从有 4 个 vCore 的虚拟机开始，在数据和计算要求发生变化时缩放到 8 个、16 个、32 个或更多 vCore。 对于 OLTP 吞吐量，目标 SQL Server VM 的每个 vCore 具有 5000 个 IOPS。 

SQL Server 数据仓库和任务关键型环境通常需要缩放到内存与 vCore 比率大于 8。 对于中等环境，你可能希望选择内存与 vCore 比率为 16，而对于较大的数据仓库环境，则可能希望该比率为 32。 

SQL Server 数据仓库环境通常受益于大型计算机的并行处理。 因此，对于大型数据仓库环境，强烈建议选择 M 系列和 Mv2 系列。

使用源计算机的 vCPU 和内存配置作为将当前本地 SQL Server 数据库迁移到 Azure VM 上的 SQL Server 的基线。 在 Azure 中使用你的核心许可证，以利用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)并节省 SQL Server 许可成本。

## <a name="memory-optimized"></a>内存优化

[内存优化的虚拟机大小](../../../virtual-machines/sizes-memory.md)是 SQL Server VM 的主要目标，也是 Microsoft 推荐的选择。 内存优化的虚拟机提供更大的内存与 CPU 比率和大中型缓存选项。 

### <a name="m-mv2-and-mdsv2-series"></a>M、Mv2 和 Mdsv2 系列

[M 系列](../../../virtual-machines/m-series.md) 为某些最大的 SQL Server 工作负载提供较高的 vCore 数量和内存。  

[Mv2 系列](../../../virtual-machines/mv2-series.md)具有最高的 vCore 计数和内存，建议用于任务关键型工作负载和数据仓库工作负载。 Mv2 系列实例具有内存优化的 VM 大小，提供卓越的计算性能以支持大型内存中数据库和工作负载，其内存与 CPU 比率高，非常适用于关系数据库服务器、大型缓存和内存中分析。

例如，[Standard_M64ms](../../../virtual-machines/m-series.md) 的内存与 vCore 比率为 28。

[Mdsv2 Medium Memory 系列](../../..//virtual-machines/msv2-mdsv2-series.md)是新 M 系列，目前只有[预览版](https://aka.ms/Mv2MedMemoryPreview)，该系列提供了众多 M 系列级别的 Azure 虚拟机（带有中间层内存产品）。 这些计算机非常适合 SQL Server 工作负载，所支持的内存与 vCore 比率最低为 10，最高为 30。

M 和 Mv2 系列的[高级存储](../../../virtual-machines/premium-storage-performance.md)和[高级存储缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)支持、[超磁盘](../../../virtual-machines/disks-enable-ultra-ssd.md)支持以及[写入加速](../../../virtual-machines/how-to-enable-write-accelerator.md)等功能可提升 SQL Server 的性能。

### <a name="edsv4-series"></a>Edsv4 系列

[Edsv4 系列](../../../virtual-machines/edv4-edsv4-series.md)专为内存密集型应用程序而设计。 这些 VM 具有较大的本地存储 SSD 容量、强大的本地磁盘 IOPS、高达 504 GiB 的 RAM。 在大多数虚拟机中，每个 vCore 几乎一致具有 8 GiB 内存，非常适合标准 SQL Server 工作负载。 

该组中有一个带有 [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) 的新建虚拟机，提供 80 个 vCore 和 504 GB 内存，其内存与 vCore 比率为 6。 此虚拟机很引人注目，因为它[孤立](../../../virtual-machines/isolation.md)存在，这意味着它肯定是主机上运行的唯一虚拟机，因此独立于其他客户工作负载。 该虚拟机的内存与 vCore 比率低于 SQL Server 的建议比率，因此仅当需要隔离时才会使用。

Edsv4 系列虚拟机支持[高级存储](../../../virtual-machines/premium-storage-performance.md)和[高级存储缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)。

### <a name="dsv2-series-11-15"></a>DSv2 系列 11-15

[DSv2 系列 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 的内存和磁盘配置与之前的 D 系列相同。 该系列的所有虚拟机的内存与 CPU 比率均为 7。 这是内存优化系列的最小值，对于入门级 SQL Server 工作负载来说，这是一个很好的低成本选项。

[DSv2 系列 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 支持[高级存储](../../../virtual-machines/premium-storage-performance.md)和[高级存储缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)，强烈建议使用此系列来获取最佳性能。

## <a name="general-purpose"></a>常规用途

[常规用途虚拟机大小](../../../virtual-machines/sizes-general.md)旨在为较小的入门级工作负载（如开发和测试、Web 服务器和较小的数据库服务器）提供均衡的内存与 vCore 比率。 

由于常规用途虚拟机的内存与 vCore 比率较小，因此必须仔细监视基于内存的性能计数器，以确保 SQL Server 能够获取所需的缓冲区缓存内存。 有关详细信息，请参阅[内存性能基线](performance-guidelines-best-practices-collect-baseline.md#memory)。 

由于对于生产工作负载，建议的最低内存与 vCore 比率为 8，因此对于运行 SQL Server 的常规用途 VM，建议的最低配置为 4 个 vCPU 和 32 GB 内存。 

### <a name="ddsv4-series"></a>Ddsv4 系列

[Ddsv4 系列](../../../virtual-machines/ddv4-ddsv4-series.md)的 vCPU、内存和临时磁盘数量相当，但支持的内存与 vCore 比率较小。 

Ddsv4 VM 的延迟更低、本地存储速度更高。

这些虚拟机非常适合需要快速访问临时存储和部门关系数据库的并行 SQL 和应用部署。 本系列的所有虚拟机的标准内存与 vCore 比率均为 4。 

因此，建议利用 D8ds_v4 作为本系列中的入门虚拟机，它具有 8 个 vCore 和 32 GB 内存。 该系列中最大的虚拟机是 D64ds_v4，它拥有 64 个 vCore 和 256 GB 的内存。

[Ddsv4 系列](../../../virtual-machines/ddv4-ddsv4-series.md)虚拟机支持[高级存储](../../../virtual-machines/premium-storage-performance.md)和[高级存储缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)。

> [!NOTE]
> [Ddsv4 系列](../../../virtual-machines/ddv4-ddsv4-series.md)的内存与 vCore 比率并非针对 SQL Server 工作负载建议的 8。 因此，请仅考虑将这些虚拟机用于较小的应用程序和开发工作负载。

### <a name="b-series"></a>B 系列

[可突发 B 系列](../../../virtual-machines/sizes-b-series-burstable.md)虚拟机大小非常适合不需要一致性能的工作负载，如概念验证以及非常小的应用程序和开发服务器。 

大多数[可突发 B 系列](../../../virtual-machines/sizes-b-series-burstable.md)虚拟机大小的内存与 vCore 比率为 4。 其中最大的虚拟机为 [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md)，它具有 20 个 vCore 和 80 GB 内存。

该系列是独一无二的，因为应用能够在营业时间突发，并且可突发的额度因虚拟机大小而异。 

当额度用尽时，VM 会恢复到基线虚拟机性能。

与其他系列中的其他 VM 大小相比，B 系列的优势是可以节省计算成本，尤其是在需要全天候尽量少使用处理能力时。

此系列支持[高级存储](../../../virtual-machines/premium-storage-performance.md)，但不支持[高级存储缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)。

> [!NOTE] 
> [可突发 B 系列](../../../virtual-machines/sizes-b-series-burstable.md)的内存与 vCore 比率并非针对 SQL Server 工作负载建议的 8。 因此，请仅考虑将这些虚拟机用于较小的应用程序、Web 服务器和开发工作负载。

### <a name="av2-series"></a>Av2 系列

[Av2 系列](../../../virtual-machines/av2-series.md) VM 最适合入门级工作负载，如开发和测试、低流量 Web 服务器、中小型应用数据库和概念验证。

只有 [Standard_A2m_v2](../../../virtual-machines/av2-series.md)（2 个 vCore 和 16 GB 内存）、[Standard_A4m_v2](../../../virtual-machines/av2-series.md)（4 个 vCore 和 32 GB 内存）和 [Standard_A8m_v2](../../../virtual-machines/av2-series.md)（8 个 vCore 和 64 GB 内存）的内存与 vCore 比率为 8，使这三个虚拟机在此系列中排名前三。 

这些虚拟机非常适合小型开发和测试 SQL Server 计算机。 

对于小型应用程序和 Web 服务器来说，具有 8 个 vCore 的 [Standard_A8m_v2](../../../virtual-machines/av2-series.md) 可能也是一个不错的选择。

> [!NOTE] 
> Av2 系列不支持高级存储，因此不建议将其用于生产 SQL Server 工作负载，即使是内存与 vCore 比率为 8 的虚拟机也是如此。

## <a name="storage-optimized"></a>存储优化

[存储优化的 VM 大小](../../../virtual-machines/sizes-storage.md)适合特定用例。 这些虚拟机经过专门涉及，具有经过优化的磁盘吞吐量和 IO。 

### <a name="lsv2-series"></a>Lsv2 系列

[Lsv2 系列](../../../virtual-machines/lsv2-series.md)具有高吞吐量、低延迟和本地 NVMe 存储的特性。 优化 Lsv2 系列 VM 以使用直接连接到 VM 的节点上的本地磁盘，而不是使用持久数据磁盘。 

这些虚拟机是适用于大数据、数据仓库、报告和 ETL 工作负载的有力选择。 本地 NVMe 存储具有高吞吐量和 IOPS 特性，非常适合用于处理将加载到数据库的文件，以及可以从源系统或其他存储库（例如 Azure Blob 存储或 Azure Data Lake）重新创建数据的其他方案。 [Lsv2 系列](../../../virtual-machines/lsv2-series.md) VM 还可以通过突发方式提高其磁盘性能，一次长达 30 分钟。

这些虚拟机的大小介于 8 到 80 个 vCPU 之间，每个 vCPU 内存为 8 GiB，每 8 个 vCPU 有 1.92 TB 的 NVMe SSD。 这意味着，此系列中最大的 VM [L80s_v2](../../../virtual-machines/lsv2-series.md) 具有 80 个 vCPU 和 640 GiB 的内存，以及 10x1.92TB 的 NVMe 存储。  所有这些虚拟机的内存与 vCore 比率始终一致，均为 8。

NVMe 存储是暂时的，这意味着，如果解除分配虚拟机或将其移动到其他主机进行服务修复，则这些磁盘上的数据将会丢失。

Lsv2 和 Ls 系列支持[高级存储](../../../virtual-machines/premium-storage-performance.md)，但不支持高级存储缓存。 不支持创建本地缓存以提高 IOPS。 

> [!WARNING]
> 将数据文件存储在临时 NVMe 存储上，可能会导致在解除分配 VM 时数据丢失。 

## <a name="constrained-vcores"></a>受约束的 vCore

高性能 SQL Server 工作负载通常需要大量内存、I/O 和吞吐量，而不会增加 vCore 计数。 

大多数 OLTP 工作负载都是由大量较小的事务驱动的应用程序数据库。 对于 OLTP 工作负载，仅读取或修改少量数据，但由用户数驱动的事务量要高得多。 必须向缓存计划提供 SQL Server 内存、存储最近访问的数据以提高性能，以及确保可将物理读取内容快速读取到内存中。 

这些 OLTP 环境需要更高的内存量、快速存储和所需的 I/O 带宽才能以最佳方式执行。 

为了保持此性能级别而不增加 SQL Server 许可成本，Azure 提供了具有[受约束的 vCPU 计数](../../../virtual-machines/constrained-vcpu.md)的 VM 大小。 

这有助于通过减少可用的 vCore，同时保持与父虚拟机相同的内存、存储和 I/O 带宽来控制许可成本。

可以将 vCPU 计数限制为原始 VM 大小的一半到四分之一。 减少可用于虚拟机的 vCore 可实现更高的内存与 vCore 比率，但计算成本将保持不变。

这些新的 VM 大小有一个用于指定活动 vCPU 数的后缀，使其更易于识别。 

例如，[M64-32ms](../../../virtual-machines/constrained-vcpu.md) 只需要许可 32 个 SQL Server vCore（具有 [M64ms](../../../virtual-machines/m-series.md) 的内存、I/O 和吞吐量），而[M64-16ms](../../../virtual-machines/constrained-vcpu.md) 只需要许可 16 个 vCore。  虽然 [M64-16ms](../../../virtual-machines/constrained-vcpu.md) 的 SQL Server 许可成本是 M64ms 的四分之一，但虚拟机的计算成本是相同的。

> [!NOTE] 
> - 大中型数据仓库工作负载仍可能受益于[受约束的 vCore VM](../../../virtual-machines/constrained-vcpu.md)，但数据仓库工作负载通常具有以下特点：用户较少，通过并行运行的查询计划处理大量数据的进程也较少。 
> - 计算成本（包括操作系统许可）与父虚拟机保持相同。 



## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅本系列中的其他文章：
- [快速核对清单](performance-guidelines-best-practices-checklist.md)
- [存储](performance-guidelines-best-practices-storage.md)
- [安全性](security-considerations-best-practices.md)
- [HADR 设置](hadr-cluster-best-practices.md)
- [收集基线](performance-guidelines-best-practices-collect-baseline.md)

有关安全最佳做法，请参阅 [Azure 虚拟机上 SQL Server 的安全注意事项](security-considerations-best-practices.md)。

查看 [Azure 虚拟机上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)中的其他 SQL Server 虚拟机文章。 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](frequently-asked-questions-faq.yml)。
