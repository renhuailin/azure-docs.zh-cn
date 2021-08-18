---
title: 通过 Spark 创建或插入数据到 Azure Cosmos DB Cassandra API
description: 本文详细介绍如何将示例数据插入 Azure Cosmos DB Cassandra API 表
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 1769ef181f977170bf205f4454ebd0254bfbe702
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777978"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>通过 Spark 创建/插入数据到 Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]
 
本文介绍如何通过 Spark 将示例数据插入 Azure Cosmos DB Cassandra API 的表中。

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
> 如果使用的是 Spark 3.0 或更高版本，则无需安装 Cosmos DB 帮助程序和连接工厂。 对于 Spark 3 连接器，还应该使用 `remoteConnectionsPerExecutor` 而不是 `connections_per_executor_max`（见上文）。 你将看到在上面的笔记本中定义了与连接相关的属性。 使用以下语法，可以按此方式定义连接属性，而无需在群集级别（Spark 上下文初始化）进行定义。

## <a name="dataframe-api"></a>数据帧 API

### <a name="create-a-dataframe-with-sample-data"></a>使用示例数据创建数据帧

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> 尚不支持在行级别实现“不存在时创建”功能。

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>保存到 Azure Cosmos DB Cassandra API

保存数据时，还可以设置生存时间和一致性策略设置，如以下示例所示：

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> 尚不支持列级 TTL。

#### <a name="validate-in-cqlsh"></a>在 cqlsh 中验证

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>可复原分布式数据库 (RDD) API

### <a name="create-a-rdd-with-sample-data"></a>使用示例数据创建 RDD
```scala
//Drop and re-create table to delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))

cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT, PRIMARY KEY(book_id,book_pub_year)) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> 尚不支持“不存在时创建”功能。

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>保存到 Azure Cosmos DB Cassandra API

将数据保存到 Cassandra API 时，还可以设置生存时间和一致性策略设置，如以下示例所示：

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>在 cqlsh 中验证

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>后续步骤

将数据插入 Azure Cosmos DB Cassandra API 表后，请继续阅读以下文章，对 Cosmos DB Cassandra API 中存储的数据执行其他操作：
 
* [读取操作](spark-read-operation.md)
* [upsert 操作](spark-upsert-operations.md)
* [删除操作](spark-delete-operation.md)
* [聚合操作](spark-aggregation-operations.md)
* [表复制操作](spark-table-copy-operations.md)
