---
title: 了解适用于 Azure Data Lake Analytics U-SQL 开发人员的 Apache Spark 代码概念。
description: 本文介绍 Apache Spark 概念，以帮助 U-SQL 开发人员了解 Spark 代码概念。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: 4faa48a9edb5ea157fde67e4a4f3008864342075
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639769"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>了解适用于 U-SQL 开发人员的 Apache Spark 代码

本节提供有关如何将 U-SQL 脚本转换为 Apache Spark 的高级指导。

- 从[比较两种语言的处理模式](#understand-the-u-sql-and-spark-language-and-processing-paradigms)开始介绍
- 提供有关如何执行以下操作的提示：
   - [转换脚本](#transform-u-sql-scripts)，包括 U-SQL [行集表达式](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions)
   - [.NET 代码](#transform-net-code)
   - [数据类型](#transform-typed-values)
   - [目录对象](#transform-u-sql-catalog-objects)。

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>了解 U-SQL 和 Spark 语言和处理模式

开始将 Azure Data Lake Analytics U-SQL 脚本迁移到 Spark 之前，了解两个系统的一般语言和处理原理会很有用。

U-SQL 是一种类似 SQL 的声明性查询语言，它使用数据流模式，允许轻松地嵌入和横向扩展以 .NET（例如 C#）、Python 和 R 编写的用户代码。用户扩展可以实现简单的表达式或用户定义的函数，但也可以帮助用户实现所谓的用户定义的运算符，这些运算符可以实现自定义运算符来执行行集级别的转换、提取和写入输出。

Spark 是一种横向扩展框架，提供 Scala、Java、Python、.NET 等多种语言绑定。在此框架中，你主要采用其中一种语言编写代码，创建称为弹性分布式数据集 (RDD)、数据帧和数据集的数据抽象，然后使用类似 LINQ 的特定域语言 (DSL) 来转换它们。 它还提供 SparkSQL 作为数据帧和数据集抽象上的声明性子语言。 DSL 提供两种类别的操作：转换和操作。 将转换应用于数据抽象不会执行转换，而是生成将提交以供评估的某个操作（例如，将结果写入到临时表或文件中，或打印结果）的执行计划。

因此，在将 U-SQL 脚本转换为 Spark 程序时，必须确定要使用哪种语言来至少生成数据帧抽象（当前最常使用的数据抽象），以及是否要使用 DSL 或 SparkSQL 来编写声明性数据流转换。 在一些更复杂的情况下，可能需要将 U-SQL 脚本拆分为 Spark 序列和借助 Azure Batch 或 Azure Functions 实现的其他步骤。

此外，Azure Data Lake Analytics 在无服务器作业服务环境中提供 U-SQL，而 Azure Databricks 和 Azure HDInsight 则以群集服务形式提供 Spark。 转换应用程序时，必须考虑立即创建群集、调整群集大小、扩展群集和停止使用群集的影响。

## <a name="transform-u-sql-scripts"></a>转换 U-SQL 脚本

U-SQL 脚本遵循以下处理模式：

1. 使用 `EXTRACT` 语句、位置或文件集规范以及内置或用户定义的提取器和所需的架构从非结构化文件读取数据，或者从 U-SQL 表（托管表或外部表）读取数据。 它被表示为行集。
2. 在多个 U-SQL 语句中转换行集，这些语句将 U-SQL 表达式应用于行集并生成新行集。
3. 最后，使用指定位置的语句 `OUTPUT` 和内置或用户定义的输出器将生成的行集输出到文件，或将其输出到 U-SQL 表。

系统延迟计算此脚本，意味着每个提取和转换步骤均组成一个表达式树，并进行全局计算（数据流）。

Spark 程序类似于使用 Spark 连接器读取数据并创建数据帧，然后使用类似 LINQ 的 DSL 或 SparkSQL 在数据帧上应用转换，再将结果写入文件、临时 Spark 表、某些编程语言类型或控制台。

## <a name="transform-net-code"></a>转换 .NET 代码

U-SQL 的表达式语言为 C#，它提供多种方法来横向扩展自定义 .NET 代码。

由于 Spark 当前不支持在本机执行 .NET 代码，因此必须将表达式重写入等效的 Spark、Scala、Java 或 Python 表达式，或者设法调用 .NET 代码。 如果脚本使用 .NET 库，则可以选择以下操作：

- 将 .NET 代码转换为 Scala 或 Python 代码。
- 将 U-SQL 脚本拆分为多个步骤，在此期间可以使用 Azure Batch 过程来应用 .NET 转换（如果可形成可接受的规模）
- 使用名为 Moebius 的开源中提供的 .NET 语言绑定。 此项目不受支持。

无论如何，如果 U-SQL 脚本中存在大量 .NET 逻辑，请通过 Microsoft 帐户代表与我们联系以获得进一步指导。

以下详细介绍了 .NET 和 C# 在 U-SQL 脚本中的不同用法案例。

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>转换标量内联 U-SQL C# 表达式

U-SQL 的表达式语言为 C#。 许多标量内联 U-SQL 表达式可以在本机实现以提高性能，而更复杂的表达式则可能需要通过调用 .NET 框架来执行。

Spark 具有自己的标量表达式语言（属于 DSL 或在 SparkSQL 中），支持调用以其承载语言编写的用户定义的函数。

如果 U-SQL 中使用标量表达式，则应首先查找最适合的本机理解的 Spark 标量表达式以获得最大性能，然后将其他表达式映射到所选 Spark 承载语言编写的用户定义函数。

请注意，.NET 和 C# 的语义类型不同于 Spark 承载语言和 Spark 的 DSL。 有关类型系统差异的详细信息，请参阅[下文](#transform-typed-values)。

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>转换用户定义的标量 .NET 函数和用户定义的聚合器

U-SQL 提供调用任意标量 .NET 函数和以 .NET 编写的用户定义聚合器的方法。

Spark 还支持以其承载的大多数语言（可从 Spark DSL 和 SparkSQL 调用）编写的用户定义函数和用户定义聚合器。

### <a name="transform-user-defined-operators-udos"></a>转换用户定义的运算符 (UDO)

U-SQL 提供可采用 .NET 编写（某种程度上也可以采用 Python 和 R）的多种类别的用户定义运算符 (UDO)，例如提取器、输出器、减速器、处理器、应用器和合并器。

Spark 不提供相同的运算符扩展性模型，但具有与某些运算符等效的功能。

Spark 中与提取器和输出器等效的是 Spark 连接器。 对于很多 U-SQL 提取器，可在 Spark 社区中找到等效的连接器。 对于其他运算符，必须编写自定义连接器。 如果 U-SQL 提取器很复杂，且使用多个 .NET 库，则最好采用 Scala 生成连接器，以使用互操作来调用对数据进行实际处理的 .NET 库。 在这种情况下，必须将 .NET Core 运行时部署到 Spark 群集，并确保引用的 .NET 库与 .NET Standard 2.0 兼容。

其他类型的 U-SQL UDO 需要使用用户定义的函数和聚合器以及对应语义的 Spark DLS 或 SparkSQL 表达式进行重写。 例如，可以将处理器映射到包含多种 UDF 调用的 SELECT，并将其打包为可采用数据帧作为参数并返回数据帧的函数。

### <a name="transform-u-sqls-optional-libraries"></a>转换 U-SQL 的可选库

U-SQL 提供一组可选演示库，这些库支持 [Python](data-lake-analytics-u-sql-python-extensions.md)、[R](data-lake-analytics-u-sql-r-extensions.md)、[JSON、XML、AVRO](https://github.com/Azure/usql/tree/master/Examples/DataFormats) 和一些[认知服务功能](data-lake-analytics-u-sql-cognitive.md)。

Spark 提供自己的 Python 和 R 集成（分别为 pySpark 和 SparkR），并提供连接器来读取和写入 JSON、XML 和 AVRO。

如果需要转换引用了认知服务库的脚本，建议通过 Microsoft 帐户代表与我们联系。

## <a name="transform-typed-values"></a>转换类型化值

由于 U-SQL 类型系统基于 .NET 类型系统，Spark 具有自己的类型系统（受主机语言绑定影响），因此必须确保正在操作的类型相近，对于某些类型，类型范围、精度和/或规模可能略有不同。 此外，U-SQL 和 Spark 处理 `null` 值的方式不同。

### <a name="data-types"></a>数据类型

下表提供了 Spark、Scala 和 PySpark 中与给定 U-SQL 类型等效的类型。

| U-SQL | Spark |  Scala | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

有关详细信息，请参阅：

- [org.apache.spark.sql.types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Spark SQL 和 DataFrames 类型](https://spark.apache.org/docs/latest/sql-ref-datatypes.html)
- [Scala 值类型](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.types](https://spark.apache.org/docs/2.3.1/api/python/_modules/pyspark/sql/types.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>NULL 处理方式

在 Spark 中，类型默认允许 NULL 值，而在 U-SQL 中，则将标量、非对象显式标记为可为 NULL。 虽然通过 Spark 可以将列定义为不可为 NULL，但不会强制约束，这[可能会导致结果错误](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836)。

在 Spark 中，NULL 表示值未知。 Spark NULL 值不同于任何值，包括自身也各不相同。 比较两个 Spark NULL 值或将 NULL 值与其他任何值进行比较均返回 unknown，这是因为每个 NULL 值都是未知的。  

此行为与 U-SQL 不同，后者遵循 C# 语义，其中 `null` 不同于任何值，但自身是相同的。  

因此，即使 `column_name` 中存在 NULL 值，使用 `WHERE column_name = NULL` 的 SparkSQL 语句 `SELECT` 仍返回零行，而在 U-SQL 中，它将返回 `column_name` 已设置为 `null` 的行。 同样，即使 `column_name` 中存在非 NULL 值，使用 `WHERE column_name != NULL` 的 Spark 语句 `SELECT` 仍返回零行，而在 U-SQL 中，它将返回具有非 NULL 值的行。 因此，如果想要使用 U-SQL NULL 检查语义，则应分别使用 [isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) 和 [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull)（或其 DSL 等效项）。

## <a name="transform-u-sql-catalog-objects"></a>转换 U-SQL 目录对象

一个主要区别在于，U-SQL 脚本可以利用其目录对象，而其中许多对象并没有直接的 Spark 等效项。

Spark 支持 Hive 元存储概念（主要为数据库和表），因此可以将 U-SQL 数据库和架构映射到 Hive 数据库，将 U-SQ 表映射到 Spark 表（请参阅[移动 U-SQL 表中存储的数据](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)），但它不支持视图、表值函数 (TVF)、存储过程、U-SQL 程序集和外部数据源等。

可通过 Spark 中的代码函数和库对 U-SQL 代码对象（例如视图、TVF、存储过程和程序集）进行建模，并使用主机语言函数和过程抽象机制（例如，通过导入 Python 模块或引用 Scala 函数）对其进行引用。

如果已使用 U-SQL 目录跨项目和团队共享数据和代码对象，则必须使用等效的共享机制（例如，使用 Maven 共享代码对象）。

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>转换 U-SQL 行集表达式和基于 SQL 的标量表达式

U-SQL 核心语言正在转换行集，该语言基于 SQL。 以下是 U-SQL 提供的最常见行集表达式的非详尽列表：

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+聚合+`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI` `JOIN` 表达式
- `CROSS`/`OUTER` `APPLY` 表达式
- `PIVOT`/`UNPIVOT` 表达式
- `VALUES` 行集构造函数

- 设置表达式 `UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

此外，U-SQL 还提供各种基于 SQL 的标量表达式，例如

- `OVER` 窗口表达式
- 各种内置聚合器和排名函数（`SUM`、`FIRST` 等）
- 一些最熟悉的 SQL 标量表达式：`CASE`、`LIKE`、(`NOT`) `IN`、`AND`、`OR` 等

Spark 为大多数这些表达式提供 DSL 和 SparkSQL 形式的等效表达式。 对于某些本机 Spark 不支持的表达式，必须组合本机 Spark 表达式和语义等效模式对其进行重新编写。 例如，必须将 `OUTER UNION` 转换为等效的投影和联合组合。

由于对 NULL 值的处理方式不同，因此，如果两个比较的列都包含 NULL 值，则 U-SQL 联接将始终匹配行，但 Spark 联接则不会匹配这类列，除非添加显式 NULL 检查。

## <a name="transform-other-u-sql-concepts"></a>转换其他 U-SQL 概念

U-SQL 还提供各种其他功能和概念，例如对 SQL Server 数据库、参数、标量和 lambda 表达式变量、系统变量和 `OPTION` 提示执行的联合查询。

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>对 SQL Server 数据库/外部表执行的联合查询

U-SQL 提供数据源、外部表以及对 Azure SQL 数据库的直接查询。 虽然 Spark 不提供相同的对象抽象，但它提供可用于查询 SQL 数据库的[适用于 Azure SQL 数据库的 Spark 连接器](../azure-sql/database/spark-connector.md)。

### <a name="u-sql-parameters-and-variables"></a>U-SQL 参数和变量

参数和用户变量在 Spark 及其承载语言中具有等效概念。

例如在 Scala 中，可以使用关键字 `var` 定义变量：

```
var x = 2 * 3;
println(x)
```

U-SQL 的系统变量（以 `@@` 开头的变量）可拆分为两个类别：

- 可设置的系统变量，可将其设置为特定值以影响脚本行为
- 信息系统变量，可查询系统和作业级别的信息

大多数可设置的系统变量在 Spark 中没有直接等效项。 在作业执行期间，可以通过将信息作为参数传递来对某些信息系统变量进行建模，而其他变量可能在 Spark 承载语言中具有等效的函数。

### <a name="u-sql-hints"></a>U-SQL 提示

U-SQL 提供多种语法方法来向查询优化器和执行引擎提供提示：  

- 设置 U-SQL 系统变量
- 与行集表达式关联的 `OPTION` 子句，用于提供数据或计划提示
- 联接表达式语法中的联接提示（例如 `BROADCASTLEFT`）

Spark 基于成本的查询优化器拥有提供提示并优化查询性能的功能。 请参阅相应文档。

## <a name="next-steps"></a>后续步骤

- [了解适用于 U-SQL 开发人员的 Spark 数据格式](understand-spark-data-formats.md)
- [.NET for Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [将大数据分析解决方案从 Azure Data Lake Storage Gen1 升级到 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [在 Azure 数据工厂中使用 Spark 活动转换数据](../data-factory/transform-data-using-spark.md)
- [在 Azure 数据工厂中使用 Hadoop Hive 活动转换数据](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure HDInsight 中的 Apache Spark 是什么](../hdinsight/spark/apache-spark-overview.md)
