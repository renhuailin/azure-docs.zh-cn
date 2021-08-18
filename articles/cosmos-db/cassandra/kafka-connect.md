---
title: 使用 Kafka Connect 集成 Apache Kafka 和 Azure Cosmos DB Cassandra API
description: 了解如何使用 DataStax Apache Kafka 连接器将数据从 Kafka 引入到 Azure Cosmos DB Cassandra API
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: a980f1cbebf9579a776713209eaaaeb525575ad8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778702"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>使用 Kafka Connect 将数据从 Apache Kafka 引入到 Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

现有 Cassandra 应用程序可以轻松使用 [Azure Cosmos DB Cassandra API](cassandra-introduction.md)，因为它提供了 [CQLv4 驱动程序兼容性](https://cassandra.apache.org/doc/latest/cassandra/getting_started/drivers.html?highlight=driver)。 你可以利用此功能与流式处理平台（例如 [Apache Kafka](https://kafka.apache.org/)）进行集成，将数据引入 Azure Cosmos DB。

只有被其他应用程序使用或引入到其他系统中时，Apache Kafka（主题）中的数据才有用。 可以[使用你选择的语言和客户端 SDK](https://cwiki.apache.org/confluence/display/KAFKA/Clients)通过 [Kafka 生成者/使用者](https://kafka.apache.org/documentation/#api) API 来构建解决方案。 Kafka Connect 提供了一个替代解决方案。 它是一个平台，用于在 Apache Kafka 和其他系统之间以可缩放且可靠的方式流式传输数据。 由于 Kafka Connect 支持包括 Cassandra 在内的现成连接器，因此你无需编写自定义代码便可将 Kafka 与 Azure Cosmos DB Cassandra API 进行集成。 

在本文中，我们将使用开源 [DataStax Apache Kafka 连接器](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html)，它在 Kafka Connect 框架上运行，可将 Kafka 主题中的记录引入到一个或多个 Cassandra 表的行中。 此示例提供了一个使用 Docker Compose 的可重用安装程序。 这非常方便，因为它使你能够使用单个命令在本地启动所有必需的组件。 这些组件包括 Kafka、Zookeeper、Kafka Connect 工作器和示例数据生成器应用程序。

下面是组件及其服务定义的明细，你可以参考 [GitHub 存储库](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml)中的完整 `docker-compose` 文件。

- Kafka 和 Zookeeper 使用 [debezium](https://hub.docker.com/r/debezium/kafka/) 映像。
- 将要作为 Docker 容器运行的 DataStax Apache Kafka 连接器在生成时基于现有的 Docker 映像 - [debezium/connect-base](https://github.com/debezium/docker-images/tree/master/connect-base/1.2)。 此映像包括 Kafka 及其 Kafka Connect 库的安装，因此添加自定义连接器非常方便。 你可以参考 [Dockerfile](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile)。
- `data-generator` 服务将随机生成的 (JSON) 数据植入到 `weather-data` Kafka 主题。 你可以参考 [GitHub 存储库](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/)中的代码和 `Dockerfile`

## <a name="prerequisites"></a>先决条件

* [预配 Azure Cosmos DB Cassandra API 帐户](manage-data-dotnet.md#create-a-database-account)

* [使用 cqlsh 或托管 shell 进行验证](cassandra-support.md#hosted-cql-shell-preview)

* 安装 [Docker](https://docs.docker.com/get-docker/) 和 [Docker Compose](https://docs.docker.com/compose/install)

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>创建密钥空间、表，并启动集成管道

使用 Azure 门户，创建演示应用程序所需的 Cassandra 密钥空间和表。

> [!NOTE]
> 使用与下面的内容相同的密钥空间和表名称

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

克隆 GitHub 存储库：

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

启动所有服务：

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> 下载并启动容器可能需要一段时间：这只是一个一次性过程。

确认是否所有容器都已启动：

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

数据生成器应用程序将开始将数据发送到 Kafka 中的 `weather-data` 主题。 你还可以执行快速的健全性检查以进行确认。 快速浏览运行 Kafka Connect 工作器的 Docker 容器：


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

进入容器 shell 后，只需启动平常的 Kafka 控制台使用者进程，就会看到天气数据（采用 JSON 格式）流入。

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Cassandra 接收器连接器设置

将下面的 JSON 内容复制到一个文件中（你可以将其命名为 `cassandra-sink-config.json`）。 你需要根据你的设置对其进行更新，本部分的其余内容将提供有关此主题的指导。

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "weather-data",
        "contactPoints": "<cosmos db account name>.cassandra.cosmos.azure.com",
        "port": 10350,
        "loadBalancing.localDc": "<cosmos db region e.g. Southeast Asia>",
        "auth.username": "<enter username for cosmosdb account>",
        "auth.password": "<enter password for cosmosdb account>",
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "/etc/alternatives/jre/lib/security/cacerts/",
        "ssl.keystore.password": "changeit",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "topic.weather-data.weather.data_by_state.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "topic.weather-data.weather.data_by_station.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter.schemas.enable": false,
        "offset.flush.interval.ms": 10000
    }
}
```

以下是属性的汇总：

基本连接

- `contactPoints`：输入 Cosmos DB Cassandra 的联系点
- `loadBalancing.localDc`：输入 Cosmos DB 帐户的区域（例如“东南亚”）
- `auth.username`：输入用户名
- `auth.password`：输入密码
- `port`：输入端口值（此值为 `10350`，而不是 `9042`。 请将其保持原样）

**SSL 配置**

Azure Cosmos DB 强制实施[基于 SSL 的安全连接](../database-security.md)，Kafka Connect 连接器也支持 SSL。

- `ssl.keystore.path`：容器中 JDK 密钥存储的路径 - `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password`：JDK 密钥存储（默认）密码
- `ssl.hostnameValidation`：我们启用了节点主机名验证
- `ssl.provider`：`JDK` 用作 SSL 提供程序

泛型参数

- `key.converter`：我们使用字符串转换器 `org.apache.kafka.connect.storage.StringConverter`
- `value.converter`：由于 Kafka 主题中的数据是 JSON，因此我们使用 `org.apache.kafka.connect.json.JsonConverter`
- `value.converter.schemas.enable`：由于我们的 JSON 有效负载没有与之关联的架构（用于演示应用），因此我们需要通过将此属性设置为 `false` 来指示 Kafka Connect 不查找架构。 如果不这样做，则会导致失败。

### <a name="install-the-connector"></a>安装连接器

使用 Kafka Connect REST 终结点安装连接器：

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

检查状态：

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

如果一切顺利，连接器应当会开始发挥作用。 它应当向 Azure Cosmos DB 进行身份验证，并开始将 Kafka 主题 (`weather-data`) 中的数据引入到 Cassandra 表（`weather.data_by_state` 和 `weather.data_by_station`）中。

现在可以查询表中的数据。 转到 Azure 门户，打开 Azure Cosmos DB 帐户的托管 CQL Shell。

:::image type="content" source="./media/kafka-connect/cqlsh.png" alt-text="开放 CQLSH":::

## <a name="query-data-from-azure-cosmos-db"></a>查询 Azure Cosmos DB 中的数据

检查 `data_by_state` 和 `data_by_station` 表。 下面是一些可帮助你入门的示例查询：

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

* [在容器和数据库上预配吞吐量](../set-throughput.md) 
* [分区键最佳做法](../partitioning-overview.md#choose-partitionkey)
* [使用 Azure Cosmos DB Capacity Planner 估算 RU/秒](../estimate-ru-with-capacity-planner.md)
