---
title: 使用 Apache Kafka 将数据从 PostgreSQL 迁移到 Azure Cosmos DB Cassandra API 帐户
description: 了解如何使用 Kafka Connect 将数据从 PostgreSQL 实时同步到 Azure Cosmos DB Cassandra API。
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: f2f6148d268a4a4b41a8b59c0751143e04cae95f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778364"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>使用 Apache Kafka 将数据从 PostgreSQL 迁移到 Azure Cosmos DB Cassandra API 帐户
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Azure Cosmos DB 中的 Cassandra API 已成为在 Apache Cassandra 上运行的企业工作负荷的极佳选择，原因各种各样，例如：

* **显著节省成本：** 使用 Azure Cosmos DB 可以节省成本，其中包括 VM、带宽以及任何适用 Oracle 许可证的成本。 另外，你无需管理数据中心、服务器、SSD 存储、网络以及电力成本。

* **更高的可伸缩性和可用性：** 它可以消除单一故障点，并提高应用程序的可伸缩性和可用性。

* **无管理和监视开销：** 作为一个完全托管式的云服务，Azure Cosmos DB 消除了管理和监视大量设置所带来的开销。

[Kafka Connect](https://kafka.apache.org/documentation/#connect) 是一个平台，用于在 [Apache Kafka](https://kafka.apache.org/) 和其他系统之间以可缩放且可靠的方式流式传输数据。 它支持多个现成的连接器，这意味着你无需自定义代码即可将外部系统与 Apache Kafka 集成。

本文将演示如何使用 Kafka 连接器的组合设置数据管道，以便将记录从关系数据库（例如 [PostgreSQL](https://www.postgresql.org/)）持续同步到 [Azure Cosmos DB Cassandra API](cassandra-introduction.md)。

## <a name="overview"></a>概述

下面是本文中提供的端到端流的概述。

PostgreSQL 表中的数据将通过 [Debezium PostgreSQL 连接器](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html)推送到 Apache Kafka，该连接器是 Kafka Connect 源连接器。 在 PostgreSQL 表中插入、更新或删除记录会被捕获为 `change data` 事件并发送到 Kafka 主题。 [DataStax Apache Kafka 连接器](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html)（Kafka Connect 接收器连接器）构成了管道的第二部分。 它会将 Kafka 主题中的变更数据事件同步到 Azure Cosmos DB Cassandra API 表。

> [!NOTE]
> 使用 DataStax Apache Kafka 连接器的特定功能，可以将数据推送到多个表。 在此示例中，连接器会帮助我们将变更数据记录持久保存到可以支持不同查询要求的两个 Cassandra 表中。

## <a name="prerequisites"></a>先决条件

* [预配 Azure Cosmos DB Cassandra API 帐户](manage-data-dotnet.md#create-a-database-account)
* [使用 cqlsh 或托管 shell 进行验证](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 或更高版本
* [Docker](https://www.docker.com/)（可选）

## <a name="base-setup"></a>基本设置

### <a name="set-up-postgresql-database-if-you-havent-already"></a>设置 PostgreSQL 数据库（如果尚未这样做）。 

可以使用现有的本地数据库，也可以在本地计算机上[下载并安装一个数据库](https://www.postgresql.org/download/)。 还可以使用 [Docker 容器](https://hub.docker.com/_/postgres)。
[!INCLUDE [pull-image-include](../../../includes/pull-image-include.md)]

若要启动容器，请执行以下操作：

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

使用 [`psql`](https://www.postgresql.org/docs/current/app-psql.html) 客户端连接到你的 PostgreSQL 实例：

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

创建一个表来存储示例订单信息：

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>使用 Azure 门户，创建演示应用程序所需的 Cassandra 密钥空间和表。

> [!NOTE]
> 使用与下面的内容相同的密钥空间和表名称

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>设置 Apache Kafka 

本文使用本地群集，但你可以选择任何其他选项。 [下载 Kafka](https://kafka.apache.org/downloads)，将其解压缩，启动 Zookeeper 和 Kafka 群集。

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>设置连接器

安装 Debezium PostgreSQL 和 DataStax Apache Kafka 连接器。 下载 Debezium PostgreSQL 连接器插件存档。 例如，若要下载连接器的版本 1.3.0（撰写本文时的最新版本），请使用[此链接](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.3.0.Final-plugin.tar.gz)。 从[此链接](https://downloads.datastax.com/#akc)下载 DataStax Apache Kafka 连接器。

解压缩两个连接器存档并将 JAR 文件复制到 [Kafka Connect plugin.path](https://kafka.apache.org/documentation/#connectconfigs)。


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> 有关详细信息，请参阅 [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) 和 [DataStax](https://docs.datastax.com/en/kafka/doc/) 文档。

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>配置 Kafka Connect 并启动数据管道

### <a name="start-kafka-connect-cluster"></a>启动 Kafka Connect 群集

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>启动 PostgreSQL 连接器实例

将连接器配置 (JSON) 保存到文件示例 `pg-source-config.json`

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

启动 PostgreSQL 连接器实例：

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> 若要删除，可以使用以下命令：`curl -X DELETE http://localhost:8083/connectors/pg-orders-source`


### <a name="insert-data"></a>插入数据

`orders_info` 表包含订单详细信息，例如订单 ID、客户 ID、城市，等等。使用以下 SQL 以随机数填充表。

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

它应将 10 条记录插入到表中。 请确保根据你的要求示例更新下面 `generate_series(1, 10)` 中的记录数，若要插入 `100` 条记录，请使用 `generate_series(1, 100)`

进行确认：

```bash
select * from retail.orders_info;
```

查看 Kafka 主题中的变更数据捕获事件

> [!NOTE]
> 请注意，主题名称是遵循[连接器约定](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names)的 `myserver.retail.orders_info`

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

你应当会看到采用 JSON 格式的变更数据事件。

### <a name="start-datastax-apache-kafka-connector-instance"></a>启动 DataStax Apache Kafka 连接器实例

将连接器配置 (JSON) 保存到文件示例 `cassandra-sink-config.json`，根据你的环境来更新属性。

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

启动连接器实例：

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

连接器应当会开始运转，从 PostgreSQL 到 Azure Cosmos DB 的端到端管道将可以运行。

### <a name="query-azure-cosmos-db"></a>查询 Azure Cosmos DB

查看 Azure Cosmos DB 中的 Cassandra 表。 下面是你可以尝试的一些查询：

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

你可以继续在 PostgreSQL 中插入更多数据，并确认记录是否已同步到 Azure Cosmos DB。

## <a name="next-steps"></a>后续步骤

* [使用 Kafka Connect 集成 Apache Kafka 和 Azure Cosmos DB Cassandra API](kafka-connect.md)
* [将 Azure 事件中心（预览版）上的 Apache Kafka Connect 与 Debezium 集成进行变更数据捕获](../../event-hubs/event-hubs-kafka-connect-debezium.md)
* [使用 Blitzz 将数据从 Oracle 迁移到 Azure Cosmos DB Cassandra API](oracle-migrate-cosmos-db-blitzz.md)
* [在容器和数据库上预配吞吐量](../set-throughput.md) 
* [分区键最佳做法](../partitioning-overview.md#choose-partitionkey)
* [使用 Azure Cosmos DB Capacity Planner 估算 RU/秒](../estimate-ru-with-capacity-planner.md)

