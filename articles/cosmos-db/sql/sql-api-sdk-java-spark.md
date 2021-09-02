---
title: 适用于 SQL API 的 Azure Cosmos DB Apache Spark 2 OLTP 连接器发行说明和资源
description: 了解有关适用于 SQL API 的 Azure Cosmos DB Apache Spark 2 OLTP 连接器的信息，包括发行日期、停用日期和 Azure Cosmos DB SQL Async Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 318faba31bec6b08c81d5d9f1d8741ddb65b5292
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113410"
---
# <a name="azure-cosmos-db-apache-spark-2-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>适用于 Core (SQL) API 的 Azure Cosmos DB Apache Spark 2 OLTP 连接器：发行说明和资源
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 更改源 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP 连接器](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP 连接器](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST 资源提供程序](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-query-getting-started.md)
> * [批量执行工具 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

可以通过使用适用于 Core (SQL) 的 Azure Cosmos DB Apache Spark 2 OLTP 连接器加速大数据分析。 利用 Spark 连接器，可以对 Azure Cosmos DB 中存储的数据运行 [Spark](https://spark.apache.org/) 作业。 支持批处理和流处理。

可以将连接器用于 [Azure Databricks](https://azure.microsoft.com/services/databricks) 或 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)，它们在 Azure 上提供 Spark 托管群集。 下表显示了支持的版本：

| 组件 | 版本 |
|---------|-------|
| Apache Spark | 2.4.x、2.3.x、2.2.x 和 2.1.x    |
| Scala | 2.11 |
| Azure Databricks（运行时版本） | 晚于 3.4 |

> [!WARNING]
> 此连接器支持 Azure Cosmos DB 的核心 (SQL) API。
> 对于适用于 MongoDB 的 Cosmos DB API，请使用[适用于 Spark 的 MongoDB 连接器](https://docs.mongodb.com/spark-connector/master/)。
> 对于 Cosmos DB Cassandra API，请使用 [Cassandra Spark 连接器](https://github.com/datastax/spark-cassandra-connector)。
>

## <a name="resources"></a>资源

| 资源 | 链接 |
|---|---|
| **SDK 下载** | [下载最新的 .jar](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)，[Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**API 文档** | [Spark 连接器参考]() |
|**参与编写 SDK** | [GitHub 上适用于 Apache Spark 的 Azure Cosmos DB 连接器](https://github.com/Azure/azure-cosmosdb-spark) | 
|**入门** | [使用 Apache Spark 到 Azure Cosmos DB 的连接器加速大数据分析](./create-sql-api-spark.md) <br> [将 Apache Spark 结构化流式处理与 Apache Kafka 和 Azure Cosmos DB 配合使用](../../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>版本历史记录

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>新增功能
- 添加了新的配置选项 `changefeedstartfromdatetime`，该选项可用于指定应处理更改源的开始时间。 有关详细信息，请参阅[配置选项](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)。

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>关键 bug 修复
- 修复了一个回归，该回归导致大型结果集（例如包含数百万行的结果集）的执行器内存消耗过多，并最终导致错误 `java.lang.OutOfMemoryError: GC overhead limit exceeded`。

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 修复了一个流式检查点边缘案例，其中 `ID` 包含应用了 `ChangeFeedMaxPagesPerBatch` 配置的管道字符 (|)。

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>新增功能
* 添加了在使用嵌套分区键时对批量更新的支持。
* 添加了在写入 Azure Cosmos DB 过程中对 Decimal 和 Float 数据类型的支持。
* 添加了对 Timestamp 类型使用 Long (Unix epoch) 作为值时对这些类型的支持。

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 修复了使用 `WriteThroughputBudget` 配置时发生的类型转换异常。

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>新增功能
* 将批量失败的错误信息添加到异常和日志。

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 修复了流式处理检查点问题。

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 为减少干扰，修复了无意中留下的“错误”级别消息的日志级别。

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 修复了分区拆分期间结构化流中的 bug。 该 bug 可能会导致缺少某些更改源记录或检查点写入出现 NULL 异常。

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 修复了导致为 readStream 提供的自定义架构被忽略的 bug。

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 修复了一个回归（未改装的 JAR 包括所有已改装的依赖项），该回归导致生成时间增加了 50%。

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 修复了一个依赖项问题，该问题导致通过 TCP 的直接传输失败，并出现 RequestTimeoutException。

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>新增功能
* 改进了连接管理和连接池以减少元数据调用数。

## <a name="faq"></a>常见问题解答
[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>后续步骤

了解有关 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 的详细信息。

详细了解 [Apache Spark](https://spark.apache.org/)。