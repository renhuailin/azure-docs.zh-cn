---
title: 配置最大并行度 (MAXDOP)
titleSuffix: Azure SQL Database
description: 详细了解最大并行度 (MAXDOP)。
ms.date: 04/12/2021
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: tsql
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.openlocfilehash: 774114a27e5bcb23bc3cdddc08f5d42b3c43bb36
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108132028"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>在 Azure SQL 数据库中配置最大并行度 (MAXDOP)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  本文介绍了 Azure SQL 数据库中的最大并行度 (MAXDOP) 配置设置。 

> [!NOTE]
> 此内容重点介绍了 Azure SQL 数据库。 Azure SQL 数据库基于 Microsoft SQL Server 数据库引擎的最新稳定版本，所以许多内容是相似的，除了故障排除和配置选项有所不同。 要详细了解 SQL Server 中的 MAXDOP，请参阅[配置最大并行度服务器配置选项](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)。

## <a name="overview"></a>概述
  MAXDOP 控制数据库引擎中的查询内并行度。 MAXDOP 值较高通常会使每个查询的并行线程数更多，查询执行速度更快。 

  在 Azure SQL 数据库中，对于每个新的单一数据库和弹性池数据库，默认的 MAXDOP 设置均为 8。 此默认设置可防止利用不必要的资源，同时仍允许数据库引擎使用并行线程更快地执行查询。 通常无需在 Azure SQL 数据库工作负载中进一步配置 MAXDOP，但将其作为高级性能优化选项使用或许很有用处。

> [!Note]
>   2020 年 9 月，基于 Azure SQL 数据库服务多年的遥测数据，MAXDOP 8 被选为[新数据库的默认值](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528)，作为适合各种客户工作负载的最佳值。 此默认值有助于防止并行度过高导致的性能问题。 在此之前，新数据库的默认设置为 MAXDOP 0。 对于在 2020 年 9 月之前创建的现有数据库，未自动更改 MAXDOP。

  通常情况下，如果数据库引擎选择并行执行查询，则执行时间将更短。 但并行度过高可能会消耗额外的处理器资源，且不会提高查询性能。 从规模上看，并行度过高可能会对同一数据库引擎实例上执行的所有查询的查询性能产生负面影响。 传统上，在 SQL Server 工作负载中，设置并行度上限是一项常见的性能优化操作。

  下表描述了在执行具有不同 MAXDOP 值的查询时，数据库引擎的行为：

| MAXDOP | 行为 | 
|--|--|
| = 1 | 数据库引擎使用单个串行线程来执行查询。 不使用并行线程。 | 
| > 1 | 数据库引擎将并行线程使用的附加[计划程序](/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling)数设置为 MAXDOP 值或逻辑处理器总数，以较小者为准。 |
| = 0 | 数据库引擎将并行线程使用的附加[计划程序](/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling)数设置为逻辑处理器总数或 64，以较小者为准。 | 
| | |

> [!Note]
> 每个查询使用至少一个计划程序和该计划程序上的一个工作线程执行。
>
> 以并行方式执行的查询使用附加计划程序和附加并行线程。 由于多个并行线程可能在同一计划程序上执行，因此用于执行查询的总线程数可能高于指定的 MAXDOP 值或逻辑处理器总数。 有关详细信息，请参阅[计划并行任务](/sql/relational-databases/thread-and-task-architecture-guide#scheduling-parallel-tasks)。

##  <a name="considerations"></a><a name="Considerations"></a> 注意事项  

-   在 Azure SQL 数据库中，可以更改默认的 MAXDOP 值：
    -   在查询级别，使用 MAXDOP [查询提示](/sql/t-sql/queries/hints-transact-sql-query)。     
    -   在数据库级别，请使用 MAXDOP [数据库范围的配置](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)。

-   有一些长期适用于 Azure SQL 数据库的 SQL Server MAXDOP 注意事项和[建议](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines)。 

-   索引操作（如创建或重新生成索引、或删除聚集索引）可能会大量占用资源。 可以在 `CREATE INDEX` 或 `ALTER INDEX` 语句中指定 MAXDOP 索引选项，以替代数据库索引操作的 MAXDOP 值。 MAXDOP 值在执行时应用于语句，但不存储在索引元数据中。 有关详细信息，请参阅 [配置并行索引操作](/sql/relational-databases/indexes/configure-parallel-index-operations)。  
  
-   除了查询和索引操作之外，数据库范围的 MAXDOP 配置选项还可控制可能使用并行执行的其他语句（如 DBCC CHECKTABLE、DBCC CHECKDB 和 DBCC CHECKFILEGROUP）的并行度。 

##  <a name="recommendations"></a><a name="Recommendations"></a> 建议  

  更改数据库的 MAXDOP 会对查询性能和资源利用率产生重大影响，正面和负面的影响都有。 但并没有一个适合所有工作负载的最佳 MAXDOP 值。 MAXDOP 的设置[建议](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines)是比较精细化的，具体取决于诸多因素。 

  某些高峰并发工作负载使用异于其他工作负载的 MAXDOP 可能会运行地更好。 正确配置的 MAXDOP 通常能够降低性能和可用性事件的风险，在某些情况下还能避免利用不必要的资源，从而降低成本，进而简化服务目标的实现。

### <a name="excessive-parallelism"></a>并行度过高

  较高的 MAXDOP 通常可缩短 CPU 密集型查询的持续时间。 但并行度过高会使其他查询得不到 CPU 和工作线程资源，从而使其他并发工作负载性能降低。 在极端情况下，并行度过高可能会占用所有数据库或弹性池资源，导致查询超时、错误和应用程序中断。 

> [!Tip]
> 建议客户避免将 MAXDOP 设置为 0，即使它目前看起来不会导致问题。

  当并发请求数超过服务目标提供的 CPU 和工作线程资源可支持的并发请求数时，并发度过高最容易导致问题。 避免使用 MAXDOP 0 可降低由于数据库扩展或 Azure SQL 数据库中硬件的未来代系为同一数据库服务对象提供更多核心导致并行度过高进而导致出现更多潜在问题的风险。

### <a name="modifying-maxdop"></a>修改 MAXDOP 

  如果确定不同于默认设置的 MAXDOP 设置是 Azure SQL 数据库工作负载的最佳设置，则可以使用 `ALTER DATABASE SCOPED CONFIGURATION` T-SQL 语句。 如需查看示例，请参阅下面的 [Transact-SQL 使用示例](#examples)部分。 若要将所创建的每个新数据库的 MAXDOP 更改为非默认值，请将此步骤添加到数据库部署过程。

  如果非默认 MAXDOP 只对工作负载中的一小部分查询有利，则可以通过添加 OPTION (MAXDOP) 提示在查询级别替代 MAXDOP。 如需查看示例，请参阅下面的 [Transact-SQL 使用示例](#examples)部分。 

  利用涉及实际并发查询负载的负载测试，全面测试 MAXDOP 配置更改。 

  如果其他 MAXDOP 设置是读写和只读工作负载的最佳设置，则主要副本和次要副本的 MAXDOP 可以单独进行配置。 这适用于 Azure SQL 数据库[读取扩展](read-scale-out.md)、[异地复制](active-geo-replication-overview.md)和[超大规模](service-tier-hyperscale.md)次要副本。 默认情况下，所有次要副本都继承主副本的 MAXDOP 配置。

## <a name="security"></a><a name="Security"></a> Security  
  
###  <a name="permissions"></a><a name="Permissions"></a> 权限  
  必须以服务器管理员、具有数据库角色 `ALTER DATABASE SCOPED CONFIGURATION` 的成员或已获得 `ALTER ANY DATABASE SCOPED CONFIGURATION` 权限的用户的身份执行 `db_owner` 语句。
 
## <a name="examples"></a>示例   

  如果为 Azure SQL 数据库新的单一数据库选择了 `SAMPLE` 选项，则这些示例将使用最新的 AdventureWorksLT 示例数据库。

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP 数据库范围的配置   

  此示例演示如何使用 [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) 语句将 `MAXDOP` 配置设置为 `2`。 此设置对新查询立即生效。 PowerShell cmdlet [Invoke-SqlCmd](/powershell/module/sqlserver/invoke-sqlcmd) 执行 T-SQL 查询以设置并返回 MAXDOP 数据库范围的配置。 

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP = 8

$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
     SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

此示例适用于[已启用读取扩展副本](read-scale-out.md)、[异地复制](active-geo-replication-overview.md)和[超大规模次要副本](service-tier-hyperscale.md)的 Azure SQL 数据库。 例如，考虑到读写和只读工作负载可能存在差异，将主副本设置为与次要副本不同的默认 MAXDOP。

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP_primary = 8
$desiredMAXDOP_secondary_readonly = 1
 
$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
    ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = ' + $desiredMAXDOP_secondary_readonly + ';
    SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

### <a name="transact-sql"></a>Transact-SQL
  
  可以使用 [Azure 门户查询编辑器](connect-query-portal.md)、[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) 或 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) 对 Azure SQL 数据库执行 T-SQL 查询。

1.  打开一个新的查询窗口。

2.  连接到要更改 MAXDOP 的数据库。 你无法更改 master 数据库中数据库范围的配置。
  
3.  将以下示例复制并粘贴到查询窗口中，然后选择“执行”。 

#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP 数据库范围的配置

  此示例演示如何使用 [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) 系统目录视图确定当前数据库的数据库范围的 MAXDOP 配置。

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  此示例演示如何使用 [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) 语句将 `MAXDOP` 配置设置为 `8`。 该设置将立即生效。  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

此示例适用于[已启用读取扩展副本](read-scale-out.md)、[异地复制](active-geo-replication-overview.md)和[超大规模](service-tier-hyperscale.md)次要副本的 Azure SQL 数据库。 例如，考虑到读写和只读工作负载可能存在差异，将主要副本设置为与次要副本不同的 MAXDOP。 所有语句都在主要副本上执行。 `sys.database_scoped_configurations` 的 `value_for_secondary` 列包含次要副本的设置。

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>MAXDOP 查询提示

  此示例演示如何使用查询提示执行查询，以将 `max degree of parallelism` 强制设置为 `2`。  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>MAXDOP 索引选项

  此示例演示如何使用索引选项重新生成索引，以将 `max degree of parallelism` 强制设置为 `12`。  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>另请参阅  
* [ALTER DATABASE SCOPED CONFIGURATION &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [配置并行索引操作](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [查询提示 (Transact-SQL)](/sql/t-sql/queries/hints-transact-sql-query)     
* [设置索引选项](/sql/relational-databases/indexes/set-index-options)     
* [了解并解决 Azure SQL 数据库阻塞问题](understand-resolve-blocking.md)

## <a name="next-steps"></a>后续步骤

* [监视和优化性能](/sql/relational-databases/performance/monitor-and-tune-for-performance)