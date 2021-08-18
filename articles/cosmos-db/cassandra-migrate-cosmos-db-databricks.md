---
title: 使用 Databricks (Spark) 将数据从 Apache Cassandra 迁移到 Azure Cosmos DB Cassandra API
description: 了解如何使用 Azure Databricks 和 Spark 将数据从 Apache Cassandra 数据库迁移到 Azure Cosmos DB Cassandra API。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 36395b8db4070dce3b26e41fcb01293e3074a24e
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393253"
---
# <a name="migrate-data-from-cassandra-to-an-azure-cosmos-db-cassandra-api-account-by-using-azure-databricks"></a>使用 Azure Databricks 将数据从 Cassandra 迁移到 Azure Cosmos DB Cassandra API 帐户
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB 中的 Cassandra API 已成为在 Apache Cassandra 上运行的企业工作负荷的极佳选择，原因有多个，例如：

* **无管理和监视开销：** 它消除了跨 OS、JVM 和 YAML 文件及其交互对设置进行管理和监视的开销。

* **显著节省成本：** 使用 Azure Cosmos DB 可以节省成本，其中包括 VM、带宽以及任何适用许可证的成本。 你不需管理数据中心、服务器、SSD 存储、网络以及电力成本。

* **能够使用现有的代码和工具：** Azure Cosmos DB 的线路协议级别与现有 Cassandra SDK 和工具兼容。 此兼容性确保只需经过细微的更改，就可以将现有代码库用于 Azure Cosmos DB Cassandra API。

可以通过许多方式将数据库工作负荷从一个平台迁移到另一个平台。 [Azure Databricks](https://azure.microsoft.com/services/databricks/) 是 [Apache Spark](https://spark.apache.org/) 的一种平台即服务 (PaaS) 产品/服务，它提供了一种大规模执行脱机迁移的方法。 本文介绍了使用 Azure Databricks 将数据从原生 Apache Cassandra 密钥空间和表迁移到 Azure Cosmos DB Cassandra API 所需的步骤。

## <a name="prerequisites"></a>先决条件

* [预配一个 Azure Cosmos DB Cassandra API 帐户](create-cassandra-dotnet.md#create-a-database-account)。

* [查看连接到 Azure Cosmos DB Cassandra API 的基础知识](cassandra-spark-generic.md)。

* 查看 [Azure Cosmos DB Cassandra API 中支持的功能](cassandra-support.md)以确保兼容性。

* 请确保已在目标 Azure Cosmos DB Cassandra API 帐户中创建了空的密钥空间和表。

* [使用 cqlsh 或托管 shell 进行验证](cassandra-support.md#hosted-cql-shell-preview)。

## <a name="provision-an-azure-databricks-cluster"></a>预配 Azure Databricks 群集

可以按说明来[预配 Azure Databricks 群集](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)。 建议选择支持 Spark 3.0 的 Databricks 运行时版本 7.5。

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="屏幕截图显示了如何查找 Databricks 运行时版本。":::

## <a name="add-dependencies"></a>添加依赖项

你需要将 Apache Spark Cassandra 连接器库添加到群集，以便连接到原生终结点和 Azure Cosmos DB Cassandra 终结点。 在群集中，选择“库” > “安装新库” > “Maven”，然后在 Maven 坐标中添加 `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0`  。

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="屏幕截图显示在 Databricks 中搜索 Maven 包。":::

选择“安装”，然后在安装完成后重启群集。

> [!NOTE]
> 请确保在安装 Cassandra 连接器库之后重启 Databricks 群集。

## <a name="create-scala-notebook-for-migration"></a>创建用于迁移的 Scala 笔记本

在 Databricks 中创建 Scala 笔记本。 将源和目标 Cassandra 配置替换为相应的凭据，并替换源和目标密钥空间和表。 然后运行以下代码：

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
    "spark.cassandra.output.concurrent.writes" -> "1000",
    //"spark.cassandra.connection.remoteConnectionsPerExecutor" -> "1", // Spark 3.x
    "spark.cassandra.connection.connections_per_executor_max"-> "1", // Spark 2.x
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
  .mode(SaveMode.Append) // only required for Spark 3.x
  .save
```

> [!NOTE]
> 为了避免[速率限制](/samples/azure-samples/azure-cosmos-cassandra-extensions-java-sample-v4/azure-cosmos-cassandra-extensions-java-sample-v4/)，需调整 `spark.cassandra.output.batch.size.rows` 和 `spark.cassandra.output.concurrent.writes` 值以及 Spark 群集中的工作器数这些重要配置。 对 Azure Cosmos DB 的请求超出预配的吞吐量或[请求单位](./request-units.md) (RU) 时，会出现速率限制。 你可能需要根据 Spark 群集中执行程序的数量，以及写入目标表的每条记录的大小（以及因此产生的 RU 开销）来调整这些设置。

## <a name="troubleshoot"></a>疑难解答

### <a name="rate-limiting-429-error"></a>速率限制（429 错误）

即使将设置减小到最小值，也可能会看到 429 错误代码或“请求速率太大”错误文本。 以下情况可能会导致速率限制：

* 分配给表的吞吐量少于 6,000 个[请求单位](./request-units.md)。 即使采用最小设置，Spark 也能以大约 6,000 个请求单位或更高的速率执行写入操作。 如果在具有共享吞吐量的密钥空间中预配了一个表，则此表在运行时的可用 RU 数可能少于 6,000。

    确保要迁移到的表在你运行迁移时至少有 6,000 个可用 RU。 如有必要，请向该表分配专用的请求单位。

* 存在大量数据而导致过度数据倾斜。 如果你有大量的数据要迁移到给定的表中，但数据中存在明显的倾斜（也就是说，为同一分区键值写入了大量的记录），则即使在表中预配了多个[请求单位](./request-units.md)，也仍会遇到速率限制。 请求单位在物理分区中平均分配，重度的数据倾斜可能会导致对单个分区的请求出现瓶颈。

    在这种情况下，请将 Spark 降低到最小吞吐量设置以强制迁移缓慢运行。 这种情况在迁移引用表或控制表时可能更常见，此时的访问频率较低，倾斜度可能会很高。 但如果任何其他类型的表中出现重度倾斜，你可能需要查看数据模型，以避免在稳定状态操作期间出现工作负荷的热分区问题。

## <a name="next-steps"></a>后续步骤

* [在容器和数据库上预配吞吐量](set-throughput.md)
* [分区键最佳做法](partitioning-overview.md#choose-partitionkey)
* [使用 Azure Cosmos DB Capacity Planner 估算 RU/秒](estimate-ru-with-capacity-planner.md)
* [Azure Cosmos DB Cassandra API 中的弹性缩放](manage-scale-cassandra.md)
