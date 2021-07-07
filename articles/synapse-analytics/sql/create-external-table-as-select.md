---
title: 从无服务器 SQL 池存储查询结果
description: 在本文中，你将了解如何使用无服务器 SQL 池将查询结果存储到存储。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 83c3fd114ab7ed3bf04d47ef3be2fc563b6b43d5
ms.sourcegitcommit: e832f58baf0b3a69c2e2781bd8e32d4f1ae932c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110586177"
---
# <a name="store-query-results-to-storage-using-serverless-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用无服务器 SQL 池将查询结果存储到存储

在本文中，你将了解如何使用无服务器 SQL 池将查询结果存储到存储。

## <a name="prerequisites"></a>先决条件

第一步是创建将在其中执行查询的数据库。 然后通过对该数据库执行[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)来初始化这些对象。 此安装脚本将创建数据源、数据库范围的凭据和用于读取这些示例中数据的外部文件格式。

按照本文中的说明创建数据源、数据库范围的凭据以及用于将数据写入到输出存储的外部文件格式。

## <a name="create-external-table-as-select"></a>Create external table as select

可以使用 CREATE EXTERNAL TABLE AS SELECT (CETAS) 语句将查询结果存储到存储。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用你创建的数据库。

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL = [SasTokenWrite]
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

> [!NOTE]
> 必须修改此脚本，并更改目标位置以再次执行。 无法在已有某些数据的位置上创建外部表。

## <a name="use-the-external-table"></a>使用外部表

可以像使用常规外部表一样，使用通过 CETAS 创建的外部表。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用你创建的数据库。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="remarks"></a>备注

存储结果后，不能修改外部表中的数据。 不能重复此脚本，因为 CETAS 不会覆盖在上一次执行中创建的基础数据。 如果你的方案需要下面的某些反馈项，请为那些反馈项投票，或在 Azure 反馈网站上建议新的反馈项：
- [支持在外部表中插入新数据](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/32981347-polybase-allow-insert-new-data-to-existing-exteran)
- [支持在外部表中删除数据](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/15158034-polybase-delete-from-external-tables)
- [在 CETAS 中指定分区](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/19520860-polybase-partitioned-by-functionality-when-creati)
- [指定文件大小和计数](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/42263617-cetas-specify-number-of-parquet-files-file-size)

支持的输出类型只有 Parquet 和 CSV。 可以在 [Azure 反馈站点](https://feedback.azure.com/forums/307516-azure-synapse-analytics/)上为其他类型投票。

## <a name="next-steps"></a>后续步骤

若要详细了解如何查询不同的文件类型，请参阅以下文章：[查询单个 CSV 文件](query-single-csv-file.md)、[查询 Parquet 文件](query-parquet-files.md)和[查询 JSON 文件](query-json-files.md)。
