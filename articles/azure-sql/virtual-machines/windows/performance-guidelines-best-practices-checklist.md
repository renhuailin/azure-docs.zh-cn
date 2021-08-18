---
title: 清单：最佳做法与指南
description: 提供一个快速清单，可用于查看有关 Azure 虚拟机 (VM) 上 SQL Server 性能优化的最佳做法和指南。
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
ms.date: 06/01/2021
ms.author: dpless
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: daca233044af362ad64d396f56e7a64441ac8f5d
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122180578"
---
# <a name="checklist-best-practices-for-sql-server-on-azure-vms"></a>清单：有关 Azure VM 上 SQL Server 的最佳做法
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文提供一个可用于快速查看有关 Azure 虚拟机 (VM) 上 SQL Server 性能优化的一系列最佳做法和指南的清单。 

如需了解更全面的详情，请参阅本系列中的其他文章：[清单](performance-guidelines-best-practices-checklist.md)、[VM 大小](performance-guidelines-best-practices-vm-size.md)、[存储](performance-guidelines-best-practices-storage.md)、[安全性](security-considerations-best-practices.md)、[HADR 配置](hadr-cluster-best-practices.md)和[收集基线](performance-guidelines-best-practices-collect-baseline.md)。 


## <a name="overview"></a>概述

在 Azure 虚拟机上运行 SQL Server 时，继续使用适用于本地服务器环境中的 SQL Server 的相同数据库性能优化选项。 但是，关系数据库在公有云中的性能取决于许多因素，如虚拟机的大小和数据磁盘的配置。

通常需要在针对成本优化和针对性能优化之间进行权衡。 这一系列性能最佳做法侧重于实现 Azure 虚拟机上 SQL Server 的最佳性能。 如果工作负荷要求较低，可能不需要每项建议的优化。 评估这些建议时应考虑性能需求、成本和工作负荷模式。

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
- 将数据磁盘格式化，为临时 `D:\` 驱动器（默认为 4 KB）以外的驱动器上放置的所有数据文件使用 64-KB 的分配单元大小。 通过 Azure 市场部署的 SQL Server VM 附带经过格式化的数据磁盘，其中分配单元大小和存储池的交错设置为 64 KB。 


若要了解详细信息，请参阅内容全面的[存储最佳做法](performance-guidelines-best-practices-storage.md)。 

## <a name="sql-server-features"></a>SQL Server 功能

下面是一个最佳做法快速清单，涵盖了在生产环境中，在 Azure 虚拟机上运行 SQL Server 实例的最佳 SQL Server 配置设置： 

- 适用时启用[数据库页压缩](/sql/relational-databases/data-compression/data-compression)。
- 启用[备份压缩](/sql/relational-databases/backup-restore/backup-compression-sql-server)。
- 对数据文件启用[即时文件初始化](/sql/relational-databases/databases/database-instant-file-initialization)。
- 限制数据库[自动增长](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-autogrow)。
- 禁用数据库[自动收缩](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-auto_shrink)。
- 禁用数据库自动关闭。
- 将所有数据库（包括[系统数据库](/sql/relational-databases/databases/move-system-databases)）转移到数据磁盘。
- 将 SQL Server 错误日志和跟踪文件目录移到数据磁盘。
- 配置默认的备份和数据库文件位置。
- 设置最高 [SQL Server 内存限制](/sql/database-engine/configure-windows/server-memory-server-configuration-options#use-)，为操作系统留出足够内存。 （[利用内存\可用字节](/sql/relational-databases/performance-monitor/monitor-memory-usage)来监视操作系统内存运行状况）。
- 启用[锁定内存页](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows)。
- 启用针对 OLTP 繁重环境的[临时工作负荷优化](/sql/database-engine/configure-windows/optimize-for-ad-hoc-workloads-server-configuration-option)。
- 评估并应用已安装的 SQL Server 版本的[最新累积更新](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server)。
- [遵循最佳做法](/sql/relational-databases/performance/best-practice-with-the-query-store)，为所有生产 SQL Server 数据库启用[查询存储](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)。
- 对任务关键型应用程序数据库启用[自动优化](/sql/relational-databases/automatic-tuning/automatic-tuning)。
- 确保遵循所有 [tempdb 最佳实践](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server)。
- 将 tempdb 置于 ephemeral D:/ 驱动器上。
- [使用建议的文件数](/troubleshoot/sql/performance/recommendations-reduce-allocation-contention#resolution)，使用多个 tempdb 数据文件，从每个核心一个文件开始，最多八个文件。
- 计划 SQL Server 代理作业以运行 [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql#a-checking-both-the-current-and-another-database)、[索引重新编制](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes#reorganize-an-index)、[索引重新生成](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes#rebuild-an-index)和[更新统计信息](/sql/t-sql/statements/update-statistics-transact-sql#examples)作业。
- 监视和管理 SQL Server [事务日志文件](/sql/relational-databases/logs/manage-the-size-of-the-transaction-log-file#Recommendations)的运行状况和大小。
- 利用可用于所使用版本的任何新 [SQL Server 功能](/sql/sql-server/what-s-new-in-sql-server-ver15)。
- 请注意考虑部署的各版本之间在[支持的功能](/sql/sql-server/editions-and-components-of-sql-server-version-15)方面的差异。

## <a name="azure-features"></a>进行映射

下面是有关在 Azure VM 上运行 SQL Server 时 Azure 特定指南的最佳做法的快速清单：

- 注册 [SQL IaaS 代理扩展](sql-agent-extension-manually-register-single-vm.md)，以获取多项[功能优势](sql-server-iaas-agent-extension-automate-management.md#feature-benefits)。
- 充分利用 SQL Server 工作负荷的最佳[备份和还原策略](backup-restore.md#decision-matrix)。
- 确保在虚拟机上[启用加速网络](../../../virtual-network/create-vm-accelerated-networking-cli.md#portal-creation)。
- 利用 [Azure 安全中心](../../../security-center/index.yml)改善虚拟机部署的总体安全状况。
- 利用 [Azure Defender](../../../security-center/azure-defender.md)（与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成）获取特定 [SQL Server VM 范围](../../../security-center/defender-for-sql-introduction.md)，包括漏洞评估和实时访问，这可减少攻击服务，同时允许合法用户在必要时访问虚拟机。 要了解详细信息，请参阅[漏洞评估](../../../security-center/defender-for-sql-on-machines-vulnerability-assessment.md)、[为 SQL Server VM](../../../security-center/defender-for-sql-on-machines-vulnerability-assessment.md) 和[实时访问](../../../security-center/just-in-time-explained.md)启用漏洞评估。 
- 利用 [Azure 顾问](../../../advisor/advisor-overview.md) 来解决[性能](../../../advisor/advisor-performance-recommendations.md)、[成本](../../../advisor/advisor-cost-recommendations.md)、[可靠性](../../../advisor/advisor-high-availability-recommendations.md)、[卓越运营](../../../advisor/advisor-operational-excellence-recommendations.md)和[安全建议](../../../advisor/advisor-security-recommendations.md)。
- 利用 [Azure Monitor](../../../azure-monitor/vm/monitor-virtual-machine.md) 从 SQL Server 环境收集、分析和处理遥测数据。 这包括识别 [VM 见解](../../../azure-monitor/vm/vminsights-overview.md)的基础结构问题并通过[日志分析](../../../azure-monitor/logs/log-query-overview.md)监视数据，以便进行更深入的诊断。
- 为开发和测试环境启用[自动关闭](../../../automation/automation-solution-vm-management.md)。 
- 实现可满足业务连续性 SLA 的高可用性和灾难恢复 (HADR) 解决方案，请参阅可用于 Azure VM 上 SQL Server 的 [HADR 选项](business-continuity-high-availability-disaster-recovery-hadr-overview.md#deployment-architectures)。 
- 使用 Microsoft Azure 门户（支持 + 故障排除）评估[资源运行状况](../../../service-health/resource-health-overview.md)和历史记录；在需要时提交新的支持请求。

## <a name="hadr-configuration"></a>HADR 配置

高可用性和灾难恢复 (HADR) 功能，如 [Always On 可用性组](availability-group-overview.md)和[故障转移群集实例](failover-cluster-instance-overview.md)依赖于基础的[Windows Server 故障转移群集](hadr-windows-server-failover-cluster-overview.md)技术。 查看修改 HADR 设置以更好地支持云环境的最佳做法。 

对于 Windows 群集，请考虑以下最佳做法： 

* 将群集更改为主动性较低的参数，以避免暂时性网络故障或 Azure 平台维护带来的意外中断。 要了解详细信息，请参阅[检测信号和阈值设置](hadr-cluster-best-practices.md#heartbeat-and-threshold)。 对于 Windows Server 2012 或更高版本，请使用以下建议值： 
   - **SameSubnetDelay**：1 秒
   - **SameSubnetThreshold**：40 个检测信号
   - **CrossSubnetDelay**：1 秒
   - **CrossSubnetThreshold**：40 个检测信号
* 将 VM 放置在可用性集或不同的可用性区域中。  要了解详细信息，请参阅 [VM 可用性设置](hadr-cluster-best-practices.md#vm-availability-settings)。 
* 每个群集节点使用一个 NIC，并使用一个子网。 
* 将群集[仲裁投票](hadr-cluster-best-practices.md#quorum-voting)配置为使用 3 个或更多奇数投票数。 不要将投票分配给 DR 区域。 
* 仔细监视[资源限制](hadr-cluster-best-practices.md#resource-limits)，避免因资源限制出现意外重启或故障转移。
   - 确保 OS、驱动程序和 SQL Server 都是最新版本。 
   - 针对 Azure VM 上的 SQL Server 优化性能。 查看本文中的其他部分了解详细信息。 
   - 减少或分散工作负荷，避免资源限制。 
   - 转到限制更高的 VM 或磁盘，避免受限。 

对于 SQL Server 可用性组或故障转移群集实例，请考虑以下最佳做法： 

* 如果经常出现意外失败，请遵循本文其余部分中概述的最佳性能做法。 
* 如果优化 SQL Server VM 性能无法解决意外的故障转移，请考虑放宽对可用性组或故障转移群集实例的[监视](hadr-cluster-best-practices.md#relaxed-monitoring)。 但这样做可能无法解决根本问题，同时可能会降低失败可能性而掩盖症状。 你可能仍需要调查并解决根本原因。 对于 Windows Server 2012 或更高版本，请使用以下建议值： 
   - **租用超时**：使用此公式计算最大租用超时值：   
    `Lease timeout < (2 * SameSubnetThreshold * SameSubnetDelay)`.    
    首先从 40 秒开始。 如果使用的是之前建议的宽松 `SameSubnetThreshold` 和 `SameSubnetDelay` 值，则租用超时值不超过 80 秒。    
   - **指定时间段内的最大失败数**：将此值设置为 6。 
* 使用虚拟网络名称 (VNN) 连接 HADR 解决方案时，即使群集只跨越一个子网，也请在连接字符串中指定 `MultiSubnetFailover = true`。 
   - 如果客户端不支持 `MultiSubnetFailover = True`，你可能需要设置 `RegisterAllProvidersIP = 0` 和 `HostRecordTTL = 300` 来缓存较短持续时间内的客户端凭据。 但这样做可能会导致对 DNS 服务器进行其他查询。 
- 要使用分布式网络名称 (DNN) 连接 HADR 解决方案，请考虑以下事项：
   - 必须使用支持 `MultiSubnetFailover = True` 的客户端驱动程序，而且此参数必须位于连接字符串中。 
   - 连接可用性组的 DNN 侦听器时，请在连接字符串中使用唯一的 DNN 端口。 
- 对基本可用性组使用数据库镜像连接字符串，免去负载均衡器或 DNN 需求。 
- 在部署高可用性解决方案之前验证 VHD 的扇区大小，避免出现未对齐的 I/O。 有关详细信息，请参阅 [KB3009974](https://support.microsoft.com/topic/kb3009974-fix-slow-synchronization-when-disks-have-different-sector-sizes-for-primary-and-secondary-replica-log-files-in-sql-server-ag-and-logshipping-environments-ed181bf3-ce80-b6d0-f268-34135711043c)。 


要了解详细信息，请参阅全面的 [HADR 最佳做法](hadr-cluster-best-practices.md)。 


## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅本系列中的其他文章：

- [VM 大小](performance-guidelines-best-practices-vm-size.md)
- [存储](performance-guidelines-best-practices-storage.md)
- [安全性](security-considerations-best-practices.md)
- [HADR 设置](hadr-cluster-best-practices.md)
- [收集基线](performance-guidelines-best-practices-collect-baseline.md)

有关安全最佳做法，请参阅 [Azure 虚拟机上 SQL Server 的安全注意事项](security-considerations-best-practices.md)。

查看 [Azure 虚拟机上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)中的其他 SQL Server 虚拟机文章。 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](frequently-asked-questions-faq.yml)。