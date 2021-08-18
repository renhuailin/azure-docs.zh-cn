---
title: 通过 SQL 见解监视 SQL 部署（预览版）
description: Azure Monitor 中的 SQL 见解概述
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: e69520f53cea9c6cd6eb662f4cc1acce2cab00f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752265"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>通过 SQL 见解监视 SQL 部署（预览版）
SQL 见解是一个综合性的解决方案，可用于监视 [Azure SQL 系列](../../azure-sql/index.yml)中的任何产品。 SQL 见解使用[动态管理视图](../../azure-sql/database/monitoring-with-dmvs.md)来公开你监视运行状况、诊断问题和优化性能时所需的数据。  

SQL 见解远程执行所有监视功能。 专用虚拟机上的监视代理连接到 SQL 资源并远程收集数据。 收集的数据存储在 [Azure Monitor 日志](../logs/data-platform-logs.md)中，因而可以轻松地进行聚合、筛选和趋势分析。 可以从 SQL 见解[工作簿模板](../visualize/workbooks-overview.md)查看收集的数据，也可以使用[日志查询](../logs/get-started-queries.md)直接深入到数据。

## <a name="pricing"></a>定价
SQL 见解不直接产生成本。 所有成本是收集数据的虚拟机、存储数据的 Log Analytics 工作区以及针对数据配置的任何警报规则产生的。 

**虚拟机**

对于虚拟机，你将按照[虚拟机定价页](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)上发布的定价付费。 所需的虚拟机数因你要监视的连接字符串数而异。 建议为每 100 个连接字符串分配 1 个 Standard_B2s 大小的虚拟机。 有关更多详细信息，请参阅 [Azure 虚拟机要求](sql-insights-enable.md#azure-virtual-machine-requirements)。

**Log Analytics 工作区**

对于 Log Analytics 工作区，你将按照 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)上发布的定价付费。 SQL 见解使用的 Log Analytics 工作区会产生数据引入成本、数据保留成本和（可选）数据导出成本。 确切的费用因引入、保留和导出的数据量而异。 而此数据量随之又因你的数据库活动以及在[监视配置文件](sql-insights-enable.md#create-sql-monitoring-profile)中定义的收集设置而异。

**警报规则**

对于 Azure Monitor 中的警报规则，你将按照 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)上发布的定价付费。 如果你选择[使用 SQL 见解创建警报](sql-insights-alerts.md)，则需要为创建的任何警报规则以及发送的任何通知付费。

## <a name="supported-versions"></a>支持的版本 
SQL 见解支持以下版本的 SQL Server：
- SQL Server 2012 及更高版本

SQL 见解支持在以下环境中运行的 SQL Server：
- Azure SQL Database
- Azure SQL 托管实例
- Azure 虚拟机上的 SQL Server（在已注册到 [SQL 虚拟机](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)提供程序的虚拟机上运行的 SQL Server）
- Azure VM（在未注册到 [SQL 虚拟机](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)提供程序的虚拟机上运行的 SQL Server）

SQL 见解不支持以下项或仅对其提供有限支持：
- 非 Azure 实例：不支持在 Azure 外部的虚拟机上运行的 SQL Server
- Azure SQL 数据库弹性池：无法收集弹性池的指标。 无法收集弹性池中数据库的指标。
- Azure SQL 数据库低服务层级：无法收集基本、S0、S1 和 S2 [服务层级](../../azure-sql/database/resource-limits-dtu-single-databases.md)上的数据库的指标
- Azure SQL 数据库无服务器层级：可以收集使用无服务器计算层级的数据库的指标。 但是，指标收集过程会重置自动暂停延迟计时器，导致数据库无法进入“已自动暂停”状态
- 次要副本：只能收集每个数据库的单个次要副本的指标。 如果数据库有多个次要副本，只能监视 1 个副本。
- 使用 Azure Active Directory 进行身份验证：唯一对监视功能支持的[身份验证](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization)方法是 SQL 身份验证。 对于 Azure VM 上的 SQL Server，不支持在自定义域控制器上使用 Active Directory 进行身份验证。  

## <a name="open-sql-insights"></a>打开 SQL 见解
打开 SQL 见解的方式是：通过 Azure 门户中“Azure Monitor”菜单的“见解”部分选择“SQL (预览版)”。   单击某个磁贴以加载要监视的 SQL 类型的体验。

:::image type="content" source="media/sql-insights/portal.png" alt-text="Azure 门户中的 SQL 见解。":::

## <a name="enable-sql-insights"></a>启用 SQL 见解 
有关启用 SQL 见解的说明，请参阅[启用 SQL 见解](sql-insights-enable.md)。

## <a name="troubleshoot-sql-insights"></a>排查 SQL 见解问题 
有关排查 SQL 见解问题的说明，请参阅[排查 SQL 见解问题](sql-insights-troubleshoot.md)。

## <a name="data-collected-by-sql-insights"></a>SQL 见解收集的数据
SQL 见解远程执行所有监视功能。 我们不会在运行 SQL Server 的虚拟机上安装任何代理。 

SQL 见解使用专用监视虚拟机从 SQL 资源远程收集数据。 将在每个监视虚拟机上安装 [Azure Monitor 代理](../agents/azure-monitor-agent-overview.md)和工作负载见解 (WLI) 扩展。 WLI 扩展包含开源的 [Telegraf 代理](https://www.influxdata.com/time-series-platform/telegraf/)。 SQL 见解使用[数据收集规则](../agents/data-collection-rule-overview.md)为 Telegraf 的 [SQL Server 插件](https://www.influxdata.com/integration/microsoft-sql-server/)指定数据收集设置。

可对 Azure SQL 数据库、Azure SQL 托管实例和 SQL Server 使用不同的数据集。 下表描述了可用的数据。 [创建监视配置文件](sql-insights-enable.md#create-sql-monitoring-profile)时，可以自定义要收集的数据集和收集频率。

下表包含以下列：
- 易记名称：创建监视配置文件时显示在 Azure 门户上的查询名称
- 配置名称：编辑监视配置文件时显示在 Azure 门户上的查询名称
- 命名空间：Log Analytics 工作区中显示的查询名称。 此标识符显示在 InsighstMetrics 表的 `Tags` 列中的 `Namespace` 属性上
- DMV：用于生成数据集的动态托管视图
- 默认已启用：默认情况下是否收集数据
- 默认收集频率：默认情况下收集数据的频率

### <a name="data-for-azure-sql-database"></a>Azure SQL 数据库的数据 
| 友好名称 | 配置名称 | 命名空间 | DMV | 默认情况下启用 | 默认收集频率 |
|:---|:---|:---|:---|:---|:---|
| DB 等待统计信息 | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | 否 | NA |
| DBO 等待统计信息 | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | 是 | 60 秒 |
| 内存分配器 | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | 是 | 60 秒 |
| 数据库 IO | AzureSQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys.database_files | 是 | 60 秒 |
| 服务器属性 | AzureSQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>sys.[databases]<br>sys.[database_service_objectives] | 是 | 60 秒 |
| 性能计数器 | AzureSQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | 是 | 60 秒 |
| 资源统计信息 | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | 是 | 60 秒 |
| 资源调控 | AzureSQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | 是 | 60 秒 |
| 请求 | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | 否 | NA |
| 计划程序| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | 否 | NA  |

### <a name="data-for-azure-sql-managed-instance"></a>Azure SQL 托管实例的数据 

| 友好名称 | 配置名称 | 命名空间 | DMV | 默认情况下启用 | 默认收集频率 |
|:---|:---|:---|:---|:---|:---|
| 等待统计信息 | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | 是 | 60 秒 |
| 内存分配器 | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | 是 | 60 秒 |
| 数据库 IO | AzureSQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | 是 | 60 秒 |
| 服务器属性 | AzureSQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | 是 | 60 秒 |
| 性能计数器 | AzureSQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| 是 | 60 秒 |
| 资源统计信息 | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | 是 | 60 秒 |
| 资源调控 | AzureSQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | 是 | 60 秒 |
| 请求 | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | 否 | NA |
| 计划程序 | AzureSQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | 否 | NA |

### <a name="data-for-sql-server"></a>SQL Server 的数据

| 友好名称 | 配置名称 | 命名空间 | DMV | 默认情况下启用 | 默认收集频率 |
|:---|:---|:---|:---|:---|:---|
| 等待统计信息 | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | 是 | 60 秒 | 
| 内存分配器 | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | 是 | 60 秒 |
| 数据库 IO | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | 是 | 60 秒 |
| 服务器属性 | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | 是 | 60 秒 |
| 性能计数器 | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | 是 | 60 秒 |
| 卷空间 | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | 是 | 60 秒 |
| SQL Server CPU | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | 是 | 60 秒 |
| 计划程序 | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | 否 | NA |
| 请求 | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | 否 | NA |
| 可用性副本状态 | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | 否 | 60 秒 |
| 可用性数据库副本 | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | 否 | 60 秒 |

## <a name="next-steps"></a>后续步骤

- 有关启用 SQL 见解的说明，请参阅[启用 SQL 见解](sql-insights-enable.md)
- 有关 SQL 见解的常见问题解答，请参阅[常见问题解答](/azure/azure-monitor/faq#sql-insights-preview)