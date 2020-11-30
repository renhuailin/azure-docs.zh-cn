---
title: SQL Server SQL 托管实例-性能分析
description: 了解如何在将 SQL Server 数据库迁移到 Azure SQL 托管实例时创建和比较性能基线。
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: c47e4c1278f222feac35a2c6ab0b067c916c0217
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326839"
---
# <a name="migration-performance-sql-server-to-sql-managed-instance-performance-analysis"></a>迁移性能： SQL Server 到 SQL 托管实例性能分析
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

创建性能基线，将 SQL 托管实例上工作负荷的性能与 SQL Server 上运行的原始工作负荷进行比较。 

## <a name="create-a-baseline"></a>创建基线

理想情况下，在迁移后性能类似或更好，因此在源上测量和记录基线性能值并将其与目标环境进行比较很重要。 性能基线是一组参数，用于定义源的平均工作负荷。 

选择一组对你的业务工作负荷非常重要的查询。 度量和记录这些查询的最小/平均/最大持续时间和 CPU 使用率，以及源服务器上的性能指标，例如平均/最大 CPU 使用率、平均/最大磁盘 IO 延迟、吞吐量、IOPS、平均/最大页生存期和 tempdb 的平均最大大小。 

以下资源可帮助定义性能基线： 

   - [监视 CPU 使用情况 ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [监视内存使用情况](/sql/relational-databases/performance-monitor/monitor-memory-usage)  和确定不同组件（例如缓冲池、计划缓存、列存储池、 [内存中 OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)等）使用的内存量。此外，应查找 "页生存期" 的 "生存期" 和 "最大值" 页。 
   - 使用 [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)   视图或 [性能计数器](/sql/relational-databases/performance-monitor/monitor-disk-usage)监视源 SQL SERVER 实例上的磁盘 IO 使用情况。 
   - 监视工作负荷和查询性能，方法是：检查动态管理视图 (或查询存储如果要从 SQL Server 2016 和更高版本) 进行迁移。 确定工作负荷中最重要的查询的平均持续时间和 CPU 使用率。 

应在迁移之前解决源 SQL Server 上的任何性能问题。 将已知问题带到任何新系统可能会导致意外的结果，并使得任何性能比较失效。 


## <a name="compare-performance"></a>比较性能 

定义基线后，请比较目标 SQL 托管实例上的类似工作负荷性能。 为保证准确性，SQL 托管实例环境尽可能与 SQL Server 环境非常相似，这一点非常重要。 

SQL 托管实例基础结构差异使得匹配的性能完全不可能。 某些查询的运行速度可能比预期更快，而另一些查询的运行速度可能较慢。 此比较的目标是验证托管实例中的工作负荷性能是否与平均) 上的 SQL Server (的性能匹配，并识别性能不符合原始性能的任何关键查询。 

性能比较可能会导致以下结果： 

- 托管实例上的工作负荷性能与源 SQL Server 上的工作负荷性能对齐或更佳。 如果存在这种情况，则表示迁移成功。 

- 工作负荷中的大多数性能参数和查询会按预期执行，但会出现一些例外，导致性能下降。 在这种情况下，请确定差异及其重要性。 如果有一些重要的查询的性能下降，请调查基础 SQL 计划是否已更改，或者是否命中了资源限制。 您可以通过对关键 (查询应用一些提示来缓解此问题，例如，更改兼容级别估计器、旧基数) 直接或使用计划指南。 确保统计信息和索引在两个环境中都是最新的和等效的。 

- 与源 SQL Server 实例相比，托管实例上的大多数查询速度较慢。 在这种情况下，请尝试确定差异的根本原因，如 [达到某些资源限制](../../managed-instance/resource-limits.md#service-tier-characteristics) ，例如 IO、内存或实例日志速率限制。 如果没有导致差异的资源限制，请尝试更改数据库的兼容级别，或者更改数据库设置（如旧基数估计），然后重新运行测试。 查看托管实例或查询存储视图提供的建议，以识别具有回归性能的查询。 

SQL 托管实例具有内置的自动计划更正功能，默认情况下已启用。 此功能可确保在过去正常运行的查询在将来不会降级。 如果未启用此功能，请用旧设置运行工作负荷，以便 SQL 托管实例可以了解性能基线。 然后，启用此功能，并使用新设置重新运行工作负荷。 

在测试的参数中进行更改或升级到更高的服务层，以达到满足你的需求的工作负荷性能的最佳配置。 

## <a name="monitor-performance"></a>监视性能 

SQL 托管实例提供了用于监视和故障排除的高级工具，你应该使用它们来监视你的实例的性能。 要监视的一些关键指标如下： 

- 实例上的 CPU 使用率：确定预配的 vCore 数目是否与工作负载适当匹配。 
- 托管实例上的页生存期，确定是否需要 [额外的内存](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444)。
-  标识存储 IO 问题的 INSTANCE_LOG_GOVERNOR 或 PAGEIOLATCH 等统计信息，尤其是在常规用途层上，你可能需要预先分配文件才能获得更好的 IO 性能。 


## <a name="considerations"></a>注意事项  

比较性能时，请考虑以下事项： 

- 源和目标之间的设置匹配。 验证各个实例、数据库和 tempdb 设置在两个环境中是否等效。 配置、兼容级别、加密设置、跟踪标志等之间的差异可能会所有偏差性能。 

- 存储根据 [最佳做法](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)进行配置。 例如，对于常规用途，你可能需要预先分配文件大小以提高性能。 

- 存在一些 [关键的环境差异](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) ，它们可能会导致托管实例和 SQL Server 之间的性能差异。 确定与你的环境相关的风险，这可能会导致性能问题。 

- 应在 SQL 托管实例上启用查询存储和自动优化，因为它们可帮助您衡量工作负荷性能并自动降低潜在的性能问题。 



## <a name="next-steps"></a>后续步骤

有关优化新的 Azure SQL 托管实例环境的详细信息，请参阅以下资源： 

- [如何确定 Azure SQL 托管实例上的工作负载性能与 SQL Server 不同？](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [SQL 托管实例和 SQL Server 之间的性能差异的主要原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Azure SQL 托管实例 (常规用途的存储性能最佳实践和注意事项) ](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Azure SQL 托管实例的实时性能监视 (此项已存档，这是否为预期目标？ ) ](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)