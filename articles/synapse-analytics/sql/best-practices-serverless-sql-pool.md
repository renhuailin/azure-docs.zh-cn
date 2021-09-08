---
title: 无服务器 SQL 池最佳做法
description: 有关使用无服务器 SQL 池的建议和最佳做法。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 655294aaf575dd828c3be6f135984eaf8c851fb6
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479753"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用无服务器 SQL 池的最佳做法

在本文中，你将探究使用无服务器 SQL 池的一组最佳做法。 无服务器 SQL 池是 Azure Synapse Analytics 中的一种资源。

使用无服务器 SQL 池，你可以查询 Azure 存储帐户中的文件。 它没有本地存储或引入功能。 因此，作为查询目标的所有文件都是无服务器 SQL 池外部的。 与从存储中读取文件相关的所有操作都可能会对查询性能产生影响。

以下是一些一般准则：
- 请确保客户端应用程序与无服务器 SQL 池并置。
  - 如果使用的是 Azure 外的客户端应用程序（例如 Power BI Desktop、SSMS、ADS），请确保在接近客户端计算机的区域中使用无服务器 SQL 池。
- 请确保存储 (Azure Data Lake、Cosmos DB) 和无服务器 SQL 池位于同一区域。
- 尝试通过使用分区并令文件大小保持在 100 MB 和 10 GB 之间，以[优化存储布局](#prepare-files-for-querying)。
- 如果要返回大量结果，请确保使用的是 SSMS 或 ADS，而非 Synapse Studio。 Synapse Studio 是一个 web 工具，不适用于大型结果集。 
- 如果按字符串列筛选结果，请尝试使用某些 `BIN2_UTF8` 排序规则。
- 尝试使用 Power BI 导入模式或 Azure Analysis Services 在客户端上缓存结果，并定期刷新。 如果使用复杂的查询或处理大量数据，则无服务器 SQL 池无法提供 Power BI 直接查询模式下的交互式体验。

## <a name="client-applications-and-network-connections"></a>客户端应用程序和网络连接

确保客户端应用程序已通过最佳连接方式连接到最近的 Synapse 工作区。
- 将客户端应用程序与 Synapse 工作区共置在一起。 如果在使用 Power BI 或 Azure Analysis Service 等应用程序，请确保它们位于 Synapse 工作区所在的同一区域中。 根据需要，创建单独的工作区与客户端应用程序配对。 如果将客户端应用程序和 Synapse 工作区放在不同的区域，这可能会增加延迟并降低流式传输结果的速度。
- 如果从本地应用程序读取数据，请确保 Synapse 工作区位于距离你所在位置最近的区域中。
- 在读取大量数据时，请确保网络带宽没有出现问题。
- 不要使用 Synapse Studio 返回大量数据。 Synapse Studio 是使用 HTTPS 协议传输数据的 Web 工具。 请使用 Azure Data Studio 或 SQL Server Management Studio 读取大量数据。

## <a name="storage-and-content-layout"></a>存储和内容布局

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>将存储和无服务器 SQL 池共置在一起

为了尽量降低延迟，请将 Azure 存储帐户或 CosmosDB 分析存储与无服务器 SQL 池终结点共置在一起。 在创建工作区期间预配的存储帐户和终结点位于同一区域。

为了获得最佳性能，如果你使用无服务器 SQL 池访问其他存储帐户，请确保它们位于同一区域。 如果它们不在同一区域，那么远程区域和终结点区域之间的数据网络传输延迟将会增加。

### <a name="azure-storage-throttling"></a>Azure 存储限制

多个应用和服务可以访问你的存储帐户。 如果应用程序、服务和无服务器 SQL 池工作负荷生成的合并 IOPS 或吞吐量超出存储帐户上限，就会发生存储限制。 这样一来，查询性能会受到严重不利影响。

检测到此限制时，无服务器 SQL 池会使用内置的处理机制来解决此问题。 无服务器 SQL 池会以较慢的节奏向存储发出请求，直到限制消除。

> [!TIP]
> 为了获得最佳的查询执行效果，不得在查询执行期间对存储帐户施加其他工作负荷。

### <a name="azure-ad-pass-through-performance"></a>Azure AD 直通性能

使用无服务器 SQL 池，可以使用 Azure Active Directory (Azure AD) 直通或 SAS 凭据来访问存储中的文件。 与 SAS 相比，使用 Azure AD 直通可能会降低性能。

如果需要提高性能，请尝试使用 SAS 凭据访问存储。

### <a name="prepare-files-for-querying"></a>准备文件以供查询

如果可以，尽可能准备文件来提升性能：

- 将大型 CSV 和 JSON 转换为 Parquet。 Parquet 是一种分列格式。 由于经过压缩，它的文件大小小于包含相同数据的 CSV 或 JSON 文件。 如果你正在读取 Parquet 文件，则无服务器 SQL 池能够跳过查询中不需要的列和行。 无服务器 SQL 池读取此类文件所需的时间和存储请求会更少。
- 如果查询目标是一个大文件，那么把它拆分为多个较小文件将会让你受益匪浅。
- 请尽量使 CSV 文件大小保持在 100 MB 和 10 GB 之间。
- 对于单个 OPENROWSET 路径或外部表 LOCATION，最好有相等大小的文件。
- 通过将分区存储到不同文件夹或文件名称来对数据进行分区。 请参阅[使用 filename 和 filepath 函数定目标到特定分区](#use-filename-and-filepath-functions-to-target-specific-partitions)。

### <a name="colocate-your-cosmosdb-analytical-storage-and-serverless-sql-pool"></a>将 CosmosDB 分析存储和无服务器 SQL 池共置在一起

请确保 CosmosDB 分析存储放置在 Synapse 工作区所在的同一区域中。 跨区域查询可能会导致延迟非常大。 使用连接字符串中的区域属性显式指定放置分析存储的区域（参阅[使用无服务器 SQL 池查询 CosmosDb](query-cosmos-db-analytical-store.md#overview)）：

```
'account=<database account name>;database=<database name>;region=<region name>'
```

## <a name="csv-optimizations"></a>CSV 优化

### <a name="use-parser_version-20-to-query-csv-files"></a>使用 PARSER_VERSION 2.0 查询 CSV 文件

查询 CSV 文件时，可以使用性能优化的分析器。 有关详细信息，请参阅 [PARSER_VERSION](develop-openrowset.md)。

### <a name="manually-create-statistics-for-csv-files"></a>手动为 CSV 文件创建统计信息

无服务器 SQL 池依赖这些统计信息来生成最佳查询执行计划。 需要时，将自动为 Parquet 文件中的列创建统计信息。 目前，不会自动为 CSV 文件中的列创建统计信息，你应当手动为查询中使用的列创建统计信息，尤其是在 DISTINCT、JOIN、WHERE、ORDER BY 和 GROUP BY 中使用的列。 有关详细信息，请查看[无服务器 SQL 池中的统计信息](develop-tables-statistics.md#statistics-in-serverless-sql-pool)。


## <a name="data-types"></a>数据类型

### <a name="use-appropriate-data-types"></a>使用适当的数据类型

在查询中使用的数据类型将影响性能。 如果遵循以下指南，可以提升性能： 

- 使用可容纳最大可能值的最小数据大小。
  - 如果最大字符值长度为 30 个字符，请使用长度为 30 的字符数据类型。
  - 如果所有字符列值都是固定大小的，请使用 char 或 nchar 。 否则，请使用 varchar 或 nvarchar 。
  - 如果最大整数列值为 500，请使用 smallint，因为它是可容纳此值的最小数据类型。 可以在[本文](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true)中找到整数数据类型范围。
- 如果可以，尽可能使用 varchar 和 char，而不是 nvarchar 和 nchar   。
- 如果可以，尽可能使用基于整数的数据类型。 SORT、JOIN 和 GROUP BY 操作在整数上的执行速度比字符数据快。
- 如果使用的是架构推理，[请查看推理数据类型](#check-inferred-data-types)。

### <a name="check-inferred-data-types"></a>查看推理数据类型

[架构推理](query-parquet-files.md#automatic-schema-inference)有助于快速编写查询，并浏览数据，而无需了解文件架构。 获得此便利的代价是，推断的数据类型可能大于实际数据类型。 当源文件中没有足够的信息来确保使用适当的数据类型时，就会发生这种情况。 例如，Parquet 文件不包含关于最大字符列长度的元数据。 因此，无服务器 SQL 池将其推断为 varchar(8000)。

可以使用 [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) 来查看得到的查询数据类型。

下面的示例展示了如何优化推理数据类型。 使用此过程来显示推理数据类型： 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

结果集如下：

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------|
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

知道查询的推理数据类型后，可以指定相应的数据类型：

```sql  
SELECT
    vendorID, tpepPickupDateTime, passengerCount
FROM 
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=2018/puMonth=*/*.snappy.parquet',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>筛选器优化

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>将通配符推送到路径中的较低级别

可以在路径中使用通配符来[查询多个文件和文件夹](query-data-storage.md#query-multiple-files-or-folders)。 无服务器 SQL 池使用存储 API 列出存储帐户中的文件，从第一个 * 开始。 它排除了与指定路径不匹配的文件。 如果有多个文件与第一个通配符后面的指定路径匹配，减少初始文件列表可以提升性能。

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>使用 filename 和 filepath 函数定目标到特定分区

数据通常是以分区形式组织。 你可以指示无服务器 SQL 池查询特定文件夹和文件。 这样做可减少查询需要读取和处理的文件数和数据量。 额外的好处是，将会提升性能。

有关详细信息，请阅读 [filename](query-data-storage.md#filename-function) 和 [filepath](query-data-storage.md#filepath-function) 函数，并查看[查询特定文件](query-specific-files.md)的示例。

> [!TIP]
> 请始终将 filepath 和 filename 函数的结果强制转换为适当的数据类型。 如果使用字符数据类型，请确保使用适当的长度。

> [!NOTE]
> 除了为 Apache Spark for Azure Synapse Analytics 中创建的每个表自动创建的表以外，外部表暂不支持用于删除分区的 filepath 和 filename 函数。

如果存储的数据未分区，请考虑将数据分区。 这样，你就可以使用这些函数来优化面向这些文件的查询。 如果从无服务器 SQL 池[查询分区的 Apache Spark for Azure Synapse 表](develop-storage-files-spark-tables.md)，则查询将自动仅面向必要的文件。

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>使用适当的排序规则对字符列使用谓词下推

parquet 文件中的数据按行组进行组织。 无服务器 SQL 池基于 WHERE 子句中指定的谓词跳过行组，从而减少 IO，进而提高查询性能。 

请注意，仅 Latin1_General_100_BIN2_UTF8 排序规支持对 parquet 文件中字符列的谓词下推。 可以使用 WITH 子句为特定列指定排序规则。 如果未使用 WITH 子句指定此排序规则，则将使用数据库排序规则。

## <a name="optimize-repeating-queries"></a>优化重复查询

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 增强查询性能和联接

[CETAS](develop-tables-cetas.md) 是无服务器 SQL 池中最重要的功能之一。 CETAS 是一种并行操作，用于创建外部表元数据，并将 SELECT 查询结果导出到存储帐户中的一组文件。

可以使用 CETAS 将查询的常用部分（如联接的引用表）具体化到一组新的文件中。 接下来，可以联接到这一个外部表，而不是在多个查询中重复常用联接。

随着 CETAS 生成 Parquet 文件，统计信息将会在第一个查询以此外部表为目标时自动创建，从而提升以 CETAS 生成的表为查询目标的后续查询的性能。

## <a name="next-steps"></a>后续步骤

有关常见问题的解决方案，请查阅[故障排除](resources-self-help-sql-on-demand.md)一文。 如果你使用专用 SQL 池而非无服务器 SQL 池，请参阅[适用于专用 SQL 池的最佳做法](best-practices-dedicated-sql-pool.md)来获取具体指导。
