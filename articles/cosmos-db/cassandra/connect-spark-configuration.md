---
title: 在 Spark 中使用 Azure Cosmos DB Cassandra API
description: 本文是 Spark 中 Cosmos DB Cassandra API 集成的主页。
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/01/2019
ms.openlocfilehash: f0e695248ecddebb4e737465a639e6385f6d29f0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778779"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>从 Spark 连接到 Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

本文是有关 Spark 中 Azure Cosmos DB Cassandra API 集成的系列文章中的一篇。 这些文章介绍了连接、数据定义语言 (DDL) 操作、基本数据操作语言 (DML) 操作，以及 Spark 中的高级 Azure Cosmos DB Cassandra API 集成。 

## <a name="prerequisites"></a>先决条件
* [预配一个 Azure Cosmos DB Cassandra API 帐户。](manage-data-dotnet.md#create-a-database-account)

* 预配所选的 Spark 环境 [[Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) | [Azure HDInsight Spark](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md) | 其他]。

## <a name="dependencies-for-connectivity"></a>连接的依赖项
* **Cassandra 的 Spark 连接器：** Spark 连接器用于连接到 Azure Cosmos DB Cassandra API。  请识别并使用 [Maven 中心]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector)内与 Spark 环境的 Spark 和 Scala 版本兼容的连接器版本。 建议使用支持 Spark 3.0 或更高版本的环境，并且 Spark 连接器在 maven 坐标上 `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` 可用。 如果使用 Spark 2.x，建议使用 Spark 2.4.5 版本的环境，并在 maven 坐标 `com.datastax.spark:spark-cassandra-connector_2.11:2.4.3` 使用 Spark 连接器。


* Cassandra API 的 Azure Cosmos DB 帮助程序库：如果使用的是 Spark 2.x 版本，则除了 Spark 连接器之外，还需要另一个名为 [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.2.0/jar) 的库（其中包含来自 Azure Cosmos DB 的 maven 坐标 `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.2.0`），以处理[速率限制](./scale-account-throughput.md#handling-rate-limiting-429-errors)。 此库包含自定义连接工厂和重试策略类。

  Azure Cosmos DB 中的重试策略配置为处理 HTTP 状态代码 429（“请求速率太大”）异常。 Azure Cosmos DB Cassandra API 在 Cassandra 本机协议中将这些异常解释为过载错误，你可以结合退让进行重试。 由于 Azure Cosmos DB 使用预配的吞吐量模型，当传入/传出速率增大时，会发生请求速率限制异常。 重试策略可以防范 Spark 作业出现数据高峰（短暂性地超过为容器分配的吞吐量）。 如果使用 Spark 3.x 连接器，则不需要实现此库。 

  > [!NOTE] 
  > 重试策略只能防范 Spark 作业出现短暂的高峰。 如果尚未配置用于运行工作负荷的足够 RU，则重试策略不适用，并且重试策略类会再次引发异常。

* **Azure Cosmos DB 帐户连接详细信息：** Azure Cassandra API 帐户名称、帐户终结点和密钥。

## <a name="optimizing-spark-connector-throughput-configuration"></a>优化 Spark 连接器吞吐量配置 

下一节中列出的所有相关参数均使用 Spark Connector for Cassandra 来控制吞吐量。 为了优化参数以最大程度地提高 spark 作业的吞吐量，需要对 `spark.cassandra.output.concurrent.writes`、`spark.cassandra.concurrent.reads` 和 `spark.cassandra.input.reads_per_sec` 进行正确的配置，以避免过多的限制和回退（从而导致吞吐量降低）。

这些配置的最优值取决于 4 个因素：

-   为数据引入的表配置的吞吐量（请求单位）。
- Spark 群集中的辅助进程数。
-   为 Spark 作业配置的执行程序数（可以使用 `spark.cassandra.connection.connections_per_executor_max` 或 `spark.cassandra.connection.remoteConnectionsPerExecutor` 进行控制，取决于 Spark 版本）
-   如果在同一个数据中心并置，则为每个请求对 cosmos DB 的平均延迟。 假设此值对于写入为 10 毫秒，对于读取为 3 毫秒。

例如，如果有 5 个辅助角色，且 `spark.cassandra.output.concurrent.writes` 的一个值 = 1，`spark.cassandra.connection.remoteConnectionsPerExecutor` 的一个值 = 1，则有 5 个辅助角色并发地写入表中，每个辅助角色各有 1 个线程。 如果执行一次写入需要 10 毫秒，那么我们在每个线程中可以每秒发送 100 个请求（1000 毫秒除以 10）。 若有 5 个辅助角色，每秒将写入 500 次。 平均成本为 5 个请求单位 (RU)/每次写入，目标表至少需要预配 2500 个请求单位（5 RU × 500 次写入/秒）。

增加执行程序数可能会增加给定作业中的线程数，进而增加吞吐量。 但是，根据作业的不同，实际的影响可能有所不同，而使用辅助角色数控制吞吐量更具确定性。 还可以通过分析给定请求来确定确切的成本，以获得请求单位 (RU) 费用。 这有助于在预配表或密钥空间的吞吐量时提高准确性。 点击[此处](./find-request-unit-charge-cassandra.md)查看相关文章，了解如何按照请求级别获取请求单位费用。 

### <a name="scaling-throughput-in-the-database"></a>缩放数据库中的吞吐量

Cassandra Spark 连接器可以高效地使 Azure Cosmos DB 中的吞吐量饱和。 因此，即使重试有效，你也需要确保在表或密钥空间级别预配足够的吞吐量 (RU)，以防止与速率限制相关的错误。 在给定表或密钥空间中，400 RU 的最小设置将无法满足要求。 即使在最小吞吐量配置设置下，Spark 连接器也可以以对应于大约 **6000 请求单位** 或更多的速率进行写入。

如果使用 Spark 进行数据移动所需的 RU 设置高于稳定状态工作负载所需的设置，则可以轻松地在 Azure Cosmos DB 中系统化地扩展和缩减吞吐量来满足给定时间段内的工作负载需求。 阅读有关 [Cassandra API 中的弹性缩放](scale-account-throughput.md)的文章，了解用于以编程方式和动态方式缩放的不同选项。 

> [!NOTE]
> 上述指导的假设是数据分布相当均匀。 如果数据严重倾斜（即，对同一分区键值进行过量的读取/写入），则即使表中预配了大量[请求单位](../request-units.md)，也仍可能会遇到瓶颈。 请求单位在物理分区中平均分配，重度的数据倾斜可能会导致对单个分区的请求出现瓶颈。
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Spark 连接器吞吐量配置参数

下表列出了连接器提供的特定于 Azure Cosmos DB Cassandra API 的吞吐量配置参数。 有关所有配置参数的详细列表，请参阅 Spark Cassandra 连接器 GitHub 存储库的[配置参考](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md)页。

| **属性名称** | **默认值** | **说明** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |每个批的行数。 请将此参数设置为 1。 此参数用于提高重型工作负荷的吞吐量。 |
| spark.cassandra.connection.connections_per_executor_max (Spark 2.x) spark.cassandra.connection.remoteConnectionsPerExecutor (Spark 3.x)  | 无 | 每个执行器的每个节点的最大连接数。 10*n 相当于 n 节点 Cassandra 群集中每个节点可以建立 10 个连接。 因此，如果需要为 5 节点 Cassandra 群集的每个执行器的每个节点建立 5 个连接，则应将此配置设置为 25。 请根据为 Spark 作业配置的并行度或执行器数目修改此值。   |
| spark.cassandra.output.concurrent.writes  |  100 | 定义每个执行器可以执行的并行写入数。 由于“batch.size.rows”设置为 1，因此请务必相应地增大此值。 请根据工作负荷要实现的并行度或吞吐量修改此值。 |
| spark.cassandra.concurrent.reads |  512 | 定义每个执行器可以执行的并行读取数。 请根据工作负荷要实现的并行度或吞吐量修改此值  |
| spark.cassandra.output.throughput_mb_per_sec  | 无 | 定义每个执行器的总写入吞吐量。 可将此参数用作 Spark 作业吞吐量的上限，并根据 Cosmos 容器的预配吞吐量修改此参数。   |
| spark.cassandra.input.reads_per_sec| 无   | 定义每个执行器的总读取吞吐量。 可将此参数用作 Spark 作业吞吐量的上限，并根据 Cosmos 容器的预配吞吐量修改此参数。  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | 定义每个 Spark 任务的、在发送到 Cassandra API 之前可以存储在内存中的批数 |
| spark.cassandra.connection.keep_alive_ms | 60000 | 定义在经过多长的时间之后未使用的连接可供使用。 | 

请根据 Spark 作业的预期工作负荷，以及为 Cosmos DB 帐户预配的吞吐量，来调整这些参数的吞吐量和并行度。


## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>从 Spark 连接到 Azure Cosmos DB Cassandra API

### <a name="cqlsh"></a>cqlsh
以下命令详细说明如何从 cqlsh 连接到 Azure CosmosDB Cassandra API。  在 Spark 中运行示例时，可以使用此命令进行验证。<br>
**从 Linux/Unix/Mac：**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>Azure Databricks
以下文章介绍了 Azure Databricks 群集预配、连接到 Azure Cosmos DB Cassandra API 时所需的群集配置，以及用于演示 DDL 操作、DML 操作等的几个示例 Notebook。<BR>
[在 Azure Databricks 中使用 Azure Cosmos DB Cassandra API](spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>Azure HDInsight-Spark
以下文章介绍了 HDinsight-Spark 服务、预配、连接到 Azure Cosmos DB Cassandra API 时所需的群集配置，以及用于演示 DDL 操作、DML 操作等的几个示例 Notebook。<BR>
[在 HDInsight-Spark 中使用 Azure Cosmos DB Cassandra API](spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>常规 Spark 环境
前面的部分与基于 Azure Spark 的 PaaS 服务相关，本部分介绍任何常规 Spark 环境。  下面详细介绍了连接器依赖项、导入和 Spark 会话配置。 “后续步骤”部分提供了 DDL 操作、DML 操作等的代码示例。  

#### <a name="connector-dependencies"></a>连接器依赖项：

1. 添加 Maven 坐标以获取 [Spark 的 Cassandra 连接器](connect-spark-configuration.md#dependencies-for-connectivity)
2. 添加 Maven 坐标以获取 Cassandra API 的 [Azure Cosmos DB 帮助器库](connect-spark-configuration.md#dependencies-for-connectivity)

#### <a name="imports"></a>导入：

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Spark 会话配置：

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
//spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10") // Spark 2.x
spark.conf.set("spark.cassandra.connection.remoteConnectionsPerExecutor", "10") // Spark 3.x
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>后续步骤

以下文章演示了 Spark 与 Azure Cosmos DB Cassandra API 的集成。 
 
* [DDL 操作](spark-ddl-operations.md)
* [创建/插入操作](spark-create-operations.md)
* [读取操作](spark-read-operation.md)
* [upsert 操作](spark-upsert-operations.md)
* [删除操作](spark-delete-operation.md)
* [聚合操作](spark-aggregation-operations.md)
* [表复制操作](spark-table-copy-operations.md)