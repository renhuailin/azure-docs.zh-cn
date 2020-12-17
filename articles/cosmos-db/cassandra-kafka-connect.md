---
title: 使用 Kafka Connect 集成 Apache Kafka 和 Azure Cosmos DB Cassandra API
description: 了解如何使用 DataStax Apache Kafka 连接器将数据从 Kafka 引入 Azure Cosmos DB Cassandra API
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: a233845e8f19cc44cd9d00a0392b1341db297fd6
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632638"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>使用 Kafka Connect 将数据从 Apache Kafka 引入 Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

现有的 Cassandra 应用程序可以轻松处理 [Azure Cosmos DB Cassandra API](cassandra-introduction.md) ，因为它的 [CQLv4 驱动程序兼容性](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)。 利用此功能可以与流式处理平台（例如 [Apache Kafka](https://kafka.apache.org/) ）集成，并将数据引入 Azure Cosmos DB。

Apache Kafka 中的数据 (主题) 仅在由其他应用程序使用或引入到其他系统时才有用。 使用所[选的语言和客户端 SDK](https://cwiki.apache.org/confluence/display/KAFKA/Clients)，可以使用[Kafka 生成者/使用者](https://kafka.apache.org/documentation/#api)api 构建解决方案。 Kafka Connect 提供了一个替代解决方案。 它是一个平台，用于以可缩放且可靠的方式在 Apache Kafka 和其他系统之间流式传输数据。 由于 Kafka Connect 支持包括 Cassandra 的货位连接器，因此你无需编写自定义代码来将 Kafka 与 Azure Cosmos DB Cassandra API 集成。 

在本文中，我们将使用开源 [DataStax Apache Kafka 连接器](https://docs.datastax.com/kafka/doc/kafka/kafkaIntro.html)，该连接器可在 Kafka Connect framework 的顶层使用，以将 Kafka 主题中的记录引入一个或多个 Cassandra 表的行中。 该示例提供了一个可重用的安装程序，它使用 Docker Compose。 这非常方便，因为它允许您使用单个命令在本地启动所有所需的组件。 这些组件包括 Kafka、Zookeeper、Kafka 连接辅助角色和示例数据生成器应用程序。

下面是组件及其服务定义的细分，你可以参考 GitHub 存储库中的完整 `docker-compose` 文件[](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml)。

- Kafka 和 Zookeeper 使用 [debezium](https://hub.docker.com/r/debezium/kafka/) 映像。
- 若要以 Docker 容器的身份运行，DataStax Apache Kafka 连接器在现有 Docker 映像- [debezium/](https://github.com/debezium/docker-images/tree/master/connect-base/1.2)融入的顶层。 此映像包括 Kafka 及其 Kafka 连接库的安装，从而使添加自定义连接器变得非常方便。 可以参考 [Dockerfile](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile)。
- `data-generator`服务种子 (JSON) 数据随机生成到 `weather-data` Kafka 主题中。 可以参考 GitHub 存储库中的 `Dockerfile` 代码[](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/)和

## <a name="prerequisites"></a>先决条件

* [预配 Azure Cosmos DB Cassandra API 帐户](create-cassandra-dotnet.md#create-a-database-account)

* [使用 cqlsh 或托管 shell 进行验证](cassandra-support.md#hosted-cql-shell-preview)

* 安装 [Docker](https://docs.docker.com/get-docker/) 和 [Docker Compose](https://docs.docker.com/compose/install)

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>创建密钥空间和表并启动集成管道

使用 Azure 门户创建演示应用程序所需的 Cassandra 密钥空间和表。

> [!NOTE]
> 使用相同的密钥空间和表名称，如下所示

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
> 下载并启动容器可能需要一些时间：这只是一次过程。

若要确认是否已启动所有容器：

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

数据生成器应用程序将开始将数据发送到 `weather-data` Kafka 中的主题。 你还可以执行快速的健全检查以确认。 查看运行 Kafka connect 辅助角色的 Docker 容器：


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

放入容器 shell 后，只需启动常见的 Kafka 控制台使用者进程，你应会看到 (以 JSON 格式) 在中流动的天气数据。

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Cassandra 接收器连接器设置

将下面的 JSON 内容复制到文件 (可以将其命名为 `cassandra-sink-config.json`) 。 你需要根据你的设置对其进行更新，本节的其余部分将提供有关本主题的指导。

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

下面是属性的摘要：

**基本连接**

- `contactPoints`：输入 Cosmos DB Cassandra 的联系点
- `loadBalancing.localDc`：输入 Cosmos DB 帐户的区域（例如东南亚）
- `auth.username`：输入用户名
- `auth.password`：输入密码
- `port`：输入端口值 (此值为 `10350` ，而不是 `9042` 。 保持原样) 

**SSL 配置**

Azure Cosmos DB [通过 ssl 强制实施安全连接](database-security.md) ，并且 Kafka 连接连接器也支持 ssl。

- `ssl.keystore.path`：容器中 JDK 密钥存储的路径- `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password`： JDK 密钥存储 (默认) 密码
- `ssl.hostnameValidation`：我们将自己的节点主机名验证
- `ssl.provider`：用作 `JDK` SSL 提供程序

**泛型参数**

- `key.converter`：我们使用字符串转换器 `org.apache.kafka.connect.storage.StringConverter`
- `value.converter`：由于 Kafka 主题中的数据是 JSON，因此使用了 `org.apache.kafka.connect.json.JsonConverter`
- `value.converter.schemas.enable`：由于 JSON 负载没有与)  (相关联的架构，因此，我们需要通过将此属性设置为来指示 Kafka Connect 不查找架构 `false` 。 如果不这样做，会导致失败。

### <a name="install-the-connector"></a>安装连接器

使用 Kafka Connect REST 终结点安装连接器：

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

检查状态：

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

如果一切顺利，连接器就应该开始编织。 它应进行身份验证，以便 Azure Cosmos DB 和开始从 Kafka 主题中引入数据， (`weather-data`) 到 Cassandra 表- `weather.data_by_state` 和 `weather.data_by_station`

你现在可以查询表中的数据。 转到 Azure 门户，为你的 Azure Cosmos DB 帐户打开托管的 CQL Shell。

:::image type="content" source="./media/cassandra-kafka-connect/cqlsh.png" alt-text="开放 CQLSH":::

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

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

* [在容器和数据库上预配吞吐量](set-throughput.md) 
* [分区键最佳做法](partitioning-overview.md#choose-partitionkey)
* [使用 Azure Cosmos DB Capacity Planner 估算 RU/秒](estimate-ru-with-capacity-planner.md)
