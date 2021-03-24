---
title: 使用无服务器 SQL 池查询 Parquet 嵌套类型
description: 本文介绍如何使用无服务器 SQL 池查询 Parquet 嵌套类型。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 2ef547bea9ba84665ec7187a5adf2f7a7d87e0a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666335"
---
# <a name="query-nested-types-in-parquet-and-json-files-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用无服务器 SQL 池查询 Parquet 和 JSON 文件中的嵌套类型

本文介绍如何在 Azure Synapse Analytics 中使用无服务器 SQL 池编写查询。 此查询将读取 Parquet 嵌套类型。
嵌套类型是表示对象或数组的复杂结构。 嵌套类型可以存储在： 
- [Parquet](query-parquet-files.md)，其中可以有多个包含数组和对象的复杂列。
- 分层 [JSON 文件](query-json-files.md)，可以在其中将复杂的 JSON 文档作为单列读取。
- Azure Cosmos DB 集合（目前处于封闭的公共预览阶段），其中每个文档都可以包含复杂的嵌套属性。

无服务器 SQL 池可将所有嵌套类型格式化为 JSON 对象和数组。 因此，你可以[使用 JSON 函数提取或修改复杂对象](/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server)，或者[使用 OPENJSON 函数分析 JSON 数据](/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server)。 

下面是一个查询示例，该查询从包含嵌套对象的 [COVID-19 开放式研究数据集](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) JSON 文件中提取标量和对象值： 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

`JSON_VALUE` 函数从指定路径处的字段返回一个标量值。 `JSON_QUERY` 函数从指定路径处的字段返回一个格式化为 JSON 的对象。

> [!IMPORTANT]
> 此示例使用 COVID-19 开放式研究数据集中的文件。 [在此处查看许可证和数据结构](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)。

## <a name="prerequisites"></a>先决条件

第一步是创建将在其中创建数据源的数据库。 然后通过对数据库运行[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)来初始化这些对象。 此安装脚本将创建数据源、数据库范围的凭据以及在这些示例中使用的外部文件格式。

## <a name="project-nested-or-repeated-data"></a>投影嵌套数据或重复数据

Parquet 文件可以包含多个具有复杂类型的列。 这些列中的值格式化为 JSON 文本，并作为 VARCHAR 列返回。 以下查询读取 structExample.parquet 文件，并演示如何读取嵌套列的值： 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

此查询返回以下结果。 每个嵌套对象的内容均以 JSON 文本的形式返回。

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date":"2009-04-25"}| {"Time":"20:51:54.3598000"}|    {"Timestamp":"5501-04-08 12:13:57.4821000"}|    {"Decimal":11143412.25350}| {"Float":0.5}|
|{"Date":"1916-04-29"}| {"Time":"00:16:04.6778000"}|    {"Timestamp":"1990-06-30 20:50:52.6828000"}|    {"Decimal":1963545.62800}|  {"Float":-2.125}|

以下查询读取 justSimpleArray.parquet 文件。 它将投影 Parquet 文件中的所有列（包括嵌套数据和重复数据）。

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

此查询将返回以下结果：

|SimpleArray|
| --- |
|[11,12,13]|
|[21,22,23]|

## <a name="read-properties-from-nested-object-columns"></a>从嵌套对象列中读取属性

使用 `JSON_VALUE` 函数可以从格式化为 JSON 文本的列中返回值：

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

结果如下表所示：

|title  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Supplementary Information An eco-epidemiolo... | Julien   | - Figure S1 : Phylogeny of... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

与 JSON 文件（大多数情况下返回包含复杂 JSON 对象的单个列）不同，Parquet 文件可以具有多个复杂列。 你可以通过对每个列使用 `JSON_VALUE` 函数来读取嵌套列的属性。 `OPENROWSET` 允许直接在 `WITH` 子句中指定嵌套属性的路径。 你可以将路径设置为列的名称，也可以在列类型后面添加 [JSON 路径表达式](/sql/relational-databases/json/json-path-expressions-sql-server)。

下面的查询将读取 structExample.parquet 文件，并演示如何呈现嵌套列的元素。 可通过两种方式引用嵌套值：
- 在类型规范后面指定嵌套值路径表达式。
- 通过使用点“.”引用字段，将列名称格式化为嵌套路径。

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>访问重复列中的元素

下面的查询将读取 justSimpleArray.parquet 文件，并使用 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) 从重复列（如数组或映射）中检索标量元素：

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

结果如下：

|SimpleArray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11,12,13] | 11   | 12 | 13 |
| [21,22,23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>访问复杂列中的子对象

下面的查询将读取 mapExample.parquet 文件，并使用 [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest&preserve-view=true) 从重复列（如数组或映射）中检索非标量元素：

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

你还可以在 `WITH` 子句中显式引用要返回的列：

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

`MapOfPersons` 结构作为 VARCHAR 列返回，并格式化为 JSON 字符串。

## <a name="project-values-from-repeated-columns"></a>投影重复列中的值

如果某些列中有标量值数组（例如 `[1,2,3]`），则可以使用以下脚本轻松扩展它们并将其与主行联接：

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>后续步骤

下一篇文章将介绍如何[查询 JSON 文件](query-json-files.md)。