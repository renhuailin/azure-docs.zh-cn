---
title: Azure 中 SQL Server 的性能准则 | Microsoft Docs
description: 提供有关在 Microsoft Azure 虚拟机中优化 SQL Server 性能的准则。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6ba30436b363353ad183396e07111b33ca912dbf
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737416"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Azure 虚拟机上的 SQL Server 的性能准则
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文提供了有关在 Microsoft Azure 虚拟机中优化 SQL Server 性能的指南。

## <a name="overview"></a>概述

在 Azure 虚拟机上运行 SQL Server 时，建议你继续使用适用于本地服务器环境中的 SQL Server 的相同数据库性能优化选项。 但是，关系数据库在公有云中的性能取决于许多因素，如虚拟机的大小和数据磁盘的配置。

[在 Azure 门户中预配的 SQL Server 映像](sql-vm-create-portal-quickstart.md)遵循常规存储[配置最佳做法](storage-configuration.md)。 在预配后，请考虑应用本文中讨论的其他优化措施。 根据你的工作负荷进行选择并通过测试进行验证。

> [!TIP]
> 通常需要在针对成本优化和针对性能优化之间进行权衡。 本文重点介绍了如何使 Azure 虚拟机上的 SQL Server 实现最佳性能。 如果工作负荷要求较低，可能不需要下面列出的每项优化。 评估这些建议时应考虑性能需求、成本和工作负荷模式。

## <a name="quick-checklist"></a>快速核对清单

下面是一个快速核对清单，用于优化 Azure 虚拟机上的 SQL Server 性能：

| 区域 | 优化 |
| --- | --- |
| [VM 大小](#vm-size-guidance) | - 使用具有 4 个或更多 vCPU 的 VM 大小，如 [Standard_M8-4ms](../../../virtual-machines/m-series.md)、[E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)[DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 或更大的大小。 <br/><br/> - 使用[内存优化](../../../virtual-machines/sizes-memory.md)的虚拟机大小，以实现 SQL Server 工作负载的最佳性能。 <br/><br/> - [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md)、 [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) 系列、 [M-](../../../virtual-machines/m-series.md)和 [Mv2](../../../virtual-machines/mv2-series.md) 系列提供 OLTP 工作负载所需的最佳内存到 vCore 的比率。 这两个 M 系列 VM 都提供最高的内存与 vCore 比率，可满足任务关键型工作负载的需求，也非常适合用于数据仓库工作负载。 <br/><br/> - 对于任务关键型工作负载和数据仓库工作负载，可能需要更高的内存与 vCore 比率。 <br/><br/> - 利用 Azure 虚拟机市场映像作为 SQL Server 设置，并配置存储选项以获得最佳 SQL Server 性能。 <br/><br/> - 收集目标工作负载的性能特征，并使用它们来确定适用于你的业务的适当 VM 大小。|
| [存储](#storage-guidance) | - 若要详细了解根据 TPC-E 和 TPC_C 基准在 Azure 虚拟机上进行的 SQL Server 性能测试，请参阅博客：[优化 OLTP 性能](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)。 <br/><br/> - 使用[高级 SSD](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) 可获得最高性价比。 为数据文件配置[只读缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)，不要为日志文件配置缓存。 <br/><br/> -如果工作负荷需要的存储延迟少于1毫秒，则使用 [超磁盘](../../../virtual-machines/disks-types.md#ultra-disk) 。 若要了解详细信息，请参阅 [迁移到超磁盘](storage-migrate-to-ultradisk.md) 。 <br/><br/> - 在选择磁盘类型之前，通过[监视应用程序](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)来收集 SQL Server 数据、日志和临时数据库文件的存储延迟要求。 如果需要 < 1-ms 存储延迟是必需的，则使用超磁盘，否则使用高级 SSD。 如果只有日志文件需要低延迟，数据文件不需要，请仅为日志文件在所需的 IOPS 和吞吐量级别[预配超级磁盘](../../../virtual-machines/disks-enable-ultra-ssd.md)。 <br/><br/>  - 建议仅将标准存储用于开发和测试目的，或用于备份文件，不应该用于生产工作负载。 <br/><br/> - 将[存储帐户](../../../storage/common/storage-account-create.md)和 SQL Server VM 保存在相同的区域。<br/><br/> - 在存储帐户中禁用 Azure [异地冗余存储](../../../storage/common/storage-redundancy.md)（异地复制）。  |
| [磁盘](#disks-guidance) | - 至少使用 2 个[高级 SSD 磁盘](../../../virtual-machines/disks-types.md#premium-ssd)（1 个用于日志文件，1 个用于数据文件）。 <br/><br/> -对于需要 < 1 ms IO 延迟的工作负荷，请为 M 系列启用写入加速器，并考虑对 Es 和 DS 系列使用超级 SSD 磁盘。 <br/><br/> - 在托管数据文件的磁盘上启用[只读缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)。<br/><br/> - [为 SQL Server 数据、日志和 TempDB 文件配置存储](storage-configuration.md)时，请在工作负荷的要求以外额外添加 20% 的高级 IOPS/吞吐容量。 <br/><br/> - 避免使用操作系统或临时磁盘进行数据库存储或日志记录。<br/><br/> - 请勿在托管日志文件的磁盘上启用缓存。  **重要说明**：更改 Azure 虚拟机磁盘的缓存设置时，请停止 SQL Server 服务。<br/><br/> - 条带化多个 Azure 数据磁盘，以提高存储吞吐量。<br/><br/> - 使用规定的分配大小格式化。 <br/><br/> - 将 TempDB 放在本地 SSD `D:\` 驱动器上，用于任务关键型 SQL Server 工作负荷（在选择正确的 VM 大小后）。 如果从 Azure 门户或 Azure 快速入门模板创建 VM，并[将临时数据库置于本地磁盘上](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)，则无需执行任何其他操作；对于所有其他情况，请按博客文章[使用 SSD 存储 TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) 中的步骤操作，以防止重启后发生故障。 如果本地驱动器的容量对于 TempDB 而言不足，请将 TempDB 放在位于具有[只读缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)的高级 SSD 盘上的[条带化](../../../virtual-machines/premium-storage-performance.md)存储池上。 |
| [I/O](#io-guidance) |- 启用数据库页面压缩。<br/><br/> - 对数据文件启用即时文件初始化。<br/><br/> - 限制数据库自动增长。<br/><br/> - 禁用数据库自动收缩。<br/><br/> - 将所有数据库（包括系统数据库）转移到数据磁盘。<br/><br/> - 将 SQL Server 错误日志和跟踪文件目录移到数据磁盘。<br/><br/> - 配置默认的备份和数据库文件位置。<br/><br/> - [在内存中启用锁定页面](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows)。<br/><br/> - 评估并应用已安装的 SQL Server 版本的[最新累积更新](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server)。 |
| [Feature-specific](#feature-specific-guidance) | - 直接备份到 Azure Blob 存储。<br/><br/>- 对于大于 12 TB 的数据库，请使用[文件快照备份](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)。 <br/><br/>- 使用多个临时数据库文件，每个核心处理 1 个文件，最多处理 8 个文件。<br/><br/>- 将最大服务器内存设置为操作系统剩余内存的 90% 或最大 50 GB。 <br/><br/>- 启用软 NUMA。 |


<br/>
有关如何和为何进行这些优化的详细信息，请参阅以下部分提供的详细信息与指导 。
<br/><br/>

## <a name="getting-started"></a>入门

如果要在 Azure VM 上创建新的 SQL Server，并且未迁移当前源系统，请根据供应商要求创建新的 SQL Server VM。  SQL Server VM 的供应商要求与在本地部署的要求相同。 

如果要使用为云生成的新应用程序创建新的 SQL Server VM，则随着数据和使用情况要求的变化，你可以轻松地调整 SQL Server VM 的大小。
使用较低层 D 系列、B 系列或 Av2 系列开始开发环境，并随着时间的推移扩展你的环境。 

针对生产 OLTP 环境的建议最小值是 4 个 vCore、32 GB 内存，以及内存与 vCore 比率为 8。 对于新环境，从有 4 个 vCore 的虚拟机开始，在数据和计算要求发生变化时缩放到 8 个、16 个、32 个或更多 vCore。 对于 OLTP 吞吐量，目标 SQL Server VM 的每个 vCore 具有 5000 个 IOPS。 

将 SQL Server VM 市场映像与门户中的存储配置结合使用。 这样便可更轻松地正确创建具有工作负载所需的大小、IOPS 和吞吐量的存储池。 必须选择支持高级存储和高级存储缓存的 SQL Server VM。 有关详细信息，请参阅[存储](#storage-guidance)部分。 

SQL Server 数据仓库和任务关键型环境通常需要缩放到内存与 vCore 比率大于 8。 对于中等环境，你可能希望选择核心与内存比率为 16，而对于较大的数据仓库环境，则可能希望该比率为 32。 

SQL Server 数据仓库环境通常受益于大型计算机的并行处理。 因此，对于大型数据仓库环境，强烈建议选择 M 系列和 Mv2 系列。

## <a name="vm-size-guidance"></a>VM 大小指导原则

使用源计算机的 vCPU 和内存配置作为将当前本地 SQL Server 数据库迁移到 Azure VM 上的 SQL Server 的基线。 在 Azure 中使用你的核心许可证，以利用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)并节省 SQL Server 许可成本。

对于生产 SQL Server 工作负载，Microsoft 建议以内存与 vCore 比率为 8 作为起点。 对于非生产工作负载，可接受更小的比率。 

根据工作负荷 (OLTP 或数据仓库) ，选择 [内存优化](../../../virtual-machines/sizes-memory.md)、 [常规用途](../../../virtual-machines/sizes-general.md)、 [存储优化](../../../virtual-machines/sizes-storage.md)或 [受限制的 vCore](../../../virtual-machines/constrained-vcpu.md) 虚拟机大小，最适合 SQL Server 性能。 

### <a name="memory-optimized"></a>内存优化

[内存优化虚拟机大小](../../../virtual-machines/sizes-memory.md)是 SQL Server Vm 和 Microsoft 推荐的选择的主目标。 内存优化的虚拟机提供更大的内存与 CPU 比率和大中型缓存选项。 

#### <a name="m-and-mv2-series"></a>M 和 Mv2 系列

[M 系列](../../../virtual-machines/m-series.md) 为某些最大的 SQL Server 工作负载提供较高的 vCore 数量和内存。  

[Mv2 系列](../../../virtual-machines/mv2-series.md)具有最高的 vCore 计数和内存，建议用于关键任务和数据仓库工作负荷。 Mv2 系列实例是内存优化的 VM 大小，提供无与伦比的计算性能，以支持大量内存中数据库和工作负荷，这对于关系数据库服务器、大型缓存和内存中分析而言非常理想。

例如， [Standard_M64ms](../../../virtual-machines/m-series.md) 的内存与 vCore 的比率为28。

M 和 Mv2 系列的某些功能对 SQL Server 性能有吸引力，其中包括 [高级存储](../../../virtual-machines/premium-storage-performance.md) 和 [高级存储缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching) 支持、 [超高磁盘](../../../virtual-machines/disks-enable-ultra-ssd.md) 支持和 [写入加速](../../../virtual-machines/how-to-enable-write-accelerator.md)。

#### <a name="edsv4-series"></a>Edsv4 系列

[Edsv4 系列](../../../virtual-machines/edv4-edsv4-series.md)专为内存密集型应用程序而设计。 与以前的 Ev3/Esv3 大小的第 2 代 VM 相比，这些 VM 具有较大的本地存储 SSD 容量、更多的本地磁盘 IOPS、高达 504 GiB 的 RAM 以及更强大的计算能力。 这些虚拟机的内存与 vCore 比率几乎全都为 8，非常适合标准 SQL Server 工作负载。 

该 VM 系列非常适合于内存密集型企业应用程序和受益于低延迟、高速本地存储的应用程序。

Edsv4 系列虚拟机支持[高级存储](../../../virtual-machines/premium-storage-performance.md)和[高级存储缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)。

#### <a name="dsv2-series-11-15"></a>DSv2 系列 11-15

[DSv2 系列 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 的内存和磁盘配置与之前的 D 系列相同。 该系列的所有虚拟机的内存与 CPU 比率均为 7。 

[DSv2 系列 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 支持[高级存储](../../../virtual-machines/premium-storage-performance.md)和[高级存储缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)，强烈建议使用此系列来获取最佳性能。

### <a name="general-purpose"></a>常规用途

[常规用途虚拟机大小](../../../virtual-machines/sizes-general.md)旨在为较小的入门级工作负载（如开发和测试、Web 服务器和较小的数据库服务器）提供均衡的内存与 vCore 比率。 

由于常规用途虚拟机的内存与 vCore 比率较小，因此必须仔细监视基于内存的性能计数器，以确保 SQL Server 能够获取所需的缓冲区缓存内存。 有关详细信息，请参阅[内存性能基线](#memory)。 

由于对于生产工作负载，建议的最低内存与 vCore 比率为 8，因此对于运行 SQL Server 的常规用途 VM，建议的最低配置为 4 个 vCPU 和 32 GB 内存。 

#### <a name="ddsv4-series"></a>Ddsv4 系列

[Ddsv4 系列](../../../virtual-machines/ddv4-ddsv4-series.md)的 vCPU、内存和临时磁盘数量相当，但支持的内存与 vCore 比率较小。 

Ddsv4 VM 的延迟更低、本地存储速度更高。

这些虚拟机非常适合需要快速访问临时存储和部门关系数据库的并行 SQL 和应用部署。 本系列的所有虚拟机的标准内存与 vCore 比率均为 4。 

因此，建议利用 D8ds_v4 作为本系列中的入门虚拟机，它具有 8 个 vCore 和 32 GB 内存。 该系列中最大的虚拟机是 D64ds_v4，它拥有 64 个 vCore 和 256 GB 的内存。

[Ddsv4 系列](../../../virtual-machines/ddv4-ddsv4-series.md)虚拟机支持[高级存储](../../../virtual-machines/premium-storage-performance.md)和[高级存储缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)。

> [!NOTE]
> [Ddsv4 系列](../../../virtual-machines/ddv4-ddsv4-series.md)的内存与 vCore 比率并非针对 SQL Server 工作负载建议的 8。 因此，请仅考虑将这些虚拟机用于较小的应用程序和开发工作负载。

#### <a name="b-series"></a>B 系列

[可突发 B 系列](../../../virtual-machines/sizes-b-series-burstable.md)虚拟机大小非常适合不需要一致性能的工作负载，如概念验证以及非常小的应用程序和开发服务器。 

大多数[可突发 B 系列](../../../virtual-machines/sizes-b-series-burstable.md)虚拟机大小的内存与 vCore 比率为 4。 其中最大的虚拟机为 [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md)，它具有 20 个 vCore 和 80 GB 内存。

该系列是独一无二的，因为应用能够在营业时间突发，并且可突发的额度因虚拟机大小而异。 

当额度用尽时，VM 会恢复到基线虚拟机性能。

与其他系列中的其他 VM 大小相比，B 系列的优势是可以节省计算成本，尤其是在需要全天候尽量少使用处理能力时。

此系列支持[高级存储](../../../virtual-machines/premium-storage-performance.md)，但不支持[高级存储缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)。

> [!NOTE] 
> [可突发 B 系列](../../../virtual-machines/sizes-b-series-burstable.md)的内存与 vCore 比率并非针对 SQL Server 工作负载建议的 8。 因此，请仅考虑将这些虚拟机用于较小的应用程序、Web 服务器和开发工作负载。

#### <a name="av2-series"></a>Av2 系列

[Av2 系列](../../../virtual-machines/av2-series.md) VM 最适合入门级工作负载，如开发和测试、低流量 Web 服务器、中小型应用数据库和概念验证。

只有 [Standard_A2m_v2](../../../virtual-machines/av2-series.md)（2 个 vCore 和 16 GB 内存）、[Standard_A4m_v2](../../../virtual-machines/av2-series.md)（4 个 vCore 和 32 GB 内存）和 [Standard_A8m_v2](../../../virtual-machines/av2-series.md)（8 个 vCore 和 64 GB 内存）的内存与 vCore 比率为 8，使这三个虚拟机在此系列中排名前三。 

这些虚拟机非常适合小型开发和测试 SQL Server 计算机。 

对于小型应用程序和 Web 服务器来说，具有 8 个 vCore 的 [Standard_A8m_v2](../../../virtual-machines/av2-series.md) 可能也是一个不错的选择。

> [!NOTE] 
> Av2 系列不支持高级存储，因此不建议将其用于生产 SQL Server 工作负载，即使是内存与 vCore 比率为 8 的虚拟机也是如此。

### <a name="storage-optimized"></a>存储优化

[存储优化的 VM 大小](../../../virtual-machines/sizes-storage.md)适用于特定用例。 这些虚拟机经过专门涉及，具有经过优化的磁盘吞吐量和 IO。 此虚拟机系列适用于大数据方案、数据仓库和大型事务数据库。 

#### <a name="lsv2-series"></a>Lsv2 系列

[Lsv2 系列提供](../../../virtual-machines/lsv2-series.md)高吞吐量、低延迟和本地 NVMe 存储。 优化 Lsv2 系列 VM 以使用直接连接到 VM 的节点上的本地磁盘，而不是使用持久数据磁盘。 

这些虚拟机是适用于大数据、数据仓库、报告和 ETL 工作负载的强大选项。 本地 NVMe 存储的高吞吐量和 IOPs 非常适合用于处理将加载到数据库的文件，以及可以从源系统或其他存储库（例如 Azure Blob 存储或 Azure Data Lake）重新创建源数据的其他方案。 [Lsv2 系列](../../../virtual-machines/lsv2-series.md) Vm 还可以将磁盘性能突发到最多30分钟。

这些虚拟机的大小介于8到 80 vCPU 之间，每个 vCPU 有8个 GiB 内存，每8个个 vcpu 有 1.92 TB 的 NVMe SSD。 这意味着，对于此系列系列的最大 VM， [L80s_v2](../../../virtual-machines/lsv2-series.md)，有80的内存，1.92 Tb 的 NVMe 存储空间为 vCPU 和640选手。  所有这些虚拟机上的内存与 vCore 的一致性都是一致的。

NVMe 存储是暂时的，这意味着，如果重新启动虚拟机，数据将在这些磁盘上丢失。

Lsv2 和 Ls 系列支持 [高级存储](../../../virtual-machines/premium-storage-performance.md)，但不支持高级存储缓存。 不支持创建本地缓存以提高 IOPs。 

> [!WARNING]
> 将数据文件存储在临时 NVMe 存储上可能会导致在释放 VM 时丢失数据。 

### <a name="constrained-vcores"></a>受约束的 vCore

高性能 SQL Server 工作负载通常需要大量内存、IO 和吞吐量，而无需较高的 vCore 计数。 

大多数 OLTP 工作负载都是由大量较小的事务驱动的应用程序数据库。 对于 OLTP 工作负载，仅读取或修改少量数据，但由用户数驱动的事务量要高得多。 必须向缓存计划提供 SQL Server 内存、存储最近访问的数据以提高性能，以及确保可将物理读取内容快速读取到内存中。 

这些 OLTP 环境需要更高的内存量、快速存储和所需的 I/O 带宽才能以最佳方式执行。 

为了保持此性能级别而不增加 SQL Server 许可成本，Azure 提供了具有[受约束的 vCPU 计数](../../../virtual-machines/constrained-vcpu.md)的 VM 大小。 

这有助于通过减少可用的 vCore，同时保持与父虚拟机相同的内存、存储和 I/O 带宽来控制许可成本。

可以将 vCPU 计数限制为原始 VM 大小的一半到四分之一。 减少虚拟机可用的 vCore 将实现更高的内存与 vCore 比率。

这些新的 VM 大小有一个用于指定活动 vCPU 数的后缀，使其更易于识别。 

例如，[M64-32ms](../../../virtual-machines/constrained-vcpu.md) 只需要许可 32 个 SQL Server vCore，并且 [M64ms](../../../virtual-machines/m-series.md) 和 [M64-16ms](../../../virtual-machines/constrained-vcpu.md) 的内存、IO 和吞吐量只需要许可 16 个 vCore。  虽然 [M64-16ms](../../../virtual-machines/constrained-vcpu.md) 的 SQL Server 许可成本是 M64ms 的四分之一，但虚拟机的计算成本是相同的。

> [!NOTE] 
> - 大中型数据仓库工作负载仍可能受益于[受约束的 vCore VM](../../../virtual-machines/constrained-vcpu.md)，但数据仓库工作负载通常具有以下特点：用户较少，通过并行运行的查询计划处理大量数据的进程也较少。 
> - 计算成本（包括操作系统许可）与父虚拟机保持相同。 

## <a name="storage-guidance"></a>存储指导原则

要详细了解根据 TPC-E 和 TPC-C 基准在 Azure 虚拟机上进行的 SQL Server 性能测试，请参阅博客[优化 OLTP 性能](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)。 

对于所有生产工作负荷，建议结合使用 Azure Blob 缓存和高级 SSD。 

> [!WARNING]
> 标准 HDD 和 SSD 具有不同的延迟和带宽，建议仅用于开发/测试工作负荷。 生产工作负荷应使用高级 SSD。

此外，我们建议在 SQL Server 虚拟机所在的数据中心内创建 Azure 存储帐户，以减小传输延迟。 创建存储帐户时应禁用异地复制，因为无法保证在多个磁盘上的写入顺序一致。 相反，请考虑在两个 Azure 数据中心之间配置一个 SQL Server 灾难恢复技术。 有关详细信息，请参阅 [《High Availability and Disaster Recovery for SQL Server on Azure Virtual Machines》](business-continuity-high-availability-disaster-recovery-hadr-overview.md)（Azure 虚拟机中 SQL Server 的高可用性和灾难恢复）。

## <a name="disks-guidance"></a>磁盘指导原则

Azure 虚拟机上有三种主要磁盘类型：

* **OS 磁盘**：创建 Azure 虚拟机时，该平台至少将一个磁盘（标记为 **C** 驱动器）附加到 VM 作为操作系统磁盘。 此磁盘是一个 VHD，在存储空间中存储为一个页 blob。
* **临时磁盘**：Azure 虚拟机包含另一个称为临时磁盘的磁盘（标记为 **D**: 驱动器）。 这是可用于暂存空间的节点上的一个磁盘。
* **数据磁盘**：还可以将其他磁盘作为数据磁盘附加到虚拟机，这些磁盘在存储空间中存储为页 Blob。

以下部分说明了有关使用这些不同磁盘的建议。

### <a name="operating-system-disk"></a>操作系统磁盘

操作系统磁盘是可以作为操作系统的运行版本来启动和装载的 VHD，标记为 **C** 驱动器。

操作系统磁盘上的默认缓存策略是 **读/写**。 对于性能敏感型应用程序，我们建议使用数据磁盘而不是操作系统磁盘。 请参阅下面有关数据磁盘的部分。

### <a name="temporary-disk"></a>临时磁盘

临时存储驱动器，标记为 **D** 驱动器，不会持久保存到 Azure Blob 存储中。 不要在 **D**: 驱动器中存储用户数据库文件或用户事务日志文件。

将 TempDB 放在本地 SSD `D:\` 驱动器上，用于任务关键型 SQL Server 工作负荷（在选择正确的 VM 大小后）。 如果从 Azure 门户或 Azure 快速入门模板创建 VM，并[将临时数据库置于本地磁盘上](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)，则无需执行任何其他操作；对于所有其他情况，请按博客文章[使用 SSD 存储 TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) 中的步骤操作，以防止重启后发生故障。 如果本地驱动器的容量对于 TempDB 而言不足，请将 TempDB 放在位于具有[只读缓存](../../../virtual-machines/premium-storage-performance.md#disk-caching)的高级 SSD 盘上的[条带化](../../../virtual-machines/premium-storage-performance.md)存储池上。

对于支持高级 SSD 的 VM，你也可以将 TempDB 存储在支持高级 SSD 且已启用读缓存的磁盘上。


### <a name="data-disks"></a>数据磁盘数

* **将高级 SSD 盘用于数据和日志文件**：如果不使用磁盘条带化，请使用两个高级 SSD 盘，一个包含日志文件，另一个包含数据。 每个高级 SSD 均根据其大小提供许多 IOPS 和带宽 (MB/s)，如[选择磁盘类型](../../../virtual-machines/disks-types.md)一文中所述。 如果使用磁盘条带化技术，例如存储空间，则可实现最佳性能，因为将具有两个池，一个用于日志文件，另一个用于数据文件。 但是，如果你打算使用 SQL Server 故障转移群集实例 (FCI)，则必须配置一个池，或者改用[高级文件共享](failover-cluster-instance-premium-file-share-manually-configure.md)。

   > [!TIP]
   > - 有关针对各种磁盘和工作负荷配置的测试结果，请参阅以下博客文章：[Azure 虚拟机上的 SQL Server 的存储配置准则](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm)。
   > - 对于需要约 50,000 IOPS 的 SQL Server 的任务关键型性能，请考虑使用超级 SSD 替换 10 -P30 磁盘。 有关详细信息，请参阅以下博客文章：[具有超级 SSD 的任务关键型性能](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/)。

   > [!NOTE]
   > 在门户中预配 SQL Server VM 时，可以选择编辑存储配置。 Azure 将根据你所做的配置来配置一个或多个磁盘。 将使用条带化功能将多个磁盘组合到单个存储池中。 数据文件和日志文件一起位于此配置中。 有关详细信息，请参阅 [SQL Server VM 的存储配置](storage-configuration.md)。

* **磁盘条带化**：为提高吞吐量，可以添加更多的数据磁盘，并使用磁盘条带化。 若要确定数据磁盘的数量，需要分析日志文件以及数据和 TempDB 文件所需的 IOPS 数量和带宽。 请注意，不同的 VM 大小对受支持的 IOP 数量和带宽有不同的限制，请参阅每个 [VM 大小](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)的 IOPS 表。 遵循以下指南：

  * 对于 Windows 8/Windows Server 2012 或更高版本，按照以下指南使用[存储空间](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11))：

      1. 对于 OLTP 工作负荷，将交错（条带大小）设置为 64 KB（65,536 字节），对于数据仓库工作负荷，将交错（条带大小）设置为 256 KB（262,144 字节），以避免分区定位错误导致的性能影响。 这必须使用 PowerShell 设置。
      2. 设置列计数 = 物理磁盘的数量。 配置的磁盘超过 8 个时，请使用 PowerShell（而不是服务器管理器 UI）。 

    例如，以下 PowerShell 创建新的存储池，其交错大小为 64 KB，其列数等于存储池中的物理磁盘数量：

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * 对于 Windows 2008 R2 或更早版本，可以使用动态磁盘（操作系统条带化卷），条带大小始终为 64 KB。 从 Windows 8/Windows Server 2012 开始不再提供此选项。 有关信息，请参阅[虚拟磁盘服务正在过渡到 Windows 存储管理 API](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api) 中的支持声明。

  * 如果将[存储空间直通 (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) 与 [SQL Server 故障转移群集实例](failover-cluster-instance-storage-spaces-direct-manually-configure.md)配合使用，则必须配置单个池。 虽然可以在该单个池上创建不同的卷，但它们都拥有相同的特征，例如相同的缓存策略。

  * 根据负载预期确定与你的存储池相关联的磁盘数。 请记住，不同的 VM 大小允许不同数量的附加数据磁盘。 有关详细信息，请参阅[虚拟机的大小](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

  * 如果使用的不是高级 SSD（开发/测试方案），建议添加 [VM 大小](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)支持的最大数量的数据磁盘并使用磁盘条带化。

* **缓存策略**：请注意用于缓存策略的以下建议，具体取决于你的存储配置。

  * 如果为数据文件和日志文件使用不同的磁盘，请在承载着数据文件和 TempDB 数据文件的数据磁盘上启用读取缓存。 这可能会明显提高性能。 不要在存放日志文件的磁盘上启用缓存，因为这会导致性能稍微降低。

  * 如果在单个存储池中使用磁盘条带化，则大多数工作负荷都会从读取缓存受益。 如果日志文件和数据文件分别具有单独的存储池，请仅在数据文件的存储池上启用读取缓存。 在某些高写入工作负荷中，不使用缓存时可能会获得更好的性能。 这只能通过测试来确定。

  * 前面的建议适用于高级 SSD。 如果使用的不是高级 SSD，不要在任何数据磁盘上启用任何缓存。

  * 有关配置磁盘缓存的说明，请参阅以下文章。 有关经典 (ASM) 部署模型，请参阅：[Set-AzureOSDisk](/previous-versions/azure/jj152847(v=azure.100)) 和 [Set-AzureDataDisk](/previous-versions/azure/jj152851(v=azure.100))。 有关 Azure 资源管理器部署模型，请参阅：[Set-AzOSDisk](/powershell/module/az.compute/set-azvmosdisk) 和 [Set-AzVMDataDisk](/powershell/module/az.compute/set-azvmdatadisk)。

     > [!WARNING]
     > 请在更改 Azure 虚拟机磁盘的缓存设置时停止 SQL Server 服务，以免出现数据库损坏的情况。

* **NTFS 分配单元大小**：格式化数据磁盘时，建议为数据和日志文件以及 TempDB 使用 64-KB 分配单元大小。 如果将 TempDB 放置在临时磁盘（D:\ 驱动器）上，则利用该驱动器获得的性能会超过对 64 KB 分配单元大小的需求。 

* **磁盘管理最佳做法**：删除数据磁盘或更改其缓存类型时，请在更改过程中停止 SQL Server 服务。 在 OS 磁盘上更改缓存设置时，Azure 会先停止 VM，在更改缓存类型后再重新启动 VM。 更改数据磁盘的缓存设置时，不会停止 VM，但会在更改期间将数据磁盘从 VM 分离，完成后再重新附加该数据磁盘。

  > [!WARNING]
  > 在进行这些操作时，如果无法停止 SQL Server 服务，则会导致数据库损坏。


## <a name="io-guidance"></a>I/O 指导原则

* 并行化应用程序和请求时可实现使用高级 SSD 的最佳结果。 高级 SSD 专为 IO 队列深度大于 1 的方案设计，因此对于单线程串行请求（即使它们是存储密集型），不会看到明显的性能提升。 例如，这会影响性能分析工具（如 SQLIO）的单线程测试结果。

* 请考虑使用[数据库页压缩](/sql/relational-databases/data-compression/data-compression)，因为这有助于提高 I/O 密集型工作负荷的性能。 但是，数据压缩可能会增加数据库服务器上的 CPU 消耗。

* 请考虑启用即时文件初始化以减少初始文件分配所需的时间。 要利用即时文件初始化，请将 SE_MANAGE_VOLUME_NAME 授予 SQL Server (MSSQLSERVER) 服务帐户并将其添加到 **执行卷维护任务** 安全策略。 如果使用的是用于 Azure 的 SQL Server 平台映像，默认服务帐户 (NT Service\MSSQLSERVER) 不会添加到 **执行卷维护任务** 安全策略。 换而言之，在 SQL Server Azure 平台映像中不启用即时文件初始化。 将 SQL Server 服务帐户添加到“执行卷维护任务”安全策略后，请重启 SQL Server 服务。 使用此功能可能有一些安全注意事项。 有关详细信息，请参阅[数据库文件初始化](/sql/relational-databases/databases/database-instant-file-initialization)。

* 请注意，**自动增长** 被视为只是非预期增长的偶发情况。 请勿使用自动增长来管理数据和日志每天的增长。 如果使用自动增长，请使用大小开关预先增长文件。

* 请确保禁用 **自动收缩** 以避免可能对性能产生负面影响的不必要开销。

* 将所有数据库（包括系统数据库）转移到数据磁盘。 有关详细信息，请参阅 [移动系统数据库](/sql/relational-databases/databases/move-system-databases)。

* 将 SQL Server 错误日志和跟踪文件目录移到数据磁盘。 在 SQL Server 配置管理器中右键单击 SQL Server 实例并选择属性，即可实现此目的。 可以在“启动参数”选项卡中更改错误日志和跟踪文件设置。在“高级”选项卡中指定转储目录。以下屏幕截图显示了错误日志启动参数的位置。

    ![SQL 错误日志屏幕截图](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* 设置默认的备份和数据库文件位置。 使用本文中的建议，并在“服务器属性”窗口中进行更改。 有关说明，请参阅 [View or Change the Default Locations for Data and Log Files (SQL Server Management Studio)](/sql/database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files)（查看或更改数据和日志文件的默认位置 (SQL Server Management Studio)）。 以下屏幕截图演示了进行这些更改的位置。

    ![SQL 数据日志和备份文件](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* 建立锁定的页以减少 IO 和任何分页活动。 有关详细信息，请参阅 [Enable the Lock Pages in Memory Option (Windows)](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows)（启用在内存中锁定页面的选项 (Windows)）。

* 如果运行的是 SQL Server 2012，安装 Service Pack 1 Cumulative Update 10。 此更新包含修复程序，适用于在 SQL Server 2012 中执行“select into temporary table”语句时出现 I/O 性能不良的情况。 有关信息，请参阅此 [知识库文章](https://support.microsoft.com/kb/2958012)。

* 请考虑在传入/传出 Azure 时压缩所有数据文件。

## <a name="feature-specific-guidance"></a>功能特定指南

某些部署可以使用更高级的配置技术，获得更多的性能好处。 下面的列表主要介绍可帮助你实现更佳性能的一些 SQL Server 功能：

### <a name="back-up-to-azure-storage"></a>备份到 Azure 存储
为在 Azure 虚拟机中运行的 SQL Server 执行备份时，可以使用 [SQL Server 备份到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)。 此功能从 SQL Server 2012 SP1 CU2 开始提供，建议在备份到附加数据磁盘时使用。 备份/还原到 Azure 存储或从中备份/还原时，请按照 [SQL Server 备份到 URL 的最佳做法和故障排除以及从 Azure 存储中存储的备份还原](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)中提供的建议操作。 此外还可以使用 [Azure 虚拟机上 SQL Server 的自动备份](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)自动执行这些备份。

对于 SQL Server 2012 以前版本，可以使用 [SQL Server 备份到 Azure 工具](https://www.microsoft.com/download/details.aspx?id=40740)。 此工具可以通过使用多个备份条带目标帮助提高备份吞吐量。

### <a name="sql-server-data-files-in-azure"></a>Azure 中的 SQL Server 数据文件

[Azure 中的 SQL Server 数据文件](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)这一新功能从 SQL Server 2014 开始提供。 使用 Azure 中的数据文件运行 SQL Server，与使用 Azure 数据磁盘时的性能特征相当。

### <a name="failover-cluster-instance-and-storage-spaces"></a>故障转移群集实例和存储空间

如果使用的是存储空间，则在“确认”页上向群集添加节点时，请清除标记为“将所有符合条件的存储添加到群集”的复选框。 

![取消选中符合条件的存储](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

如果正在使用存储空间，且选中了“将所有符合条件的存储添加到群集”，Windows 将在群集进程中分离虚拟磁盘。 这样一来，这些虚拟磁盘不会出现在磁盘管理器或资源管理器之中，除非从群集中删除存储空间，并使用 PowerShell 将其重新附加。 存储空间会将多个磁盘分组到存储池。 有关详细信息，请参阅[存储空间](/windows-server/storage/storage-spaces/overview)。

## <a name="multiple-instances"></a>多个实例 

在将多个 SQL Server 实例部署到单个虚拟机时，请考虑以下最佳做法： 

- 为每个 SQL Server 实例设置最大服务器内存，确保有剩余内存供操作系统使用。 如果更改分配给虚拟机的内存量，请确保更新 SQL Server 实例的内存限制。 
- 为数据、日志和 TempDB 设置单独的 LUN，因为它们都具有不同的工作负载模式，并且你不希望它们相互影响。 
- 在接近生产的巨大工作负载下彻底测试你的环境，确保它可应对你的应用程序 SLA 内的工作负载峰值。 

系统过载的信号可能包括但不限于工作线程用尽、响应时间过长和/或调度程序系统内存停滞。 



## <a name="collect-performance-baseline"></a>收集性能基线

要使用更规范的方法，请使用 PerfMon/LogMan 收集性能计数器并捕获 SQL Server 等待统计信息，以更好地了解源环境的一般压力和潜在瓶颈。 

首先按照[应用程序性能检查表](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)收集源工作负载在峰值时间的 CPU、内存、[IOPS](../../../virtual-machines/premium-storage-performance.md#iops)、[吞吐量](../../../virtual-machines/premium-storage-performance.md#throughput)和[延迟](../../../virtual-machines/premium-storage-performance.md#latency)。 

在高峰时段收集数据，例如典型工作日期间的工作负载，以及其他高负载过程（如日结束处理和周末 ETL 工作负载）。 请考虑为非典型的繁重工作负载（如季度末处理）纵向扩展资源，然后在工作负载完成后纵向缩减。 

使用性能分析选择可根据工作负载的性能要求缩放的 [VM 大小](../../../virtual-machines/sizes-memory.md)。


### <a name="iops-and-throughput"></a>IOPS 和吞吐量

SQL Server 的性能在很大程度上取决于 I/O 子系统。 除非数据库适合物理内存，否则 SQL Server 会不断将数据库页引入或引出缓冲池。 应以不同的方式处理 SQL Server 的数据文件。 对日志文件的访问是按顺序进行的，除非需要回滚事务，此时将随机访问数据文件（包括 TempDB）。 如果 I/O 子系统速度较慢，则用户可能会遇到性能问题，例如响应时间长和任务因超时而无法完成。 

默认情况下，Azure 市场虚拟机在物理磁盘上具有与数据文件不同的日志文件。 TempDB 数据文件计数和大小符合最佳做法，并以临时 D:/ 驱动为目标。 

以下 PerfMon 计数器可帮助验证 SQL Server 所需的 IO 吞吐量： 
* \LogicalDisk\Disk Reads/Sec（读取和写入 IOPS）
* \LogicalDisk\Disk Writes/Sec（读取和写入 IOPS） 
* \LogicalDisk\Disk Bytes/Sec（数据、日志和 TempDB 文件的吞吐量要求）

使用峰值级别的 IOPS 和吞吐量要求，评估与测量的容量相匹配的 VM 大小。 

如果你的工作负载要求读取和写入的 IOPS 分别为 2 万次和 1 万次，你可以选择 E16s_v3（有最多 3.2 万次缓存的和 2.56 万次未缓存的 IOPS）或 M16_s（有最多 2 万次缓存的和 1 万次未缓存的 IOPS）以及 2 个使用存储空间进行条带化的 P30 磁盘。 

请确保了解工作负载的吞吐量和 IOPS 要求，因为 VM 具有不同的 IOPS 和吞吐量缩放限制。

### <a name="memory"></a>内存

跟踪 OS 使用的外部内存以及 SQL Server 内部使用的内存。 识别任一组件的压力将有助于调整虚拟机的大小并识别调整的机会。 

以下 PerfMon 计数器可帮助验证 SQL Server 虚拟机的内存运行状况： 
* [\Memory\Available MBytes](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer:Memory Manager\Target Server Memory (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Memory Manager\Total Server Memory (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Buffer Manager\Lazy writes/sec](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Buffer Manager\Page life expectancy](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

### <a name="compute--processing"></a>计算/处理

Azure 中的计算管理方式与本地计算管理方式不同。 由于管理开销和获取新硬件的成本，本地服务器设计为持续数年无需升级。 虚拟化可缓解其中一些问题，并且应用程序经过优化，可充分利用基础硬件，这意味着任何对资源消耗的重大更改都需要重新平衡整个物理环境。 

在 Azure 中，这并不困难，因为在不同系列的硬件上，甚至在不同的区域中，很容易获得新虚拟机。 

在 Azure 中，你希望利用尽可能多的虚拟机资源，因此，应将 Azure 虚拟机的平均 CPU 配置得尽可能高，而不影响工作负载。 

以下 PerfMon 计数器可帮助验证 SQL Server 虚拟机的计算运行状况：
* \Processor Information(_Total)\% Processor Time
* \Process(sqlservr)\% Processor Time

> [!NOTE] 
> 理想情况下，尝试使用 80% 的计算能力，峰值时超过 90%，但在任何持续的时间内均不会达到 100%。 从根本上说，你只需预配应用程序所需的计算能力，然后计划根据业务需求进行纵向扩展或纵向缩减。 

## <a name="next-steps"></a>后续步骤

有关安全最佳做法，请参阅 [Azure 虚拟机上 SQL Server 的安全注意事项](security-considerations-best-practices.md)。

查看 [Azure 虚拟机上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)中的其他 SQL Server 虚拟机文章。 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](frequently-asked-questions-faq.md)。