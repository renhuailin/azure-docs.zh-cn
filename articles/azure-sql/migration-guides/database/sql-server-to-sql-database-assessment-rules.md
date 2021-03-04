---
title: 用于 SQL 数据库迁移 SQL Server 的评估规则
description: 评估规则，用于确定在迁移到 Azure SQL 数据库之前必须解决的源 SQL Server 实例的问题。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: bf825572226bf5d7432fd3ad825f2f3a13355c53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054501"
---
# <a name="assessment-rules-for-sql-server-to-sql-database-migration"></a>用于 SQL 数据库迁移 SQL Server 的评估规则
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

迁移工具通过运行大量评估规则来验证你的源 SQL Server 实例，以确定在将 SQL Server 数据库迁移到 Azure SQL 数据库之前必须解决的问题。 

本文提供了用于评估将 SQL Server 数据库迁移到 Azure SQL Database 的可行性的规则列表。 


## <a name="bulk-insert"></a>大容量插入<a id="BulkInsert"></a>

**标题： Azure SQL 数据库中不支持具有非 Azure blob 数据源的 BULK INSERT。**   
**类别**：问题   

**描述**   
Azure SQL 数据库无法访问文件共享或 Windows 文件夹。 请参阅 "受影响的对象" 部分，了解未引用 Azure blob BULK INSERT 语句的特定用法。 在迁移到 Azure SQL 数据库后，不能使用 "BULK INSERT" 源源不是 Azure blob 存储的对象。 


**推荐**   
迁移到 Azure SQL 数据库时，需要将使用本地文件或文件共享的 BULK INSERT 语句转换为使用 Azure blob 存储中的文件。 或者，迁移到 Azure 虚拟机上的 SQL Server。

## <a name="compute-clause"></a>Compute 子句<a id="ComputeClause"></a>

**Title：计算子句已终止，已被删除。**   
**类别**：警告   

**描述**   
计算子句会生成在结果集末尾作为附加摘要列显示的总计。 但是，在 Azure SQL 数据库中不再支持此子句。 


**推荐**   
T-sql 模块需要改用 ROLLUP 运算符进行重写。 下面的代码演示如何将计算替换为汇总： 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

详细信息： [废止数据库引擎功能 SQL Server ](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="change-data-capture-cdc"></a>更改数据捕获 (CDC) <a id="CDC"></a>

**标题： Azure SQL 数据库中不支持 (CDC) 的变更数据捕获**   
**类别**：问题   


**描述**   
在 Azure SQL 数据库中不支持变更数据捕获 (CDC) 。 评估是否可以改用更改跟踪。  或者，迁移到 azure SQL 托管实例或 Azure 虚拟机上的 SQL Server。 


**推荐**   
在 Azure SQL 数据库中不支持变更数据捕获 (CDC) 。 评估是否可以改为使用更改跟踪或考虑迁移到 Azure SQL 托管实例。

详细信息： [启用 AZURE SQL 更改跟踪](https://social.technet.microsoft.com/wiki/contents/articles/2976.azure-sql-how-to-enable-change-tracking.aspx)

## <a name="clr-assemblies"></a>CLR 程序集<a id="ClrAssemblies"></a>

**标题： Azure SQL 数据库中不支持 SQL CLR 程序集**   
**类别**：问题   


**描述**   
Azure SQL Database 不支持 SQL CLR 程序集。 


**推荐**   
目前无法在 Azure SQL 数据库中实现此目的。 建议的替代解决方案将要求应用程序代码和数据库更改仅使用 Azure SQL 数据库支持的程序集。 或者迁移到 azure SQL 托管实例或 Azure 虚拟机上的 SQL Server

详细信息： [SQL 数据库中不支持的 transact-sql 差异](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>加密提供程序<a id="CryptographicProvider"></a>

**标题：发现使用了 CREATE 加密提供程序或 ALTER 加密提供程序，这在 Azure SQL 数据库中不受支持**   
**类别**：问题   

**描述**   
Azure SQL Database 不支持加密提供程序语句，因为它无法访问文件。 有关加密提供程序语句的特定用法，请参阅受影响的对象部分。 `CREATE CRYPTOGRAPHIC PROVIDER` `ALTER CRYPTOGRAPHIC PROVIDER` 迁移到 Azure SQL 数据库后，具有或的对象将无法正常运行。  


**推荐**   
用或查看 `CREATE CRYPTOGRAPHIC PROVIDER` 对象 `ALTER CRYPTOGRAPHIC PROVIDER` 。 在所需的任何此类对象中，删除这些功能的使用。 或者，迁移到 Azure 虚拟机上的 SQL Server

## <a name="cross-database-references"></a>跨数据库引用<a id="CrossDataseReferences"></a>

**标题： Azure SQL 数据库不支持跨数据库查询**   
**类别**：问题   

**描述**   
此服务器上的数据库使用 Azure SQL 数据库中不支持的跨数据库查询。 


**推荐**   
Azure SQL 数据库不支持跨数据库查询。 建议执行以下操作： 
- 将依赖数据库 () 迁移到 Azure SQL 数据库，并使用弹性数据库查询 (目前为预览版) 功能跨 Azure SQL 数据库进行查询。 
- 将相关数据集从其他数据库移入要迁移的数据库。 
- 迁移到 Azure SQL 托管实例。
- 迁移到 Azure 虚拟机上的 SQL Server。 

详细信息： [请参阅 AZURE SQL 数据库弹性数据库查询 (预览) ](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>数据库兼容性<a id="DbCompatLevelLowerThan100"></a>

**标题： Azure SQL 数据库不支持低于100的兼容级别。**   
**类别**：警告   

**描述**   
数据库兼容性级别是一项非常有用的工具，可通过允许升级 SQL Server 数据库引擎来升级数据库现代化，同时保持连接应用程序的功能状态，同时保持相同的升级前数据库兼容性级别。 Azure SQL Database 不支持低于100的兼容级别。 


**推荐**   
评估 Azure SQL 托管实例上的数据库兼容级别升级为100时，应用程序的功能是否完整。 或者，迁移到 Azure 虚拟机上的 SQL Server

## <a name="database-mail"></a>数据库邮件<a id="DatabaseMail"></a>

**标题： Azure SQL 数据库中不支持数据库邮件。**   
**类别**：警告   

**描述**   
此服务器使用 Azure SQL 数据库中不支持的数据库邮件功能。


**推荐**   
请考虑迁移到支持数据库邮件的 Azure SQL 托管实例。  另外，请考虑使用 Azure 函数和 Sendgrid 在 Azure SQL 数据库上完成邮件功能。

详细信息： [使用 Azure Functions 脚本从 AZURE SQL Database 发送电子邮件](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)


## <a name="database-principal-alias"></a>数据库主体别名<a id="DatabasePrincipalAlias"></a>

**标题： SYS。DATABASE_PRINCIPAL_ALIASES 已不再使用，已被删除。**   
**类别**：问题   

**描述**   
系统.DATABASE_PRINCIPAL_ALIASES 中止，已在 Azure SQL 数据库中删除。  


**推荐**   
请使用角色而不是别名。

详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK 选项<a id="DisableDefCNSTCHK"></a>

**标题： SET 选项 DISABLE_DEF_CNST_CHK 已停止使用，已被删除。**   
**类别**：问题   

**描述**   
SET 选项 DISABLE_DEF_CNST_CHK 已停止，并已在 Azure SQL 数据库中删除。  


详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW 提示<a id="FastFirstRowHint"></a>

**Title： FASTFIRSTROW 查询提示已停止使用，已将其删除。**   
**类别**：警告   

**描述**   
FASTFIRSTROW 查询提示已停止使用，已在 Azure SQL 数据库中删除。  


**推荐**   
 (FAST n) ，而不是使用 FASTFIRSTROW 查询提示使用选项。

详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**标题： Azure SQL 数据库中不支持 Filestream**   
**类别**：问题   

**描述**   
在 Azure SQL 数据库中不支持用于在 NTFS 文件系统中存储非结构化数据（例如文本文档、图像和视频）的 Filestream 功能。 


**推荐**   
将非结构化文件上传到 Azure Blob 存储，并在 Azure SQL 数据库中存储与这些文件相关的元数据 (名称、类型、URL 位置、存储密钥等 ) 。 可能需要对应用程序重新进行工程处理，使其能够与 Azure SQL 数据库进行流式处理。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [向/从 AZURE SQL 博客流式传输 blob](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)


## <a name="linked-server"></a>链接服务器<a id="LinkedServer"></a>

**标题： Azure SQL 数据库中不支持链接服务器功能**   
**类别**：问题   

**描述**   
链接服务器使 SQL Server 数据库引擎对 SQL Server 的实例以外的 OLE DB 数据源执行命令。 


**推荐**   
Azure SQL Database 不支持链接服务器功能。 为了消除对链接服务器的需求，建议执行以下操作： 
- 确定远程 SQL server 中的从属数据集，并考虑将它们移到正在迁移的数据库中。 
- 将依赖数据库 () 迁移到 Azure，并使用弹性数据库查询 (预览) 功能跨 Azure SQL 数据库中的数据库进行查询。 

详细信息： [请参阅 AZURE SQL 数据库弹性查询 (预览) ](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**标题： Azure SQL 数据库中不支持 "开始分布式事务"。**   
**类别**：问题   

**描述**   
在 Azure SQL 数据库中不支持通过 Transact-sql BEGIN DISTRIBUTED TRANSACTION 和 Microsoft 分布式事务处理协调器 (MS DTC) 管理的分布式事务。  


**推荐**   
查看 Azure Migrate 中的受影响对象部分，以查看使用 BEGIN 分布式 TRANSACTION 的所有对象。 请考虑将参与者数据库迁移到 Azure SQL 托管实例，其中支持跨多个实例的分布式事务 (当前在预览版中) 。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [AZURE SQL 托管实例的多个服务器之间的事务 ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET (批量) <a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**标题： Azure SQL 数据库不支持使用非 Azure blob 存储数据源在大容量操作中使用的 OpenRowSet。**   
**类别**：问题   

**描述** OPENROWSET 通过内置的批量提供程序支持大容量操作，该提供程序允许以行集的形式读取和返回文件中的数据。 Azure SQL 数据库中不支持具有非 Azure blob 存储数据源的 OPENROWSET。


**推荐**   
Azure SQL Database 无法访问文件共享和 Windows 文件夹，因此必须从 Azure blob 存储导入文件。 因此，OPENROWSET 函数中仅支持 blob 类型数据源。 或者，迁移到 Azure 虚拟机上的 SQL Server

详细信息： [解决迁移到 SQL 数据库的过程中的 transact-sql 差异](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET (提供程序) <a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**标题： Azure SQL 数据库中不支持 SQL 或非 SQL 提供程序的 OpenRowSet。**   
**类别**：问题   

**描述**   
OpenRowSet with SQL 或非 SQL 提供程序是访问链接服务器中的表的替代方法，是使用 OLE DB 连接和访问远程数据的一次性、即席方法。 在 Azure SQL 数据库中不支持 SQL 或非 SQL 提供程序的 OpenRowSet。


**推荐**   
Azure SQL 数据库仅支持从 Azure blob 存储进行导入。 或者，迁移到 Azure 虚拟机上的 SQL Server

详细信息： [解决迁移到 SQL 数据库的过程中的 transact-sql 差异](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>非 ANSI 左外部联接<a id="NonANSILeftOuterJoinSyntax"></a>

**Title：非 ANSI 样式左外部联接已停止使用并且已被移除。**   
**类别**：警告   

**描述**   
非 ANSI 样式左外部联接已停止，并已在 Azure SQL 数据库中删除。 


**推荐**   
使用 ANSI 联接语法。

详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="non-ansi-right-outer-join"></a>非 ANSI 右外部联接<a id="NonANSIRightOuterJoinSyntax"></a>

**标题：非 ANSI 样式右外部联接已停止使用，已被删除。**   
**类别**：警告   

**描述**   
非 ANSI 样式右外部联接已停止，并已在 Azure SQL 数据库中删除。 


**推荐**   
使用 ANSI 联接语法。

详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="next-column"></a>下一列<a id="NextColumn"></a>

**标题：名为 NEXT 的表和列将导致 Azure SQL 数据库出错。**   
**类别**：问题   

**描述**   
检测到名为 NEXT 的表或列。 Microsoft SQL Server 中引入的序列使用 ANSI 标准 NEXT VALUE FOR 函数。 如果表或列名为 NEXT 且该列的别名为 VALUE，在省略 ANSI 标准 AS 的情况下，所得到的语句可能会导致错误。  


**推荐**   
在设置表或列的别名时重写语句以包含 ANSI 标准 AS 关键字。 例如，当列命名为 NEXT 并且该列的别名为 VALUE 时，该查询 `SELECT NEXT VALUE FROM TABLE` 将导致错误，并且应重新编写为从表中选择 "下一步" 作为值。 同样，如果表命名为 NEXT，并且该表的别名为 VALUE，则查询 `SELECT Col1 FROM NEXT VALUE` 将导致错误，并且应重写为 `SELECT Col1 FROM NEXT AS VALUE` 。

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**标题：旧样式 RAISERROR 调用应替换为新式等效项。**   
**类别**：警告   

**描述**   
类似于以下示例的 RAISERROR 调用称为旧样式，因为它们不包含逗号和括号。 `RAISERROR 50001 'this is a test'`. 此调用 RAISERROR 的方法在 Azure SQL 数据库中已停止并被删除。 


**推荐**   
使用当前的 RAISERROR 语法重写语句，或评估的新式方法 `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` 是否可行。

详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="server-audits"></a>服务器审核<a id="ServerAudits"></a>

**标题：使用 Azure SQL 数据库审核功能替换服务器审核**   
**类别**：警告   

**描述**   
在 Azure SQL 数据库中不支持服务器审核。


**推荐**   
请考虑 Azure SQL 数据库审核功能来替换服务器审核。  Azure SQL 支持审核功能，并且功能更丰富于 SQL Server。 Azure SQL 数据库可以审核各种数据库操作和事件，包括：对数据的访问、架构更改 (DDL) 、数据更改 (DML) 、帐户、角色和权限 (DCL、安全异常。 通过 Azure SQL 数据库审核，组织可以深入了解数据库中发生的事件和更改，包括对数据的更新和查询。 或者迁移到 azure SQL 托管实例或 Azure 虚拟机上的 SQL Server。

详细信息： [AZURE SQL 数据库的审核 ](../../database/auditing-overview.md)

## <a name="server-credentials"></a>服务器凭据<a id="ServerCredentials"></a>

**标题： Azure SQL 数据库中不支持服务器范围的凭据**   
**类别**：警告   

**描述**   
凭据是一条记录，其中包含连接到 SQL Server 外的资源所需的身份验证信息 (凭据) 。 Azure SQL 数据库支持数据库凭据，但不支持在 SQL Server 作用域创建的凭据。   


**推荐**   
Azure SQL 数据库支持数据库范围的凭据。 将服务器范围的凭据转换为数据库范围的凭据。 或者迁移到 azure SQL 托管实例或 Azure 虚拟机上的 SQL Server

详细信息： [创建数据库作用域凭据](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Service Broker<a id="ServiceBroker"></a>

**标题： Azure SQL 数据库中不支持 Service Broker 功能**   
**类别**：问题   

**描述**   
SQL Server Service Broker 在 SQL Server 数据库引擎中为消息和队列应用程序提供本机支持。 在 Azure SQL 数据库中不支持 Service Broker 功能。


**推荐**   
在 Azure SQL 数据库中不支持 Service Broker 功能。 请考虑迁移到在同一实例中支持 service broker 的 Azure SQL 托管实例。 或者，迁移到 Azure 虚拟机上的 SQL Server。 

## <a name="server-scoped-triggers"></a>服务器范围内的触发器<a id="ServerScopedTriggers"></a>

**标题： Azure SQL 数据库中不支持服务器范围内的触发器**   
**类别**：警告   

**描述**   
触发器是一种特殊的存储过程，可执行它来响应表中的特定操作，例如插入、删除或更新数据。 在 Azure SQL 数据库中不支持服务器范围内的触发器。 Azure SQL Database 不支持以下触发器选项：对于 "登录"、"加密"、"追加"、"不用于复制" 和 "外部名称选项" (没有外部方法支持) ，"所有服务器" 选项 (DDL 触发器) ，在登录事件上触发 (Logon 触发器) ，Azure SQL Database 不支持 CLR 触发器。


**推荐**   
请改用数据库级触发器。 或者迁移到 azure SQL 托管实例或 Azure 虚拟机上的 SQL Server

详细信息： [解决迁移到 SQL 数据库的过程中的 transact-sql 差异](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>SQL 代理作业<a id="AgentJobs"></a>

**标题： Azure SQL 数据库中不支持 SQL Server 代理作业**   
**类别**：警告   

**描述**   
SQL Server 代理是一种 Microsoft Windows 服务，可执行计划的管理任务，这些任务在 SQL Server 中称为 "作业"。 SQL Server 代理作业在 Azure SQL 数据库中不可用。 


**建议** 使用弹性作业 (预览) ，这是 Azure SQL 数据库中 SQL Server 代理作业的替代项。 弹性数据库作业 Azure SQL 数据库，可在自动重试并提供最终完成保证的同时，可靠地执行跨多个数据库的 T-sql 脚本。 另外，请考虑迁移到 azure SQL 托管实例或 Azure 虚拟机上的 SQL Server。

详细信息： [弹性数据库作业 (预览版入门) ](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>SQL 数据库大小<a id="SQLDBDatabaseSize"></a>

**标题： Azure SQL 数据库不支持大于 100 TB 的数据库大小。**   
**类别**：问题   

**描述**   
数据库的大小大于支持的最大大小 100 TB。 


**推荐**   
评估数据是否可以存档或压缩或分片到多个数据库中。 或者，迁移到 Azure 虚拟机上的 SQL Server。 

详细信息： [vCore 资源限制](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**标题： SQL Mail 已停止使用。**   
**类别**：警告   

**描述**   
SQL Mail 已不再使用，并已在 Azure SQL 数据库中删除。


**推荐**   
考虑迁移到 azure 虚拟机上的 Azure SQL 托管实例或 SQL Server，并使用数据库邮件。

详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**Title：检测到的语句引用了在 Azure SQL 数据库中不可用的已删除系统存储过程。**   
**类别**：警告   

**描述**   
以下不受支持的系统和扩展存储过程不能在 Azure SQL 数据库中使用-、、、、和 `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` `sp_activedirectory_start` 。


**推荐**    
删除对 Azure SQL 数据库中已删除的不受支持的系统过程的引用。

详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="trace-flags"></a>跟踪标志<a id="TraceFlags"></a>

**标题： Azure SQL 数据库不支持跟踪标志**   
**类别**：警告   

**描述**   
跟踪标志用于临时设置特定服务器的特征或关闭特定行为。 跟踪标志经常用于诊断性能问题，或调试存储过程或复杂的计算机系统。 Azure SQL Database 不支持跟踪标志。 


**推荐**   
查看 Azure Migrate 中的 "受影响的对象" 部分，查看 Azure SQL 数据库中不支持的所有跟踪标志并评估它们是否可以删除。 或者，迁移到支持有限数量的全局跟踪标志或 Azure 虚拟机上的 SQL Server 的 Azure SQL 托管实例。

详细信息： [解决迁移到 SQL 数据库的过程中的 transact-sql 差异](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Windows 身份验证<a id="WindowsAuthentication"></a>

**标题：在 Azure SQL 数据库中不支持通过 Windows 身份验证映射的数据库用户 (集成安全性) 。**   
**类别**：警告   

**描述**   
Azure SQL 数据库支持两种类型的身份验证 
- SQL 身份验证：使用用户名和密码 
- Azure Active Directory 身份验证：使用由 Azure Active Directory 管理的标识，支持托管域和集成域。 

在 Azure SQL 数据库中不支持通过 Windows 身份验证映射的数据库用户 (集成安全性) 。 



**推荐**   
将本地 Active Directory 与 Azure Active Directory 联合。 然后，可以将 Windows 标识替换为等效的 Azure Active Directory 标识。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [SQL 数据库安全功能](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**标题： Azure SQL 数据库中不支持 xp_cmdshell。**   
**类别**：问题   

**描述**   
在 Azure SQL 数据库中不支持生成 Windows 命令 shell 并传入要执行的字符串 xp_cmdshell。 


**推荐**   
查看 Azure Migrate 中的 "受影响的对象" 部分以查看使用 xp_cmdshell 的所有对象，并评估是否可以删除对 xp_cmdshell 或受影响的对象的引用。 还应考虑探索提供基于云的自动化和配置服务的 Azure 自动化。 或者，迁移到 Azure 虚拟机上的 SQL Server。 

## <a name="next-steps"></a>后续步骤

若要开始将 SQL Server 迁移到 Azure SQL 数据库，请参阅 [SQL Server 到 SQL 托管实例迁移指南](sql-server-to-sql-database-guide.md)。

- 有关在执行各种数据库和数据迁移方案及专门任务时可为你提供帮助的 Microsoft 与第三方服务和工具的矩阵，请参阅[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

- 若要了解有关 SQL 数据库的详细信息，请参阅：
   - [Azure SQL 数据库概述](../../database/sql-database-paas-overview.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 

- 有关云迁移的框架和采用周期的详细信息，请参阅
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负载计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- 若要评估应用程序访问层，请参阅 [Data Access Migration Toolkit（预览版）](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 若要详细了解如何执行数据访问层 A/B 测试，请参阅[数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。
