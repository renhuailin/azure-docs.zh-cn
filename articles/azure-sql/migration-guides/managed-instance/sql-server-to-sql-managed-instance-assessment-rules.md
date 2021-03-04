---
title: SQL Server SQL 托管实例迁移的评估规则
description: 评估规则，用于确定在迁移到 Azure SQL 托管实例之前必须解决的源 SQL Server 实例的问题。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: 760a6496ff297ae6328810589f780b430d55b18a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054500"
---
# <a name="assessment-rules-for-sql-server-to-sql-managed-instance-migration"></a>SQL Server SQL 托管实例迁移的评估规则
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

迁移工具通过运行大量评估规则来验证你的源 SQL Server 实例，以确定在将 SQL Server 数据库迁移到 Azure SQL 托管实例之前必须解决的问题。 

本文提供了用于评估将 SQL Server 数据库迁移到 Azure SQL 托管实例的可行性的规则列表。 

## <a name="analysiscommand-job"></a>AnalysisCommand 作业<a id="AnalysisCommandJob"></a>

**标题： Azure SQL 托管实例中不支持 "AnalysisCommand 作业" 步骤。**   
**类别**：警告   


**描述**   
这是运行 Analysis Services 命令的作业步骤。 Azure SQL 托管实例中不支持 AnalysisCommand 作业步骤。

**推荐**     
查看 Azure Migrate 中的 "受影响的对象" 部分，查看使用 Analysis Service 命令作业步骤的所有作业，并评估是否可以删除作业步骤或受影响的对象。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [SQL Server 代理 AZURE SQL 中的差异托管实例 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>AnalysisQuery 作业<a id="AnalysisQueryJob"></a>

**标题： Azure SQL 托管实例中不支持 "AnalysisQuery 作业" 步骤。**   
**类别**：警告   

**描述**   
这是运行 Analysis Services 查询的作业步骤。 Azure SQL 托管实例中不支持 AnalysisQuery 作业步骤。


**推荐**   
查看 Azure Migrate 中的 "受影响的对象" 部分，查看使用 Analysis Service 查询作业步骤的所有作业，并评估是否可以删除作业步骤或受影响的对象。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [SQL Server 代理 AZURE SQL 中的差异托管实例 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>文件中的程序集<a id="AssemblyFromFile"></a>

**标题： Azure SQL 托管实例中不支持带有文件参数的 "CREATE ASSEMBLY" 和 "ALTER ASSEMBLY"。**   
**类别**：警告   

**描述**   
Azure SQL 托管实例无法访问文件共享或 Windows 文件夹。 请参阅 "受影响的对象" 部分，了解未引用 Azure blob BULK INSERT 语句的特定用法。 使用 "BULK INSERT" 的对象（源不是 Azure blob 存储）将无法在迁移到 Azure SQL 托管实例后工作。


**推荐**   
迁移到 Azure SQL 托管实例时，需要将使用本地文件或文件共享的 BULK INSERT 语句转换为使用 Azure blob 存储中的文件。 或者，迁移到 Azure 虚拟机上的 SQL Server。 

详细信息： [AZURE SQL 中的 CLR 差异托管实例 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>大容量插入<a id="BulkInsert"></a>

**标题： Azure SQL 托管实例中不支持与非 Azure blob 数据源 BULK INSERT。**   
**类别**：问题   

**描述**   
Azure SQL 托管实例无法访问文件共享或 Windows 文件夹。 请参阅 "受影响的对象" 部分，了解未引用 Azure blob BULK INSERT 语句的特定用法。 使用 "BULK INSERT" 的对象（源不是 Azure blob 存储）将无法在迁移到 Azure SQL 托管实例后工作。


**推荐**   
迁移到 Azure SQL 托管实例时，需要将使用本地文件或文件共享的 BULK INSERT 语句转换为使用 Azure blob 存储中的文件。

详细信息： [AZURE SQL 托管实例中的大容量插入和 OPENROWSET 差异 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>CLR 安全性<a id="ClrStrictSecurity"></a>

**标题：标记为 SAFE 或 EXTERNAL_ACCESS 的 CLR 程序集被视为不安全**   
**类别**：问题   

**描述**   
CLR 严格安全模式是在 Azure SQL 托管实例中强制执行的。 此模式默认处于启用状态，并为包含用户定义的、包含标记为 SAFE 或 EXTERNAL_ACCESS 的 CLR 程序集的数据库引入了重大更改。


**推荐**   
CLR 在 .NET Framework 中使用代码访问安全性 (CAS)（不可再作为安全边界）。 从 SQL Server 2017 (4.x) 数据库引擎开始，引入了一个 `sp_configure` 称为 clr strict security 的选项，以增强 clr 程序集的安全性。 默认情况下，将启用 clr 严格安全性，并将安全和 EXTERNAL_ACCESS CLR 程序集视为不安全。 禁用 clr strict security 后，使用 PERMISSION_SET = SAFE 创建的 CLR 程序集可以访问外部系统资源、调用非托管代码以及获取 sysadmin 特权。 启用严格安全性后，未签名的任何程序集都将加载失败。 此外，如果数据库具有安全或 EXTERNAL_ACCESS 的程序集，则 RESTORE 或 ATTACH 数据库语句可以完成，但程序集可能无法加载。 若要加载程序集，您必须更改或删除并重新创建每个程序集，以便使用在服务器上具有具有 UNSAFE ASSEMBLY 权限的相应登录名的证书或非对称密钥对程序集进行签名。

详细信息： [CLR 严格安全性](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>Compute 子句<a id="ComputeClause"></a>

**Title：计算子句已终止，已被删除。**   
**类别**：警告   

**描述**   
计算子句会生成在结果集末尾作为附加摘要列显示的总计。 但是，在 Azure SQL 托管实例中不再支持此子句。



**推荐**   
T-sql 模块需要改用 ROLLUP 运算符进行重写。 下面的代码演示如何将计算替换为汇总： 

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

详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>加密提供程序<a id="CryptographicProvider"></a>

**标题：发现使用 CREATE 加密提供程序或 ALTER 加密提供程序，这在 Azure SQL 托管实例中不受支持。**   
**类别**：问题   

**描述**   
Azure SQL 托管实例不支持加密提供程序语句，因为它无法访问文件。 有关加密提供程序语句的特定用法，请参阅受影响的对象部分。 迁移到 Azure SQL 托管实例后，具有 "创建加密提供程序" 或 "ALTER 加密提供程序" 的对象将无法正常工作。


**推荐**   
用 "创建加密提供程序" 或 "更改加密提供程序" 查看对象。 在所需的任何此类对象中，删除这些功能的使用。 或者，迁移到 Azure 虚拟机上的 SQL Server。 

详细信息： [AZURE SQL 托管实例中的加密提供程序差异 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>数据库兼容性<a id="DbCompatLevelLowerThan100"></a>

**标题：不支持低于100的数据库兼容级别**   
**类别**：警告   

**描述**   
数据库兼容性级别是一项非常有用的工具，可通过允许升级 SQL Server 数据库引擎来升级数据库现代化，同时保持连接应用程序的功能状态，同时保持相同的升级前数据库兼容性级别。 Azure SQL 托管实例不支持低于100的兼容级别。 如果在 Azure SQL 托管实例上还原兼容级别低于100的数据库，则兼容级别将升级到100。 


**建议**.。。评估 Azure SQL 托管实例上的数据库兼容级别升级为100时，应用程序的功能是否完整。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [AZURE SQL 托管实例中支持的兼容级别 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>数据库主体别名<a id="DatabasePrincipalAlias"></a>

**标题： SYS。DATABASE_PRINCIPAL_ALIASES 已不再使用，已被删除。**   
**类别**：问题   

**描述**   
系统.DATABASE_PRINCIPAL_ALIASES 中止，已在 Azure SQL 托管实例中删除。


**推荐**   
请使用角色而不是别名。

详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK 选项<a id="DisableDefCNSTCHK"></a>

**标题： SET 选项 DISABLE_DEF_CNST_CHK 已停止使用，已被删除。**   
**类别**：问题   

**描述**   
SET 选项 DISABLE_DEF_CNST_CHK 已停止，并已在 Azure SQL 托管实例中删除。


详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW 提示<a id="FastFirstRowHint"></a>

**Title： FASTFIRSTROW 查询提示已停止使用，已将其删除。**   
**类别**：警告   

**描述**   
FASTFIRSTROW 查询提示已停止使用，已在 Azure SQL 托管实例中删除。


**推荐**   
 (FAST n) ，而不是使用 FASTFIRSTROW 查询提示使用选项。

详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**在 Azure SQL 托管实例中不支持 Title： Filestream 和 Filetable。**   
**类别**：问题   

**描述**   
Azure SQL 托管实例不支持通过 Filestream 功能在 NTFS 文件系统中存储非结构化数据（例如文本文档、图像和视频）。 **无法迁移此数据库，因为无法在 Azure SQL 托管实例上还原包含 Filestream 文件组的备份。**


**推荐**   
将非结构化文件上传到 Azure Blob 存储，并在 Azure SQL 托管实例中存储与这些文件相关的元数据 (名称、类型、URL 位置、存储密钥等 ) 。 可能需要对应用程序重新进行工程处理，使其能够与 Azure SQL 托管实例进行流式处理。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [SQL Azure 博客流式传输 blob](https://azure.microsoft.com/en-in/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>异类 MS DTC<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**标题： Azure SQL 托管实例不支持在非 SQL Server 远程服务器上开始分布式事务。**   
**类别**：问题   

**描述**   
如果未 SQL Server 远程服务器，则通过 Transact-sql BEGIN DISTRIBUTED TRANSACTION 和 Microsoft 分布式事务处理协调器管理的分布式事务将不托管实例支持 (MS DTC) 。 


**推荐**   
查看 Azure Migrate 中的受影响对象部分，以查看使用 BEGIN 分布式 TRANSACTION 的所有对象。 请考虑将参与者数据库迁移到 Azure SQL 托管实例，其中支持跨多个实例的分布式事务 (当前在预览版中) 。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [AZURE SQL 托管实例的多个服务器之间的事务 ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>同源 MS DTC<a id="MIHomogeneousMSDTCTransactSQL"></a>

**标题： Azure SQL 托管实例的多个服务器支持 "开始分布式事务"。**   
**类别**：问题   

**描述**   
通过使用 SQL BEGIN DISTRIBUTED TRANSACTION 启动的分布式事务，并由 Microsoft 分布式事务处理协调器管理 (MS DTC) 跨多个服务器支持 Azure SQL 托管实例。


**推荐**   
查看 Azure Migrate 中的受影响对象部分，以查看使用 BEGIN 分布式 TRANSACTION 的所有对象。 请考虑将参与者数据库迁移到 Azure SQL 托管实例，其中支持跨多个实例的分布式事务 (当前在预览版中) 。 或者，迁移到 Azure 虚拟机上的 SQL Server。 

详细信息： [AZURE SQL 托管实例的多个服务器之间的事务](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>链接服务器 (非 SQL 提供程序) <a id="LinkedServerWithNonSQLProvider"></a>

**标题： Azure SQL 托管实例不支持具有非 SQL Server 提供程序的链接服务器。**   
**类别**：问题   

**描述**   
链接服务器使 SQL Server 数据库引擎对 SQL Server 的实例以外的 OLE DB 数据源执行命令。 Azure SQL 托管实例不支持具有非 SQL Server 提供程序的链接服务器。 


**推荐**   
如果远程服务器提供程序不 SQL Server （例如 Oracle、Sybase 等），则 Azure SQL 托管实例不支持链接服务器功能。 

为了消除对链接服务器的需求，建议执行以下操作： 
- 确定远程非 SQL server (s) 的从属数据库，并考虑将它们移到要迁移的数据库中。 
- 将依赖数据库 () 迁移到受支持的目标，如 SQL 托管实例、SQL 数据库、Azure Synapse SQL 和 SQL Server 实例。 
- 请考虑在 azure SQL 托管实例之间创建链接服务器，并在 Azure 虚拟机 (SQL VM) 中创建 SQL Server。  然后从 SQL VM 创建链接服务器到 Oracle、Sybase 等。此方法确实涉及两个跃点，但可用作临时解决方法。  
- 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [AZURE SQL 中的链接服务器差异托管实例](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>合并作业<a id="MergeJob"></a>

**标题： Azure SQL 托管实例中不支持 "合并作业" 步骤。**   
**类别**：警告   

**描述**   
这是激活复制合并代理的作业步骤。 复制合并代理是一个将数据库表中保存的初始快照应用于订阅服务器的实用工具可执行文件。 它还合并自初始快照创建后发布服务器上发生的增量数据更改，并根据配置的规则或通过使用创建的自定义冲突解决程序来协调冲突。 Azure SQL 托管实例中不支持合并作业步骤。


**推荐**   
查看 Azure Migrate 中的 "受影响的对象" 部分以查看使用合并作业步骤的所有作业，并评估是否可以删除作业步骤或受影响的对象。 或者，迁移到 Azure 虚拟机上的 SQL Server

详细信息： [SQL Server 代理 AZURE SQL 中的差异托管实例 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>MI 数据库大小<a id="MIDatabaseSize<"></a>

**标题： Azure SQL 托管实例不支持大于 8 TB 的数据库大小。**   
**类别**：问题   

**描述**   
数据库的大小大于实例保留存储的最大值。 **由于大小超出了允许的限制，因此无法选择此数据库进行迁移。**


**推荐**   
评估数据是否可以压缩或分片到多个数据库中。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [AZURE SQL 托管实例的硬件生成特征 ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>MI 实例大小<a id="MIInstanceSize<"></a>

**标题： Azure SQL 托管实例中的实例存储大小上限不能大于 8 TB。**   
**类别**：警告   

**描述**   
所有数据库的大小都大于实例保留存储的最大值。  


**推荐**   
如果所有数据库都必须在同一实例上，请考虑将数据库迁移到不同的 Azure SQL 托管实例，或迁移到 Azure 虚拟机上的 SQL Server。 

详细信息： [AZURE SQL 托管实例的硬件生成特征 ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>多个日志文件<a id="MultipleLogFiles<"></a>

**标题： Azure SQL 托管实例不支持多个日志文件。**   
**类别**：问题   

**描述**   
SQL Server 允许数据库记录到多个文件。 此数据库有多个在 Azure SQL 托管实例中不受支持的日志文件。 * * 无法迁移此数据库，因为无法在 Azure SQL 托管实例上还原备份。 
**

**推荐**   
Azure SQL 托管实例仅支持每个数据库一个日志。 在将此数据库迁移到 Azure 之前，需要删除所有其他日志文件： 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

详细信息： [AZURE SQL 托管实例中不支持的数据库选项  ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>下一列<a id="NextColumn"></a>

**Title：名为 NEXT 的表和列将导致 Azure SQL 托管实例出错。**   
**类别**：问题   

**描述**   
检测到名为 NEXT 的表或列。 Microsoft SQL Server 中引入的序列使用 ANSI 标准 NEXT VALUE FOR 函数。 如果表或列名为 NEXT 且该列的别名为 VALUE，在省略 ANSI 标准 AS 的情况下，所得到的语句可能会导致错误。


**推荐**   
在设置表或列的别名时重写语句以包含 ANSI 标准 AS 关键字。 例如，当列命名为 NEXT 并且该列的别名为 VALUE 时，查询从表中选择下一个值将导致错误，并且应重新编写为从表中选择 "下一步" 作为值。 同样，如果将表命名为 NEXT，并且该表的别名为 VALUE，则从 NEXT 值中选择 Col1 将导致错误，应将其重写为 SELECT Col1 FROM FROM 作为值。



## <a name="non-ansi-style-left-outer-join"></a>非 ANSI 样式左外部联接<a id="NonANSILeftOuterJoinSyntax"></a>

**Title：非 ANSI 样式左外部联接已停止使用并且已被移除。**   
**类别**：警告   

**描述**   
非 ANSI 样式左外部联接已停止，并已在 Azure SQL 托管实例中删除。 


**推荐**   
使用 ANSI 联接语法。

详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>非 ANSI 样式右外部联接<a id="NonANSIRightOuterJoinSyntax"></a>

**标题：非 ANSI 样式右外部联接已停止使用，已被删除。**   
**类别**：警告   

**描述**   
非 ANSI 样式右外部联接已停止，已在 Azure SQL 托管实例中删除。 



详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

**推荐**   
使用 ANSI 联接语法。

## <a name="databases-exceed-100"></a>数据库超过100 <a id="NumDbExceeds100"></a>

**标题： Azure SQL 托管实例每个实例最多支持100数据库。**   
**类别**：警告   

**描述**   
Azure SQL 托管实例中支持的最大数据库数为100，除非已达到实例存储大小限制。



**推荐**   
如果所有数据库都必须在同一实例上，请考虑将数据库迁移到不同的 Azure SQL 托管实例，或迁移到 Azure 虚拟机上的 SQL Server。 

详细信息： [AZURE SQL 托管实例资源限制 ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET (非 blob 数据源) <a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**标题： Azure SQL 托管实例中不支持使用非 Azure blob 存储数据源在大容量操作中使用的 OpenRowSet。**   
**类别**：问题   

**描述**   
OPENROWSET 通过内置的批量提供程序支持大容量操作，该提供程序允许以行集的形式读取和返回文件中的数据。 Azure SQL 托管实例中不支持具有非 Azure blob 存储数据源的 OPENROWSET。 



**推荐**   
OPENROWSET 函数可用来仅对 (在托管、本地或虚拟机) 的 SQL Server 实例执行查询。 仅支持将 SQLNCLI.MSI、SQLNCLI11 和 SQLOLEDB 值作为提供程序。 因此，建议的操作是确定从远程非 SQL Server (s) 的从属数据库，并考虑将它们移到正在迁移的数据库中。 或者，迁移到 Azure 虚拟机上的 SQL Server

详细信息： [AZURE SQL 托管实例中的大容量插入和 OPENROWSET 差异 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET (非 SQL 提供程序) <a id="OpenRowsetWithNonSQLProvider"></a>

**标题： Azure SQL 托管实例中不支持带有非 SQL 提供程序的 OpenRowSet。**   
**类别**：问题   

**描述**   
当访问链接服务器中的表时，这种方法是一种替代方法，并且是一种使用 OLE DB 连接并访问远程数据的一次性的临时方法。 Azure SQL 托管实例中不支持带有非 SQL 提供程序的 OpenRowSet。 



**推荐**   
OPENROWSET 函数可用来仅对 (在托管、本地或虚拟机) 的 SQL Server 实例执行查询。 仅支持将 SQLNCLI.MSI、SQLNCLI11 和 SQLOLEDB 值作为提供程序。 因此，建议的操作是确定从远程非 SQL Server (s) 的从属数据库，并考虑将它们移到正在迁移的数据库中。

详细信息： [AZURE SQL 托管实例中的大容量插入和 OPENROWSET 差异 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>PowerShell 作业<a id="PowerShellJob"></a>

**标题： Azure SQL 托管实例中不支持 PowerShell 作业步骤。**   
**类别**：警告   

**描述**   
这是运行 PowerShell 脚本的作业步骤。 Azure SQL 托管实例中不支持 PowerShell 作业步骤。 



**推荐**   
查看 Azure Migrate 中的 "受影响的对象" 部分，查看使用 PowerShell 作业步骤的所有作业，并评估是否可以删除作业步骤或受影响的对象。  评估是否可以使用 Azure Automation。 或者，迁移到 Azure 虚拟机上的 SQL Server

详细信息： [SQL Server 代理 AZURE SQL 中的差异托管实例 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>队列读取器作业<a id="QueueReaderJob"></a>

**标题： Azure SQL 托管实例中不支持队列读取器作业步骤。**   
**类别**：警告   

**描述**   
这是激活复制队列读取器代理的作业步骤。 复制队列读取器代理是一个可执行文件，它读取存储在 Microsoft SQL Server 队列或 Microsoft 消息队列中的消息，然后将这些消息应用于发布服务器。 队列读取器代理与允许排队更新的快照发布和事务发布一起使用。 Azure SQL 托管实例中不支持队列读取器作业步骤。


**推荐**   
查看 Azure Migrate 中的 "受影响的对象" 部分，查看使用队列读取器作业步骤的所有作业，并评估是否可以删除作业步骤或受影响的对象。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [SQL Server 代理 AZURE SQL 中的差异托管实例 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**标题：旧样式 RAISERROR 调用应替换为新式等效项。**   
**类别**：警告   

**描述**   
类似于以下示例的 RAISERROR 调用称为旧样式，因为它们不包含逗号和括号。 RAISERROR 50001 "这是一个测试"。 此调用 RAISERROR 的方法在 Azure SQL 托管实例中已停止并被删除。



**推荐**   
使用当前的 RAISERROR 语法重写语句，或评估的新式方法 `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` 是否可行。

详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="service-broker"></a>Service broker<a id="ServiceBrokerWithNonLocalAddress"></a>

**标题： Azure SQL 托管实例部分支持 Service Broker 功能。**   
**类别**：问题   

**描述**   
SQL Server Service Broker 在 SQL Server 数据库引擎中为消息和队列应用程序提供本机支持。 此数据库已启用跨实例 Service Broker，这在 Azure SQL 托管实例中不受支持。 


**推荐**   
Azure SQL 托管实例不支持跨实例服务代理，即，地址不是本地的。 在将此数据库迁移到 Azure 之前，需要使用以下命令禁用 Service Broker： `ALTER DATABASE [database_name] SET DISABLE_BROKER` ;此外，还可能需要删除或停止 Service Broker 终结点，以防止消息到达 SQL 实例。 将数据库迁移到 Azure 后，可以查看 Azure 服务总线功能，以实现基于云的通用消息传递系统，而不是 Service Broker。 或者，迁移到 Azure 虚拟机上的 SQL Server。 

详细信息： [Service Broker AZURE SQL 中的差异托管实例 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker)

## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**标题： SQL Mail 已停止使用。**   
**类别**：警告   


**描述**   
SQL Mail 已不再使用，并已在 Azure SQL 托管实例中删除。



**推荐**   
使用数据库邮件。

详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**Title：检测到的语句引用的删除的系统存储过程在 Azure SQL 托管实例中不可用。**   
**类别**：警告   

**描述**   
以下不受支持的系统和扩展存储过程不能用于 Azure SQL 托管实例- `sp_dboption` 、、、、 `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` 和 `sp_activedirectory_start` 。 




**推荐**   
删除对 Azure SQL 托管实例中已删除的不受支持的系统过程的引用。

详细信息： [废止数据库引擎功能 SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Transact-sql 作业<a id="TransactSqlJob"></a>

**Title： TSQL 作业步骤在 Azure SQL 托管实例中包含不受支持的命令**   
**类别**：警告   


**描述**   
这是一个在计划时间运行 TSQL 脚本的作业步骤。 TSQL 作业步骤包括 Azure SQL 托管实例不支持的命令。



**推荐**   
查看 Azure Migrate 中的 "受影响的对象" 部分，查看在 Azure SQL 托管实例中包含不受支持的命令的所有作业，并评估是否可以删除作业步骤或受影响的对象。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [SQL Server 代理 AZURE SQL 中的差异托管实例 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>跟踪标志<a id="TraceFlags"></a>

**标题：找不到 Azure SQL 托管实例中不支持的跟踪标志**   
**类别**：警告   


**描述**   
Azure SQL 托管实例仅支持有限数量的全局跟踪标志。 不支持会话跟踪标志。



**推荐**   
查看 Azure Migrate 中的 "受影响的对象" 部分以查看 Azure SQL 托管实例不支持的所有跟踪标志，并评估它们是否可以删除。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [跟踪标志](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Windows 身份验证<a id="WindowsAuthentication"></a>

**标题：通过 Windows 身份验证映射的数据库用户 (集成安全性) 在 Azure SQL 中不受支持托管实例**   
**类别**：警告   


**描述**   
Azure SQL 托管实例支持两种类型的身份验证： 
- 使用用户名和密码的 SQL 身份验证
- Azure Active Directory 身份验证，使用 Azure Active Directory 管理的标识，支持托管域和集成域。 

Azure SQL 托管实例不支持通过 Windows 身份验证映射的数据库用户 (集成安全性) 。 


**推荐**   
将本地 Active Directory 与 Azure Active Directory 联合。 然后，可以将 Windows 标识替换为等效的 Azure Active Directory 标识。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [SQL 托管实例安全功能](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**标题： Azure SQL 托管实例不支持 xp_cmdshell。**   
**类别**：问题   


**描述**   
Azure SQL 托管实例不支持生成 Windows 命令 shell 并传入要执行的字符串 Xp_cmdshell。 



**推荐**   
查看 Azure Migrate 中的 "受影响的对象" 部分以查看使用 xp_cmdshell 的所有对象，并评估是否可以删除对 xp_cmdshell 或受影响的对象的引用。 请考虑探索提供基于云的自动化和配置服务的 Azure 自动化。 或者，迁移到 Azure 虚拟机上的 SQL Server。

详细信息： [AZURE SQL 中的存储过程差异托管实例](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

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