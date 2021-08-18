---
title: Synapse SQL 开发最佳做法
description: 针对 Synapse SQL 进行开发时应知道的建议和最佳做法。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: eb107ecfa24de98fc9fab4dd52c8daa568eb852f
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862499"
---
# <a name="development-best-practices-for-synapse-sql"></a>Synapse SQL 开发最佳做法

本文介绍在开发数据仓库解决方案时的指导和最佳做法。 

## <a name="dedicated-sql-pool-development-best-practices"></a>专用 SQL 池开发最佳做法

### <a name="reduce-cost-with-pause-and-scale"></a>使用暂停和缩放来降低成本

若要了解如何通过暂停和缩放来降低成本，请参阅[管理计算](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?context=/azure/synapse-analytics/context/context)。

### <a name="maintain-statistics"></a>维护统计信息

请确保每日或在每次加载后更新统计信息。  创建和更新统计信息的性能与成本之间总有一些取舍。 如果你发现维护所有统计信息所需时间太长，请更仔细地挑选哪些列要进行统计、哪些列需要频繁更新。  

例如，你可能想要更新每天都要添加新值的日期列。 

> [!NOTE]
> 对涉及联接的列、WHERE 子句中使用的列、在 GROUP BY 中找到的列进行信息统计，可以获得最大效益。

另请参阅[管理表统计信息](develop-tables-statistics.md)、[CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true)。

### <a name="hash-distribute-large-tables"></a>哈希分布大型表

默认情况下，表是以轮循机制分布的。 此功能让用户可以轻松开始创建表，而不必确定表的分布方式。  轮循机制表的性能应可满足某些工作负荷的要求。 但在大多数情况下，选择分布列可获得更佳的性能。  

按列分布表的性能远远高于轮循机制表的最常见例子是联接两个大型事实表。  

例如，如果有一个依 order_id 分布的订单表，以及一个也是依 order_id 分布的事务表，如果将订单数据联接到事务表上的 order_id，此查询将变成传递查询。 

这意味着不再需要进行数据移动操作。  减少步骤意味着加快查询速度。  更少的数据移动也将让查询更快。

> [!TIP]
> 加载分布的表时，请确保传入数据的分布键没有排序，因为这会拖慢加载速度。  

请参阅以下链接，了解有关选择分布列如何能提升性能，以及如何在 CREATE TABLES 语句的 WITH 子句中定义分布式表的额外详细信息。

另请参阅[表概述](develop-tables-overview.md)、[表分布](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?context=/azure/synapse-analytics/context/context)、[选择表分布](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) 以及 [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)。

### <a name="do-not-over-partition"></a>不要过度分区
尽管数据分区对于维护数据（通过分区切换）或优化扫描（通过分区排除）很有效，但分区过多会减慢查询速度。  通常，在 SQL Server 上运行良好的高粒度分区策略可能无法在专用 SQL 池中正常工作。  

> [!NOTE]
> 通常，在 SQL Server 上运行良好的高粒度分区策略可能无法在专用 SQL 池中正常工作。  

如果每个分区的行数少于 1 百万，太多分区还会降低聚集列存储索引的效率。 专用 SQL 池对将数据进行分区，分到 60 个数据库中。 

因此，如果创建具有 100 个分区的表，将得到 6000 个分区。  每个工作负荷都不同，因此最佳建议是尝试不同的分区，找出最适合工作负荷的分区。  

可以考虑的一种选择是，使用比 SQL Server 中适当粒度更粗的粒度。  例如，考虑使用每周或每月分区，而不是每日分区。

另请参阅[表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context)。

### <a name="minimize-transaction-sizes"></a>最小化事务大小

在事务中运行的 INSERT、UPDATE、DELETE 语句，失败时必须回滚。  为了将长时间回滚的可能性降到最低，请尽可能将事务大小最小化。  这可以通过将 INSERT、UPDATE、DELETE 语句分成小部分来达成。  

例如，如果预期 INSERT 需要 1 小时，可将 INSERT 分成四个部分，以便将每次运行缩短到 15 分钟。

> [!TIP]
> 使用特殊的最低限度日志记录方案，像是 CTAS、TRUNCATE、DROP TABLE 或 INSERT 空表，来降低回滚的风险。  

另一个消除回滚的作法是使用“仅元数据”操作（像是分区切换）进行数据管理。  

例如，不要运行 DELETE 语句来删除表中所有 order_date 为 2001 年 10 月的行，而是将数据每月分区后，再从另一个表将有空分区之数据的分区调动出来（请参阅 ALTER TABLE 示例）。  

对于未分区的表，请考虑使用 CTAS 将想要保留的数据写入表中，而不是使用 DELETE。  如果 CTAS 花费的时间相同，则运行起来要安全得多，因为它的事务日志记录最少，并且可以根据需要快速取消。

另请参阅[了解事务](develop-transactions.md)、[优化事务](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?context=/azure/synapse-analytics/context/context)、[表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context)、[TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true) 和 [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context)。

### <a name="use-the-smallest-possible-column-size"></a>使用最小可能的列大小

定义 DDL 时，使用支持数据的最小数据类型将提高查询性能。 此操作对 CHAR 和 VARCHAR 列尤其重要。  

如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。  避免将所有字符列定义为较大的默认长度。  此外，请将列定义为 VARCHAR（当它只需要这样的大小时）而非 NVARCHAR。

另请参阅[表概述](develop-tables-overview.md)、[表数据类型](develop-tables-data-types.md)和 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)。

### <a name="optimize-clustered-columnstore-tables"></a>优化聚集列存储表

聚集列存储索引是将数据存储在专用 SQL 池中最有效率的方式之一。  默认情况下，专用 SQL 池中的表会创建为聚集列存储。  

为了让列存储表的查询获得最佳性能，良好的分段质量很重要。  当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  

压缩行组中的行数可以测量分段质量。  有关检测和改善聚集列存储表分段质量的分步说明，请参阅[列存储索引质量不佳的原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context#causes-of-poor-columnstore-index-quality)和[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context)一文。  

由于高质量列存储段很重要，因此可以考虑使用中型或大型资源类中的用户 ID 来加载数据。 使用较低的[数据仓库单位](resource-consumption-models.md)值意味着需要向加载用户分配较大的资源类。

由于列存储表通常要等到每个表中的行数超过 100 万且每个专用 SQL 池表分区成 60 个表之后，才会数据推送到压缩的列存储段，因此除非列存储表中的行数超过 6000 万，否则这种表对查询无益。  

> [!TIP]
> 对于少于 6,000 万行的表，列存储索引可能不是最佳解决方案。  

此外，如果要进行数据分区，则要考虑的是每个分区必须有 1 百万个行，使用聚集列存储索引才有益。  如果表有 100 个分区，则它至少必须有 60 亿行才能受益于聚集列存储（60 个分，100 个分区，100 万行）。  

如果表中不到 60 亿行，请减少分区数目，或考虑改用堆表。  使用具有辅助索引的堆表而不是列存储表也许能提升性能，值得试验。

查询列存储表时，如果只选择需要的列，查询运行将更快速。  

另请参阅[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context)、[列存储索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true)、[重新生成列存储索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context#rebuilding-indexes-to-improve-segment-quality)。

## <a name="serverless-sql-pool-development-best-practices"></a>无服务器 SQL 池开发最佳做法

### <a name="general-considerations"></a>一般注意事项

使用无服务器 SQL 池，你可以查询 Azure 存储帐户中的文件。 它没有本地存储或引入功能，这意味着作为查询目标的所有文件都在无服务器 SQL 池外部。 因此，与从存储中读取文件相关的所有操作都可能会对查询性能产生影响。

### <a name="colocate-azure-storage-account-and-serverless-sql-pool"></a>将 Azure 存储帐户和无服务器 SQL 池共置在一起

为了尽量降低延迟，请将 Azure 存储帐户和无服务器 SQL 池终结点共置在一起。 在创建工作区期间预配的存储帐户和终结点位于同一区域。

为了获得最佳性能，如果你使用无服务器 SQL 池访问其他存储帐户，请确保它们位于同一区域。 如果它们不在同一区域，那么从远程区域到终结点区域的数据网络传输延迟将会增加。

### <a name="azure-storage-throttling"></a>Azure 存储限制

多个应用和服务可以访问你的存储帐户。 如果应用程序、服务和无服务器 SQL 池工作负荷生成的合并 IOPS 或吞吐量超出存储帐户上限，就会发生存储限制。 发生存储限制时，将对查询性能造成严重的负面影响。

检测到此限制后，无服务器 SQL 池会使用内置的处理机制来处理此情况。 无服务器 SQL 池会以较慢的节奏向存储发出请求，直到限制消除。 

然而，为了获得最佳的查询执行效果，建议不要在查询执行期间让存储帐户负重执行其他工作负荷。

### <a name="prepare-files-for-querying"></a>准备文件以供查询

如果可以，尽可能准备文件来提升性能：

- 将 CSV 转换为 Parquet（Parquet 是分栏格式）。 由于将进行压缩，因此其文件大小比具有相同数据的 CSV 文件小，且无服务器 SQL 池进行读取所需的时间和存储请求更少。
- 如果查询目标是一个大文件，那么把它拆分为多个较小文件将会让你受益匪浅。
- 尽量让 CSV 文件大小小于 10GB。
- 对于单个 OPENROWSET 路径或外部表 LOCATION，最好有相等大小的文件。
- 通过将分区存储到不同的文件夹或文件名，对数据进行分区（请查看[使用 filename 和 filepath 函数定目标到特定分区](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)）。

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>使用 fileinfo 和 filepath 函数定目标到特定分区

数据通常是以分区形式组织。 你可以指示无服务器 SQL 池查询特定文件夹和文件。 这样做可减少查询需要读取和处理的文件数和数据量。 

因此，将获得更好的性能。 有关详细信息，请查看 [filename](query-data-storage.md#filename-function) 和 [filepath](query-data-storage.md#filepath-function) 函数，以及有关如何[查询特定文件](query-specific-files.md)的示例。

如果存储中的数据没有进行分区，可以考虑对其进行分区，以便使用这些函数来优化以这些文件为目标的查询。

从无服务器 SQL 池[查询已分区的 Apache Spark for Azure Synapse 外部表](develop-storage-files-spark-tables.md)时，查询将自动仅以所需的文件作为目标。

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 增强查询性能和联接

[CETAS](develop-tables-cetas.md) 是无服务器 SQL 池中最重要的功能之一。 CETAS 是一种并行操作，用于创建外部表元数据，并将 SELECT 查询的结果导出到存储帐户中的一组文件。

可以使用 CETAS 将查询的常用部分（如联接的引用表）存储到一组新的文件中。 接下来，可以联接到这一个外部表，而不是在多个查询中重复常用联接。 

随着 CETAS 生成 Parquet 文件，统计信息将会在第一个查询以此外部表为目标时自动创建，从而提升性能。

### <a name="next-steps"></a>后续步骤

如果需要本文中未提供的信息，请使用本页左侧的“搜索文档”功能来搜索所有 SQL 池文档。  可以在[有关 Azure Synapse Analytics 的 Microsoft 问答页面](/answers/topics/azure-synapse-analytics.html)上向其他用户和 Azure Synapse Analytics 产品组发布问题。 我们会主动观察此论坛，确保用户的问题获得其他用户或我们的回答。  

如果你更喜欢在 Stack Overflow 上提问，还可以访问 [Azure Synapse Analytics Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)。
