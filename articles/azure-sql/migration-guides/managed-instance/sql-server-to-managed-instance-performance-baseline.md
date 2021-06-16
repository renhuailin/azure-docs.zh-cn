---
title: SQL Server 到 Azure SQL 托管实例：性能基线
description: 了解如何在将 SQL Server 数据库迁移到 Azure SQL 托管实例时创建和比较性能基线。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: ee51e3ee0fd9f0084e8dddf03a9e181b38bc2fb4
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110689398"
---
# <a name="migration-performance-sql-server-to--azure-sql-managed-instance-performance-baseline"></a>迁移性能：SQL Server 到 Azure SQL 托管实例性能基线
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

创建性能基线，对 SQL 托管实例上工作负载的性能和 SQL Server 上运行的原始工作负载的性能进行比较。 

## <a name="create-a-baseline"></a>创建基线

理想情况下，迁移后的性能应与之前相当或比之前更好，因此，请务必测量和记录源上的基线性能值，然后将其与目标环境进行比较。 性能基线是一组参数，用于定义源上的平均工作负载。 

选择一组对你的业务工作负载非常重要并代表业务工作负载的查询。 测量和记录这些查询的最小/平均/最大持续时间和 CPU 使用情况，以及源服务器上的性能指标，例如平均/最大 CPU 使用情况、平均/最大磁盘 IO 延迟、吞吐量、IOPS、平均/最大页生存期和 tempdb 的平均最大大小。 

以下资源可帮助定义性能基线： 

   - [监视 CPU 使用情况](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [监视内存使用情况](/sql/relational-databases/performance-monitor/monitor-memory-usage) 并确定不同组件（如缓冲池、计划缓存、列存储池、 [内存中 OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage) 等）使用的内存量。此外，还应查找页生存期内存性能计数器的平均值和峰值。 
   - 使用  [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)  视图或 [性能计数器](/sql/relational-databases/performance-monitor/monitor-disk-usage)监视源 SQL Server 实例上的磁盘 IO 使用情况。 
   - 检查动态管理视图（若要从 SQL Server 2016 及更高版本迁移，请检查查询存储），以监视工作负载和查询性能。 确定工作负载中最重要的查询的平均持续时间和 CPU 使用情况。 

应在迁移之前解决源 SQL Server 上的任何性能问题。 将已知问题带到任何新系统可能会导致意外的结果，并使得任何性能比较失效。 


## <a name="compare-performance"></a>比较性能 

定义基线后，请比较目标 SQL 托管实例上的类似工作负载性能。 为保证准确性，SQL 托管实例环境应尽可能与 SQL Server 环境相似，这一点非常重要。 

SQL 托管实例基础结构存在差异，因此不太可能实现性能匹配。 某些查询的运行速度可能比预期更快，而另一些查询的运行速度可能较慢。 比较的目的是验证托管实例中的工作负载性能是否与 SQL Server 上的性能匹配（平均值），并识别性能与原始性能不匹配的任何关键查询。 

性能比较可能会出现以下结果： 

- 托管实例上的工作负载性能与源 SQL Server 上的性能相当，或者优于后者。 如果存在这种情况，则表示迁移成功。 

- 工作负载中的大多数性能参数和查询按预期工作，但也存在一些性能下降的例外情况。 对于这种情况，需要识别差异及其重要性。 如果某些重要查询出现性能下降，请调查是基础 SQL 计划已经更改还是查询遇到了资源限制。 可以通过直接或使用计划指南来对关键查询（例如，更改兼容级别、旧基数估算器）应用一些提示来缓解这种情况。 确保统计信息和索引在两个环境中都是最新且等效的。 

- 与源 SQL Server 实例相比，托管实例上的大多数查询速度较慢。 对于这种情况，请尝试识别差异的根本原因，如 [即将达到资源限制](../../managed-instance/resource-limits.md#service-tier-characteristics)（如 IO、内存或实例日志速率限制）。 如果没有任何资源限制会导致出现差异，请尝试更改数据库的兼容性级别，或更改数据库设置（例如传统基数估算），然后重新运行测试。 查看托管实例或查询存储视图中提供的建议来识别使性能倒退的查询。 

SQL 托管实例具有内置的自动计划更正功能（默认已启用）。 此功能确保过去正常工作的查询在将来不会导致性能下降。 如果未启用此功能，请用旧设置运行工作负载，使 SQL 托管实例可以了解性能基线。 然后，启用此功能，并使用新设置重新运行工作负载。 

更改测试参数或升级到更高服务层级，以达到满足需求的工作负载性能的最佳配置。 

## <a name="monitor-performance"></a>监视性能 

SQL 托管实例提供用于监视和故障排除的高级工具，你应使用这些工具来监视实例的性能。 要监视的一些关键指标如下： 

- 实例上的 CPU 使用率：确定预配的 vCore 数目是否与工作负载适当匹配。 
- 托管实例上的页生存期：确定[是否需要增加内存](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444)。
-  标识存储 IO 问题的统计信息（例如 INSTANCE_LOG_GOVERNOR 或 PAGEIOLATCH），尤其是在“常规用途”层级上，可能需要在其中预先分配文件才能获得更好的 IO 性能。 


## <a name="considerations"></a>注意事项  

比较性能时，请注意以下事项： 

- 源和目标之间的设置是否匹配。 验证两个环境中的各个实例、数据库和 tempdb 设置是否等效。 配置、兼容级别、加密设置、跟踪标志等方面的差异都会使性能产生偏差。 

- 存储是根据[最佳做法](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)配置的。 例如，对于常规用途，你可能需要预先分配文件大小以提高性能。 

- 存在[关键环境差异](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)，可能导致托管实例与 SQL Server 之间出现性能差异。 确定与环境有关的可能导致性能问题的风险。 

- 应在 SQL 托管实例上启用查询存储和自动优化，因为它们有助于衡量工作负载性能并自动缓解潜在的性能问题。 



## <a name="next-steps"></a>后续步骤

如需详细了解如何优化新的 Azure SQL 托管实例环境，请参阅以下资源： 

- [如何确定 Azure SQL 托管实例上的工作负载性能与 SQL Server 不同的原因？](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [SQL 托管实例与 SQL Server 之间存在性能差异的主要原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Azure SQL 托管实例（常规用途）的存储性能最佳做法和注意事项](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Azure SQL 托管实例的实时性能监视（已存档，这是预期的目标吗？）](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)