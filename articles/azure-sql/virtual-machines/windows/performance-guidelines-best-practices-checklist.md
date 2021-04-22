---
title: 清单：性能最佳做法和准则
description: 提供一个快速清单，可用于查看有关 Azure 虚拟机 (VM) 上 SQL Server 性能优化的最佳做法和指南。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 84f2f4f679de80cd9b5fc986d40e084bae8a4cad
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313753"
---
# <a name="checklist-performance-best-practices-for-sql-server-on-azure-vms"></a>清单：有关 Azure VM 上 SQL Server 性能的最佳做法
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文提供一个可用于快速查看有关 Azure 虚拟机 (VM) 上 SQL Server 性能优化的一系列最佳做法和指南的清单。 

如需了解更全面详尽的信息，请参阅本系列中的其他文章：[VM 大小](performance-guidelines-best-practices-vm-size.md)、[存储](performance-guidelines-best-practices-storage.md)、[收集基线](performance-guidelines-best-practices-collect-baseline.md)。 


## <a name="overview"></a>概述

在 Azure 虚拟机上运行 SQL Server 时，继续使用适用于本地服务器环境中的 SQL Server 的相同数据库性能优化选项。 但是，关系数据库在公有云中的性能取决于许多因素，如虚拟机的大小和数据磁盘的配置。

通常需要在针对成本优化和针对性能优化之间进行权衡。 此性能最佳做法系列的重点是确保 Azure 虚拟机上的 SQL Server 实现最佳性能。 如果工作负荷要求较低，可能不需要每项建议的优化。 评估这些建议时应考虑性能需求、成本和工作负荷模式。

## <a name="vm-size"></a>VM 大小

下面是有关在 Azure VM 上运行 SQL Server 时应选择的 VM 大小的最佳做法的快速清单： 

- 使用具有 4 个或更多 vCPU 的 VM 大小，如 [Standard_M8-4ms](../../../virtual-machines/m-series.md)、[E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series) 或 [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15)，或使用更大的大小。 
- 使用[内存优化](../../../virtual-machines/sizes-memory.md)的虚拟机大小，以实现 SQL Server 工作负载的最佳性能。 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md)、[Edsv4](../../../virtual-machines/edv4-edsv4-series.md) 系列、[M-](../../../virtual-machines/m-series.md) 和 [Mv2-](../../../virtual-machines/mv2-series.md) 系列提供 OLTP 工作负载所需的最佳内存与 vCore 比率。 这两个 M 系列的 VM 都提供最高的内存与 vCore 比率，可满足任务关键型工作负载的需求，也非常适合用于数据仓库工作负载。 
- 对于任务关键型工作负载和数据仓库工作负载，考虑应用更高的内存与 vCore 比率。 
- 使用 Azure 虚拟机市场映像作为 SQL Server 设置，并配置存储选项以获得最佳 SQL Server 性能。 
- 收集目标工作负载的性能特征，并使用它们来确定适用于你的业务的 VM 大小。

若要了解详细信息，请参阅内容全面的 [VM 大小最佳做法](performance-guidelines-best-practices-vm-size.md)。 

## <a name="storage"></a>存储

下面是有关在 Azure VM 上运行 SQL Server 时的存储配置的最佳做法的快速清单： 

- 在选择磁盘类型之前，监视应用程序并确定 SQL Server 数据、日志和 tempdb 文件的[存储带宽和延迟要求](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements)。 
- 为了优化存储性能，请规划可用的最高未缓存 IOPS，并使用数据缓存作为数据读取的性能功能，同时避免[虚拟机和磁盘上限/限制](../../../virtual-machines/premium-storage-performance.md#throttling)。
- 将数据、日志和 tempdb 文件放在不同的驱动器上。
    - 对于数据驱动器，仅使用[高级 P30 和 P40 磁盘](../../../virtual-machines/disks-types.md#premium-ssd)以确保可提供缓存支持
    - 对于日志驱动器，规划容量并测试性能与成本，同时评估[高级 P30 - P80 磁盘](../../../virtual-machines/disks-types.md#premium-ssd)。
      - 如果需要亚毫秒存储延迟，请对事务日志使用 [Azure 超级磁盘](../../../virtual-machines/disks-types.md#ultra-disk)。 
      - 对于 M 系列虚拟机部署，请考虑[写入加速器](../../../virtual-machines/how-to-enable-write-accelerator.md)，而不是使用 Azure 超级磁盘。
    - 在选择最佳 VM 大小后，请将 [tempdb](/sql/relational-databases/databases/tempdb-database) 放置在大多数 SQL Server 工作负载的本地临时 SSD `D:\` 驱动器上。 
      - 如果本地驱动器的容量对 tempdb 来说不足够，请考虑增加 VM 的大小。 有关详细信息，请参阅[数据文件缓存策略](performance-guidelines-best-practices-storage.md#data-file-caching-policies)。
- 使用[存储空间](/windows-server/storage/storage-spaces/overview)对多个 Azure 数据磁盘进行条纹化，以将 I/O 带宽增加到目标虚拟机的 IOPS 和吞吐量上限。
- 将数据文件磁盘的[主机缓存](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits)设置为只读。
- 将日志文件磁盘的[主机缓存](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits)设置为无。
    - 请不要在包含 SQL Server 文件的磁盘上启用读取/写入缓存。 
    - 更改磁盘的缓存设置之前，请始终停止 SQL Server 服务。
- 对于开发和测试工作负荷，请考虑使用标准存储。 不建议将标准 HDD/SDD 用于生产工作负载。
- [基于额度的磁盘突发](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) 仅应考虑用于较小的开发/测试工作负载和部门系统。
- 预配与 SQL Server VM 位于同一区域的存储帐户。 
- 在存储帐户上禁用 Azure 异地冗余存储（异地复制）并使用 LRS（本地冗余存储）。
- 将数据磁盘格式化，为临时 `D:\` 驱动器（默认为 4 KB）以外的驱动器上放置的所有数据文件使用 64 KB 的分配单元大小。 通过 Azure 市场部署的 SQL Server VM 附带经过格式化的数据磁盘，其中分配单元大小和存储池的交错设置为 64 KB。 

若要了解详细信息，请参阅内容全面的[存储最佳做法](performance-guidelines-best-practices-storage.md)。 


## <a name="azure--sql-feature-specific"></a>Azure 和 SQL 特定功能

下面是有关在 Azure VM 上运行 SQL Server 时 SQL Server 和 Azure 特定配置的最佳做法的快速清单： 

- 注册 [SQL IaaS 代理扩展](sql-agent-extension-manually-register-single-vm.md)，以获取多项[功能优势](sql-server-iaas-agent-extension-automate-management.md#feature-benefits)。 
- 启用数据库页面压缩。
- 对数据文件启用即时文件初始化。
- 限制数据库自动增长。
- 禁用数据库自动收缩。
- 将所有数据库（包括系统数据库）转移到数据磁盘。
- 将 SQL Server 错误日志和跟踪文件目录移到数据磁盘。
- 配置默认的备份和数据库文件位置。
- [在内存中启用锁定页面](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows)。
- 评估并应用已安装的 SQL Server 版本的[最新累积更新](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server)。
- 直接备份到 Azure Blob 存储。
- 使用多个 [tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) 文件，每个核心 1 个文件，最多 8 个文件。



## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅本系列中的其他文章：
- [VM 大小](performance-guidelines-best-practices-vm-size.md)
- [存储](performance-guidelines-best-practices-storage.md)
- [收集基线](performance-guidelines-best-practices-collect-baseline.md)

有关安全最佳做法，请参阅 [Azure 虚拟机上 SQL Server 的安全注意事项](security-considerations-best-practices.md)。

查看 [Azure 虚拟机上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)中的其他 SQL Server 虚拟机文章。 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](frequently-asked-questions-faq.md)。
