---
title: Spark 上 Azure Cosmos DB Cassandra API 中的 DDL 操作
description: 本文详细介绍了针对 Spark 上 Azure Cosmos DB Cassandra API 的密钥空间和表 DDL 操作。
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 99eee3fd7506f3baf8b119b3fdf4ba881850d61e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777977"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Spark 上 Azure Cosmos DB Cassandra API 中的 DDL 操作
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

本文详细介绍了针对 Spark 上 Azure Cosmos DB Cassandra API 的密钥空间和表 DDL 操作。

## <a name="spark-context"></a>Spark 上下文

 Cassandra API 连接器要求将 Cassandra 连接的详细信息作为 spark 上下文的一部分进行初始化。 当你启动笔记本时，Spark 上下文已初始化。建议你不要停止并重新初始化它。 一种解决方案是在群集 spark 配置中添加群集级别的 Cassandra API 实例配置。 这是每个群集的一次性活动。 将以下代码添加到 Spark 配置，作为空格分隔的键值对：
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="cassandra-api-related-configuration"></a>与 Cassandra API 相关的配置 

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//if using Spark 2.x, CosmosDB library for multiple retry
//import com.microsoft.azure.cosmosdb.cassandra
//spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

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
> 如果使用的是 Spark 3.0 或更高版本，则无需安装 Cosmos DB 帮助程序和连接工厂。 对于 Spark 3 连接器，还应使用 `remoteConnectionsPerExecutor` 而不是 `connections_per_executor_max`（见上文）。

## <a name="keyspace-ddl-operations"></a>密钥空间 DDL 操作

### <a name="create-a-keyspace"></a>创建密钥空间

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>在 cqlsh 中验证

在 cqlsh 中运行以下命令，可看到先前创建的密钥空间。

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>删除密钥空间

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>在 cqlsh 中验证

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>表 DDL 操作

**注意事项：**  

- 可使用 create table 语句在表级别分配吞吐量。  
- 一个分区键可存储 20 GB 的数据。  
- 一条记录最多可存储 2 MB 的数据。  
- 一个分区键范围可存储多个分区键。

### <a name="create-a-table"></a>创建表

```scala
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT, PRIMARY KEY(book_id,book_pub_year)) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>在 cqlsh 中验证

在 cqlsh 中运行以下命令，可看到名为“books”的表 

```bash
USE books_ks;
DESCRIBE books;
```

预配的吞吐量和默认 TTL 值未显示在上一个命令的输出中，可从门户获取这些值。

### <a name="alter-table"></a>更改表

可使用 alter table 命令更改以下值：

* 预配的吞吐量 
* 生存时间值
<br>目前不支持更改列。

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>删除表

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>在 cqlsh 中验证

在 cqlsh 中运行以下命令，可看到“books”表不再可用：

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>后续步骤

创建密钥空间和表后，请继续阅读以下有关 CRUD 操作的文章：
 
* [创建/插入操作](spark-create-operations.md)  
* [读取操作](spark-read-operation.md)  
* [upsert 操作](spark-upsert-operations.md)  
* [删除操作](spark-delete-operation.md)  
* [聚合操作](spark-aggregation-operations.md)  
* [表复制操作](spark-table-copy-operations.md)  
