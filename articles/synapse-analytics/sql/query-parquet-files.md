---
title: 使用无服务器 SQL 池查询 Parquet 文件
description: 本文介绍了如何使用无服务器 SQL 池查询 Parquet 文件。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6ed309637c03fc4701f2b973f64b2473f43b0d2a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734877"
---
# <a name="query-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用无服务器 SQL 池查询 Parquet 文件

在本文中，你将学习如何使用无服务器 SQL 池来编写读取 Parquet 文件的查询。

## <a name="quickstart-example"></a>快速入门示例

你可以使用 `OPENROWSET` 函数通过提供文件的 URL 来读取 parquet 文件的内容。

### <a name="read-parquet-file"></a>读取 parquet 文件

查看 `PARQUET` 文件内容的最简单方法是向 `OPENROWSET` 函数提供文件 URL，并指定 parquet `FORMAT`。 如果文件公开可用，或者你的 Azure AD 标识可以访问该文件，则你应该能够使用类似于以下示例的查询来查看该文件的内容：

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

请确保你可以访问此文件。 如果文件由 SAS 密钥或自定义 Azure 标识保护，则你需要[为 sql 登录设置服务器级别的凭据](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)。

> [!IMPORTANT]
> 请确保使用 UTF-8 数据库排序规则（例如 `Latin1_General_100_BIN2_UTF8`），因为 PARQUET 文件中的字符串值使用 UTF-8 编码进行编码。
> PARQUET 文件中的文本编码与排序规则不匹配可能会导致意外的转换错误。
> 可以使用以下 T-SQL 语句轻松更改当前数据库的默认排序规则：`alter database current collate Latin1_General_100_BIN2_UTF8`

如果使用 `Latin1_General_100_BIN2_UTF8` 排序规则，与其他排序规则相比，你将获得额外的性能提升。 `Latin1_General_100_BIN2_UTF8` 排序规则与 parquet 字符串排序规则兼容。 SQL 池能够消除 Parquet 文件的某些部分，这些部分将不包含查询中所需的数据（文件/列段修剪）。 如果使用其他排序规则，parquet 文件中的所有数据都将加载到 Synapse SQL 中，并且在 SQL 进程中进行筛选。 `Latin1_General_100_BIN2_UTF8` 排序规则提供仅适用于 parquet 和 CosmosDB 的额外性能优化。 缺点是会丢失细粒度的比较规则，比如不区分大小写。

### <a name="data-source-usage"></a>数据源使用情况

上面的示例使用了文件的完整路径。 作为替代方法，你可以创建一个外部数据源（其中包含指向存储根文件夹的位置），并在 `OPENROWSET` 函数中使用该数据源和指向该文件的相对路径：

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

如果数据源由 SAS 密钥或自定义标识保护，则你可以[为数据源配置以数据库为作用域的凭据](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)。

### <a name="explicitly-specify-schema"></a>显式指定架构

使用 `OPENROWSET`，你可以使用 `WITH` 子句显式指定要从文件中读取的列：

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

> [!IMPORTANT]
> 请确保在 `WITH` 子句中为所有字符串列显式指定某种 UTF-8 排序规则（例如 `Latin1_General_100_BIN2_UTF8`），或在数据库级别设置某种 UTF-8 排序规则。
> 文件中的文本编码和字符串列排序规则不匹配可能会导致意外的转换错误。
> 可以使用 T-SQL 语句 `alter database current collate Latin1_General_100_BIN2_UTF8` 轻松地更改当前数据库的默认排序规则
> 可以使用以下定义轻松设置列类型的排序规则：`geo_id varchar(6) collate Latin1_General_100_BIN2_UTF8`

以下各部分介绍了如何查询各种类型的 PARQUET 文件。

## <a name="prerequisites"></a>先决条件

第一步是创建数据库，其中包含了引用[纽约黄色出租车](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)存储帐户的数据源。 然后通过对该数据库执行[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)来初始化这些对象。 此安装脚本将创建数据源、数据库范围的凭据以及在这些示例中使用的外部文件格式。

## <a name="dataset"></a>数据集

本示例中使用[纽约黄色出租车](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)数据集。 可以使用与[读取 CSV 文件](query-parquet-files.md)相同的方式查询 Parquet 文件。 唯一的区别是 `FILEFORMAT` 参数应设置为 `PARQUET`。 本文中的示例演示了关于读取 Parquet 文件的详细信息。

## <a name="query-set-of-parquet-files"></a>Parquet 文件的查询集

在查询 Parquet 文件时只能指定所需的列。

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>自动架构推理

读取 Parquet 文件时，无需使用 OPENROWSET WITH 子句。 系统会自动从 Parquet 文件中读取列名称和数据类型。

下面的示例显示 Parquet 文件的自动架构推理功能。 该示例在不指定架构的情况下返回 2018 年 9 月的行数。

> [!NOTE]
> 读取 Parquet 文件时，无需指定 OPENROWSET WITH 子句中的列。 在这种情况下，无服务器 SQL 池查询服务将利用 Parquet 文件中的元数据，并按名称绑定列。

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=9/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>查询分区数据

此示例中提供的数据集划分（分区）为单独的子文件夹。 可以使用 filepath 函数将特定分区作为目标。 此示例显示 2017 年前三个月的费用金额（按年、月和 payment_type）。

> [!NOTE]
> 无服务器 SQL 池查询与 Hive/Hadoop 分区方案兼容。

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>类型映射

有关 Parquet 类型到 SQL 原生类型的映射，请查看 [Parquet 的类型映射](develop-openrowset.md#type-mapping-for-parquet)。

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何[查询 Parquet 嵌套类型](query-parquet-nested-types.md)。
