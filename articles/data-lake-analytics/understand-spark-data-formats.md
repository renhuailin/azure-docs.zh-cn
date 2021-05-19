---
title: 了解适用于 Azure Data Lake Analytics U-SQL 开发人员的 Apache Spark 数据格式。
description: 本文介绍 Apache Spark 概念，以帮助 U_SQL 开发人员了解 U-SQL 和 Spark 数据格式之间的差异。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 399914186ce9de62ef46b682c8d4a6e51426cc26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221105"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>了解 U-SQL 和 Spark 数据格式之间的差异

如果要使用 [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) 或 [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)，则建议将数据从 [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 迁移到 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)。

除了移动文件外，还需要使 U-SQL 表中存储的数据可由 Spark 访问。

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>移动 Azure Data Lake Storage Gen1 文件中存储的数据

文件中存储的数据可以通过多种方式移动：

- 编写 [Azure 数据工厂](../data-factory/introduction.md)管道以将数据从 [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 帐户复制到 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 帐户。
- 编写 Spark 作业，该作业从 [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 帐户读取数据并将其写入 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 帐户。 根据用例，如果不需要保留原始文件格式，则可以采用不同的格式（如 Parquet）进行编写。

我们建议你阅读[将大数据分析解决方案从 Azure Data Lake Storage Gen1 升级到 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md) 一文

## <a name="move-data-stored-in-u-sql-tables"></a>移动 U-SQL 表中存储的数据

Spark 无法理解 U-SQL 表。 如果已将数据存储在 U-SQL 表中，则将运行 U-SQL 作业，该作业可提取表数据并将其保存为 Spark 可识别的格式。 最合适的格式是在 Hive 元存储的文件夹布局之后创建一组 Parquet 文件。

可以在具有内置 Parquet 输出器的 U-SQL 中，使用包含文件集的动态输出分区创建分区文件夹来实现输出。 [处理比以往更多的文件并使用 Parquet](/archive/blogs/azuredatalake/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) 提供了有关如何创建此类 Spark 可用数据的示例。

完成此转换后，按[移动 Azure Data Lake Storage Gen1 文件存储中的数据](#move-data-stored-in-azure-data-lake-storage-gen1-files)一章中所述复制数据。

## <a name="caveats"></a>注意事项

- 数据语义，复制文件时，将在字节级别进行复制。 因此，[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 帐户中应显示相同的数据。 但请注意，Spark 可能会以不同的方式解释某些字符。 例如，对于 CSV 文件中的行分隔符，可以使用不同的默认值。
    此外，如果要（从表中）复制类型化数据，则 Parquet 和 Spark 的某些类型化值（如 float）可能有不同的精度和小数位数，并且可能以不同的方式处理 null 值。 例如，U-SQL 的 null 值具有 C# 语义，而 Spark 的 null 值具有三值逻辑。

- 数据组织（分区）U-SQL 表提供两级分区。 外部级别 (`PARTITIONED BY`) 是按值分区，并且主要映射到使用文件夹层次结构的 Hive/Spark 分区方案中。 你将需要确保将 null 值映射到正确的文件夹。 U-SQL 中的内部级别 (`DISTRIBUTED BY`) 提供 4 个分发方案：轮循机制、范围、哈希和直接哈希。
    Hive/Spark 表仅支持使用与 U-SQL 不同的哈希函数的值分区或哈希分区。 输出 U-SQL 表数据时，你可能只能映射到 Spark 的值分区，并且可能需要根据最终 Spark 查询进一步优化数据布局。

## <a name="next-steps"></a>后续步骤

- [了解适用于 U-SQL 开发人员的 Spark 代码概念](understand-spark-code-concepts.md)
- [将大数据分析解决方案从 Azure Data Lake Storage Gen1 升级到 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET for Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [在 Azure 数据工厂中使用 Spark 活动转换数据](../data-factory/transform-data-using-spark.md)
- [在 Azure 数据工厂中使用 Hadoop Hive 活动转换数据](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure HDInsight 中的 Apache Spark 是什么](../hdinsight/spark/apache-spark-overview.md)