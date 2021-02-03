---
title: 使用 Databricks (Spark) 将数据从 Apache Cassandra 迁移到 Azure Cosmos DB Cassandra API
description: 了解如何使用 Azure Databricks 和 Spark 将数据从 Apache Cassandra 数据库迁移到 Azure Cosmos DB Cassandra API。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 3cbcb7eb3695e6f57daef741d4cd4b15577d8f58
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493265"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>使用 Azure Databricks 将数据从 Cassandra 迁移到 Azure Cosmos DB Cassandra API 帐户
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB 中的 Cassandra API 已成为在 Apache Cassandra 上运行的企业工作负荷的极佳选择，原因各种各样，例如： 

* **无管理和监视开销：** 它不需管理和监视跨 OS、JVM 和 yaml 文件的大量设置，也不需进行交互。

* **显著节省成本：** 可以通过 Azure Cosmos DB 节省成本，包括 VM 的成本、带宽和任何适用的许可证。 另外，你无需管理数据中心、服务器、SSD 存储、网络以及电力成本。 

* **能够使用现有的代码和工具：** Azure Cosmos DB 提供的线路协议级别与现有 Cassandra SDK 和工具兼容。 此兼容性确保只需经过细微的更改，就可以将现有代码库用于 Azure Cosmos DB Cassandra API。

可以通过多种方式将数据库工作负荷从一个平台迁移到另一个平台。 [Azure Databricks](https://azure.microsoft.com/services/databricks/) 是 [Apache Spark](https://spark.apache.org/) 的一种平台即服务产品，它提供了一种大规模执行脱机迁移的方法。 本文介绍使用 Azure Databricks 将数据从本机 Apache Cassandra 密钥空间/表迁移到 Azure Cosmos DB Cassandra API 所需的步骤。

## <a name="prerequisites"></a>先决条件

* [预配 Azure Cosmos DB Cassandra API 帐户](create-cassandra-dotnet.md#create-a-database-account)

* [回顾连接到 Azure Cosmos DB Cassandra API 的基础知识](cassandra-spark-generic.md)

* 查看 [Azure Cosmos DB Cassandra API 中支持的功能](cassandra-support.md)以确保兼容性。

* 请确保已在目标 Azure Cosmos DB Cassandra API 帐户中创建了空的密钥空间和表

* [使用 cqlsh 或托管 shell 进行验证](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>预配 Azure Databricks 群集

可以按说明来[预配 Azure Databricks 群集](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)。 但是，请注意，Apache Cassandra 连接器目前不支持 Apache Spark 3.x。 你将需要使用受支持的 Apache Spark v2.x 版来预配 Databricks 运行时。 我们建议选择一个 Databricks 运行时版本，该版本支持最新版本的 Spark 2.x，并且不能晚于 Scala 2.11 版本：

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks 运行时":::


## <a name="add-dependencies"></a>添加依赖项

需要将 Apache Spark Cassandra 连接器库添加到群集，以便连接到本机和 Cosmos DB Cassandra 终结点。 在群集中，选择“库”->“新安装”->“maven”->“搜索包”：

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks 搜索包":::

在搜索框中键入 `Cassandra`，选择可用的最新 `spark-cassandra-connector` maven 存储库，然后选择“安装”：

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Databricks 的“选择包”":::

> [!NOTE]
> 确保在安装了 Cassandra 连接器库之后重启 Databricks 群集。

## <a name="create-scala-notebook-for-migration"></a>创建用于迁移的 Scala 笔记本

在 Databricks 中使用以下代码创建 Scala 笔记本。 将源和目标 cassandra 配置替换为相应的凭据、源/目标密钥空间和表，然后运行以下代码：

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .save
```

> [!NOTE]
> `spark.cassandra.output.batch.size.rows` `spark.cassandra.output.concurrent.writes` 和配置对于 `connections_per_executor_max` 避免[速率限制](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/)非常重要，因为对 Azure Cosmos DB 的请求超过预配吞吐量/ ([请求单位](./request-units.md)) 时，会发生这种情况。 你可能需要根据 Spark 群集中执行程序的数量，以及写入目标表的每条记录的大小（以及因此产生的 RU 开销）来调整这些设置。

## <a name="troubleshooting"></a>疑难解答

### <a name="rate-limiting-429-error"></a>速率限制 (429 错误) 
`request rate is large`尽管将以上设置降低到其最小值，但你可能会看到错误代码429或错误文本。 下面是一些这类情况：

- **分配给表的吞吐量少于6000个 [请求单位](./request-units.md)**。 即使是最小设置，Spark 也能以6000个请求单位或更多的速率来执行写入操作。 如果在预配了共享吞吐量的密钥空间中预配了一个表，则在运行时此表有可能少于6000个 ru。 在运行迁移时，请确保要迁移到的表中至少有6000个 ru 可用，并在必要时将专用请求单元分配给该表。 
- 数据 **量过大，数据量过大**。 如果有大量的数据 (表行) 迁移到给定的表中，但数据中存在明显的偏差 (也就是说，为同一分区键值写入大量的记录) ，则即使在表中预配了大量的 [请求单位](./request-units.md) ，也仍会遇到速率限制。 这是因为，请求单元在物理分区之间平均划分，而繁重的数据偏斜可能会导致对单个分区的请求瓶颈，从而导致速率限制。 在这种情况下，建议将 Spark 降低到最小吞吐量设置，以避免速率限制，并强制迁移运行缓慢。 这种情况在迁移引用或控制表时可能更常见，在这种情况下，访问频率较低，但可能会很高。 但是，如果任何其他类型的表中出现重大倾斜，还建议查看数据模型，以避免在稳定状态操作期间对工作负荷进行热分区问题。 
- **无法获取大表的计数**。 `select count(*) from table`对于大型表，目前不支持运行。 你可以从 Azure 门户中获取指标的计数 (请参阅我们的 [故障排除文章](cassandra-troubleshoot.md)) ，但是，如果需要从 Spark 作业的上下文中确定大表的计数，则可以将数据复制到临时表，然后使用 spark SQL 获取计数，例如，以下 (将替换为 `<primary key>` 生成的临时表) 中的某些字段。

  ```scala
  val ReadFromCosmosCassandra = sqlContext
    .read
    .format("org.apache.spark.sql.cassandra")
    .options(cosmosCassandra)
    .load

  ReadFromCosmosCassandra.createOrReplaceTempView("CosmosCassandraResult")
  %sql
  select count(<primary key>) from CosmosCassandraResult
  ```

## <a name="next-steps"></a>后续步骤

* [在容器和数据库上预配吞吐量](set-throughput.md) 
* [分区键最佳做法](partitioning-overview.md#choose-partitionkey)
* [使用 Azure Cosmos DB Capacity Planner 估算 RU/秒](estimate-ru-with-capacity-planner.md)
* [Azure Cosmos DB Cassandra API 中的弹性缩放](manage-scale-cassandra.md)
