---
title: 使用 Apache Spark 迁移到 Azure Managed Instance for Apache Cassandra
description: 了解如何使用 Apache Spark 迁移到 Azure Managed Instance for Apache Cassandra。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 06/02/2021
ms.openlocfilehash: e85d95dcd455ea2c9f4d9a14d9b68bebf06d3df6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731231"
---
# <a name="migrate-to-azure-managed-instance-for-apache-cassandra-using-apache-spark"></a>使用 Apache Spark 迁移到 Azure Managed Instance for Apache Cassandra

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra 目前以公共预览版形式提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

我们建议在可能的情况下，使用 Apache Cassandra 本机复制通过配置[混合群集](configure-hybrid-cluster.md)将现有群集中的数据迁移到 Azure Managed Instance for Apache Cassandra。 此方法使用 Apache Cassandra 的 gossip 协议将源数据中心的数据复制到新的托管实例数据中心。 但在某些情况下，源数据库版本不兼容，或者混合群集设置不可行。 

本文介绍如何使用 Cassandra Spark 连接器和 Azure Databricks for Apache Spark 以脱机方式将数据迁移到 Azure Managed Instance for Apache Cassandra。

## <a name="prerequisites"></a>先决条件

* 使用 [Azure 门户](create-cluster-portal.md)或 [Azure CLI](create-cluster-cli.md) 预配一个 Azure Managed Instance for Apache Cassandra 群集，并确保可以[使用 CQLSH 连接到该群集](./create-cluster-portal.md#connecting-to-your-cluster)。

* [在托管的 Cassandra VNet 中预配一个 Azure Databricks 帐户](deploy-cluster-databricks.md)。 另外请确保该帐户可通过网络访问源 Cassandra 群集。

* 确保已将密钥空间/表方案从源 Cassandra 数据库迁移到目标 Cassandra 托管实例数据库。


## <a name="provision-an-azure-databricks-cluster"></a>预配 Azure Databricks 群集

建议选择支持 Spark 3.0 的 Databricks 运行时版本 7.5。

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-runtime.png" alt-text="屏幕截图显示了如何查找 Databricks 运行时版本。":::

## <a name="add-dependencies"></a>添加依赖项

将 Apache Spark Cassandra 连接器库添加到群集，以便连接到本机终结点和 Azure Cosmos DB Cassandra 终结点。 在群集中，选择“库” > “安装新库” > “Maven”，然后在 Maven 坐标中添加 `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0`  。

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-search-packages.png" alt-text="屏幕截图显示在 Databricks 中搜索 Maven 包。":::

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
val sourceCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val targetCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.connection.remoteConnectionsPerExecutor" -> "10",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from source Cassandra
val DFfromSourceCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(sourceCassandra)
  .load
  
//Write to target Cassandra
DFfromSourceCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(targetCassandra)
  .mode(SaveMode.Append) // only required for Spark 3.x
  .save
```

> [!NOTE]
> 如果你需要保留或回溯每一行的 `writetime`，请参阅[实时迁移](dual-write-proxy-migration.md)一文。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure CLI 管理 Azure Managed Instance for Apache Cassandra 资源](manage-resources-cli.md)