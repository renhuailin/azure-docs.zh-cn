---
title: 专用 SQL 池的数据加载最佳做法
description: 有关将数据加载到 Azure Synapse Analytics 中的专用 SQL 池的建议和性能优化。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 08/26/2021
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: ee3be53c6a52f0bc0a8ceab0424a7a6c99a0f441
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539580"
---
# <a name="best-practices-for-loading-data-into-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>有关将数据加载到 Azure Synapse Analytics 中的专用 SQL 池的最佳做法

在本文中，你将了解有关加载数据的建议和性能优化。

## <a name="prepare-data-in-azure-storage"></a>在 Azure 存储中准备数据

若要尽量减少延迟，请将你的存储层级和专用 SQL 池置于一起。

将数据导出为 ORC 文件格式时，如果存在较大的文本列，可能会收到“Java 内存不足”错误。 若要解决此限制方面的问题，请仅导出列的一个子集。

PolyBase 无法加载数据大小超过 1,000,000 字节的行。 将数据置于 Azure Blob 存储或 Azure Data Lake Store 的文本文件中时，这些数据必须少于 1,000,000 字节。 无论表架构如何，都有此字节限制。

所有文件格式有不同的性能特征。 为实现最快加载，请使用压缩的分隔文本文件。 UTF-8 和 UTF-16 之间的性能差异是最小的。

将大型压缩文件拆分为较小的压缩文件。

## <a name="run-loads-with-enough-compute"></a>使用足够的计算资源运行负载

若要尽量提高加载速度，请一次只运行一个加载作业。 如果这不可行，请将同时运行的负载的数量降至最低。 如果预期的加载作业较大，可以考虑在加载前纵向扩展专用 SQL 池。

若要使用适当的计算资源运行负载，请创建指定运行负载的加载用户。 将每个加载用户分配给一个特定的资源类或工作负载组。 若要运行负载，请以某个加载用户的身份登录，然后运行该负载。 该负载使用用户的资源类运行。  与尝试根据当前的资源类需求更改用户的资源类相比，此方法更简单。


### <a name="create-a-loading-user"></a>创建加载用户

此示例将创建一个分类到特定工作负荷组的加载用户。 第一步是 **连接到主服务器** 并创建登录名。

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

请连接到专用 SQL 池并创建用户。 以下代码假定你连接到名为 mySampleDataWarehouse 的数据库。 它展示了如何创建一个名为“loader”的用户，并授予该用户使用 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)创建表和进行加载的权限。 然后，它将该用户分类到包含最多资源的 DataLoads 工作负荷组。 

```sql
   -- Connect to the dedicated SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
       MIN_PERCENTAGE_RESOURCE = 0
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```

<br><br>
>[!IMPORTANT] 
>这是将 SQL 池的 100% 资源分配给单个负载的极端示例。 这样，提供的最大并发数就会是 1。 请注意，这应该只用于初始加载，在这种情况下，你将需要创建带有其自己的配置的额外工作负荷组，以便在工作负荷之间均衡资源。 

若要使用加载工作负荷组的资源运行加载，请以 loader 身份登录并运行该加载。 

## <a name="allow-multiple-users-to-load"></a>允许多个用户进行加载

通常需要允许多个用户将数据加载到数据仓库中。 使用 [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) 进行加载需要数据库的“控制”权限。  “控制”权限允许对所有架构进行控制性访问。 可能不需要让所有加载用户都具有对所有架构的控制访问权限。 若要限制权限，请使用 DENY CONTROL 语句。

例如，考虑为部门 A 使用数据库架构 schema_A，为部门 B 使用 schema_B；让数据库用户 user_A 和 user_B 分别作为部门 A 和 B 中加载的 PolyBase 用户。 这些用户已被授予 CONTROL 数据库权限。 架构 A 和 B 的创建者现在使用 DENY 锁定其架构：

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

现在 user_A 和 user_B 被锁在其他部门的架构之外。

## <a name="load-to-a-staging-table"></a>加载到临时表

若要尽量提高将数据移到数据仓库表中的加载速度，请将数据加载到临时表中。  将临时表定义为堆，并将轮循机制用于分发选项。

可以认为，加载通常是一个两步的过程：首先将数据加载到临时表中，然后将数据插入生产数据仓库表中。 如果生产表使用哈希分发，在在使用哈希分发来定义临时表的情况下，加载和插入的总时间可能会更短。 加载到临时表需要的时间较长，但第二步将行插入到生产表中不会导致数据跨分布区移动。

## <a name="load-to-a-columnstore-index"></a>加载到列存储索引

列存储索引需要将数据压缩成高质量的行组，因此需要大量的内存。 若要最大程度地提高压缩和索引效率，列存储索引需将最多 1,048,576 行压缩到每个行组中。 存在内存压力时，列存储索引可能无法达到最大压缩率。 这会影响查询性能。 若要进行深入了解，请参阅[列存储内存优化](data-load-columnstore-compression.md)。

- 若要确保加载用户有足够的内存来实现最大压缩率，请使用属于中大型资源类的加载用户。
- 加载足够的行，以便完全填充新的行组。 在大容量加载期间，数据会以 1,048,576 行为一个完整的行组直接压缩到列存储中。 不到 102,400 行的加载会将行发送到增量存储中以 B 树索引的形式保存。 如果加载的行太少，这些行可能会全部进入增量存储中，不会立即压缩成列存储格式。

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>使用 SQLBulkCopy API 或 BCP 时增加批大小


使用 COPY 语句进行加载将为专用 SQL 池提供最高吞吐量。 如果无法使用 COPY 进行加载，并且必须使用 [SqLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 或 [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)，则应考虑增加批大小以提高吞吐量。

> [!TIP]
> 10 万行到 1 百万行之间的批大小是建议用于确定最佳批大小容量的基线。 

## <a name="manage-loading-failures"></a>管理加载失败

使用外部表的加载可能因“查询已中止 -- 从外部源读取时已达最大拒绝阈值”错误而失败。 此消息表示外部数据包含脏记录。 如果数据类型和列数目与外部表的列定义不匹配，或数据不符合指定的外部文件格式，则会将数据记录视为脏记录。

若要解决脏记录问题，请确保外部表和外部文件格式定义正确，并且外部数据符合这些定义。 如果部分外部数据记录已更改，可使用 [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true) 中的拒绝选项来选择拒绝查询这些记录。

## <a name="insert-data-into-a-production-table"></a>将数据插入到生产表中

可以使用 [INSERT 语句](/sql/t-sql/statements/insert-transact-sql?view=azure-sqldw-latest&preserve-view=true)将数据一次性加载到小型表中，甚至可以使用 `INSERT INTO MyLookup VALUES (1, 'Type 1')` 之类的语句定期重新加载某个查找。  但是，单独插入的效率不如执行大容量加载的效率。

如果一天中有成千上万的单个插入，可将插入成批进行大容量加载。  制定将单个插入追加到某个文件的流程，然后创建另一流程来定期加载该文件。

## <a name="create-statistics-after-the-load"></a>创建加载后的统计信息

为了提高查询性能，在首次加载数据或者在数据发生重大更改之后，必须针对所有表的所有列创建统计信息。 可以手动创建统计信息，也可以启用[自动创建统计信息](../sql-data-warehouse/sql-data-warehouse-tables-statistics.md?context=/azure/synapse-analytics/context/context)。

有关统计信息的详细说明，请参阅[统计信息](develop-tables-statistics.md)。 以下示例演示如何针对 Customer_Speed 表的五个列创建统计信息。

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>轮换存储密钥

好的安全做法是定期更改 Blob 存储的访问密钥。 由于有两个用于 Blob 存储帐户的存储密钥，因此可以转换着使用这两个密钥。

若要轮换 Azure 存储帐户密钥，请执行以下操作：

对于每个已更改密钥的存储帐户，请发出 [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) 命令。

示例：

已创建原始密钥

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

将密钥从密钥 1 轮换为密钥 2

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

无需对基础外部数据源进行更改。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 PolyBase 以及如何设计提取、加载和转换 (ELT) 过程，请参阅[为 Azure Synapse Analytics 设计 ELT](../sql-data-warehouse/design-elt-data-loading.md?context=/azure/synapse-analytics/context/context)。
- 有关加载教程，请参阅[使用 PolyBase 将数据从 Azure Blob 存储加载到 Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)。
- 若要监视数据加载，请参阅[使用 DMV 监视工作负荷](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?context=/azure/synapse-analytics/context/context)。