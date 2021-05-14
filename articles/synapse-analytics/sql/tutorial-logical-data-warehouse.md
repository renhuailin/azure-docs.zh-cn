---
title: 教程：使用无服务器 SQL 池构建逻辑数据仓库
description: 本教程介绍如何使用无服务器 SQL 池在 Azure 数据源上轻松创建逻辑数据仓库
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/28/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 4e408832affd84fcde41c79d33ec7f157611ef08
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166805"
---
# <a name="tutorial-create-logical-data-warehouse-with-serverless-sql-pool"></a>教程：使用无服务器 SQL 池创建逻辑数据仓库

在本教程中，你将了解如何在 Azure 存储和 Azure Cosmos DB 上创建逻辑数据仓库 (LDW)。

LDW 是以 Azure 数据资源（如 Azure Data Lake storage (ADLS)、Azure Cosmos DB 分析存储或 Azure Blob 存储）为基础构建的关系层。

## <a name="create-an-ldw-database"></a>创建 LDW 数据库

你需要创建一个自定义数据库，你将在该数据库中存储引用外部数据源的外部表和视图。

```sql
CREATE DATABASE Ldw
      COLLATE Latin1_General_100_BIN2_UTF8;
```

此排序规则在读取 Parquet 和 Cosmos DB 时提供最佳性能。 如果不想指定数据库排序规则，请确保在列定义中指定此排序规则。

## <a name="configure-data-sources-and-formats"></a>配置数据源和格式

第一步，需要配置数据源并指定远程存储的数据的文件格式。

### <a name="create-data-source"></a>创建数据源

数据源表示连接字符串信息，用于描述放置数据的位置以及对数据源进行身份验证的方式。

以下示例演示了一个引用公共 [ECDC COVID 19 Azure 开放式数据集](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/)的数据源定义示例：

```sql
CREATE EXTERNAL DATA SOURCE ecdc_cases WITH (
    LOCATION = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/'
);
```

如果数据源的所有者允许匿名访问或为调用方 Azure AD 标识提供显式访问权限，则调用方可以在没有凭据的情况下访问数据源。

可以显式定义在访问外部数据源上的数据时使用的自定义凭据。
- Synapse 工作区的托管标识
- Azure 存储的共享访问签名
- 只读 Cosmos DB 帐户密钥

作为先决条件，需要在数据库中创建一个主密钥：
```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Setup you password - you need to create master key only once';
```

在以下外部数据源中，Synapse SQL 池应使用工作区的托管标识访问存储中的数据。

```sql
CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE ecdc_cases WITH (
    LOCATION = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/',
    CREDENTIAL = WorkspaceIdentity
);
```

要访问 Cosmos DB 分析存储，需要定义一个包含只读 Cosmos DB 帐户密钥的凭据。

```sql
CREATE DATABASE SCOPED CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
```

### <a name="define-external-file-formats"></a>定义外部文件格式

外部文件格式定义存储在外部数据源中的文件的结构。 可以定义 Parquet 和 CSV 外部文件格式：

```sql
CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
GO
CREATE EXTERNAL FILE FORMAT CsvFormat WITH (  FORMAT_TYPE = CSV );
```

## <a name="explore-your-data"></a>浏览数据

设置数据源后，可以使用 `OPENROWSET` 函数浏览数据。 [OPENROWSET](develop-openrowset.md) 函数会读取远程数据源（如文件）的内容，并将内容作为行集返回。

```sql
select top 10  *
from openrowset(bulk 'latest/ecdc_cases.parquet',
                data_source = 'ecdc_cases'
                format='parquet') as a
```

`OPENROWSET` 函数将为你提供有关外部文件或容器中的列的信息，并使你能够定义外部表和视图的架构。

## <a name="create-external-tables-on-azure-storage"></a>在 Azure 存储上创建外部表

发现架构后，可以在 yu=our 外部数据源之上创建外部表和视图。 正确做法是在数据库架构中组织表和视图。 在下面的查询中，你可以创建一个架构，并将所有访问 Azure data Lake storage 中的 ECDC COVID 数据集的对象放入该架构中：

```sql
create schema ecdc_adls;
```

数据库架构可以用于对对象进行分组并定义每个架构的权限。 

定义架构后，可以创建引用文件的外部表。
以下外部表将引用放置在 Azure 存储中的 ECDC COVID parquet 文件：

```sql
create external table ecdc_adls.cases (
    date_rep        date,
    day    smallint,
    month             smallint,
    year  smallint,
    cases smallint,
    deaths            smallint,
    countries_and_territories       varchar(256),
    geo_id             varchar(60),
    country_territory_code           varchar(16),
    pop_data_2018           int,
    continent_exp             varchar(32),
    load_date      datetime2(7),
    iso_country   varchar(16)
) with (
    data_source= ecdc_cases,
    location = 'latest/ecdc_cases.parquet',
    file_format = ParquetFormat
);
```

请确保使用尽可能小的类型的字符串和数字列，以优化查询性能。

## <a name="create-views-on-azure-cosmos-db"></a>在 Azure Cosmos DB 上创建视图

作为外部表的替代方法，可以在外部数据的基础上创建视图。 

与上一示例中所示的表类似，应将视图放置在单独的架构中：

```sql
create schema ecdc_cosmosdb;
```

现在可以在引用 Cosmos DB 容器的架构中创建一个视图：

```sql
CREATE OR ALTER VIEW ecdc_cosmosdb.Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) WITH
     ( date_rep varchar(20), 
       cases bigint,
       geo_id varchar(6) 
     ) as rows
```

要优化性能，应在 `WITH` 架构定义中使用尽可能小的类型。

> [!NOTE]
> 应将 Azure Cosmos DB 帐户密钥放置在单独的凭据中，并从 `OPENROWSET` 函数引用此凭据。
> 不要在视图定义中保留你的帐户密钥。

## <a name="access-and-permissions"></a>访问和权限

作为最后一步，应创建能够访问 LDW 的数据库用户，并授予他们从外部表和视图选择数据的权限。
在以下脚本中，你可以看到如何添加新用户并提供读取数据的权限：

```sql
CREATE USER [jovan@contoso.com] FROM EXTERNAL PROVIDER;
GO
DENY ADMINISTER DATABASE BULK OPERATIONS TO [jovan@contoso.com]
GO
GRANT SELECT ON SCHEMA::ecdc_adls TO [jovan@contoso.com]
GO
GRANT SELECT ON OBJECT::ecdc_cosmosDB.cases TO [jovan@contoso.com]
GO
GRANT REFERENCES ON CREDENTIAL::MyCosmosDbAccountCredential TO [jovan@contoso.com]
GO
```

安全规则取决于安全策略。 以下是一些一般准则：
- 你应拒绝为新用户提供 `ADMINISTER DATABASE BULK OPERATIONS` 权限，因为他们应只能使用你准备的外部表和视图来读取数据。
- 应只为某些用户能够使用的表提供 `SELECT` 权限。
- 如果提供对使用视图的数据的访问权限，对于将用于访问外部数据源的凭据，应为其授予 `REFERENCES` 权限。

## <a name="next-steps"></a>后续步骤

- 若要了解如何将无服务器 SQL 池连接到 Power BI Desktop 并创建报表，请参阅[将无服务器 SQL 池连接到 Power BI Desktop 并创建报表](tutorial-connect-power-bi-desktop.md)。
- 若要了解如何在无服务器 SQL 池中使用外部表，请参阅[通过 Synapse SQL 使用外部表](develop-tables-external-tables.md?tabs=sql-pool)

