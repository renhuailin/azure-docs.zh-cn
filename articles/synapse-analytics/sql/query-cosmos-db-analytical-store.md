---
title: 在 Azure Synapse Link 中使用无服务器 SQL 池查询 Azure Cosmos DB 数据
description: 本文介绍如何在 Azure Synapse Link 中使用无服务器 SQL 池查询 Azure Cosmos DB。
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 03/02/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: a0f3e5f707600933ce68e51634145cd3515c5d6a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183018"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link"></a>在 Azure Synapse Link 中使用无服务器 SQL 池查询 Azure Cosmos DB 数据

利用无服务器 SQL 池，可以近乎实时地分析通过 [Azure Synapse Link](../../cosmos-db/synapse-link.md) 启用的 Azure Cosmos DB 容器中的数据，而不会影响事务工作负荷的性能。 它提供熟悉的 T-SQL 语法，用于查询[分析存储](../../cosmos-db/analytical-store-introduction.md)中的数据，并提供通过 T-SQL 接口建立的集成连接，用于连接到各种商业智能 (BI) 和即席查询工具。

查询 Azure Cosmos DB 时，可通过 [OPENROWSET](develop-openrowset.md) 函数支持完整的 [SELECT](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) 外围应用，其中包括大多数 [SQL 函数和运算符](overview-features.md)。 你还可以使用 [create external table as select](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS) 将从 Azure Cosmos DB 读取数据的查询的结果和数据存储在 Azure Blob 存储或 Azure Data Lake Storage 中。 目前无法使用 CETAS 将无服务器 SQL 池查询结果存储到 Azure Cosmos DB。

在本文中，你将了解如何编写一个与无服务器 SQL 池配合使用的查询，该查询将从通过 Azure Synapse Link 启用的 Azure Cosmos DB 容器中查询数据。 然后，你可以在[此教程](./tutorial-data-analyst.md)中详细了解如何构建基于 Azure Cosmos DB 容器的无服务器 SQL 池视图并将其连接到 Power BI 模型。 此教程使用一个具有[定义完善的 Azure Cosmos DB 架构](../../cosmos-db/analytical-store-introduction.md#schema-representation)的容器。 你还可以查看 Learn 模块，了解如何[使用适用于 Azure Synapse Analytics 的 SQL Serverless 查询 Azure Cosmos DB](/learn/modules/query-azure-cosmos-db-with-sql-serverless-for-azure-synapse-analytics/)

## <a name="prerequisites"></a>先决条件

- 请确保已准备好分析存储：
  - 在 [Cosmos DB 容器](../quickstart-connect-synapse-link-cosmos-db.md#enable-azure-cosmos-db-analytical-store)上启用分析存储。
  - 获取将用于查询分析存储的具有只读密钥的连接字符串。 
  - 获取[将用于访问 Cosmos DB 容器的只读密钥](../../cosmos-db/database-security.md#primary-keys)
- 请确保已应用所有[最佳做法](best-practices-serverless-sql-pool.md)，例如：
  - 请确保 Cosmos DB 分析存储与无服务器 SQL 池位于同一区域。
  - 请确保客户端应用程序（Power BI、分析服务）与无服务器 SQL 池位于同一区域。
  - 如果要返回大量数据（大于 80GB），请考虑使用分析服务等缓存层，并在分析服务模型中加载小于 80GB 的分区。
  - 如果要使用字符串列筛选数据，请确保将 `OPENROWSET` 函数与 `WITH` 具有最小可能类型的 explicit 子句结合使用（例如，如果知道该属性最多包含 5个字符，请勿使用 VARCHAR (1000)）。

## <a name="overview"></a>概述

无服务器 SQL 池可用于通过 `OPENROWSET` 函数查询 Azure Cosmos DB 分析存储。 
- 带内联密钥的 `OPENROWSET`。 此语法可用于查询 Azure Cosmos DB 集合，无需准备凭据。
- `OPENROWSET`，其引用的凭据包含 Cosmos DB 帐户密钥。 此语法可用于在 Azure Cosmos DB 集合上创建视图。

### <a name="openrowset-with-key"></a>[带密钥的 OPENROWSET](#tab/openrowset-key)

为了支持在 Azure Cosmos DB 分析存储中查询和分析数据的功能，使用无服务器 SQL 池。 无服务器 SQL 池使用 `OPENROWSET` SQL 语法，因此必须先将 Azure Cosmos DB 连接字符串转换为以下格式：

```sql
OPENROWSET( 
       'CosmosDB',
       '<SQL connection string for Azure Cosmos DB>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Azure Cosmos DB SQL 连接字符串为 `OPENROWSET` 函数指定 Azure Cosmos DB 帐户名称、数据库名称、数据库帐户主密钥和可选的区域名称。 其中的部分信息可从标准 Azure Cosmos DB 连接字符串中获取。

从标准 Azure Cosmos DB 连接字符串格式转换：

```
AccountEndpoint=https://<database account name>.documents.azure.com:443/;AccountKey=<database account master key>;
```

SQL 连接字符串具有以下格式：

```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

此区域是可选的。 如果省略，则使用容器的主要区域。

在 `OPENROWSET` 语法中指定的 Azure Cosmos DB 容器名称不带引号。 在 `OPENROWSET` 语法中，如果容器名称包含特殊字符，例如短划线 (-)，则应将名称括在方括号 (`[]`) 中。

### <a name="openrowset-with-credential"></a>[带凭据的 OPENROWSET](#tab/openrowset-credential)

你可以使用引用凭据的 `OPENROWSET` 语法：

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<SQL connection string for Azure Cosmos DB without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

在本例中，Azure Cosmos DB SQL 连接字符串不包含密钥。 连接字符串具有以下格式：

```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

数据库帐户主密钥置于服务器级别的凭据或数据库范围的凭据中。 

---

> [!IMPORTANT]
> 请确保使用某种 UTF-8 数据库排序规则（例如 `Latin1_General_100_CI_AS_SC_UTF8`），因为 Azure Cosmos DB 分析存储中的字符串值会编码为 UTF-8 文本。
> 文件中的文本编码和排序规则不匹配可能会导致意外的文本转换错误。
> 可以使用 T-SQL 语句 `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` 轻松地更改当前数据库的默认排序规则。

> [!NOTE]
> 无服务器 SQL 池不支持查询 Azure Cosmos DB 事务存储。

## <a name="sample-dataset"></a>示例数据集

本文中的示例基于[欧洲疾病预防控制中心 (ECDC) COVID-19 病例](../../open-datasets/dataset-ecdc-covid-cases.md)和 [COVID-19 开放式研究数据集 (CORD-19)，doi:10.5281/zenodo.3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) 中提供的数据。

可以在这些页面上查看数据的许可证和结构。 还可以下载 [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) 和 [CORD-19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) 数据集的示例数据。

若要按照本文中的说明使用无服务器 SQL 池来查询 Azure Cosmos DB 数据，请确保创建以下资源：

* 一个[启用了 Azure Synapse Link](../../cosmos-db/configure-synapse-link.md) 的 Azure Cosmos DB 数据库帐户。
* 一个名为 `covid` 的 Azure Cosmos DB 数据库。
* 两个名为 `Ecdc` 和 `Cord19` 的 Azure Cosmos DB 容器，其中加载了前面的示例数据集。

你可以将以下连接字符串用于测试目的：`Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==`。 请注意，此连接将不能保证性能，因为此帐户可能位于远程区域（与 Synapse SQL 终结点相比）。

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>利用自动架构推理浏览 Azure Cosmos DB 数据

在 Azure Cosmos DB 中浏览数据的最简单方法是使用自动架构推理功能。 通过省略 `WITH` 语句中的 `OPENROWSET` 子句，可以指示无服务器 SQL 池自动检测（推断）Azure Cosmos DB 容器的分析存储的架构。

### <a name="openrowset-with-key"></a>[带密钥的 OPENROWSET](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### <a name="openrowset-with-credential"></a>[带凭据的 OPENROWSET](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

---

在上一示例中，我们已指示无服务器 SQL 池连接到通过 Azure Cosmos DB 密钥（上一示例中的虚拟密钥）进行身份验证的 Azure Cosmos DB 帐户 `MyCosmosDbAccount` 中的 `covid` 数据库。 然后，我们访问了 `West US 2` 区域中 `Ecdc` 容器的分析存储。 由于没有特定属性的投影，因此 `OPENROWSET` 函数会返回 Azure Cosmos DB 项中的所有属性。

下表显示了此查询的结果（假定 Azure Cosmos DB 容器中的项具有 `date_rep`、`cases` 和 `geo_id` 属性）：

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

如果需要从同一个 Azure Cosmos DB 数据库中的另一个容器浏览数据，则可使用相同的连接字符串，将所需的容器作为第三个参数引用：

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>显式指定架构

虽然 `OPENROWSET` 中的自动架构推理功能提供了简单易用的查询，但你的业务方案可能会要求你将架构显式指定为 Azure Cosmos DB 数据中的只读的相关属性。

`OPENROWSET` 函数使你可以显式指定要从容器中的数据读取哪些属性以及指定其数据类型。

假设我们已将一些具有以下结构的数据从 [ECDC COVID 数据集](../../open-datasets/dataset-ecdc-covid-cases.md)导入 Azure Cosmos DB：

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Azure Cosmos DB 中的这些平面 JSON 文档可表示为 Synapse SQL 中的一组行和列。 使用 `OPENROWSET` 函数可以在 `WITH` 子句中指定要读取的属性的子集和确切的列类型：

### <a name="openrowset-with-key"></a>[带密钥的 OPENROWSET](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

### <a name="openrowset-with-credential"></a>[带凭据的 OPENROWSET](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
   
```

---
此查询的结果可能如下表所示：

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

若要详细了解应该用于 Azure Cosmos DB 值的 SQL 类型，请参阅本文末尾的 [SQL 类型映射规则](#azure-cosmos-db-to-sql-type-mappings)。

## <a name="create-view"></a>创建视图

不建议也不支持在 master 数据库或默认数据库中创建视图。 因此，你需要为视图创建用户数据库。

确定架构后，可以在 Azure Cosmos DB 数据的基础上准备视图。 应将 Azure Cosmos DB 帐户密钥置于单独的凭据中，从 `OPENROWSET` 函数引用此凭据。 不要在视图定义中保留你的帐户密钥。

```sql
CREATE CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

不要使用没有显式定义的架构的 `OPENROWSET`，因为它可能会影响性能。 请确保使用列的最小可能大小，例如 VARCHAR(100)，而不是使用默认的 VARCHAR(8000)。 应使用某种 UTF-8 排序规则作为默认数据库排序规则，或将其设置为显式列排序规则，以避免 [UTF-8 转换问题](../troubleshoot/reading-utf8-text.md)。 排序规则 `Latin1_General_100_BIN2_UTF8` 可以在你使用某些字符串列来筛选数据时提供最佳性能。

## <a name="query-nested-objects-and-arrays"></a>查询嵌套对象和数组

使用 Azure Cosmos DB，可以通过将数据模型作为嵌套对象或数组进行编写来表示更复杂的数据模型。 Azure Synapse Link for Azure Cosmos DB 的自动同步功能直接管理分析存储中的架构表示形式，包括处理那些允许从无服务器 SQL 池中进行丰富查询的嵌套数据类型。

例如，[CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) 数据集具有遵循以下结构的 JSON 文档：

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

当 `OPENROWSET` 函数读取 Azure Cosmos DB 中的嵌套对象和数组时，它们会在查询结果中表示为 JSON 字符串。 使用 `WITH` 子句时，可以指定对象中嵌套值的路径：

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19)
WITH (  paper_id    varchar(8000),
        title        varchar(1000) '$.metadata.title',
        metadata     varchar(max),
        authors      varchar(max) '$.metadata.authors'
) AS docs;
```

此查询的结果可能如下表所示：

| paper_id | title | metadata | 作者 |
| --- | --- | --- | --- |
| bb11206963e831f… | Supplementary Information An eco-epidemi… | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1… | The Use of Convalescent Sera in Immune-E… | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649… | Tylosema esculentum (Marama) Tuber and B… | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

详细了解如何分析 [Azure Synapse Link 中的复杂数据类型](../how-to-analyze-complex-schema.md)和[无服务器 SQL 池中的嵌套结构](query-parquet-nested-types.md)。

> [!IMPORTANT]
> 如果在文本中看到意外字符（例如 `MÃƒÂ©lade`，本应为 `Mélade`），则表明数据库排序规则未设置为 [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) 排序规则。
> 使用 SQL 语句（例如 `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8`）[将数据库的排序规则更改](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation)为 UTF-8 排序规则。

## <a name="flatten-nested-arrays"></a>平展嵌套数组

Azure Cosmos DB 数据可能具有与 [CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) 数据集中的 authors 数组类似的嵌套子数组：

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

在某些情况下，可能需要将顶部项（元数据）中的属性与数组 (authors) 的所有元素进行“联接”。 无服务器 SQL 池可用于通过对嵌套数组应用 `OPENJSON` 函数来平展嵌套结构：

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

此查询的结果可能如下表所示：

| title | 作者 | first | last | affiliation |
| --- | --- | --- | --- | --- |
| Supplementary Information An eco-epidemi… |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Supplementary Information An eco-epidemi… | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4# |`{"laboratory":"","institution":"U…` | 
| Supplementary Information An eco-epidemi… |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Supplementary Information An eco-epidemi… |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> 如果在文本中看到意外字符（例如 `MÃƒÂ©lade`，本应为 `Mélade`），则表明数据库排序规则未设置为 [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) 排序规则。 使用 SQL 语句（例如 `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8`）[将数据库的排序规则更改](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation)为 UTF-8 排序规则。

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB 到 SQL 类型的映射

尽管 Azure Cosmos DB 事务存储与架构无关，但分析存储已架构化，因此可以针对分析查询性能进行优化。 使用 Azure Synapse Link 的自动同步功能，Azure Cosmos DB 可以直接管理分析存储中的架构表示形式，包括处理嵌套数据类型。 由于无服务器 SQL 池会查询分析存储，因此必须了解如何将 Azure Cosmos DB 输入数据类型映射到 SQL 数据类型。

SQL (Core) API 的 Azure Cosmos DB 帐户支持数字、字符串、布尔值、Null、嵌套对象或数组 JSON 属性类型。 如果在 `OPENROWSET` 中使用 `WITH` 子句，则需选择与这些 JSON 类型匹配的 SQL 类型。 下表显示了应该用于 Azure Cosmos DB 中的不同属性类型的 SQL 列类型。

| Azure Cosmos DB 属性类型 | SQL 列类型 |
| --- | --- |
| 布尔 | bit |
| Integer | bigint |
| 小数 | FLOAT |
| 字符串 | varchar（UTF-8 数据库排序规则） |
| 日期时间（ISO 格式的字符串） | varchar(30) |
| 日期时间（UNIX 时间戳） | bigint |
| Null | `any SQL type` 
| 嵌套对象或数组 | varchar(max)（UTF-8 数据库排序规则），序列化为 JSON 文本 |

## <a name="full-fidelity-schema"></a>全保真架构

Azure Cosmos DB 全保真架构记录容器中每个属性的值及其最佳匹配类型。 具有全保真架构的容器中的 `OPENROWSET` 函数同时在每个单元格中提供类型和实际值。 假设下面的查询从具有全保真架构的容器中读取项：

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) as rows
```

此查询的结果将返回格式化为 JSON 文本的类型和值：

| date_rep | cases | geo_id |
| --- | --- | --- |
| {"date":"2020-08-13"} | {"int32":"254"} | {"string":"RS"} |
| {"date":"2020-08-12"} | {"int32":"235"}| {"string":"RS"} |
| {"date":"2020-08-11"} | {"int32":"316"} | {"string":"RS"} |
| {"date":"2020-08-10"} | {"int32":"281"} | {"string":"RS"} |
| {"date":"2020-08-09"} | {"int32":"295"} | {"string":"RS"} |
| {"string":"2020/08/08"} | {"int32":"312"} | {"string":"RS"} |
| {"date":"2020-08-07"} | {"float64":"339.0"} | {"string":"RS"} |

对于每个值，你都可以查看 Azure Cosmos DB 容器项中标识的类型。 `date_rep` 属性的大多数值包含 `date` 值，但部分值在 Azure Cosmos DB 中错误地存储为字符串。 全保真架构会返回类型设置正确的 `date` 值和格式设置不正确的 `string` 值。
病例数是存储为 `int32` 值的信息，但有一个作为十进制数输入的值。 该值有 `float64` 类型。 如果某些值超过了最大的 `int32` 数，则会将其存储为 `int64` 类型。 此示例中的所有 `geo_id` 值都存储为 `string` 类型。

> [!IMPORTANT]
> 没有 `WITH` 子句的 `OPENROWSET` 函数同时公开了具有预期类型的值和输入的类型错误的值。 此函数旨在用于数据浏览，而不用于报告。 请勿分析此函数返回的 JSON 值来生成报表。 请使用显式 [WITH 子句](#query-items-with-full-fidelity-schema)来创建报表。 应该清除 Azure Cosmos DB 容器中具有错误类型的值，以在全保真分析存储中应用更正。

如果需要查询 Mongo DB API 类型的 Azure Cosmos DB 帐户，可以在分析存储中详细了解全保真架构表示形式，以及要在 [Azure Cosmos DB 分析存储是什么？](../../cosmos-db/analytical-store-introduction.md#analytical-schema)中使用的扩展属性名称。

### <a name="query-items-with-full-fidelity-schema"></a>查询具有全保真架构的项

查询全保真架构时，需要在 `WITH` 子句中显式指定 SQL 类型和预期的 Azure Cosmos DB 属性类型。

在下面的示例中，我们假设 `string` 是 `geo_id` 属性的正确类型，`int32` 是 `cases` 属性的正确类型：

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

`geo_id` 和 `cases` 的其他类型的值会作为 `NULL` 值返回。 此查询将只引用在表达式中具有指定类型 (`cases.int32`) 的 `cases`。

如果有其他类型（`cases.int64`、`cases.float64`）的无法在 Azure Cosmos DB 容器中清除的值，则需在 `WITH` 子句中显式引用这些值，并将结果组合在一起。 以下查询会聚合存储在 `cases` 列中的 `int32`、`int64` 和 `float64`：

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

在此示例中，病例的数量存储为 `int32`、`int64` 或 `float64` 值。 必须提取所有值才能计算每个国家/地区的病例数。

## <a name="troubleshooting"></a>故障排除

查看[自助页面](resources-self-help-sql-on-demand.md#cosmos-db)，查找可帮助你解决 Cosmos DB 查询的潜在问题的已知问题或故障排除步骤。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- [通过 Azure Synapse Link 使用 Power BI 和无服务器 SQL 池](../../cosmos-db/synapse-link-power-bi.md)
- [在无服务器 SQL 池中创建和使用视图](create-use-views.md)
- [教程：如何通过 DirectQuery 构建基于 Azure Cosmos DB 的无服务器 SQL 池视图并将其连接到 Power BI 模型](./tutorial-data-analyst.md)
- 如果遇到一些错误或遇到性能问题，请访问 [Cosmos DB 自助页的 Synapse 链接](resources-self-help-sql-on-demand.md#cosmos-db)。
- 还可查看 Learn 模块，了解如何[使用适用于 Azure Synapse Analytics 的 SQL Serverless 查询 Azure Cosmos DB](/learn/modules/query-azure-cosmos-db-with-sql-serverless-for-azure-synapse-analytics/)。