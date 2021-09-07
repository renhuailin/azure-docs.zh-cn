---
title: 教程：使用无服务器 SQL 池构建逻辑数据仓库
description: 本教程介绍如何使用无服务器 SQL 池在 Azure 数据源上轻松创建逻辑数据仓库
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 08/20/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: de0374f2ea26e3fa1dc7d25e7c837187f8914918
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254739"
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

以下示例演示了一个引用公共 [ECDC COVID 19 Azure 开放式数据集](../../open-datasets/dataset-ecdc-covid-cases.md)的数据源定义示例：

```sql
CREATE EXTERNAL DATA SOURCE ecdc_cases WITH (
    LOCATION = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/'
);
```

如果数据源的所有者允许匿名访问或为调用方 Azure AD 标识提供显式访问权限，则调用方可以在没有凭据的情况下访问数据源。

可以显式定义在访问外部数据源上的数据时使用的自定义凭据。
- Synapse 工作区的[托管标识](develop-storage-files-storage-access-control.md?tabs=managed-identity)
- Azure 存储的[共享访问签名](develop-storage-files-storage-access-control.md?tabs=shared-access-signature)
- 只读 Cosmos DB 帐户密钥，可用于读取 Cosmos DB 分析存储。

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
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
```

任何具有 Synapse 管理员角色的用户都可以使用这些凭据来访问 Azure Data Lake 存储或 Cosmos DB 分析存储。 如果拥有不具有 Synapse 管理员角色的低权限用户，则需要向他们授予引用以下数据库范围凭据的显式权限：

```sql
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::WorkspaceIdentity TO <user>
GO
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::MyCosmosDbAccountCredential TO <user>
GO
```

有关详细信息，请参阅[授予数据库范围凭据权限](/sql/t-sql/statements/grant-database-scoped-credential-transact-sql)页。

### <a name="define-external-file-formats"></a>定义外部文件格式

外部文件格式定义存储在外部数据源中的文件的结构。 可以定义 Parquet 和 CSV 外部文件格式：

```sql
CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
GO
CREATE EXTERNAL FILE FORMAT CsvFormat WITH (  FORMAT_TYPE = DELIMITEDTEXT );
```

在[此文](develop-tables-external-tables.md?tabs=native#syntax-for-create-external-file-format)中可以找到详细信息

## <a name="explore-your-data"></a>浏览数据

设置数据源后，可以使用 `OPENROWSET` 函数浏览数据。 [OPENROWSET](develop-openrowset.md) 函数会读取远程数据源（如文件）的内容，并将内容作为行集返回。

```sql
select top 10  *
from openrowset(bulk 'latest/ecdc_cases.parquet',
                data_source = 'ecdc_cases',
                format='parquet') as a
```

`OPENROWSET` 函数将为你提供有关外部文件或容器中的列的信息，并使你能够定义外部表和视图的架构。

## <a name="create-external-tables-on-azure-storage"></a>在 Azure 存储上创建外部表

发现架构后，可以在外部数据源之上创建外部表和视图。 正确做法是在数据库架构中组织表和视图。 在下面的查询中，你可以创建一个架构，并将所有访问 Azure data Lake storage 中的 ECDC COVID 数据集的对象放入该架构中：

```sql
create schema ecdc_adls;
```

数据库架构可以用于对对象进行分组并定义每个架构的权限。 

定义架构后，可以创建引用文件的外部表。
以下外部表将引用放置在 Azure 存储中的 ECDC COVID parquet 文件：

```sql
create external table ecdc_adls.cases (
    date_rep                   date,
    day                        smallint,
    month                      smallint,
    year                       smallint,
    cases                      smallint,
    deaths                     smallint,
    countries_and_territories  varchar(256),
    geo_id                     varchar(60),
    country_territory_code     varchar(16),
    pop_data_2018              int,
    continent_exp              varchar(32),
    load_date                  datetime2(7),
    iso_country                varchar(16)
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
以下脚本演示如何添加一个要使用 Azure AD 标识进行身份验证的新用户：

```sql
CREATE USER [jovan@contoso.com] FROM EXTERNAL PROVIDER;
GO
```

可以不创建 Azure AD 主体，而是创建使用登录名和密码进行身份验证的 SQL 主体。

```sql
CREATE LOGIN [jovan] WITH PASSWORD = 'My Very strong Password ! 1234';
CREATE USER [jovan] FROM LOGIN [jovan];
```

对于这两种做法，都可以向用户分配权限。

```sql
DENY ADMINISTER DATABASE BULK OPERATIONS TO [jovan@contoso.com]
GO
GRANT SELECT ON SCHEMA::ecdc_adls TO [jovan@contoso.com]
GO
GRANT SELECT ON OBJECT::ecdc_cosmosDB.cases TO [jovan@contoso.com]
GO
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::MyCosmosDbAccountCredential TO [jovan@contoso.com]
GO
```

安全规则取决于安全策略。 以下是一些一般准则：
- 你应拒绝为新用户提供 `ADMINISTER DATABASE BULK OPERATIONS` 权限，因为他们应只能使用你准备的外部表和视图来读取数据。
- 应只为某些用户能够使用的表提供 `SELECT` 权限。
- 如果提供对使用视图的数据的访问权限，对于将用于访问外部数据源的凭据，应为其授予 `REFERENCES` 权限。

此用户具有查询外部数据所需的最小权限。 如果要创建可以设置权限、外部表和视图的 Power User，可以向该用户授予 `CONTROL` 权限：

```sql
GRANT CONTROL TO [jovan@contoso.com]
```

### <a name="role-based-security"></a>基于角色的安全性

良好的做法是不向单个用户分配权限，而是将用户组织成角色，并在角色级别管理权限。
以下代码示例创建一个新角色来表示可以分析 COVID-19 案例的人员，并将三个用户添加到此角色：

```sql
CREATE ROLE CovidAnalyst;

ALTER ROLE CovidAnalyst ADD MEMBER [jovan@contoso.com];
ALTER ROLE CovidAnalyst ADD MEMBER [milan@contoso.com];
ALTER ROLE CovidAnalyst ADD MEMBER [petar@contoso.com];
```

可将权限分配到属于该组的所有用户：

```sql
GRANT SELECT ON SCHEMA::ecdc_cosmosdb TO [CovidAnalyst];
GO
DENY SELECT ON SCHEMA::ecdc_adls TO [CovidAnalyst];
GO
DENY ADMINISTER DATABASE BULK OPERATIONS TO [CovidAnalyst];
```

这种基于角色的安全访问控制可以简化安全规则的管理。

## <a name="next-steps"></a>后续步骤

- 若要了解如何将无服务器 SQL 池连接到 Power BI Desktop 并创建报表，请参阅[将无服务器 SQL 池连接到 Power BI Desktop 并创建报表](tutorial-connect-power-bi-desktop.md)。
- 若要了解如何在无服务器 SQL 池中使用外部表，请参阅[通过 Synapse SQL 使用外部表](develop-tables-external-tables.md?tabs=sql-pool)
