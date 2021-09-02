---
title: 使用无服务器 SQL 池查询文件夹和多个文件
description: 无服务器 SQL 池支持使用通配符（类似于 Windows 操作系统中使用的通配符）读取多个文件/文件夹。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: a91ca96e69ae5408a3232513eea3ba1443c97064
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123253594"
---
# <a name="query-folders-and-multiple-files"></a>查询文件夹和多个文件  

在本文中，你将了解如何在 Azure Synapse Analytics 中使用无服务器 SQL 池编写查询。

无服务器 SQL 池支持使用通配符（类似于 Windows 操作系统中使用的通配符）读取多个文件/文件夹。 但是，由于允许使用多个通配符，因此需要更大的灵活性。

## <a name="prerequisites"></a>先决条件

第一步是创建将在其中执行查询的数据库。 然后通过对该数据库执行[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)来初始化这些对象。 此安装脚本将创建数据源、数据库范围的凭据以及在这些示例中使用的外部文件格式。

将使用文件夹 csv/taxi 执行示例查询。 它包含 2016 年 7 月到 2018 年 6 月的 NYC Taxi - Yellow Taxi Trip Records 数据。 csv/taxi 中的文件采用以下格式按年份和月份命名：yellow_tripdata_\<year>-\<month>.csv

## <a name="read-all-files-in-folder"></a>读取文件夹中的所有文件

下面的示例从 csv/taxi 文件夹中读取所有 NYC Yellow Taxi 数据文件，并返回每年乘客总数和搭乘总次数。 它还显示聚合函数的使用情况。

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（如列数及其数据类型）。

### <a name="read-subset-of-files-in-folder"></a>读取文件夹中的文件子集

下面的示例使用通配符读取 csv/taxi 文件夹中的 2017 NYC Yellow Taxi 数据文件，并返回每个付款类型的总费用金额。

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（例如列数及其数据类型）。

## <a name="read-folders"></a>读取文件夹

为 OPENROWSET 提供的路径也可以是指向文件夹的路径。 以下部分包含这些查询类型。

### <a name="read-all-files-from-specific-folder"></a>读取特定文件夹中的所有文件

可以使用文件级别通配符读取文件夹中的所有文件，如[读取文件夹中所有文件](#read-all-files-in-folder)所示。 但有一种方法可以查询文件夹并使用该文件夹中的所有文件。

如果 OPENROWSET 中提供的路径指向文件夹，则该文件夹中的所有文件都将用作查询的源。 以下查询将读取 csv/taxi 文件夹中的所有文件。

> [!NOTE]
> 请注意以下查询中路径的末尾是否存在 /。 它表示文件夹。 如果省略了 /，则查询将改为以名为 taxi 的文件作为目标。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（例如列数及其数据类型）。

### <a name="read-all-files-from-multiple-folders"></a>读取多个文件夹中的所有文件

可以通过使用通配符从多个文件夹读取文件。   下面的查询读取 csv 文件夹中所有以 t 开头并以 i 结尾的文件夹中的所有文件。

> [!NOTE]
> 请注意以下查询中路径的末尾是否存在 /。 它表示文件夹。 如果省略 /，则查询将改为以名为 t&ast;i 的文件作为目标。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（例如列数及其数据类型）。

由于只有一个文件夹与条件匹配，因此查询结果与[读取文件夹中的所有文件](#read-all-files-in-folder)的相同。

## <a name="traverse-folders-recursively"></a>以递归方式遍历文件夹

如果在路径末尾指定 /**，则无服务器 SQL 池可以递归方式遍历文件夹。 以下查询读取 csv 文件夹中所有文件夹和子文件夹中的所有文件。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/**', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（例如列数及其数据类型）。

## <a name="multiple-wildcards"></a>多个通配符

可以在不同的路径级别使用多个通配符。  例如，可以将之前的查询扩充为仅读取所有以 t 开头并以 i 结尾的文件夹中包含 2017 数据的文件。

> [!NOTE]
> 请注意以下查询中路径的末尾是否存在 /。 它表示文件夹。 如果省略 /，则查询将改为以名为 t&ast;i 的文件作为目标。
> 每个查询最多有 10 个通配符。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（例如列数及其数据类型）。

由于只有一个文件夹与条件匹配，因此查询结果与[读取文件夹中文件的子集](#read-subset-of-files-in-folder)和[读取特定文件夹中的所有文件](#read-all-files-from-specific-folder)的相同。 [查询 Parquet 文件](query-parquet-files.md)中介绍了更复杂的通配符使用方案。

## <a name="next-steps"></a>后续步骤

有关详细信息，可参阅[查询特定文件](query-specific-files.md)一文。
