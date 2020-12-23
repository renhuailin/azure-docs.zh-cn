---
title: 使用 Azure Synapse 链接预览中的无服务器 SQL 池查询 Azure Cosmos DB 数据
description: 本文介绍如何使用 Azure Synapse 链接预览版中的无服务器 SQL 池查询 Azure Cosmos DB。
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 12/04/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 22103ad580fa474f44eaf42c696d19bbbd137c8e
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095094"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link-preview"></a>使用 Azure Synapse 链接预览中的无服务器 SQL 池查询 Azure Cosmos DB 数据

> [!IMPORTANT]
> 适用于 Azure Cosmos DB 的 Azure Synapse 链接的无服务器 SQL 池支持当前为预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


无服务器 SQL 池允许分析 Azure Cosmos DB 容器中的数据，这些数据可以近乎实时地使用 [Azure Synapse 链接](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 启用，而不会影响事务工作负荷的性能。 它提供了一种熟悉的 T-sql 语法，用于查询 [分析存储](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 中的数据，以及通过 t-sql 接口将集成连接到各种商业智能 (BI) 和即席查询工具。

对于查询 Azure Cosmos DB，可通过[OPENROWSET](develop-openrowset.md)函数支持完整的[SELECT](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) surface area，其中包括大多数[SQL 函数和运算符](overview-features.md)。 还可以存储从 Azure Cosmos DB 读取数据的查询的结果，以及 Azure Blob 存储中的数据，或使用 [create external table as select](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS) 来 Azure Data Lake Storage。 当前无法使用 CETAS 将无服务器 SQL 池查询结果存储到 Azure Cosmos DB。

在本文中，你将学习如何编写包含无服务器 SQL 池的查询，该查询将从使用 Azure Synapse 链接启用的 Azure Cosmos DB 容器中查询数据。 然后，你可以在 [本教程](./tutorial-data-analyst.md)中详细了解如何通过 Azure Cosmos DB 容器构建无服务器 SQL 池视图并将其连接到 Power BI 模型。

> [!IMPORTANT]
> 本教程使用具有 [Azure Cosmos DB 定义完善的架构](../../cosmos-db/analytical-store-introduction.md#schema-representation)的容器。 无服务器 SQL 池为 [Azure Cosmos DB 完全保真架构](#full-fidelity-schema) 提供的查询体验是临时性的行为，将根据预览反馈进行更改。 不要依赖于函数的结果集架构 `OPENROWSET` ，不使用 `WITH` 从具有完全保真架构的容器读取数据的子句，因为查询体验可能与定义的架构匹配，并根据定义完善的架构更改。 你可以在 [Azure Synapse Analytics 反馈论坛](https://feedback.azure.com/forums/307516-azure-synapse-analytics)中发布你的反馈。 你还可以联系 [Azure Synapse 链接产品团队](mailto:cosmosdbsynapselink@microsoft.com) 以提供反馈。

## <a name="overview"></a>概述

无服务器 SQL 池可用于通过函数查询 Azure Cosmos DB 分析存储 `OPENROWSET` 。 
- `OPENROWSET` 带内联键的。 此语法可用于查询 Azure Cosmos DB 集合，而无需准备凭据。
- `OPENROWSET` 包含 Cosmos DB 帐户密钥的引用凭据。 此语法可用于在 Azure Cosmos DB 集合上创建视图。

### <a name="openrowset-with-key"></a>[带有键的 OPENROWSET](#tab/openrowset-key)

若要支持在 Azure Cosmos DB 分析存储中查询和分析数据，无服务器 SQL 池使用以下 `OPENROWSET` 语法：

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Azure Cosmos DB 连接字符串指定 Azure Cosmos DB 帐户名称、数据库名称、数据库帐户主密钥和函数的可选区域名称 `OPENROWSET` 。

连接字符串具有以下格式：
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

在语法中指定 Azure Cosmos DB 容器名称时不带引号 `OPENROWSET` 。 如果容器名称包含任何特殊字符（例如，短划线 (-) ），则应将名称括在方括号中 (`[]`) 中 `OPENROWSET` 。

### <a name="openrowset-with-credential"></a>[带有 credential 的 OPENROWSET](#tab/openrowset-credential)

你可以使用 `OPENROWSET` 引用 credential 的语法：

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<Azure Cosmos DB connection string without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

在这种情况下，Azure Cosmos DB 连接字符串不包含键。 连接字符串具有以下格式：
```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

数据库帐户主密钥置于服务器级别凭据或数据库作用域凭据中。 

---

> [!IMPORTANT]
> 请确保使用某种 UTF-8 数据库排序规则（例如）， `Latin1_General_100_CI_AS_SC_UTF8` 因为 Azure Cosmos DB 分析存储中的字符串值将编码为 utf-8 文本。
> 文件和排序规则中的文本编码之间的不匹配可能会导致意外的文本转换错误。
> 您可以使用 T-sql 语句轻松更改当前数据库的默认排序规则 `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` 。

> [!NOTE]
> 无服务器 SQL 池不支持查询 Azure Cosmos DB 事务存储。

## <a name="sample-dataset"></a>示例数据集

本文中的示例基于来自 [欧洲数据中心的数据，用于 COVID 和控制 (ECDC) COVID-19 事例](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) 和 [-19 开放式研究数据集 (缆线) ，doi>10.1145： 10.5281/zenodo](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)。

可以在这些页面上查看数据的许可证和结构。 你还可以下载 [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) 和 [电源线-19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) 数据集的示例数据。

若要按照本文中的说明，展示如何使用无服务器 SQL 池查询 Azure Cosmos DB 数据，请确保创建以下资源：

* [已启用 Azure Synapse 链接](../../cosmos-db/configure-synapse-link.md)的 Azure Cosmos DB 数据库帐户。
* 一个名为的 Azure Cosmos DB 数据库 `covid` 。
* 两个名为的 Azure Cosmos DB 容器 `Ecdc` ，并 `Cord19` 加载了前面的示例数据集。

你可以使用以下连接字符串来测试目的： `Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==` 。 请注意，此连接将不能保证性能，因为此帐户与 Synapse SQL 终结点可能位于远程区域。

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>利用自动架构推理浏览 Azure Cosmos DB 数据

在 Azure Cosmos DB 中浏览数据的最简单方法是使用自动架构推理功能。 通过 `WITH` 从语句中省略子句 `OPENROWSET` ，可以指示无服务器 SQL 池自动检测 (推断) Azure Cosmos DB 容器的分析存储的架构。

### <a name="openrowset-with-key"></a>[带有键的 OPENROWSET](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### <a name="openrowset-with-credential"></a>[带有 credential 的 OPENROWSET](#tab/openrowset-credential)

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

在前面的示例中，我们 `covid` `MyCosmosDbAccount` 通过使用 Azure Cosmos DB 密钥 (上述示例中的虚拟) 中所述，将无服务器 SQL 池连接到 Azure Cosmos DB 帐户中的数据库。 然后， `Ecdc` 在该区域中访问容器的分析存储 `West US 2` 。 由于没有特定属性的投影，该 `OPENROWSET` 函数将返回 Azure Cosmos DB 项中的所有属性。

假定 Azure Cosmos DB 容器中的项具有 `date_rep` 、 `cases` 和 `geo_id` 属性，下表显示了此查询的结果：

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

如果需要从同一个 Azure Cosmos DB 数据库的另一个容器浏览数据，则可以使用相同的连接字符串，并将所需的容器作为第三个参数引用：

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>显式指定架构

虽然中的自动架构推理功能 `OPENROWSET` 提供了简单易用的 querience，但你的业务方案可能要求你将架构从 Azure Cosmos DB 数据显式指定为只读相关属性。

`OPENROWSET`函数使您可以显式指定要从容器中的数据读取哪些属性以及指定其数据类型。

假设我们已从 [ECDC COVID 数据集](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) 将一些数据导入到 Azure Cosmos DB 中的以下结构：

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Azure Cosmos DB 中的这些简单 JSON 文档可表示为 Synapse SQL 中的一组行和列。 使用 `OPENROWSET` 函数可以指定要读取的属性的子集和子句中的确切列类型 `WITH` ：

### <a name="openrowset-with-key"></a>[带有键的 OPENROWSET](#tab/openrowset-key)
```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
### <a name="openrowset-with-credential"></a>[带有 credential 的 OPENROWSET](#tab/openrowset-credential)
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
此查询的结果如下表所示：

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

有关应该用于 Azure Cosmos DB 值的 SQL 类型的详细信息，请参阅本文末尾的 [sql 类型映射规则](#azure-cosmos-db-to-sql-type-mappings) 。

## <a name="create-view"></a>创建视图

确定架构后，可以在 Azure Cosmos DB 数据的基础上准备视图。 应将 Azure Cosmos DB 帐户密钥置于单独的凭据中，并从函数引用此凭据 `OPENROWSET` 。 不要在视图定义中保留你的帐户密钥。

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

不要使用 `OPENROWSET` 没有显式定义的架构，因为这可能会影响性能。 请确保使用列的最小可能大小 (例如 VARCHAR (100) ，而不是默认 VARCHAR (8000) # A5。 你应使用某种 UTF-8 排序规则作为默认数据库排序规则或将其设置为显式列排序规则，以避免 [utf-8 转换问题](/azure/synapse-analytics/troubleshoot/reading-utf8-text)。 排序规则 `Latin1_General_100_BIN2_UTF8` 可以在使用某些字符串列筛选数据时提供最佳性能。

## <a name="query-nested-objects-and-arrays"></a>查询嵌套对象和数组

使用 Azure Cosmos DB，可以通过将数据模型作为嵌套对象或数组进行撰写来表示更复杂的数据模型。 适用于 Azure Cosmos DB 的 Azure Synapse 链接的 autosync 功能管理现成分析存储中的架构表示形式，其中包括处理允许从无服务器 SQL 池中进行丰富查询的嵌套数据类型。

例如，" [缆线-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) " 数据集具有遵循此结构的 JSON 文档：

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

当函数读取 Azure Cosmos DB 中的嵌套对象和数组时，它们会在查询结果中表示为 JSON 字符串 `OPENROWSET` 。 使用子句时，可以指定对象中嵌套值的路径 `WITH` ：

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

此查询的结果如下表所示：

| paper_id | title | metadata | 作者 |
| --- | --- | --- |
| bb11206963e831f... | 补充信息 a epidemi .。。 | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1... | 在免疫-E ... 中使用 Convalescent Sera | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649... | Tylosema esculentum (Marama) Tuber and B .。。 | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

详细了解如何分析 [Azure Synapse 链接中的复杂数据类型](../how-to-analyze-complex-schema.md) 和 [无服务器 SQL 池中的嵌套结构](query-parquet-nested-types.md)。

> [!IMPORTANT]
> 如果在文本（ `MÃƒÂ©lade` 而不是）中看到意外字符 `Mélade` ，则数据库排序规则不会设置为 [utf-8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) 排序规则。
> 使用 SQL 语句（如）[将数据库的排序规则更改](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation)为 utf-8 排序规则 `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` 。

## <a name="flatten-nested-arrays"></a>平展嵌套数组

Azure Cosmos DB 数据可能具有与来自 "子" 数据集的作者的数组类似的嵌套 [方式](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) ：

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

在某些情况下，可能需要将顶部项中的属性 "联接" (元数据) 与数组的所有元素 (作者) 。 无服务器 SQL 池可用于通过对嵌套数组应用函数来平展嵌套结构 `OPENJSON` ：

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

此查询的结果如下表所示：

| title | 作者 | first | last | 关系 |
| --- | --- | --- | --- | --- |
| 补充信息 a epidemi .。。 |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
补充信息 a epidemi .。。 | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4# |`{"laboratory":"","institution":"U…` | 
| 补充信息 a epidemi .。。 |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| 补充信息 a epidemi .。。 |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Marc-olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> 如果在文本（ `MÃƒÂ©lade` 而不是）中看到意外字符 `Mélade` ，则数据库排序规则不会设置为 [utf-8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) 排序规则。 使用 SQL 语句（如）[将数据库的排序规则更改](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation)为 utf-8 排序规则 `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` 。

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB 到 SQL 类型的映射

尽管 Azure Cosmos DB 事务存储与架构无关，但分析存储架构化以优化分析查询性能。 利用 Azure Synapse 链接的 autosync 功能，Azure Cosmos DB 管理现成的分析存储中的架构表示形式，其中包括处理嵌套的数据类型。 由于无服务器 SQL 池查询分析存储区，因此必须了解如何将 Azure Cosmos DB 输入数据类型映射到 SQL 数据类型。

Azure Cosmos DB SQL (Core) API 的帐户支持 number、string、Boolean、null、嵌套对象或数组的 JSON 属性类型。 如果正在使用中的子句，则需要选择与这些 JSON 类型匹配的 SQL 类型 `WITH` `OPENROWSET` 。 下表显示了在 Azure Cosmos DB 中用于不同属性类型的 SQL 列类型。

| Azure Cosmos DB 属性类型 | SQL 列类型 |
| --- | --- |
| 布尔 | bit |
| Integer | bigint |
| 小数 | float |
| 字符串 | varchar (UTF-8 数据库排序规则)  |
|  (ISO 格式的字符串的日期时间)  | varchar (30)  |
| UNIX 时间戳 (日期时间)  | bigint |
| Null | `any SQL type` 
| 嵌套的对象或数组 | varchar (最大)  (UTF-8 数据库排序规则) ，序列化为 JSON 文本 |

## <a name="full-fidelity-schema"></a>完全保真架构

Azure Cosmos DB 完全保真架构记录容器中每个属性的值和其最佳匹配类型。 `OPENROWSET`具有完全保真架构的容器上的函数同时提供每个单元中的类型和实际值。 假设下面的查询从具有完全保真架构的容器中读取项：

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
| {"date"： "2020-08-13"} | {"int32"： "254"} | {"string"： "RS"} |
| {"date"： "2020-08-12"} | {"int32"： "235"}| {"string"： "RS"} |
| {"date"： "2020-08-11"} | {"int32"： "316"} | {"string"： "RS"} |
| {"date"： "2020-08-10"} | {"int32"： "281"} | {"string"： "RS"} |
| {"date"： "2020-08-09"} | {"int32"： " | {"string"： "RS"} |
| {"string"： "2020/08/08"} | {"int32"： "312"} | {"string"： "RS"} |
| {"date"： "2020-08-07"} | {"float64"： "339.0"} | {"string"： "RS"} |

对于每个值，你都可以查看 Azure Cosmos DB 容器项中标识的类型。 属性的大多数值 `date_rep` 包含 `date` 值，但其中某些值在 Azure Cosmos DB 中错误地存储为字符串。 完全保真架构将返回正确的类型 `date` 值和格式不正确的 `string` 值。
事例数量是存储为值的信息 `int32` ，但有一个输入为十进制数的值。 此值的 `float64` 类型为。 如果某些值超过了最大值 `int32` ，则会将其存储为 `int64` 类型。 `geo_id`此示例中的所有值都存储为 `string` 类型。

> [!IMPORTANT]
> `OPENROWSET`没有子句的函数 `WITH` 同时公开了具有预期类型的值和输入了输入错误类型的值。 此函数用于数据浏览，而不用于报告。 请勿分析从此函数返回的 JSON 值以生成报表。 使用显式 [WITH 子句](#query-items-with-full-fidelity-schema) 来创建报表。 应该清除 Azure Cosmos DB 容器中具有错误类型的值，以在完全保真分析存储中应用更正。

如果需要查询 Mongo DB API 类型 Azure Cosmos DB 帐户，可以在分析存储中了解有关完整保真架构表示形式的详细信息，以及在 [什么是 Azure Cosmos DB 分析存储 (预览) ？](../../cosmos-db/analytical-store-introduction.md#analytical-schema)。

### <a name="query-items-with-full-fidelity-schema"></a>查询具有完全保真架构的项

查询完全保真架构时，需要在子句中显式指定 SQL 类型和预期 Azure Cosmos DB 属性类型 `WITH` 。 请不要 `OPENROWSET` `WITH` 在报表中使用不带子句的子句，因为结果集的格式可能会根据反馈在预览中进行更改。

在下面的示例中，我们假设 `string` 是属性的正确类型 `geo_id` ，并且 `int32` 是属性的正确类型 `cases` ：

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

`geo_id` `cases` 具有其他类型的和的值将作为值返回 `NULL` 。 此查询将只引用 `cases` 表达式中具有指定类型的 (`cases.int32`) 。

如果具有其他类型 (的值 `cases.int64` ， `cases.float64` 则无法在 Azure Cosmos DB 容器中清除) ，则需要在子句中显式引用这些值， `WITH` 并将结果组合在一起。 下面的查询将聚合 `int32` 、 `int64` 和，并 `float64` 将其存储在 `cases` 列中：

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

在此示例中，事例的数量存储为 `int32` 、 `int64` 或 `float64` 值。 必须提取所有值才能计算每个国家/地区的事例数。

## <a name="known-issues"></a>已知问题

- 无服务器 SQL 池为 [Azure Cosmos DB 完全保真架构](#full-fidelity-schema) 提供的查询体验是临时行为，将根据预览反馈进行更改。 不要依赖于在 `OPENROWSET` `WITH` 公共预览版期间没有子句提供函数的架构，因为查询体验可能与基于客户反馈的定义完善的架构相一致。 若要提供反馈，请联系 [Azure Synapse 链接产品团队](mailto:cosmosdbsynapselink@microsoft.com)。
- 如果 `OPENROWSET` 列排序规则没有 utf-8 编码，则无服务器 SQL 池将返回编译时警告。 您可以 `OPENROWSET` 使用 t-sql 语句轻松更改当前数据库中运行的所有函数的默认排序规则 `alter database current collate Latin1_General_100_CI_AS_SC_UTF8` 。

下表列出了可能的错误和故障排除操作。

| 错误 | 根本原因 |
| --- | --- |
| 语法错误：<br/> -附近有语法错误 `Openrowset`<br/> - `...` 不是可识别的 `BULK OPENROWSET` 提供程序选项。<br/> -附近有语法错误 `...` | 可能的根本原因：<br/> -不使用 CosmosDB 作为第一个参数。<br/> -在第三个参数中使用字符串而不是标识符。<br/> -未指定第三个参数 (容器名称) 。 |
| CosmosDB 连接字符串中存在错误。 | -未指定帐户、数据库或密钥。 <br/> -连接字符串中存在无法识别的选项。<br/> -在 `;` 连接字符串的末尾放置一个分号 () 。 |
| 解析 CosmosDB 路径失败，出现错误 "不正确的帐户名称" 或 "数据库名称不正确"。 | 找不到指定的帐户名称、数据库名称或容器，或者没有为指定的集合启用分析存储。|
| 解析 CosmosDB 路径失败，出现错误 "机密值" 或 "机密为 null 或空"。 | 帐户密钥无效或缺失。 |
| 类型的列 `column name` `type name` 与外部数据类型不兼容 `type name` 。 | 子句中指定的列类型 `WITH` 与 Azure Cosmos DB 容器中的类型不匹配。 尝试更改列类型，如 " [Azure Cosmos DB 到 SQL 类型映射](#azure-cosmos-db-to-sql-type-mappings)" 一节中所述，或使用 `VARCHAR` 类型。 |
| 列包含 `NULL` 所有单元中的值。 | 子句中可能出现错误的列名称或路径表达式 `WITH` 。 子句中的列类型) 后的列名称 (或路径表达式 `WITH` 必须与 Azure Cosmos DB 集合中的某些属性名称相匹配。 比较区分 *大小写*。 例如， `productCode` 和 `ProductCode` 是不同的属性。 |

可以在 [Azure Synapse Analytics 反馈页](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862)上报告建议和问题。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- [使用 Azure Synapse 链接 Power BI 和无服务器 SQL 池](../../cosmos-db/synapse-link-power-bi.md)
- [在无服务器 SQL 池中创建和使用视图](create-use-views.md)
- [有关通过 Azure Cosmos DB 生成无服务器 SQL 池视图并通过 DirectQuery 将它们连接到 Power BI 模型的教程](./tutorial-data-analyst.md)
