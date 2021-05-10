---
title: 了解适用于 Azure Data Lake Analytics U-SQL 开发人员的 Apache Spark。
description: 本文介绍 Apache Spark 概念，以帮助你了解 U-SQL 开发人员之间的差异。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: a66a82a6d2a5bb1f534ed211091793b2ab4d2a28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221088"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>了解适用于 U-SQL 开发人员的 Apache Spark

Microsoft 支持多种 Analytics 服务，例如 [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)、[Azure HDInsight](../hdinsight/hdinsight-overview.md) 以及 Azure Data Lake Analytics。 我们了解到，开发人员在构建分析管道时，明确表现出对开源解决方案的偏好。 为了帮助 U-SQL 开发人员了解 Apache Spark 和如何将 U-SQL 脚本转换为 Apache Spark，我们创作了本指南。

其中包括大量可以执行的步骤和几个备选项。

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>将 U-SQL 转换为 Apache Spark 的步骤

1. 转换作业业务流程管道。

   如果使用 [Azure 数据工厂](../data-factory/introduction.md)来协调 Azure Data Lake Analytics 脚本，则必须对其进行调整以协调新的 Spark 程序。
2. 了解 U-SQL 和 Spark 在数据管理方式上的差异

   如果要将数据从 [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 移动到 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)，必须复制文件数据和目录经维护的数据。 请注意，Azure Data Lake Analytics 仅支持 Azure Data Lake Storage Gen1。 请参阅[了解 Spark 数据格式](understand-spark-data-formats.md)
3. 将 U-SQL 脚本转换为 Spark

   在转换 U-SQL 脚本之前，必须选择一个分析服务。 可用的计算服务包括：
      - [Azure 数据工厂数据流](../data-factory/concepts-data-flow-overview.md)映射数据流是一种直观的数据转换过程，允许数据工程师无需编写代码即可开发图形数据转换逻辑。 虽然它不适合执行复杂的用户代码，但可以轻松表示传统类似 SQL 的数据流转换
      - [Azure HDInsight Hive](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) HDInsight 上的 Apache Hive 适用于提取、转换和加载 (ETL) 操作。 表示你将要将 U-SQL 脚本转换为 Apache Hive。
      - Apache Spark 引擎（例如 [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) 或 [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)）表示你将要将 U-SQL 脚本转换为 Spark。 有关详细信息，请参阅[了解 Spark 数据格式](understand-spark-data-formats.md)

> [!CAUTION]
> [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) 和 [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) 都是群集服务，并非如 Azure Data Lake Analytics 的无服务器作业。 必须考虑如何预配群集以实现适当的成本/性能比，以及如何管理其生存期以最大程度降低成本。 这些服务的用户代码采用 .NET 编写，具有不同的性能特征，因此必须采用受支持的语言编写包装器或重写代码。 有关详细信息，请参阅[了解 Spark 数据格式](understand-spark-data-formats.md)、[了解适用于 U-SQL 开发人员的 Apache Spark 代码概念](understand-spark-code-concepts.md) 以及 [.Net for Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>后续步骤

- [了解适用于 U-SQL 开发人员的 Spark 数据格式](understand-spark-data-formats.md)
- [了解适用于 U-SQL 开发人员的 Spark 代码概念](understand-spark-code-concepts.md)
- [将大数据分析解决方案从 Azure Data Lake Storage Gen1 升级到 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET for Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [在 Azure 数据工厂中使用 Hadoop Hive 活动转换数据](../data-factory/transform-data-using-hadoop-hive.md)
- [在 Azure 数据工厂中使用 Spark 活动转换数据](../data-factory/transform-data-using-spark.md)
- [Azure HDInsight 中的 Apache Spark 是什么](../hdinsight/spark/apache-spark-overview.md)