---
title: 在 Synapse SQL 池中创建和使用外部表
description: 本部分介绍如何在 Synapse SQL 池中创建和使用外部表。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: cfce86e74a5e32f266dd0bbad84a179d8158a687
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2021
ms.locfileid: "113303681"
---
# <a name="create-and-use-native-external-tables-using-sql-pools-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用 SQL 池创建和使用本机外部表

本部分介绍如何在 Synapse SQL 池中创建和使用[本机外部表](develop-tables-external-tables.md)。 与在外部数据源定义中包含 `TYPE=HADOOP` 的外部表相比，本机外部表的性能更好。 这是因为，本机外部表使用本机代码来访问外部数据。 

当你想要控制对 Synapse SQL 池中外部数据的访问时，外部表非常有用。 如果你想要将 Power BI 等工具与 Synapse SQL 池结合使用，外部表也很有用。 外部表可以访问两种类型的存储：
- 公用存储，用户可访问其中的公用存储文件。
- 受保护存储，用户使用 SAS 凭据、Azure AD 标识或 Synapse 工作区的托管标识访问存储文件。

> [!NOTE]
>  在专用 SQL 池中，只能使用 Parquet 本机外部表。 由于此功能尚未在所有区域中推出，专用 SQL 池中的本机 Parquet 外部表目前以受限公共预览版提供。 如果你想要参与公共预览，请联系 Microsoft 技术客户经理/云解决方案架构师，确定你是否可以在专用池中使用本机 Parquet 外部表。 如果你想要在专用 SQL 池中使用正式版功能，或者需要访问 CSV 或 ORC 文件，请使用 Hadoop 外部表。 无服务器 SQL 池中的本机外部表已推出正式版。
> 请在[通过 Synapse SQL 使用外部表](develop-tables-external-tables.md)中详细了解本机外部表与 Hadoop 外部表的差别。

## <a name="prerequisites"></a>先决条件

第一步是创建将在其中创建表的数据库。 然后创建要在此示例中使用的以下对象：
- DATABASE SCOPED CREDENTIAL `sqlondemand`，可以访问受 SAS 保护的 `https://sqlondemandstorage.blob.core.windows.net` Azure 存储帐户。

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- EXTERNAL DATA SOURCE `sqlondemanddemo`，引用了使用 SAS 密钥保护的演示存储帐户；以及 EXTERNAL DATA SOURCE `nyctlc`，引用了位置 `https://azureopendatastorage.blob.core.windows.net/nyctlc/` 上公开可用的 Azure 存储帐户。

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE nyctlc
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/')
    GO
    CREATE EXTERNAL DATA SOURCE DeltaLakeStorage
    WITH ( location = 'https://sqlondemandstorage.blob.core.windows.net/delta-lake/' );
    ```

- 文件格式 `QuotedCSVWithHeaderFormat` 和 `ParquetFormat`，描述 CSV 和 parquet 文件类型。

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    GO
    CREATE EXTERNAL FILE FORMAT DeltaLakeFormat WITH (  FORMAT_TYPE = DELTA );
    GO
    ```

本文中的查询将在示例数据库上执行，并使用这些对象。 

## <a name="external-table-on-a-file"></a>基于某个文件的外部表

可以创建用于访问 Azure 存储帐户中数据的外部表，该帐户允许具有某些 Azure AD 标识或 SAS 密钥的用户进行访问。 创建外部表的方式可以与创建常规 SQL Server 外部表的方式相同。 

下面的查询将创建一个外部表，该表读取 SynapseSQL 演示 Azure 存储帐户中的 population.csv 文件，该帐户使用 `sqlondemanddemo` 数据源来进行引用，并受名为 `sqlondemand` 的数据库范围的凭据保护。 

数据源和数据库范围的凭据在[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)中创建。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用你创建的数据库。 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

本机 CSV 表目前仅在无服务器 SQL 池中可用。

## <a name="external-table-on-a-set-of-files"></a>基于文件集的外部表

可以创建从 Azure 存储中的一组文件读取数据的外部表：

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'yellow/puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = nyctlc,
         FILE_FORMAT = ParquetFormat
);
```

可以指定文件必须符合哪种模式才能被外部表引用。 只需为 Parquet 和 CSV 表指定模式。 如果使用 Delta Lake 格式，则只需指定一个根文件夹，外部表将自动查找模式。

> [!NOTE]
> 表是基于分区文件夹结构创建的，但你无法利用某种形式的分区消除。 如果你想要通过跳过不满足某些条件（例如，本例中的特定年份或月份）的文件来获得更好的性能，请使用[基于外部数据的视图](create-use-views.md#partitioned-views)。

## <a name="delta-lake-external-table"></a>Delta Lake 外部表

可以在 Delta Lake 文件夹的顶层创建外部表。 基于[单个文件](#external-table-on-a-file)或[文件集](#external-table-on-a-set-of-files)创建的外部表，与基于 Delta Lake 格式创建的外部表的唯一差别在于，在 Delta Lake 外部表中，需要引用包含 Delta Lake 结构的文件夹。

> [!div class="mx-imgBorder"]
>![ECDC COVID-19 Delta Lake 文件夹](./media/shared/covid-delta-lake-studio.png)

下面是基于 Delta Lake 文件夹创建的表定义示例：

```sql
CREATE EXTERNAL TABLE Covid (
     date_rep date,
     cases int,
     geo_id varchar(6)
) WITH (
        LOCATION = 'covid', --> the root folder containing the Delta Lake files
        data_source = DeltaLakeStorage,
        FILE_FORMAT = DeltaLakeFormat
);
```

Delta Lake 目前为公共预览版，存在一些已知问题和限制。 在 [Synapse 无服务器 SQL 池自助页](resources-self-help-sql-on-demand.md#delta-lake)上查看已知问题。

## <a name="use-an-external-table"></a>使用外部表

可以在查询中使用[外部表](develop-tables-external-tables.md)，其方式与在 SQL Server 查询中使用外部表的方式相同。

以下查询演示了如何使用已在上一部分中创建的 population 外部表。 它按降序返回国家/地区名称及其 2019 年的人口。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用你创建的数据库。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

此查询的性能可能因区域而异。 你的工作区可能不会放入到这些示例中使用的 Azure 存储帐户所在的同一区域。 对于生产工作负载，请将 Synapse 工作区和 Azure 存储放在同一区域。

## <a name="next-steps"></a>后续步骤

有关如何将查询结果存储到存储中的信息，请参阅[将查询结果存储到存储](../sql/create-external-table-as-select.md)一文。
