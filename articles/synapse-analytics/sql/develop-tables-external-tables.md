---
title: 通过 Synapse SQL 使用外部表
description: 使用 Synapse SQL 中的外部表读取或写入数据文件
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/26/2021
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 834feed476c307bc1a16bf95719b630389e58511
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430785"
---
# <a name="use-external-tables-with-synapse-sql"></a>通过 Synapse SQL 使用外部表

外部表指向位于 Hadoop、Azure 存储 Blob 或 Azure Data Lake Storage 中的数据。 外部表用于读取文件中的数据，或将数据写入 Azure 存储中的文件。 借助 Synapse SQL，可以使用外部表通过专用 SQL 池或无服务器 SQL 池读取和写入数据。

根据外部数据源的类型，可以使用两种类型的外部表：
- Hadoop 外部表，可用于读取和导出各种数据格式（例如 CSV、Parquet 和 ORC）的数据。 Hadoop 外部表可在专用 SQL 池中使用，但无法在无服务器 SQL 池中使用。
- 原生外部表，可用于以各种数据格式（如 CSV 和 Parquet）读取和导出数据。 原生外部表可在无服务器 SQL 池中使用，在专用 Synapse SQL 池中为预览版。

下表列出了 Hadoop 和本机外部表之间的主要区别：

| 外部表类型 | Hadoop | 本机 |
| --- | --- | --- |
| 专用 SQL 池 | 可用 | Parquet 表在门控式预览版中提供 - 请联系你的 Microsoft 技术客户经理或云解决方案架构师，看看是否可以将专用 SQL 池添加到门控式预览版。 |
| 无服务器 SQL 池 | 不可用 | 可用 |
| 支持的格式 | 带分隔符/CSV、Parquet、ORC、Hive RC 和 RC | 无服务器 SQL 池：带分隔符/CSV、Parquet 和 Delta Lake（预览版）<br/>专用 SQL 池：Parquet |
| 文件夹分区清除 | 否 | 仅适用于从 Synapse 工作区中的 Apache Spark 池同步到无服务器 SQL 池的分区表 |
| 适用于位置的自定义格式 | 是 | 是，使用 `/year=*/month=*/day=*` 等通配符 |
| 递归文件夹扫描 | 否 | 仅在无服务器 SQL 池中（当在位置路径末尾指定了 `/**` 时） |
| 存储筛选器下推 | 否 | 在无服务器 SQL 池中为“是”。 对于字符串下推，需要对 `VARCHAR` 列使用 `Latin1_General_100_BIN2_UTF8` 排序规则。 |
| 存储身份验证 | 存储访问密钥 (SAK)、AAD 直通、托管标识、自定义应用程序 Azure AD 标识 | 共享访问签名 (SAS)、AAD 直通、托管标识 |

> [!NOTE]
> Delta Lake 格式的原生外部表为公共预览版。 [CETAS](develop-tables-cetas.md) 不支持以 Delta Lake 格式导出内容。

## <a name="external-tables-in-dedicated-sql-pool-and-serverless-sql-pool"></a>专用 SQL 池和无服务器 SQL 池中的外部表

可以使用外部表来执行以下操作：

- 使用 Transact-SQL 语句查询 Azure Blob 存储和 Azure Data Lake Gen2。
- 使用 [CETAS](develop-tables-cetas.md) 将查询结果存储到 Azure Blob 存储或 Azure Data Lake Storage 中的文件
- 从 Azure Blob 存储和 Azure Data Lake Storage 导入数据并将其存储到专用 SQL 池中（仅限专用池中的 Hadoop 表）。

> [!NOTE]
> 与 [CREATE TABLE AS SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context) 语句结合使用时，从外部表中选择数据可将数据导入到专用 SQL 池中的表。 除了 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)以外，外部表也可用于加载数据。 
> 
> 有关加载操作的教程，请参阅[使用 PolyBase 从 Azure Blob 存储加载数据](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)。

可通过以下步骤在 Synapse SQL 池中创建外部表：

1. [CREATE EXTERNAL DATA SOURCE](#create-external-data-source) 以引用外部 Azure 存储并指定应当用来访问该存储的凭据。
2. [CREATE EXTERNAL FILE FORMAT](#create-external-file-format) 以描述 CSV 或 Parquet 文件的格式。
3. 基于以相同文件格式放置在数据源上的文件来 [CREATE EXTERNAL TABLE](#create-external-table)。

### <a name="security"></a>安全性

用户必须具有对外部表的 `SELECT` 权限才能读取数据。
外部表使用数据库范围的凭据访问基础 Azure 存储，这些凭据使用以下规则在数据源中定义：
- 没有凭据的数据源使外部表可访问 Azure 存储上公开可用的文件。
- 数据源可能有一个凭据，外部表可以使用该凭据通过 SAS 令牌或工作区托管标识仅访问 Azure 存储上的文件 - 有关示例，请参阅[开发存储文件存储访问控制](develop-storage-files-storage-access-control.md#examples)一文。

## <a name="create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE

外部数据源用于连接到存储帐户。 [此处](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)提供了完整文档。

### <a name="syntax-for-create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE 的语法

#### <a name="hadoop"></a>[Hadoop](#tab/hadoop)

使用 `TYPE=HADOOP` 的外部数据源仅在专用 SQL 池中可用。

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
     , TYPE = HADOOP
)
[;]
```

#### <a name="native"></a>[本机](#tab/native)

没有 `TYPE=HADOOP` 限制的外部数据源通常可在无服务器 SQL 池中使用，在专用池中为公共预览版。

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
)
[;]
```

---

### <a name="arguments-for-create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE 的参数

#### <a name="data_source_name"></a>data_source_name

指定数据源的用户定义名称。 该名称在数据库中必须唯一。

#### <a name="location"></a>位置
LOCATION = `'<prefix>://<path>'` - 提供连接协议和外部数据源的路径。 以下模式可用于位置：

| 外部数据源        | 位置前缀 | 位置路径                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob 存储          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Blob 存储          | `http[s]`       | `<storage_account>.blob.core.windows.net/<container>/subfolders` |
| Azure Data Lake Store Gen 1 | `http[s]`       | `<storage_account>.azuredatalakestore.net/webhdfs/v1` |
| Azure Data Lake Store Gen 2 | `http[s]`       | `<storage_account>.dfs.core.windows.net/<container>/subfolders`  |

`https:` 前缀允许使用路径中的子文件夹。

#### <a name="credential"></a>凭据
CREDENTIAL = `<database scoped credential>` 是可选凭据，用于在 Azure 存储上进行身份验证。 无凭据的外部数据源可以访问公共存储帐户，也可以使用调用方的 Azure AD 标识访问存储中的文件。 
- 在专用 SQL 池中，数据库范围的凭据可以指定自定义应用程序标识、工作区托管标识或 SAK 密钥。 
- 在无服务器 SQL 池中，数据库范围的凭据可以指定工作区托管标识或 SAK 密钥。 

#### <a name="type"></a>TYPE
选项 TYPE = `HADOOP` 用于指定应使用基于 Java 的技术来访问基础文件。 此参数不能用于使用内置原生读取器的无服务器 SQL 池。

### <a name="example-for-create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE 的示例

#### <a name="hadoop"></a>[Hadoop](#tab/hadoop)

以下示例在专用 SQL 池中为 Azure Data Lake Gen2 创建一个指向 New York 数据集的 Hadoop 外部数据源：

```sql
CREATE DATABASE SCOPED CREDENTIAL [ADLS_credential]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

以下示例为 Azure Data Lake Gen2 创建一个指向公开可用的 New York 数据集的外部数据源：

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/',
       TYPE = HADOOP)
```

#### <a name="native"></a>[本机](#tab/native)

以下示例在无服务器或专用 SQL 池中为 Azure Data Lake Gen2 创建一个允许使用 SAS 凭据进行访问的外部数据源：

```sql
CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

以下示例为 Azure Data Lake Gen2 创建一个指向公开可用的 New York 数据集的外部数据源：

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

创建一个外部文件格式对象，用于定义存储在 Azure Blob 存储或 Azure Data Lake Storage 中的外部数据。 创建外部文件格式是创建外部表的先决条件。 [此处](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)提供了完整文档。

创建外部文件格式会指定外部表引用的数据的实际布局。

### <a name="syntax-for-create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT 的语法

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
    | PARSER_VERSION = {'parser_version'}
}
```

### <a name="arguments-for-create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT 的参数

file_format_name - 指定外部文件格式的名称。

FORMAT_TYPE = [ PARQUET | DELIMITEDTEXT] - 指定外部数据的格式。

- PARQUET - 指定 Parquet 格式。
- DELIMITEDTEXT - 指定具有列分隔符（也称为字段终止符）的文本格式。

FIELD_TERMINATOR = *field_terminator* - 仅适用于带分隔符的文本文件。 字段终止符指定一个或多个字符，用于在带分隔符的文本文件中标记每个字段（列）的末尾。 默认为竖线字符（“|”）。

示例：

- FIELD_TERMINATOR = '|'
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR = ꞌ\tꞌ

STRING_DELIMITER = *string_delimiter* - 为带分隔符的文本文件中的字符串类型数据指定字段终止符。 字符串分隔符的长度是一个或多个字符，并且用单引号括起来。 默认为空字符串 ("")。

示例：

- STRING_DELIMITER = '"'
- STRING_DELIMITER = '*'
- STRING_DELIMITER = ꞌ,ꞌ

FIRST_ROW = *First_row_int* - 指定首先要读取的行号，此值将应用于所有文件。 如果将值设置为 2，则在加载数据时，会跳过每个文件中的第一行（标题行）。 会基于行终止符（/r/n、/r、/n）的存在跳过行。

USE_TYPE_DEFAULT = { TRUE | **FALSE** } - 指定在从文本文件中检索数据时，如何处理带分隔符的文本文件中缺失的值。

TRUE - 如果从文本文件中检索数据，则使用外部表定义中对应列的默认值数据类型来存储每个缺失值。 例如，将缺失值替换为：

- 如果列定义为数字列，则替换为 0。 不支持小数列，它们会导致错误。
- 如果列是字符串列，则替换为空字符串 ("")。
- 如果列是日期列，则替换为 1900-01-01。

FALSE - 将所有缺失值作为 NULL 存储。 在带分隔符的文本文件中使用 NULL 一词存储的任何 NULL 值都会作为字符串“NULL”导入。

Encoding = {'UTF8' | 'UTF16'} - 无服务器 SQL 池可以读取 UTF8 和 UTF16 编码的带分隔符的文本文件。

DATA_COMPRESSION = *data_compression_method* - 此参数为外部数据指定数据压缩方法。 

PARQUET 文件格式类型支持以下压缩方法：

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

从 PARQUET 外部表进行读取时，会忽略此参数，但在使用 [CETAS](develop-tables-cetas.md) 写入外部表时会使用此参数。

DELIMITEDTEXT 文件格式类型支持以下压缩方法：

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'

PARSER_VERSION = 'parser_version' 指定读取 CSV 文件时要使用的分析器版本。 可用的分析器版本为 `1.0` 和 `2.0`。 此选项仅适用于无服务器 SQL 池。

### <a name="example-for-create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT 的示例

以下示例为人口普查文件创建外部文件格式：

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>CREATE EXTERNAL TABLE

CREATE EXTERNAL TABLE 命令为 Synapse SQL 创建一个外部表，用于访问 Azure Blob 存储或 Azure Data Lake Storage 中存储的数据。 

### <a name="syntax-for-create-external-table"></a>CREATE EXTERNAL TABLE 的语法

```sql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
        [, TABLE_OPTIONS = N'{"READ_OPTIONS":["ALLOW_INCONSISTENT_READS"]}' ]
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
```

### <a name="arguments-create-external-table"></a>CREATE EXTERNAL TABLE 的参数

{ database_name.schema_name.table_name | schema_name.table_name | table_name }

要创建的表的一到三部分名称。 对于外部表，Synapse SQL 池仅存储表元数据。 在 Synapse SQL 数据库中不移动或存储任何实际数据。

<column_definition>, ...*n* ]

CREATE EXTERNAL TABLE 支持配置列名、数据类型和排序规则的功能。 不能对外部表使用 DEFAULT CONSTRAINT。

>[!IMPORTANT]
>列定义（包括数据类型和列数）必须与外部文件中的数据匹配。 如果存在不匹配，则在查询实际数据时会拒绝文件行。

从 Parquet 文件读取数据时，可以仅指定所要读取的列，并跳过其余的列。

#### <a name="location"></a>LOCATION

LOCATION = '*folder_or_filepath*'

为 Azure Blob 存储中的实际数据指定文件夹或文件路径和文件名。 位置从根文件夹开始。 根文件夹是外部数据源中指定的数据位置。

![外部表的递归数据](./media/develop-tables-external-tables/folder-traversal.png)

与 Hadoop 外部表不同的是，除非在路径末尾指定 /**，否则本机外部表不返回子文件夹。 在此示例中，如果 LOCATION='/webdata/'，则无服务器 SQL 池查询将返回 mydata.txt 中的行。 它不返回 mydata2.txt 和 mydata3.txt，因为这些文件位于子文件夹中。 Hadoop 表会返回所有子文件夹中的所有文件。
 
Hadoop 和本机外部表均会跳过名称以下划线 (_) 或句点 (.) 开头的文件。

#### <a name="data_source"></a>DATA_SOURCE

DATA_SOURCE = *external_data_source_name* - 指定包含外部数据位置的外部数据源的名称。 要创建外部数据源，请使用 [CREATE EXTERNAL DATA SOURCE](#create-external-data-source)。

FILE_FORMAT = *external_file_format_name* - 指定为外部数据存储文件类型和压缩方法的外部文件格式对象的名称。 若要创建外部文件格式，请使用 [CREATE EXTERNAL FILE FORMAT](#create-external-file-format)。

#### <a name="table_options"></a>TABLE_OPTIONS

TABLE_OPTIONS = json 选项 - 指定描述如何读取基础文件的选项集。 目前，唯一可用的选项是 `"READ_OPTIONS":["ALLOW_INCONSISTENT_READS"]`，它指示外部表忽略对基础文件进行的更新，即使这可能导致一些不一致的读取操作。 仅在经常追加文件的特殊情况下使用此选项。 此选项在 CSV 格式的无服务器 SQL 池中可用。

### <a name="permissions-create-external-table"></a>CREATE EXTERNAL TABLE 的权限

若要从某个外部表中进行选择，需要提供拥有列出和读取权限的适当凭据。

### <a name="example-create-external-table"></a>CREATE EXTERNAL TABLE 示例

以下示例创建一个外部表。 它返回第一行：

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>从 Azure Data Lake 中的文件创建和查询外部表

现在，可以使用 Synapse Studio 的 Data Lake 浏览功能，通过简单的右键单击文件操作，使用 Synapse SQL 池创建和查询外部表。 仅 Parquet 文件支持通过单击手势从 ADLS Gen2 存储帐户创建外部表。 

### <a name="prerequisites"></a>先决条件

- 你必须有权访问工作区，并且至少具有 `Storage Blob Data Contributor` 访问角色（拥有对 ADLS Gen2 帐户或访问控制列表 (ACL) 的访问权限），然后才能查询这些文件。

- 必须至少拥有在 Synapse SQL 池（专用或无服务器）中创建和查询外部表的[权限](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#permissions-2&preserve-view=true)。

在“数据”面板中，选择要从其创建外部表的文件：
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

此时会打开一个对话框窗口。 选择“专用 SQL 池”或“无服务器 SQL 池”，为表命名，然后选择“打开脚本”：

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

系统会从该文件推理架构并自动生成 SQL 脚本：
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

运行该脚本。 该脚本将自动运行 Select Top 100 *.：
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

现已创建外部表，将来若要浏览此外部表的内容，用户可以直接从“数据”窗格查询：
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>后续步骤

查看 [CETAS](develop-tables-cetas.md) 一文，了解如何将查询结果保存到 Azure 存储中的外部表。 或者可以开始查询 [Apache Spark for Azure Synapse 外部表](develop-storage-files-spark-tables.md)。
