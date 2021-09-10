---
title: 监视 Azure 上的 SAP 数据参考
description: 监视 Azure 上的 SAP 时所需的重要参考资料。
author: Ajayan1008
ms.topic: reference
ms.author: v-hborys
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.custom: subject-monitoring
ms.date: 08/27/2021
ms.openlocfilehash: b2e5fe3b8a6ce5afec5489bfa6c95e34641455c6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101809"
---
# <a name="monitor-sap-on-azure-preview-data-reference"></a>监视 Azure 上的 SAP（预览）数据参考

本文提供了对所收集日志数据的参考，用于分析 Azure Monitor for SAP Solutions 的性能和可用性。 有关收集和分析 Azure 上的 SAP 监视数据的详细信息，请参阅[监视 Azure 上的 SAP（预览）](monitor-sap-on-azure.md)。

## <a name="metrics"></a>指标

Azure Monitor for SAP Solutions 不支持指标。

## <a name="azure-monitor-logs-tables"></a>Azure Monitor 日志表

本节介绍与 Azure Monitor for SAP Solutions 相关并可通过 Log Analytics 进行查询的所有 Azure Monitor 日志 Kusto 表。 Azure Monitor for SAP Solutions 使用自定义日志。 某些表的架构由第三方提供商（如 SAP）定义。 下面是 Azure Monitor for SAP Solutions 的当前自定义日志以及指向源的链接以获取详细信息。

### <a name="saphana_hostconfig_cl"></a>SapHana_HostConfig_CL

有关详细信息，请参阅 SAP HANA SQL 和系统视图参考中的 [M_LANDSCAPE_HOST_CONFIGURATION 系统视图](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.00/en-US/20b1d83875191014b028e076c874e9d7.html)。

### <a name="saphana_hostinformation_cl"></a>SapHana_HostInformation_CL

有关详细信息，请参阅 SAP HANA SQL 和系统视图参考中的 [M_HOST_INFORMATION 系统视图](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20b10028751910148c1c9de602d771de.html)。

### <a name="saphana_systemoverview_cl"></a>SapHana_SystemOverview_CL

有关详细信息，请参阅 SAP HANA SQL 和系统视图参考中的 [M_SYSTEM_OVERVIEW 系统视图](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20c61f0675191014a1d5f96d9668855b.html)。

### <a name="saphana_loadhistory_cl"></a>SapHana_LoadHistory_CL

有关详细信息，请参阅 SAP HANA SQL 和系统视图参考中的 [M_LOAD_HISTORY_HOST 系统视图](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/3fa52abf1d854edbb7342a69364bcb0e.html)。

### <a name="saphana_disks_cl"></a>SapHana_Disks_CL

有关详细信息，请参阅 SAP HANA SQL 和系统视图参考中的 [M_DISKS 系统视图](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20aef7a275191014b37acbc35b4f20a4.html)。

### <a name="saphana_systemavailability_cl"></a>SapHana_SystemAvailability_CL

有关详细信息，请参阅 SAP HANA SQL 和系统视图参考中的 [M_SYSTEM_AVAILABILITY 系统视图](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.00/en-US/1ef9723a03214bd889c4fb8947765aa4.html)。

### <a name="saphana_backupcatalog_cl"></a>SapHana_BackupCatalog_CL

有关详细信息，请参阅：
- [M_BACKUP_CATALOG_FILES 系统视图](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20a8437d7519101495a3fa7ad9961cf6.html?q=M_BACKUP_CATALOG)
- [M_BACKUP_CATALOG 系统视图](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/1.0.12/en-US/20a8100e75191014870ecf908b5d2abf.html)

### <a name="saphana_systemreplication_cl"></a>SapHana_SystemReplication_CL

有关详细信息，请参阅 SAP HANA SQL 和系统视图参考中的 [M_SERVICE_REPLICATION 系统视图](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/2021_2_QRC/en-US/20c43fc975191014b0ece11b47a86c10.html)。
 
### <a name="prometheus_osexporter_cl"></a>Prometheus_OSExporter_CL

有关详细信息，请参阅 [GitHub 上的 prometheus/node_exporter](https://github.com/prometheus/node_exporter)。

### <a name="prometheus_haclusterexporter_cl"></a>Prometheus_HaClusterExporter_CL

有关详细信息，请参阅 [ClusterLabs/ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)。

### <a name="mssql_dbconnections_cl"></a>MSSQL_DBConnections_CL

有关详细信息，请参阅：
- [sys.dm_exec_sessions (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) 
- [sys.databases (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql)

### <a name="mssql_systemprops_cl"></a>MSSQL_SystemProps_CL

有关详细信息，请参阅： 
- [sys.dm_os_windows_info (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-windows-info-transact-sql) 
- [sys.database_files (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)
- [sys.dm_exec_sql_text (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)
- [sys.dm_exec_query_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)
- [sys.dm_io_virtual_file_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)
- [sys.dm_db_partition_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql)
- [sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)
- [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)
- [与 SQL Server 操作系统相关的动态管理视图 (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql)
- [sys.availability_groups (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-availability-groups-transact-sql)
- [sys.dm_exec_requests (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)
- [sys.dm_xe_session_targets (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-xe-session-targets-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)
- [backupset (Transact-SQL)](/sql/relational-databases/system-tables/backupset-transact-sql)
- [sys.sysprocesses (Transact-SQL)](/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql)

### <a name="mssql_fileoverview_cl"></a>MSSQL_FileOverview_CL

有关详细信息，请参阅 [sys.database_files (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)。

### <a name="mssql_memoryoverview_cl"></a>MSSQL_MemoryOverview_CL

有关详细信息，请参阅 [sys.dm_os_memory_clerks (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql)。

### <a name="mssql_top10statements_cl"></a>MSSQL_Top10Statements_CL

有关详细信息，请参阅：
- [sys.dm_exec_sql_text (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)
- [sys.dm_exec_query_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)

### <a name="mssql_ioperformance_cl"></a>MSSQL_IOPerformance_CL

有关详细信息，请参阅 [sys.dm_io_virtual_file_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)。

### <a name="mssql_tablesizes_cl"></a>MSSQL_TableSizes_CL

有关详细信息，请参阅 [sys.dm_db_partition_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql)。

### <a name="mssql_batchrequests_cl"></a>MSSQL_BatchRequests_CL

有关详细信息，请参阅 [sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)。

### <a name="mssql_waitpercs_cl"></a>MSSQL_WaitPercs_CL

有关详细信息，请参阅 [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)。

### <a name="mssql_pagelifeexpectancy2_cl"></a>MSSQL_PageLifeExpectancy2_CL

有关详细信息，请参阅 [sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)。

### <a name="mssql_error_cl"></a>MSSQL_Error_CL

有关详细信息，请参阅 [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)。

### <a name="mssql_cpuusage_cl"></a>MSSQL_CPUUsage_CL

有关详细信息，请参阅[与 SQL Server 操作系统相关的动态管理视图 (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql)。

### <a name="mssql_aooverview_cl"></a>MSSQL_AOOverview_CL

有关详细信息，请参阅 [sys.availability_groups (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-availability-groups-transact-sql)。

### <a name="mssql_aowaiter_cl"></a>MSSQL_AOWaiter_CL

有关详细信息，请参阅 [sys.dm_exec_requests (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)。

### <a name="mssql_aowaitstats_cl"></a>MSSQL_AOWaitstats_CL

有关详细信息，请参阅 [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)。

### <a name="mssql_aofailovers_cl"></a>MSSQL_AOFailovers_CL

有关详细信息，请参阅：
- [sys.dm_xe_session_targets (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-xe-session-targets-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)

### <a name="mssql_bckbackups2_cl"></a>MSSQL_BckBackups2_CL

有关详细信息，请参阅 [backupset (Transact-SQL)](/sql/relational-databases/system-tables/backupset-transact-sql)。

### <a name="mssql_blockingprocesses_cl"></a>MSSQL_BlockingProcesses_CL

有关详细信息，请参阅 [sys.sysprocesses (Transact-SQL)](/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql)。

## <a name="next-steps"></a>后续步骤

- 有关使用 Azure Monitor for SAP Solutions 的详细信息，请参阅[监视 Azure 上的 SAP](monitor-sap-on-azure.md)。
- 有关 Azure Monitor 的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../../../azure-monitor/essentials/monitor-azure-resource.md)。
