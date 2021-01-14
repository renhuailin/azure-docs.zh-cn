---
title: 无服务器 SQL 池最佳做法
description: 使用无服务器 SQL 池的建议和最佳实践。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: c752bc6ae49f009056067545fde292dc29027d5d
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208125"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中无服务器 SQL 池的最佳实践

本文将介绍使用无服务器 SQL 池的最佳实践集合。 无服务器 SQL 池是 Azure Synapse 分析中的资源。

## <a name="general-considerations"></a>一般注意事项

无服务器 SQL 池允许查询 Azure 存储帐户中的文件。 它没有本地存储或引入功能。 因此，查询所面向的所有文件都是无服务器 SQL 池外部的。 与从存储中读取文件相关的所有操作都可能会对查询性能产生影响。

## <a name="colocate-your-storage-and-serverless-sql-pool"></a>归置存储和无服务器 SQL 池

若要最大程度地减少延迟，请将 Azure 存储帐户或 CosmosDB 分析存储归置到无服务器的 SQL 池终结点。 在创建工作区期间预配的存储帐户和终结点位于同一区域。

为了获得最佳性能，如果访问具有无服务器 SQL 池的其他存储帐户，请确保它们位于同一区域。 如果它们不在同一区域，那么远程区域和终结点区域之间的数据网络传输延迟将会增加。

## <a name="azure-storage-throttling"></a>Azure 存储限制

多个应用和服务可以访问你的存储帐户。 当应用程序、服务和无服务器 SQL 池工作负荷生成的组合 IOPS 或吞吐量超过存储帐户的限制时，将发生存储限制。 这样一来，查询性能会受到严重不利影响。

检测到限制时，无服务器 SQL 池有内置处理方法来解决该问题。 无服务器 SQL 池会以较慢的速度向存储请求请求，直到限制得以解决。

> [!TIP]
> 为了获得最佳的查询执行效果，不得在查询执行期间对存储帐户施加其他工作负荷。

## <a name="prepare-files-for-querying"></a>准备文件以供查询

如果可以，尽可能准备文件来提升性能：

- 将大型 CSV 和 JSON 转换为 Parquet。 Parquet 是一种分列格式。 由于经过压缩，它的文件大小小于包含相同数据的 CSV 或 JSON 文件。 如果您正在读取 Parquet 文件，则无服务器 SQL 池可以跳过查询中不需要的列和行。 无服务器 SQL 池需要更少的时间，更少的存储请求才能读取它。
- 如果查询目标是一个大文件，那么把它拆分为多个较小文件将会让你受益匪浅。
- 尝试将 CSV 文件大小保留为 100 MB 和 10 GB。
- 对于单个 OPENROWSET 路径或外部表 LOCATION，最好有相等大小的文件。
- 通过将分区存储到不同文件夹或文件名称来对数据进行分区。 请参阅[使用 filename 和 filepath 函数定目标到特定分区](#use-filename-and-filepath-functions-to-target-specific-partitions)。

## <a name="push-wildcards-to-lower-levels-in-the-path"></a>将通配符推送到路径中的较低级别

可以在路径中使用通配符来[查询多个文件和文件夹](query-data-storage.md#query-multiple-files-or-folders)。 无服务器 SQL 池列出存储帐户中的文件，从第一个 * 开始，使用存储 API。 它排除了与指定路径不匹配的文件。 如果有多个文件与第一个通配符后面的指定路径匹配，减少初始文件列表可以提升性能。

## <a name="use-appropriate-data-types"></a>使用适当的数据类型

在查询中使用的数据类型将影响性能。 如果遵循以下指南，可以提升性能： 

- 使用可容纳最大可能值的最小数据大小。
  - 如果最大字符值长度为 30 个字符，请使用长度为 30 的字符数据类型。
  - 如果所有字符列值都是固定大小的，请使用 char 或 nchar 。 否则，请使用 varchar 或 nvarchar 。
  - 如果最大整数列值为 500，请使用 smallint，因为它是可容纳此值的最小数据类型。 可以在[本文](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true)中找到整数数据类型范围。
- 如果可以，尽可能使用 varchar 和 char，而不是 nvarchar 和 nchar   。
- 如果可以，尽可能使用基于整数的数据类型。 SORT、JOIN 和 GROUP BY 操作在整数上的执行速度比字符数据快。
- 如果使用的是架构推理，[请查看推理数据类型](#check-inferred-data-types)。

## <a name="check-inferred-data-types"></a>查看推理数据类型

[架构推理](query-parquet-files.md#automatic-schema-inference)有助于快速编写查询，并浏览数据，而无需了解文件架构。 此便利的代价是推断数据类型可能大于实际数据类型。 当源文件中没有足够的信息来确保使用适当的数据类型时，就会发生这种情况。 例如，Parquet 文件不包含关于最大字符列长度的元数据。 因此无服务器 SQL 池会将其推断为 varchar (8000) 。

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
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

知道查询的推理数据类型后，可以指定相应的数据类型：

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>使用 filename 和 filepath 函数定目标到特定分区

数据通常是以分区形式组织。 可以指示无服务器 SQL 池查询特定文件夹和文件。 这样做可减少查询需要读取和处理的文件数和数据量。 额外的好处是，将会提升性能。

有关详细信息，请阅读 [filename](query-data-storage.md#filename-function) 和 [filepath](query-data-storage.md#filepath-function) 函数，并查看[查询特定文件](query-specific-files.md)的示例。

> [!TIP]
> 请始终将 filepath 和 filename 函数的结果强制转换为适当的数据类型。 如果使用字符数据类型，请确保使用适当的长度。

> [!NOTE]
> 除了为 Apache Spark for Azure Synapse Analytics 中创建的每个表自动创建的表以外，外部表暂不支持用于删除分区的 filepath 和 filename 函数。

如果存储的数据未分区，请考虑将数据分区。 这样，你就可以使用这些函数来优化面向这些文件的查询。 在从无服务器 SQL 池中 [查询 Azure Synapse 表的分区 Apache Spark](develop-storage-files-spark-tables.md) 时，查询将自动仅针对所需的文件。

## <a name="use-parser_version-20-to-query-csv-files"></a>使用 PARSER_VERSION 2.0 查询 CSV 文件

查询 CSV 文件时，可以使用性能优化的分析器。 有关详细信息，请参阅 [PARSER_VERSION](develop-openrowset.md)。

## <a name="manually-create-statistics-for-csv-files"></a>手动为 CSV 文件创建统计信息

无服务器 SQL 池依赖统计信息来生成最佳查询执行计划。 需要时，将自动为 Parquet 文件中的列创建统计信息。 此时，不会为 CSV 文件中的列自动创建统计信息，应为查询中使用的列手动创建统计信息，尤其是在 DISTINCT、JOIN、WHERE、ORDER BY 和 GROUP BY 中使用的列。 有关详细信息，请查看 [无服务器 SQL 池中的统计](develop-tables-statistics.md#statistics-in-serverless-sql-pool) 信息。

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 增强查询性能和联接

[CETAS](develop-tables-cetas.md) 是无服务器 SQL 池中可用的最重要的功能之一。 CETAS 是一种并行操作，用于创建外部表元数据，并将 SELECT 查询结果导出到存储帐户中的一组文件。

可以使用 CETAS 将查询的常用部分（如联接的引用表）存储到一组新的文件中。 接下来，可以联接到这一个外部表，而不是在多个查询中重复常用联接。

当 CETAS 生成 Parquet 文件时，当第一个查询针对此外部表时，将自动创建统计信息，从而提高了以 CETAS 生成的面向表的后续查询的性能。

## <a name="azure-ad-pass-through-performance"></a>Azure AD 直通性能

无服务器 SQL 池允许通过使用 Azure Active Directory (Azure AD) 传递或 SAS 凭据来访问存储中的文件。 与 SAS 相比，使用 Azure AD 直通可能会降低性能。

在 Azure AD 直通性能得到改善前，如需提升性能，请尝试使用 SAS 凭据来访问存储。

## <a name="next-steps"></a>后续步骤

有关常见问题的解决方案，请查阅[故障排除](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。 如果使用的是专用 SQL 池而不是无服务器 SQL 池，请参阅 [专用 sql 池的最佳实践](best-practices-sql-pool.md) 以了解具体指导。
