---
title: SQL Server 到 Azure SQL 托管实例迁移的评估规则
description: 这些评估规则用于确定源 SQL Server 实例存在的问题，这些问题必须在迁移到 Azure SQL 托管实例之前解决。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: rajeshsetlem
ms.author: rsetlem
ms.reviewer: mathoma, cawrites
ms.date: 12/15/2020
ms.openlocfilehash: 0d3ef1e9a7a4d30e4a716992caf4ec3f2f04cc1c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625575"
---
# <a name="assessment-rules-for-sql-server-to--azure-sql-managed-instance-migration"></a>SQL Server 到 Azure SQL 托管实例迁移的评估规则
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

迁移工具通过运行大量评估规则来验证源 SQL Server 实例，以确定在将 SQL Server 数据库迁移到 Azure SQL 托管实例之前必须解决的问题。 

本文列举了用于评估将 SQL Server 数据库迁移到 Azure SQL 托管实例的可行性的规则。 

## <a name="analysiscommand-job"></a>AnalysisCommand 作业<a id="AnalysisCommandJob"></a>

**标题：AnalysisCommand 作业步骤在 Azure SQL 托管实例中不受支持。**    
**类别**：警告   


**描述**   
这是用于运行 Analysis Services 命令的作业步骤。 AnalysisCommand 作业步骤在 Azure SQL 托管实例中不受支持。

建议     
检查 Azure Migrate 中的“受影响的对象”部分，查看所有使用 Analysis Services 命令作业步骤的作业，并评估是否可以删除作业步骤或受影响的对象。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息：[Azure SQL 托管实例中的 SQL Server 代理差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>AnalysisQuery 作业<a id="AnalysisQueryJob"></a>

**标题：AnalysisQuery 作业步骤在 Azure SQL 托管实例中不受支持。**    
**类别**：警告   

**描述**   
这是用于运行 Analysis Services 查询的作业步骤。 AnalysisQuery 作业步骤在 Azure SQL 托管实例中不受支持。


建议   
检查 Azure Migrate 中的“受影响的对象”部分，查看所有使用 Analysis Services 查询作业步骤的作业，并评估是否可以删除作业步骤或受影响的对象。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息：[Azure SQL 托管实例中的 SQL Server 代理差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>文件中的程序集<a id="AssemblyFromFile"></a>

**标题：带有文件参数的“CREATE ASSEMBLY”和“ALTER ASSEMBLY”在 Azure SQL 托管实例中不受支持。**    
类别：问题   

**描述**   
Azure SQL 托管实例不支持带有文件参数的“CREATE ASSEMBLY”或“ALTER ASSEMBLY”。 支持二进制参数。 有关在其中使用文件参数的特定对象，请参阅“受影响的对象”部分。

建议   
使用带文件参数的“CREATE ASSEMBLY”或“ALTER ASSEMBLY”检查对象。 如果任何此类对象是必需的，请将文件参数转换为二进制参数。 或者，迁移到 Azure 虚拟机上的 SQL Server。 

详细信息：[Azure SQL 托管实例中的 CLR 差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>大容量插入<a id="BulkInsert"></a>

**标题：使用非 Azure Blob 数据源的 BULK INSERT 在 Azure SQL 托管实例中不受支持。**    
类别：问题   

**描述**   
Azure SQL 托管实例无法访问文件共享或 Windows 文件夹。 请查看“受影响的对象”部分，了解 BULK INSERT 语句的特定用法，这些语句不引用 Azure Blob。 如果源不是 Azure Blob 存储，则在迁移到 Azure SQL 托管实例后，使用“BULK INSERT”的对象将无法工作。


建议   
迁移到 Azure SQL 托管实例时，需要将 BULK INSERT 语句从使用本地文件或文件共享转换为使用 Azure Blob 存储中的文件。

详细信息：[Azure SQL 托管实例中 Bulk Insert 与 OPENROWSET 之间的差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>CLR 安全性<a id="ClrStrictSecurity"></a>

标题：标记为 SAFE 或 EXTERNAL_ACCESS 的 CLR 程序集被认为 UNSAFE   
**类别**：警告   

**描述**   
Azure SQL 托管实例中强制实施 CLR 严格安全性模式。 此模式默认启用，并为包含用户定义的标记为 SAFE 或 EXTERNAL_ACCESS 的 CLR 程序集的数据库引入了中断性变更。


建议   
CLR 在 .NET Framework 中使用代码访问安全性 (CAS)（不可再作为安全边界）。 从 SQL Server 2017 (14.x) 数据库引擎开始，引入了称为 CLR 严格安全性的 `sp_configure` 选项，以增强 CLR 程序集的安全性。 默认启用 CLR 严格安全性，并将 SAFE CLR 程序集和 EXTERNAL_ACCESS CLR 程序集与标记为 UNSAFE 的程序集同等对待。 禁用 CLR 严格安全性后，如果在创建 CLR 程序集时将 PERMISSION_SET 设置为 SAFE，则该程序集也许可以访问外部系统资源、调用非托管代码以及获取“sysadmin”特权。 启用严格安全性后，未签名的任何程序集都将加载失败。 此外，如果数据库具有 SAFE 或 EXTERNAL_ACCESS 程序集，则 RESTORE 或 ATTACH DATABASE 语句可以完成，但程序集可能加载失败。 若要加载程序集，必须更改或删除每个程序集并重新创建，以便使用证书或非对称密钥对程序集进行签名，这样的证书或密钥具有与服务器上的 UNSAFE ASSEMBLY 权限相应的登录名。

详细信息：[CLR 严格安全性](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>COMPUTE 子句<a id="ComputeClause"></a>

标题：COMPUTE 子句已弃用，并且已被删除。   
**类别**：警告   

**描述**   
COMPUTE 子句生成的总计在结果集的末尾显示为附加的汇总列。 但是，此子句在 Azure SQL 托管实例中不再受支持。



建议   
需要改用 ROLLUP 运算符来重新编写 T-SQL 模块。 下面的代码演示如何将 COMPUTE 替换为 ROLLUP： 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

详细信息：[SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>Cryptographic provider<a id="CryptographicProvider"></a>

标题：已发现使用了 CREATE CRYPTOGRAPHIC PROVIDER 或 ALTER CRYPTOGRAPHIC PROVIDER，但这两个语句在 Azure SQL 托管实例中不受支持。   
类别：问题   

**描述**   
Azure SQL 托管实例不支持 CRYPTOGRAPHIC PROVIDER 语句，因为它无法访问文件。 请查看“受影响的对象”部分，了解 CRYPTOGRAPHIC PROVIDER 语句的特定用法。 迁移到 Azure SQL 托管实例后，使用“CREATE CRYPTOGRAPHIC PROVIDER”或“ALTER CRYPTOGRAPHIC PROVIDER”的对象将无法工作。


建议   
检查使用“CREATE CRYPTOGRAPHIC PROVIDER”或“ALTER CRYPTOGRAPHIC PROVIDER”的对象。 在必须使用的任何此类对象中，删除所使用的这些功能。 或者，迁移到 Azure 虚拟机上的 SQL Server。 

详细信息：[Azure SQL 托管实例中的 Cryptographic provider 差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>数据库兼容性<a id="DbCompatLevelLowerThan100"></a>

标题：不支持低于 100 的数据库兼容性级别   
**类别**：警告   

**描述**   
数据库兼容性级别是一个非常有用的工具，借助该工具，可以在升级 SQL Server 数据库引擎的同时，通过保持与升级前相同的数据库兼容性级别来使连接应用程序保持正常运行状态，从而帮助实现数据库现代化。 Azure SQL 托管实例不支持低于 100 的兼容性级别。 如果在 Azure SQL 托管实例上还原兼容性级别低于 100 的数据库，则兼容性级别将升级到 100。 


建议... 在 Azure SQL 托管实例上将数据库兼容性级别升级到 100 后，评估应用程序功能是否完好无损。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息：[Azure SQL 托管实例中支持的兼容性级别](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>DATABASE_PRINCIPAL_ALIASES<a id="DatabasePrincipalAlias"></a>

标题：SYS.DATABASE_PRINCIPAL_ALIASES 已弃用，并且已被删除。   
类别：问题   

**描述**   
在 Azure SQL 托管实例中，SYS.DATABASE_PRINCIPAL_ALIASES 已弃用，并且已被删除。


建议   
请使用角色而不是别名。

详细信息：[SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK 选项<a id="DisableDefCNSTCHK"></a>

标题：SET 选项 DISABLE_DEF_CNST_CHK 已弃用，并且已被删除。   
类别：问题   

**描述**   
在 Azure SQL 托管实例中，SET 选项 DISABLE_DEF_CNST_CHK 已弃用，并且已被删除。


详细信息：[SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW 提示<a id="FastFirstRowHint"></a>

标题：FASTFIRSTROW 查询提示已弃用，并且已被删除。   
**类别**：警告   

**描述**   
在 Azure SQL 托管实例中，FASTFIRSTROW 查询提示已弃用，并且已被删除。


建议   
FASTFIRSTROW 查询提示改用 OPTION (FAST n)。

详细信息：[SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>文件流<a id="FileStream"></a>

标题：文件流和 Filetable 在 Azure SQL 托管实例中不受支持。   
类别：问题   

**描述**   
文件流功能使用户可以在 NTFS 文件系统中存储文档文档、图像和视频等非结构化数据，但此功能在 Azure SQL 托管实例中不受支持。 无法迁移此数据库，因为无法在 Azure SQL 托管实例上还原包含文件流文件组的备份。


建议   
将非结构化文件上传到 Azure Blob 存储，并在 Azure SQL 托管实例中存储与这些文件相关的元数据（名称、类型、URL 位置、存储密钥等）。 可能需要对应用程序进行重新设计，以启用与 Azure SQL 托管实例的 Blob 流式传输。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息：[与 SQL Azure 的 Blob 流式传输](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)博客

## <a name="heterogeneous-ms-dtc"></a>异类 MS DTC<a id="MIHeterogeneousMSDTCTransactSQL"></a>

标题：Azure SQL 托管实例不支持对非 SQL Server 远程服务器使用 BEGIN DISTRIBUTED TRANSACTION。   
类别：问题   

**描述**   
如果远程服务器不是 SQL Server，则由 Transact SQL BEGIN DISTRIBUTED TRANSACTION 启动并由 Microsoft 分布式事务处理协调器 (MS DTC) 管理的分布式事务 Azure SQL 托管实例中不受支持。 


建议   
检查 Azure Migrate 中的“受影响的对象”部分，查看使用 BEGIN DISTRUBUTED TRANSACTION 的所有对象。 考虑将参与者数据库迁移到 Azure SQL 托管实例，其中支持跨多个实例的分布式事务（目前处于预览阶段）。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息：[Azure SQL 托管实例的跨多个服务器的事务](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>同源 MS DTC<a id="MIHomogeneousMSDTCTransactSQL"></a>

标题：Azure SQL 托管实例支持跨多个服务器的 BEGIN DISTRIBUTED TRANSACTION。   
类别：问题   

**描述**   
Azure SQL 托管实例支持由 Transact SQL BEGIN DISTRIBUTED TRANSACTION 启动并由 Microsoft 分布式事务处理协调器 (MS DTC) 管理的跨多个服务器的分布式事务。


建议   
检查 Azure Migrate 中的“受影响的对象”部分，查看使用 BEGIN DISTRUBUTED TRANSACTION 的所有对象。 考虑将参与者数据库迁移到 Azure SQL 托管实例，其中支持跨多个实例的分布式事务（目前处于预览阶段）。 或者，迁移到 Azure 虚拟机上的 SQL Server。 

详细信息：[Azure SQL 托管实例的跨多个服务器的事务](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>链接服务器（非 SQL 提供程序）<a id="LinkedServerWithNonSQLProvider"></a>

标题：Azure SQL 托管实例不支持将链接服务器用于非 SQL 提供程序。   
类别：问题   

**描述**   
链接服务器使 SQL Server 数据库引擎可以对 SQL Server 实例外部的 OLE DB 数据源执行命令。 Azure SQL 托管实例不支持将链接服务器用于非 SQL 提供程序。 


建议   
如果远程服务器提供程序不是 SQL Server，而是 Oracle 或 Sybase 等，则 Azure SQL 托管实例不支持链接服务器功能。 

建议执行以下操作以避免需要使用链接服务器： 
- 确定哪些从属数据库属于非 SQL 远程服务器，并考虑将它们移到要迁移的数据库中。 
- 将从属数据库迁移到受支持的目标，例如 SQL 托管实例、SQL 数据库、Azure Synapse SQL 和 SQL Server 实例。 
- 考虑在 Azure SQL 托管实例和 Azure 虚拟机上的 SQL Server (SQL VM) 之间创建链接服务器。  然后在 SQL VM 和 Oracle 或 Sybase 等服务器之间创建链接服务器。此方法确实涉及两个跃点，但可用作临时解决方法。  
- 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息：[Azure SQL 托管实例中的链接服务器差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>合并作业<a id="MergeJob"></a>

标题：合并作业步骤在 Azure SQL 托管实例中不受支持。   
**类别**：警告   

**描述**   
这是用于激活复制合并代理的作业步骤。 复制合并代理是一个将数据库表中保存的初始快照应用于订阅服务器的实用工具可执行文件。 它还合并自初始快照创建后发布服务器上发生的增量数据更改，并根据配置的规则或通过使用创建的自定义冲突解决程序来协调冲突。 合并作业步骤在 Azure SQL 托管实例中不受支持。


建议   
检查 Azure Migrate 中的“受影响的对象”部分，查看所有使用合并作业步骤的作业，并评估是否可以删除作业步骤或受影响的对象。 或者，迁移到 Azure 虚拟机上的 SQL Server

详细信息：[Azure SQL 托管实例中的 SQL Server 代理差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>MI 数据库大小<a id="MIDatabaseSize<"></a>

标题：Azure SQL 托管实例不支持超过 8 TB 的数据库大小。   
类别：问题   

**描述**   
数据库大小超过实例的最大保留存储空间。 不能选择此数据库进行迁移，因为大小超出了允许的限制。


建议   
评估数据是否可以存档、压缩或分片到多个数据库中。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息：[Azure SQL 托管实例的硬件代系特性](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>MI 实例大小<a id="MIInstanceSize<"></a>

标题：Azure SQL 托管实例中的实例最大存储大小不能超过 8 TB。   
**类别**：警告   

**描述**   
所有数据库的总大小超过实例的最大保留存储空间。  


建议   
如果所有数据库都必须在同一个实例上，请考虑将数据库迁移到其他 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server。 

详细信息：[Azure SQL 托管实例的硬件代系特性](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>多个日志文件<a id="MultipleLogFiles<"></a>

标题：Azure SQL 托管实例不支持多个日志文件。   
类别：问题   

**描述**   
SQL Server 允许数据库记录到多个文件。 此数据库具有多个日志文件，但 Azure SQL 托管实例不支持多个日志文件。 **无法迁移此数据库，因为无法在 Azure SQL 托管实例上还原备份。 
**

建议   
Azure SQL 托管实例仅支持每个数据库一个日志。 将此数据库迁移到 Azure 之前，需要保留一个日志文件而删除其余所有日志文件： 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

详细信息：[Azure SQL 托管实例中不支持的数据库选项](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>Next 列<a id="NextColumn"></a>

标题：名为 NEXT 的表和列将导致 Azure SQL 托管实例发生错误。   
类别：问题   

**描述**   
检测到名为 NEXT 的表或列。 Microsoft SQL Server 中引入的序列使用 ANSI 标准 NEXT VALUE FOR 功能。 如果表或列名为 NEXT 且该列的别名为 VALUE，在省略 ANSI 标准 AS 的情况下，所得到的语句可能会导致错误。


建议   
在设置表或列的别名时重写语句以包含 ANSI 标准 AS 关键字。 例如，当列名为 NEXT 且该列的别名为 VALUE 时，查询 SELECT NEXT VALUE FROM TABLE 将导致发生错误，并且应重新编写为 SELECT NEXT AS VALUE FROM TABLE。 例如，当表名为 NEXT 且该表的别名为 VALUE 时，查询 SELECT Col1 FROM NEXT VALUE 将导致发生错误，并且应重新编写为 SELECT Col1 FROM NEXT AS VALUE。



## <a name="non-ansi-style-left-outer-join"></a>非 ANSI 样式左外部联接<a id="NonANSILeftOuterJoinSyntax"></a>

标题：非 ANSI 样式左外部联接已弃用，并且已被删除。   
**类别**：警告   

**描述**   
在 Azure SQL 托管实例中，非 ANSI 样式左外部联接已弃用，并且已被删除。 


建议   
使用 ANSI 联接语法。

详细信息：[SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>非 ANSI 样式右外部联接<a id="NonANSIRightOuterJoinSyntax"></a>

标题：非 ANSI 样式右外部联接已弃用，并且已被删除。   
**类别**：警告   

**描述**   
在 Azure SQL 托管实例中，非 ANSI 样式右外部联接已弃用，并且已被删除。 



详细信息：[SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

建议   
使用 ANSI 联接语法。

## <a name="databases-exceed-100"></a>数据库数目超过 100 个<a id="NumDbExceeds100"></a>

标题：每个 Azure SQL 托管实例最多支持 100 个数据库。   
**类别**：警告   

**描述**   
除非已达到实例存储大小限制，否则 Azure SQL 托管实例支持的最大数据库数目为 100 个。



建议   
如果所有数据库都必须在同一个实例上，请考虑将数据库迁移到其他 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server。 

详细信息：[Azure SQL 托管实例资源限制](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET（非 Blob 数据源）<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

标题：Azure SQL 托管实例不支持将批量操作中使用的 OpenRowSet 用于非 Azure Blob 存储数据源。   
类别：问题   

**描述**   
OPENROWSET 通过内置的 BULK 提供程序支持批量操作，该提供程序使文件中的数据可被读取并作为行集返回。 Azure SQL 托管实例不支持将 OPENROWSET 用于非 Azure Blob 存储数据源。 

建议   
由于 Azure SQL 托管实例无法访问文件共享和 Windows 文件夹，因此必须从 Azure Blob 存储导入文件。 因此，OPENROWSET 函数中仅支持 DATASOURCE Blob 类型。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息：[Azure SQL 托管实例中 Bulk Insert 与 OPENROWSET 之间的差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET（非 SQL 提供程序）<a id="OpenRowsetWithNonSQLProvider"></a>

标题：Azure SQL 托管实例不支持将 OpenRowSet 用于非 SQL 提供程序。   
类别：问题   

**描述**   
当访问链接服务器中的表时，这种方法是一种替代方法，并且是一种使用 OLE DB 连接并访问远程数据的一次性的临时方法。 Azure SQL 托管实例不支持将 OpenRowSet 用于非 SQL 提供程序。 



建议   
OPENROWSET 函数只能用于对 SQL Server 实例（托管实例、本地实例或虚拟机中的实例）执行查询。 仅支持将值 SQLNCLI、SQLNCLI11 和 SQLOLEDB 用作提供程序。 因此，建议的操作是确定哪些从属数据库属于非 SQL 远程服务器，并考虑将它们移到要迁移的数据库中。

详细信息：[Azure SQL 托管实例中 Bulk Insert 与 OPENROWSET 之间的差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>PowerShell 作业<a id="PowerShellJob"></a>

标题：PowerShell 作业步骤在 Azure SQL 托管实例中不受支持。   
**类别**：警告   

**描述**   
这是运行 PowerShell 脚本的作业步骤。 PowerShell 作业步骤在 Azure SQL 托管实例中不受支持。 



建议   
检查 Azure Migrate 中的“受影响的对象”部分，查看所有使用 PowerShell 作业步骤的作业，并评估是否可以删除作业步骤或受影响的对象。  评估是否可以使用 Azure 自动化。 或者，迁移到 Azure 虚拟机上的 SQL Server

详细信息：[Azure SQL 托管实例中的 SQL Server 代理差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>队列读取器作业<a id="QueueReaderJob"></a>

标题：队列读取器作业步骤在 Azure SQL 托管实例中不受支持。   
**类别**：警告   

**描述**   
这是用于激活复制队列读取器代理的作业步骤。 复制队列读取器代理是一个可执行文件，用于读取存储在 Microsoft SQL Server 队列或 Microsoft 消息队列中的消息，然后将这些消息应用于发布服务器。 队列读取器代理与允许排队更新的快照发布和事务发布一起使用。 队列读取器作业步骤在 Azure SQL 托管实例中不受支持。


建议   
检查 Azure Migrate 中的“受影响的对象”部分，查看所有使用队列读取器作业步骤的作业，并评估是否可以删除作业步骤或受影响的对象。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息：[Azure SQL 托管实例中的 SQL Server 代理差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

标题：应将旧式的 RAISERROR 调用替换为等效的新式调用。   
**类别**：警告   

**描述**   
类似于以下示例的 RAISERROR 调用称为旧式，因为它们不包含逗号和括号。 RAISERROR 50001 'this is a test'. 在 Azure SQL 托管实例中，RAISERROR 的这种调用方法已弃用，并且已被删除。



建议   
使用当前的 RAISERROR 语法重新编写语句，或评估 `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` 的新式方法是否可行。

详细信息：[SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

标题：SQL Mail 已弃用。   
**类别**：警告   


**描述**   
在 Azure SQL 托管实例中，SQL Mail 已弃用，并且已被删除。



建议   
使用数据库邮件。

详细信息：[SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


标题：检测到引用已删除的系统存储过程的语句，这些系统存储过程在 Azure SQL 托管实例中不可用。   
**类别**：警告   

**描述**   
今后不能在 Azure SQL 托管实例中使用不受支持的系统存储过程和扩展存储过程 - `sp_dboption`、`sp_addserver`、`sp_dropalias`、`sp_activedirectory_obj`、`sp_activedirectory_scp` 和 `sp_activedirectory_start`。 




建议   
删除对不受支持并且已从 Azure SQL Managed托管实例中删除的系统存储过程的引用。

详细信息：[SQL Server 中弃用的数据库引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Transact-SQL 作业<a id="TransactSqlJob"></a>

标题：TSQL 作业步骤包含 Azure SQL 托管实例不支持的命令   
**类别**：警告   


**描述**   
这是在计划的时间运行 TSQL 脚本的作业步骤。 TSQL 作业步骤包含 Azure SQL 托管实例不支持的命令。



建议   
检查 Azure Migrate 中的“受影响的对象”部分，查看所有包含 Azure SQL 托管实例不支持的命令的作业，并评估是否可以删除作业步骤或受影响的对象。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息：[Azure SQL 托管实例中的 SQL Server 代理差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>跟踪标志<a id="TraceFlags"></a>

标题：找到 Azure SQL 托管实例不支持的跟踪标志   
**类别**：警告   


**描述**   
Azure SQL 托管实例仅支持有限数量的全局跟踪标志， 不支持会话跟踪标志。



建议   
检查 Azure Migrate 中的“受影响的对象”部分，查看 Azure SQL 托管实例不支持的所有跟踪标志，并评估是否可以删除这些跟踪标志。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息：[跟踪标志](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Windows 身份验证<a id="WindowsAuthentication"></a>

标题：通过 Windows 身份验证映射的数据库用户（集成安全性）在 Azure SQL 托管实例中不受支持   
**类别**：警告   


**描述**   
Azure SQL 托管实例支持以下两种类型的身份验证： 
- SQL 身份验证，使用用户名和密码
- Azure Active Directory 身份验证，使用 Azure Active Directory 管理的标识，支持托管域和集成域。 

通过 Windows 身份验证映射的数据库用户（集成安全性）在 Azure SQL 托管实例中不受支持。 


建议   
将本地 Active Directory 与 Azure Active Directory 联合。 然后，可以将 Windows 标识替换为等效的 Azure Active Directory 标识。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息：[SQL 托管实例安全功能](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

标题：xp_cmdshell 在 Azure SQL 托管实例中不受支持。   
类别：问题   


**描述**   
xp_cmdshell 会生成 Windows 命令 shell 并传入要执行的字符串，它在 Azure SQL 托管实例中不受支持。 



建议   
检查 Azure Migrate 中的“受影响的对象”部分，查看所有使用 xp_cmdshell 的作业，并评估是否可以删除对 xp_cmdshell 的引用或受影响的对象。 考虑了解 Azure 自动化，它提供基于云的自动化和配置服务。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息：[Azure SQL 托管实例中的存储过程差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>后续步骤

若要开始将 SQL Server 迁移到 Azure SQL 托管实例，请参阅 [SQL Server 到 SQL 托管实例的迁移指南](sql-server-to-managed-instance-guide.md)。

- 有关在执行各种数据库和数据迁移方案及专门任务时可为你提供帮助的 Microsoft 与第三方服务和工具的矩阵，请参阅[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

- 若要详细了解 Azure SQL 托管实例，请参阅：
   - [Azure SQL 托管实例中的服务层级](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server 与 Azure SQL 托管实例之间的差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 


- 有关云迁移的框架和采用周期的详细信息，请参阅
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负载计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 若要评估应用程序访问层，请参阅 [Data Access Migration Toolkit（预览版）](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 若要详细了解如何执行数据访问层 A/B 测试，请参阅[数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。
