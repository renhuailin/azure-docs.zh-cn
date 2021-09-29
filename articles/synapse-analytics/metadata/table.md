---
title: 共享元数据表
description: Azure Synapse Analytics 提供了一个共享的元数据模型，如果使用该模型在无服务器 Apache Spark 池中创建一个表，则可以从无服务器 SQL 池和专用 SQL 池访问该表，而无需复制数据。
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 1f9cb59e7216f038baf96c3a809fb88c6e584d6c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124735152"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Azure Synapse Analytics 共享元数据表


Azure Synapse Analytics 允许不同的工作区计算引擎在其 Apache Spark 池和无服务器 SQL 池之间共享数据库和 Parquet 支持的表。

Spark 作业创建数据库后，你可以通过 Spark，在该数据库中创建使用 Parquet 作为存储格式的表。 表名称将转换为小写，并且需要使用小写名进行查询。 这些表将立即可供任何 Azure Synapse 工作区 Spark 池查询。 还可以在任何 Spark 作业中按权限使用这些表。

Spark 创建的表、托管表和外部表还可以使用相同名称在无服务器 SQL 池的相应已同步数据库中以外部表形式提供。 [在 SQL 中公开 Spark 表](#expose-a-spark-table-in-sql)提供了有关表同步的更多详细信息。

由于表以异步方式同步到无服务器 SQL 池，因此这些表出现的时间会有延迟。

## <a name="manage-a-spark-created-table"></a>管理 Spark 创建的表

使用 Spark 管理 Spark 创建的数据库。 例如，通过无服务器 Apache Spark 池作业删除数据库，通过 Spark 在数据库中创建表。

如果在无服务器 SQL 池的此类数据库中创建对象，或尝试删除数据库，该操作会失败。 不能通过无服务器 SQL 池更改原始 Spark 数据库。

## <a name="expose-a-spark-table-in-sql"></a>使用 SQL 公开 Spark 表

### <a name="shared-spark-tables"></a>共享的 Spark 表

Spark 提供两种类型的、由 Azure Synapse 在 SQL 中自动公开的表：

- 托管表

  Spark 提供多种选项（例如 TEXT、CSV、JSON、JDBC、PARQUET、ORC、HIVE、DELTA 和 LIBSVM），供用户选择如何在托管表中存储数据。 这些文件通常存储在托管表数据所存储到的 `warehouse` 目录中。

- 外部表

  Spark 还可让用户通过提供 `LOCATION` 选项或使用 Hive 格式，来基于现有数据创建外部表。 可以基于各种数据格式（包括 Parquet）创建此类外部表。

Azure Synapse 目前只共享使用 SQL 引擎以 Parquet 或 CSV 格式存储其数据的托管 Spark 表和外部 Spark 表。 其他格式支持的表不会自动同步。 你可以在自己的 SQL 数据库中自行显式将此类表作为外部表进行同步，前提是 SQL 引擎支持这些表的基础格式。

### <a name="share-spark-tables"></a>共享 Spark 表

可共享的托管 Spark 表和外部 Spark 表在 SQL 引擎中作为具有以下属性的外部表公开：

- SQL 外部表的数据源是表示 Spark 表位置文件夹的数据源。
- SQL 外部表的文件格式为 Parquet。
- SQL 外部表的访问凭据是直通身份验证凭据。

由于所有 Spark 表名称是有效的 SQL 表名称，且所有 Spark 列名称是有效的 SQL 列名称，因此 Spark 表名称和列名称将用于 SQL 外部表。

Spark 表与 Synapse SQL 引擎提供的数据类型不同。 下表将 Spark 表数据类型映射到了 SQL 类型：

| Spark 数据类型 | SQL 数据类型 | 注释 |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | 使用排序规则 `Latin1_General_100_BIN2_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | 使用排序规则 `Latin1_General_100_BIN2_UTF8` 序列化为 JSON |
| `map`       |    `varchar(max)`   | 使用排序规则 `Latin1_General_100_BIN2_UTF8` 序列化为 JSON |
| `struct`    |    `varchar(max)`   | 使用排序规则 `Latin1_General_100_BIN2_UTF8` 序列化为 JSON |

## <a name="security-model"></a>安全模型

Spark 数据库和表及其在 SQL 引擎中的已同步表示形式将在基础存储级别受到保护。 由于它们当前对对象本身不拥有权限，因此可以在对象资源管理器中查看对象。

创建托管表的安全主体被视为该表的所有者，对该表以及基础文件夹和文件拥有所有权限。 此外，数据库的所有者将自动成为该表的共同所有者。

如果使用直通身份验证创建 Spark 或 SQL 外部表，则数据只会在文件夹和文件级别受到保护。 如果某人查询这种类型的外部表，则该查询提交者的安全标识会传递给文件系统，而文件系统将检查该提交者是否有访问权限。

有关如何设置文件夹和文件权限的详细信息，请参阅 [Azure Synapse Analytics 共享数据库](database.md)。

## <a name="examples"></a>示例

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>在 Spark 中创建 Parquet 支持的托管表并从无服务器 SQL 池进行查询

在此场景中，你有一个名为 `mytestdb` 的 Spark 数据库。 请参阅[使用无服务器 SQL 池创建并连接到 Spark 数据库](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool)。

运行以下命令，使用 SparkSQL 创建托管的 Spark 表：

```sql
    CREATE TABLE mytestdb.myparquettable(id int, name string, birthdate date) USING Parquet
```

此命令在数据库 `mytestdb` 中创建表 `myparquettable`。 表名称将转换为小写。 在短暂的延迟后，可以在无服务器 SQL 池中看到该表。 例如，在无服务器 SQL 池中运行以下语句。

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

请验证结果中是否包含 `myparquettable`。

>[!NOTE]
>不使用 Parquet 作为存储格式的表不会同步。

接下来，通过 Spark 在该表中插入一些值，例如，在 C# 笔记本中使用以下 C# Spark 语句：

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myparquettable");
```

现在，可以从无服务器 SQL 池中读取数据，如下所示：

```sql
SELECT * FROM mytestdb.dbo.myparquettable WHERE name = 'Alice';
```

在结果中应会看到以下行：

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>在 Spark 中创建 Parquet 支持的外部表并从无服务器 SQL 池进行查询

此示例基于在前一个托管表示例中创建的 Parquet 数据文件创建一个外部 Spark 表。

例如，使用 SparkSQL 运行：

```sql
CREATE TABLE mytestdb.myexternalparquettable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

请将占位符 `<fs>` 替换为表示工作区默认文件系统的文件系统名称，并将占位符 `<synapse_ws>` 替换为运行此示例所用的 synapse 工作区的名称。

以上示例在数据库 `mytestdb` 中创建表 `myexternalparquettable`。 在短暂的延迟后，可以在无服务器 SQL 池中看到该表。 例如，在无服务器 SQL 池中运行以下语句。

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

请验证结果中是否包含 `myexternalparquettable`。

现在，可以从无服务器 SQL 池中读取数据，如下所示：

```sql
SELECT * FROM mytestdb.dbo.myexternalparquettable WHERE name = 'Alice';
```

在结果中应会看到以下行：

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure Synapse Analytics 的共享元数据](overview.md)
- [详细了解 Azure Synapse Analytics 的共享元数据数据库](database.md)
