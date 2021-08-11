---
title: Synapse SQL 资源消耗
description: 了解 Azure Synapse Analytics 中的 Synapse SQL 消耗模型。
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e31fd58887dfa179bc01f89ec7e56dc93fa3b6b8
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540233"
---
# <a name="synapse-sql-resource-consumption"></a>Synapse SQL 资源消耗

本文介绍了 Synapse SQL 的资源消耗模型。

## <a name="serverless-sql-pool"></a>无服务器 SQL 池

无服务器 SQL 池是一项按查询付费的服务，不要求选取合适的大小。 系统会根据你的要求自动进行调整，你无需管理基础结构，也不需为解决方案选取合适的大小。

## <a name="dedicated-sql-pool---data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>专用 SQL 池 - 数据仓库单位 (DWU) 和计算数据仓库单位 (cDWU)

提供的建议涉及如何选择理想数目的数据仓库单位 (DWU) 来优化价格和性能，以及如何更改单位数。

### <a name="data-warehouse-units"></a>数据仓库单位

Synapse SQL 池表示所预配的分析资源的集合。 分析资源定义为 CPU、内存和 IO 的组合。 这三项资源捆绑到称为数据仓库单位 (DWU) 的计算规模单位中。 DWU 表示抽象、规范化的计算资源和性能度量值。 更改服务级别会更改可用于系统的 DWU 数量。 此更改进而会调整系统的性能和成本。

若要提高性能，可以增加数据仓库单位数。 若要降低性能，可以减少数据仓库单位数。 存储和计算成本分别计费，因此更改数据仓库单位数不会影响存储成本。

数据仓库单位性能基于这些数据仓库负载指标：

- 标准数据仓库查询扫描大量行并执行复杂聚合的速度有多快。 这是一种 I/O 和 CPU 密集型操作。
- 数据仓库从 Azure 存储 Blob 或 Azure Data Lake 引入数据的速度有多快。 这是一种网络和 CPU 密集型操作。
- [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) T-SQL 命令复制表的速度。 此操作涉及从存储读取数据、将数据分配到设备的节点上，以及重新将数据写入到存储。 这是一种 CPU、IO 和网络密集型操作。

增加 DWU：

- 以线性方式更改系统对扫描、聚合和 CTAS 语句的性能
- 增加 PolyBase 加载操作的读取器和编写器数量
- 增加并发查询和并发槽的最大数量。

### <a name="service-level-objective"></a>服务级别目标

服务级别目标 (SLO) 是确定数据仓库的成本和性能级别的可伸缩性设置。 第 2 代服务级别以计算数据仓库单位 (cDWU) 计量，例如 DW2000c。 第 1 代服务级别以 DWU 计量，例如 DW2000。

服务级别目标 (SLO) 是确定数据仓库的成本和性能级别的可伸缩性设置。 第 2 代专用 SQL 池的服务级别是以数据仓库单位 (DWU) 计量的，例如 DW2000c。

> [!NOTE]
> Azure Synapse Analytics Gen2 最近添加了额外的缩放功能，以支持低至 100 cDWU 的计算层。 当前在 Gen1 上需要较低计算层的现有数据仓库现可升级到当前可用区域中的 Gen2，无需额外成本。  如果你的区域尚不支持，仍可升级到支持的区域。 有关详细信息，请参阅[升级到 Gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

在 T-SQL 中，SERVICE_OBJECTIVE 设置决定了专用 SQL 池的服务级别和性能层级。

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

### <a name="performance-tiers-and-data-warehouse-units"></a>性能层和数据仓库单位

每个性能层用于其数据仓库单位测量的单位都略有不同。 当规模单位直接转换为计费时，这种差异会反映在发票上。

- 第 1 代数据仓库以数据仓库单位计量 (DWU)。
- 第 2 代数据仓库以计算数据仓库单位 (cDWU) 计量。

DWU 和 cDWU 都支持增加或减少计算，以及在无需使用数据仓库时暂停计算。 这些操作均可按需进行。 第 2 代还会在计算节点上使用基于本地磁盘的缓存以提高性能。 缩放或暂停系统时，缓存将失效，因此在达到最佳性能前，缓存需要预热一段时间。

增加数据仓库单位时，将以线性方式增加计算资源。 第 2 代提供最佳查询性能和最大规模。 第 2 代系统还可以最大限度利用缓存。

#### <a name="capacity-limits"></a>容量限制

每个 SQL Server（例如 myserver.database.windows.net）都有一个允许指定数据仓库单位数的[数据库事务单位 (DTU)](../../azure-sql/database/service-tiers-dtu.md) 配额。 有关详细信息，请参阅[工作负荷管理容量限制](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management)。

### <a name="assess-the-number-of-data-warehouse-units-you-need"></a>评估所需的数据仓库单位数

合适的数据仓库单位数很大程度上取决于工作负荷及已加载到系统的数据量。

查找最适合工作负荷的 DWU 的步骤：

1. 首先选择一个较小的 DWU。
2. 在测试数据加载到系统中时，监视应用程序性能，将所选 DWU 数目与观测到的性能变化进行比较。
3. 确认峰值活动周期的其他要求。 在活动中表现出明显峰值和低谷的工作负载可能需要频繁地进行缩放。

SQL 池是一个横向扩展系统，可预配大量计算并查询大量数据。 要查看其真正的缩放功能（尤其是针对较大的 DWU），建议在缩放的同时对数据集进行缩放，确保可向 CPU 提供足够的数据。 对于规模测试，建议至少使用 1 TB。

> [!NOTE]
>
> 此外，如果可以计算节点之间拆分工作，与多个并行化只会增加查询性能。 如果发现该缩放未更改性能，建议优化表设计和/或查询。 有关查询优化指南，请参阅[管理用户查询](../overview-terminology.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)。

### <a name="permissions"></a>权限

更改数据仓库单位需要 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) 中所述的权限。

Azure 内置角色（如 SQL DB 参与者和 SQL Server 参与者）可以更改 DWU 设置。

#### <a name="view-current-dwu-settings"></a>查看当前的 DWU 设置

查看当前的 DWU 设置：

1. 在 Visual Studio 中打开“SQL Server 对象资源管理器”。
2. 连接到与逻辑 SQL 服务器关联的 master 数据库。
3. 从 sys.database_service_objectives 动态管理视图中选择。 以下是示例：

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

### <a name="change-data-warehouse-units"></a>更改数据仓库单位

#### <a name="azure-portal"></a>Azure 门户

若要更改 DWU，请执行以下操作：

1. 依次打开 [Azure 门户](https://portal.azure.com)和数据库，并选择“缩放”。

2. 在“缩放”下，向左或向右移动滑块，以更改 DWU 设置。

3. 选择“保存”。 此时会显示确认消息。 选择“是”以确认，或选择“否”以取消。

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要更改 DWU，请使用 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell cmdlet。 以下示例将托管在服务器 MyServer 上的数据库 MySQLDW 的服务级别目标设置为 DW1000。

```powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

有关详细信息，请参阅[适用于 Azure Synapse Analytics 的 PowerShell cmdlet](/powershell/module/az.synapse)

#### <a name="t-sql"></a>T-SQL

使用 T-SQL，可查看当前的 DWU 设置，更改设置，以及检查进度。

若要更改 DWU，请执行以下操作：

1. 连接到与服务器关联的 master 数据库。
2. 使用 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) TSQL 语句。 以下示例将数据库 MySQLDW 的服务级别目标设置为 DW1000c。

```sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

#### <a name="rest-apis"></a>REST API

若要更改 DWU，请使用[创建或更新数据库 REST API](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true)。 以下示例将托管在服务器 MyServer 上的数据库 MySQLDW 的服务级别目标设置为 DW1000c。 该服务器位于名为 ResourceGroup1 的 Azure 资源组中。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

有关更多 REST API 示例，请参阅[适用于 Azure Synapse Analytics 的 REST API](/rest/api/synapse/?view=azps-6.1.0&preserve-view=true)。

### <a name="check-status-of-dwu-changes"></a>检查 DWU 更改的状态

DWU 更改可能需要几分钟才能完成。 如果要自动缩放，请考虑实现逻辑以确保在已完成某些操作后才继续另一项操作。

检查通过不同终结点的数据库状态可允许你正确实现自动化。 门户会在完成操作时提供通知，并且会提供数据库当前状态，但不允许以编程方式检查状态。

不能使用 Azure 门户为横向扩展操作检查数据库状态。

检查 DWU 更改的状态：

1. 连接到与服务器关联的 master 数据库。
2. 提交以下查询以检查数据库状态。

```sql
SELECT    *
FROM      sys.databases
;
```

1. 提交以下查询以检查操作状态

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

此 DMV 返回针对专用 SQL 池的各种管理操作的相关信息，例如操作和操作状态（IN_PROGRESS 或 COMPLETED）。

### <a name="the-scaling-workflow"></a>缩放工作流

启动缩放操作时，系统首先终止所有打开的会话，回退所有打开的事务以确保状态一致。 对于缩放操作，缩放仅在此事务回退完成后才会发生。

- 对于增加操作，系统会分离所有计算节点，预配额外计算节点，然后重新附加到存储层。
- 对于减少操作，系统会分离所有计算节点，然后仅将所需节点重新附加到存储层。

## <a name="next-steps"></a>后续步骤

若要了解有关如何管理性能的详细信息，请参阅[用于工作负荷管理的资源类](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)和[内存和并发限制](../sql-data-warehouse/memory-concurrency-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
