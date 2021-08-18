---
title: 从 Spark 将数据更新插入到 Azure Cosmos DB Cassandra API
description: 本文详细介绍了如何从 Spark 更新插入到 Azure Cosmos DB Cassandra API 表
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: ab09908a51004c1f9c2cf202705c47481271427a
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113355332"
---
# <a name="upsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>从 Spark 将数据更新插入到 Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

本文介绍如何从 Spark 将数据更新插入到 Azure Cosmos DB Cassandra API。

## <a name="cassandra-api-configuration"></a>Cassandra API 配置

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//if using Spark 2.x, CosmosDB library for multiple retry
//import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
// if using Spark 2.x
// spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
//spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10") // Spark 2.x
spark.conf.set("spark.cassandra.connection.remoteConnectionsPerExecutor", "10") // Spark 3.x
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

> [!NOTE]
> 如果使用的是 Spark 3.0 或更高版本，则无需安装 Cosmos DB 帮助程序和连接工厂。 对于 Spark 3 连接器，还应该使用 `remoteConnectionsPerExecutor` 而不是 `connections_per_executor_max`（见上文）。 你将看到在上面的笔记本中定义了与连接相关的属性。 使用以下语法，可以按此方式定义连接属性，而无需在群集级别（Spark 上下文初始化）进行定义。 但是，使用需要 spark 上下文的操作时（例如，如下所示的 `update` 的 `CassandraConnector(sc)`），需要在群集级别定义连接属性。

## <a name="dataframe-api"></a>数据帧 API

### <a name="create-a-dataframe"></a>创建 dataframe 

```scala
// (1) Update: Changing author name to include prefix of "Sir"
// (2) Insert: adding a new book

val booksUpsertDF = Seq(
    ("b00001", "Sir Arthur Conan Doyle", "A study in scarlet", 1887),
    ("b00023", "Sir Arthur Conan Doyle", "A sign of four", 1890),
    ("b01001", "Sir Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
    ("b00501", "Sir Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
    ("b00300", "Sir Arthur Conan Doyle", "The hounds of Baskerville", 1901),
    ("b09999", "Sir Arthur Conan Doyle", "The return of Sherlock Holmes", 1905)
    ).toDF("book_id", "book_author", "book_name", "book_pub_year")
booksUpsertDF.show()
```

### <a name="upsert-data"></a>更新插入数据

```scala
// Upsert is no different from create
booksUpsertDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .save()
```

### <a name="update-data"></a>更新数据

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

//This runs on the driver, leverage only for one off updates
cdbConnector.withSessionDo(session => session.execute("update books_ks.books set book_price=99.33 where book_id ='b00300' and book_pub_year = 1901;"))
```

## <a name="rdd-api"></a>RDD API
> [!NOTE]
> 从 RDD API 更新插入与创建操作相同 

## <a name="next-steps"></a>后续步骤

继续下面的文章，对 Azure Cosmos DB Cassandra API 表中存储的数据执行其他操作：
 
* [删除操作](cassandra-spark-delete-ops.md)
* [聚合操作](cassandra-spark-aggregation-ops.md)
* [表复制操作](cassandra-spark-table-copy-ops.md)
