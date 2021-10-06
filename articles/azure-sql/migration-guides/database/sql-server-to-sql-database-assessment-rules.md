---
title: SQL Server 到 Azure SQL 数据库迁移的评估规则
description: 这些评估规则用于确定源 SQL Server 实例存在的问题，这些问题必须在迁移到 Azure SQL 数据库之前解决。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: rajeshsetlem
ms.author: rsetlem
ms.reviewer: mathoma, cawrites
ms.date: 12/15/2020
ms.openlocfilehash: 17b3cd2c0257989a220be6b35e93d88db7225188
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457461"
---
# <a name="assessment-rules-for-sql-server-to-azure-sql-database-migration"></a>SQL Server 到 Azure SQL 数据库迁移的评估规则
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

迁移工具通过运行大量评估规则来验证源 SQL Server 实例，以确定在将 SQL Server 数据库迁移到 Azure SQL 数据库之前必须解决的问题。 

本文列举了用于评估将 SQL Server 数据库迁移到 Azure SQL 数据库的可行性的规则。 


## <a name="bulk-insert"></a>大容量插入<a id="BulkInsert"></a>

**标题：使用非 Azure Blob 数据源的 BULK INSERT 在 Azure SQL 数据库中不受支持。**    
类别：问题   

**描述**   
Azure SQL 数据库无法访问文件共享或 Windows 文件夹。 请查看“受影响的对象”部分，了解 BULK INSERT 语句的特定用法，这些语句不引用 Azure Blob。 如果源不是 Azure Blob 存储，则在迁移到 Azure SQL 数据库后，使用“BULK INSERT”的对象将无法正常运行。 


建议   
迁移到 Azure SQL 数据库时，需要将 BULK INSERT 语句从使用本地文件或文件共享转换为使用 Azure Blob 存储中的文件。 或者，迁移到 Azure 虚拟机上的 SQL Server。

## <a name="compute-clause"></a>COMPUTE 子句<a id="ComputeClause"></a>

标题：COMPUTE 子句已弃用，并且已被删除。   
**类别**：警告   

**描述**   
COMPUTE 子句生成的总计在结果集的末尾显示为附加的汇总列。 但是，此子句在 Azure SQL 数据库中不再受支持。 


建议   
需要改用 ROLLUP 运算符来重新编写 T-SQL 模块。 下面的代码演示如何将 COMPUTE 替换为 ROLLUP： 

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

有关详细信息，请参阅 [SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="clr-assemblies"></a>CLR 程序集<a id="ClrAssemblies"></a>

**标题：Azure SQL 数据库中不支持 SQL CLR 程序集**   
类别：问题   


**描述**   
Azure SQL 数据库不支持 SQL CLR 程序集。 


建议   
目前无法在 Azure SQL 数据库中实现此目的。 建议的替代解决方案将要求应用程序代码和数据库更改仅使用 Azure SQL 数据库支持的程序集。 或者，迁移到 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server

有关详细信息，请参阅 [SQL 数据库中不支持的 Transact-SQL 差异](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>Cryptographic provider<a id="CryptographicProvider"></a>

**标题：已发现使用了 CREATE CRYPTOGRAPHIC PROVIDER 或 ALTER CRYPTOGRAPHIC PROVIDER，但这两个语句在 Azure SQL 数据库中不受支持**   
类别：问题   

**描述**   
Azure SQL 数据库不支持 CRYPTOGRAPHIC PROVIDER 语句，因为它无法访问文件。 请查看“受影响的对象”部分，了解 CRYPTOGRAPHIC PROVIDER 语句的特定用法。 使用 `CREATE CRYPTOGRAPHIC PROVIDER` 或 `ALTER CRYPTOGRAPHIC PROVIDER` 的对象在迁移到 Azure SQL 数据库后将无法正常运行。  


建议   
查看使用了 `CREATE CRYPTOGRAPHIC PROVIDER` 或 `ALTER CRYPTOGRAPHIC PROVIDER` 的对象。 在必须使用的任何此类对象中，删除所使用的这些功能。 或者，迁移到 Azure 虚拟机上的 SQL Server

## <a name="cross-database-references"></a>跨数据库引用<a id="CrossDataseReferences"></a>

**标题：Azure SQL 数据库不支持跨数据库查询**   
类别：问题   

**描述**   
此服务器上的数据库使用了 Azure SQL 数据库中不支持的跨数据库查询。 


建议   
Azure SQL 数据库不支持跨数据库查询。 建议执行以下操作： 
- 将从属数据库迁移到 Azure SQL 数据库，并使用弹性数据库查询（目前为预览版）功能跨 Azure SQL 数据库进行查询。 
- 将从属数据集从其他数据库移入要迁移的数据库。 
- 迁移到 Azure SQL 托管实例。
- 迁移到 Azure 虚拟机上的 SQL Server。 

有关详细信息，请参阅 [Azure SQL 数据库弹性数据库查询（预览版）](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>数据库兼容性<a id="DbCompatLevelLowerThan100"></a>

**标题：Azure SQL 数据库不支持低于 100 的兼容性级别。**    
**类别**：警告   

**描述**   
数据库兼容性级别是一个非常有用的工具，借助该工具，可以在升级 SQL Server 数据库引擎的同时，通过保持与升级前相同的数据库兼容性级别来使连接应用程序保持正常运行状态，从而帮助实现数据库现代化。 Azure SQL 数据库不支持低于 100 的兼容性级别。 


建议   
在 Azure SQL 托管实例上将数据库兼容性级别升级到 100 后，评估应用程序功能是否完好无损。 或者，迁移到 Azure 虚拟机上的 SQL Server

## <a name="database-mail"></a>数据库邮件<a id="DatabaseMail"></a>

**标题：Azure SQL 数据库中不支持数据库邮件。**    
**类别**：警告   

**描述**   
此服务器使用 Azure SQL 数据库中不支持的数据库邮件功能。


建议   
请考虑迁移到支持数据库邮件的 Azure SQL 托管实例。  另外，请考虑使用 Azure Functions 和 Sendgrid 在 Azure SQL 数据库上完成邮件功能。

## <a name="database-principal-alias"></a>DATABASE_PRINCIPAL_ALIASES<a id="DatabasePrincipalAlias"></a>

标题：SYS.DATABASE_PRINCIPAL_ALIASES 已弃用，并且已被删除。   
类别：问题   

**描述**   
Azure SQL 数据库已弃用SYS.DATABASE_PRINCIPAL_ALIASES 并已将其删除。  


建议   
请使用角色而不是别名。

有关详细信息，请参阅 [SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK 选项<a id="DisableDefCNSTCHK"></a>

标题：SET 选项 DISABLE_DEF_CNST_CHK 已弃用，并且已被删除。   
类别：问题   

**描述**   
Azure SQL 数据库已弃用 SET option DISABLE_DEF_CNST_CHK 并已将其删除。  


有关详细信息，请参阅 [SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW 提示<a id="FastFirstRowHint"></a>

标题：FASTFIRSTROW 查询提示已弃用，并且已被删除。   
**类别**：警告   

**描述**   
Azure SQL 数据库已弃用 FASTFIRSTROW 查询提示并已将其删除。  


建议   
FASTFIRSTROW 查询提示改用 OPTION (FAST n)。

有关详细信息，请参阅 [SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>文件流<a id="FileStream"></a>

**标题：Azure SQL 数据库不支持文件流**   
类别：问题   

**描述**   
文件流功能使用户可以在 NTFS 文件系统中存储文本文档、图像和视频等非结构化数据，但此功能在 Azure SQL 数据库中不受支持。 


建议   
将非结构化文件上传到 Azure Blob 存储，并在 Azure SQL 数据库中存储与这些文件相关的元数据（名称、类型、URL 位置、存储密钥等）。 可能需要对应用程序进行重新设计，以实现与 Azure SQL 数据库的 Blob 流式传输。 或者，迁移到 Azure 虚拟机上的 SQL Server。

有关详细信息，请参阅 [与 SQL Azure 的 Blob 流式传输](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)博客


## <a name="linked-server"></a>链接服务器<a id="LinkedServer"></a>

**标题：Azure SQL 数据库不支持链接服务器功能**   
类别：问题   

**描述**   
链接服务器使 SQL Server 数据库引擎可以对 SQL Server 实例外部的 OLE DB 数据源执行命令。 


建议   
Azure SQL 数据库不支持链接服务器功能。 建议执行以下操作以避免需要使用链接服务器： 
- 确定哪些从属数据集属于 SQL 远程服务器，并考虑将它们移到要迁移的数据库中。 
- 将从属数据库迁移到 Azure，并使用弹性数据库查询（目前为预览版）功能跨 Azure SQL 数据库进行查询。 

有关详细信息，请参阅 [Azure SQL 数据库弹性查询（预览版）](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**标题：Azure SQL 数据库不支持 BEGIN DISTRIBUTED TRANSACTION。**    
类别：问题   

**描述**   
由 Transact SQL BEGIN DISTRIBUTED TRANSACTION 启动并由 Microsoft 分布式事务处理协调器 (MS DTC) 管理的分布式事务在 Azure SQL 数据库中不受支持。  


建议   
检查 Azure Migrate 中的“受影响的对象”部分，查看使用 BEGIN DISTRUBUTED TRANSACTION 的所有对象。 考虑将参与者数据库迁移到 Azure SQL 托管实例，其中支持跨多个实例的分布式事务（目前处于预览阶段）。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息：[Azure SQL 托管实例的跨多个服务器的事务](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET（批量）<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**标题：Azure SQL 数据库不支持将批量操作中使用的 OpenRowSet 用于非 Azure Blob 存储数据源。**    
类别：问题   

**说明** OPENROWSET 通过内置的 BULK 提供程序支持批量操作，该提供程序使文件中的数据可被读取并作为行集返回。 Azure SQL 数据库不支持将 OPENROWSET 用于非 Azure Blob 存储数据源。


建议   
Azure SQL 数据库无法访问文件共享和 Windows 文件夹，因此必须从 Azure Blob 存储导入文件。 因此，OPENROWSET 函数中仅支持 DATASOURCE Blob 类型。 或者，迁移到 Azure 虚拟机上的 SQL Server

有关详细信息，请参阅[解析迁移到 SQL 数据库的过程中的 Transact-SQL 差异](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET（提供程序）<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**标题：Azure SQL 数据库不支持将 OpenRowSet 用于 SQL 或非 SQL 提供程序。**    
类别：问题   

**描述**   
将 OpenRowSet 用于 SQL 或非 SQL 提供程序是一种替代方法，并且是一种使用 OLE DB 连接并访问远程数据的一次性的临时方法。 Azure SQL 数据库不支持将 OpenRowSet 用于 SQL 或非 SQL 提供程序。


建议   
Azure SQL 数据库仅支持从 Azure blob 存储进行导入。 或者，迁移到 Azure 虚拟机上的 SQL Server

有关详细信息，请参阅[解析迁移到 SQL 数据库的过程中的 Transact-SQL 差异](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>非 ANSI 左外部联接<a id="NonANSILeftOuterJoinSyntax"></a>

标题：非 ANSI 样式左外部联接已弃用，并且已被删除。   
**类别**：警告   

**描述**   
Azure SQL 数据库中已弃用非 ANSI 样式左外部联接并且已将其删除。 


建议   
使用 ANSI 联接语法。

有关详细信息，请参阅 [SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="non-ansi-right-outer-join"></a>非 ANSI 右外部联接<a id="NonANSIRightOuterJoinSyntax"></a>

标题：非 ANSI 样式右外部联接已弃用，并且已被删除。   
**类别**：警告   

**描述**   
Azure SQL 数据库中已弃用非 ANSI 样式右外部联接并且已将其删除。 


建议   
使用 ANSI 联接语法。

有关详细信息，请参阅 [SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="next-column"></a>Next 列<a id="NextColumn"></a>

**标题：名为 NEXT 的表和列将导致 Azure SQL 数据库发生错误。**    
类别：问题   

**描述**   
检测到名为 NEXT 的表或列。 Microsoft SQL Server 中引入的序列使用 ANSI 标准 NEXT VALUE FOR 功能。 如果表或列名为 NEXT 且该列的别名为 VALUE，在省略 ANSI 标准 AS 的情况下，所得到的语句可能会导致错误。  


建议   
在设置表或列的别名时重写语句以包含 ANSI 标准 AS 关键字。 例如，当列名为 NEXT 且该列的别名为 VALUE 时，查询 `SELECT NEXT VALUE FROM TABLE` 将导致发生错误，应将其重写为 SELECT NEXT AS VALUE FROM TABLE。 同样，如果表名为 NEXT 且该表的别名为 VALUE，则查询 `SELECT Col1 FROM NEXT VALUE` 将导致发生错误，应将其重写为 `SELECT Col1 FROM NEXT AS VALUE`。

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

标题：应将旧式的 RAISERROR 调用替换为等效的新式调用。   
**类别**：警告   

**描述**   
类似于以下示例的 RAISERROR 调用称为旧式，因为它们不包含逗号和括号。 `RAISERROR 50001 'this is a test'`. Azure SQL 数据库已弃用 RAISERROR 的这种调用方法并已将其删除。 


建议   
使用当前的 RAISERROR 语法重写该语句，或评估 `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` 的新式方法是否可行。

有关详细信息，请参阅 [SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="server-audits"></a>服务器审核<a id="ServerAudits"></a>

**标题：使用 Azure SQL 数据库审核功能替换服务器审核**   
**类别**：警告   

**描述**   
Azure SQL 数据库不支持服务器审核功能。


建议   
请考虑使用 Azure SQL 数据库审核功能替代服务器审核。  Azure SQL 支持审核功能，并且该功能比 SQL Server 的功能更强大。 Azure SQL 数据库可以审核各种数据库操作和事件，包括：访问数据、架构更改 (DDL)、数据更改 (DML)、帐户、角色、权限 (DCL) 和安全异常。 Azure SQL 数据库审核功能可提高组织深入了解数据库中发生的事件和更改（包括针对数据的更新和查询）的能力。 或者，迁移到 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server。

有关详细信息，请参阅 [Azure SQL 数据库的审核功能](../../database/auditing-overview.md)

## <a name="server-credentials"></a>服务器凭据<a id="ServerCredentials"></a>

**标题：Azure SQL 数据库不支持服务器范围的凭据**   
**类别**：警告   

**描述**   
凭据是包含连接到 SQL Server 外部资源所需的身份验证信息（凭据）的记录。 Azure SQL 数据库支持数据库凭据，但不支持在 SQL Server 范围创建的凭据。   


建议   
Azure SQL 数据库支持数据库范围的凭据。 将服务器范围的凭据转换为数据库范围的凭据。 或者，迁移到 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server

有关详细信息，请参阅[创建数据库范围的凭据](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Service Broker<a id="ServiceBroker"></a>

**标题：Azure SQL 数据库不支持 Service Broker 功能**   
类别：问题   

**描述**   
SQL Server Service Broker 在 SQL Server 数据库引擎中为消息传递和队列应用程序提供本机支持。 Azure SQL 数据库不支持 Service Broker 功能。


建议   
Azure SQL 数据库不支持 Service Broker 功能。 请考虑迁移到在同一实例中支持 Service Broker 的 Azure SQL 托管实例。 或者，迁移到 Azure 虚拟机上的 SQL Server。 

## <a name="server-scoped-triggers"></a>服务器范围的触发器<a id="ServerScopedTriggers"></a>

**标题：Azure SQL 数据库不支持服务器范围的触发器**   
**类别**：警告   

**描述**   
触发器是一种特殊的存储过程，用户可执行它来响应表中的特定操作，例如插入、删除或更新数据。 Azure SQL 数据库不支持服务器范围的触发器。 Azure SQL 数据库不支持以下触发器选项：FOR LOGON、ENCRYPTION、WITH APPEND、NOT FOR REPLICATION、EXTERNAL NAME 选项（没有外部方法支持）、ALL SERVER 选项（DDL 触发器）、LOGON 事件触发器（登录触发器），Azure SQL 数据库不支持 CLR 触发器。


建议   
请改为使用数据库级别触发器。 或者，迁移到 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server

有关详细信息，请参阅[解析迁移到 SQL 数据库的过程中的 Transact-SQL 差异](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>SQL 代理作业<a id="AgentJobs"></a>

**标题：SQL Server 代理作业在 Azure SQL 数据库中不可用。**    
**类别**：警告   

**描述**   
SQL Server 代理是一项 Microsoft Windows 服务，它在执行计划的管理任务，这些任务 SQL Server 中被称为“作业”。 SQL Server 代理作业在 Azure SQL 数据库中不可用。 


**建议** 使用弹性作业（预览版），这是 Azure SQL 数据库中 SQL Server 代理作业的替代项。 使用 Azure SQL 数据库的弹性数据库作业，可以跨多个数据库可靠执行 T-SQL 脚本，同时自动重试并提供最终完成保证。 或者，请考虑迁移到 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server。

有关详细信息，请参阅[弹性数据库作业（预览版）入门](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>SQL 数据库大小<a id="SQLDBDatabaseSize"></a>

**标题：Azure SQL 数据库不支持超过 100 TB 的数据库大小。**    
类别：问题   

**描述**   
数据库的大小大于支持的最大大小 100 TB。 


建议   
评估数据是否可以存档、压缩或分片到多个数据库中。 或者，迁移到 Azure 虚拟机上的 SQL Server。 

有关详细信息，请参阅 [vCore 资源限制](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

标题：SQL Mail 已弃用。   
**类别**：警告   

**描述**   
Azure SQL 数据库已弃用 SQL Mail 并已将其删除。


建议   
请考虑迁移到 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server 并使用数据库邮件。

有关详细信息，请参阅 [SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**标题：检测到引用已删除的系统存储过程的语句，这些系统存储过程在 Azure SQL 数据库中不可用。**    
**类别**：警告   

**描述**   
无法在 Azure SQL 数据库中使用以下不受支持的系统存储过程和扩展存储过程：`sp_dboption`、`sp_addserver`、`sp_dropalias`、`sp_activedirectory_obj`、`sp_activedirectory_scp`、`sp_activedirectory_start`。


建议    
删除对不受支持并且已从 Azure SQL 数据库中删除的系统存储过程的引用。

有关详细信息，请参阅 [SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="trace-flags"></a>跟踪标志<a id="TraceFlags"></a>

**标题：Azure SQL 数据库不支持跟踪标志**   
**类别**：警告   

**描述**   
跟踪标志用于临时设置特定服务器的特征或关闭特定行为。 跟踪标志经常用于诊断性能问题，或调试存储过程或复杂的计算机系统。 Azure SQL 数据库不支持跟踪标志。 


建议   
检查 Azure Migrate 中的“受影响的对象”部分，查看 Azure SQL 数据库不支持的所有跟踪标志，并评估是否可以删除这些跟踪标志。 或者，迁移到支持有限数量的全局跟踪标志或 Azure 虚拟机上的 SQL Server 的 Azure SQL 托管实例。

有关详细信息，请参阅[解析迁移到 SQL 数据库的过程中的 Transact-SQL 差异](../../database/transact-sql-tsql-differences-sql-server.md#t-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Windows 身份验证<a id="WindowsAuthentication"></a>

**标题：通过 Windows 身份验证映射的数据库用户（集成安全性）在 Azure SQL 数据库中不受支持。**    
**类别**：警告   

**描述**   
Azure SQL 数据库支持两种类型的身份验证 
- SQL 身份验证：使用用户名和密码 
- Azure Active Directory 身份验证：使用 Azure Active Directory 管理的标识，支持托管域和集成域。 

通过 Windows 身份验证映射的数据库用户（集成安全性）在 Azure SQL 数据库中不受支持。 



建议   
将本地 Active Directory 与 Azure Active Directory 联合。 然后，可以将 Windows 标识替换为等效的 Azure Active Directory 标识。 或者，迁移到 Azure 虚拟机上的 SQL Server。

有关详细信息，请参阅 [SQL 数据库安全功能](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**标题：xp_cmdshell 在 Azure SQL 数据库中不受支持。**    
类别：问题   

**描述**   
xp_cmdshell 会生成 Windows 命令 shell 并传入要执行的字符串，它在 Azure SQL 数据库中不受支持。 


建议   
检查 Azure Migrate 中的“受影响的对象”部分，查看所有使用 xp_cmdshell 的作业，并评估是否可以删除对 xp_cmdshell 的引用或受影响的对象。 另请考虑了解 Azure 自动化，它提供基于云的自动化和配置服务。 或者，迁移到 Azure 虚拟机上的 SQL Server。 

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
