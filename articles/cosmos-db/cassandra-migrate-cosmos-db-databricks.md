---
title: '使用 Databricks (Spark 将数据从 Apache Cassandra 迁移到 Azure Cosmos DB Cassandra API) '
description: 了解如何使用 Azure Databricks 和 Spark 将数据从 Apache Cassandra 数据库迁移到 Azure Cosmos DB Cassandra API。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 877aa96b189de47d158721df6585cb94ace4a855
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94932886"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>使用 Azure Databricks 将数据从 Cassandra 迁移到 Azure Cosmos DB Cassandra API 帐户
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB 中的 Cassandra API 已成为在 Apache Cassandra 上运行的企业工作负荷的极佳选择，原因各种各样，例如： 

* **无管理和监视开销：** 它不需管理和监视跨 OS、JVM 和 yaml 文件的大量设置，也不需进行交互。

* **显著节省成本：** 可以通过 Azure Cosmos DB 节省成本，包括 VM 的成本、带宽和任何适用的许可证。 此外，无需管理数据中心、服务器、SSD 存储、网络和电力成本。 

* **能够使用现有的代码和工具：** Azure Cosmos DB 提供的线路协议级别与现有 Cassandra SDK 和工具兼容。 此兼容性确保只需经过细微的更改，就可以将现有代码库用于 Azure Cosmos DB Cassandra API。

可以通过多种方式将数据库工作负荷从一个平台迁移到另一个平台。 [Azure Databricks](https://azure.microsoft.com/services/databricks/) 是一种平台即服务产品， [Apache Spark](https://spark.apache.org/) 用于提供一种大规模执行脱机迁移的方法。 本文介绍将数据从本机 Apache Cassandra 大量/tables 迁移到使用 Azure Databricks Azure Cosmos DB Cassandra API 所需的步骤。

## <a name="prerequisites"></a>先决条件

* [预配 Azure Cosmos DB Cassandra API 帐户](create-cassandra-dotnet.md#create-a-database-account)

* [回顾连接到 Azure Cosmos DB Cassandra API 的基础知识](cassandra-spark-generic.md)

* 查看 [Azure Cosmos DB Cassandra API 中支持的功能](cassandra-support.md) 以确保兼容性。

* 请确保已在目标 Azure Cosmos DB Cassandra API 帐户中创建了空的密钥空间和表

* [使用 cqlsh 或托管 shell 进行验证](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>预配 Azure Databricks 群集

可以按照说明 [设置 Azure Databricks 群集](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)。 但是，请注意 Apache Spark 1.x 目前不受 Apache Cassandra 连接器支持。 你将需要使用支持的 Apache Spark 版本的来预配 Databricks 运行时。 建议 Databricks 运行时版本6.6：

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks 运行时":::


## <a name="add-dependencies"></a>添加依赖项

需要将 Apache Spark Cassandra 连接器库添加到群集，以便连接到本机和 Cosmos DB Cassandra 终结点。 在群集中，选择 "库"-> 安装 > maven-> 搜索包：

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks 搜索包":::

`Cassandra`在 "搜索" 框中键入，选择可用的最新 `spark-cassandra-connector` maven 存储库，并选择 "安装"：

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Databricks 选择包":::

> [!NOTE]
> 确保在安装 Cassandra 连接器库之后重新启动 Databricks 群集。

## <a name="create-scala-notebook-for-migration"></a>创建用于迁移的 Scala 笔记本

在 Databricks 中使用以下代码创建 Scala 笔记本。 将源和目标 cassandra 配置替换为相应的凭据以及源/目标大量和表，然后运行：

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
> `spark.cassandra.output.concurrent.writes`和 `connections_per_executor_max` 配置对于避免[速率限制](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/)非常重要，因为当请求 Cosmos DB 超过预配吞吐量 ([请求单位](https://docs.microsoft.com/azure/cosmos-db/request-units)) 时，就会发生这种情况。 你可能需要根据 Spark 群集中的执行器的数量调整这些设置，并可能需要 (的大小，从而使每个记录写入目标表的) 。

## <a name="next-steps"></a>后续步骤

* [在容器和数据库上预配吞吐量](set-throughput.md) 
* [分区键最佳做法](partitioning-overview.md#choose-partitionkey)
* [使用 Azure Cosmos DB Capacity Planner 估算 RU/秒](estimate-ru-with-capacity-planner.md)
* [灵活扩展 Azure Cosmos DB Cassandra API](manage-scale-cassandra.md)
