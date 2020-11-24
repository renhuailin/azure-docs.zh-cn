---
title: 在 Azure HDInsight 中通过 Confluent 架构注册表 Apache Kafka
description: 在虚拟网络中部署一个包含边缘节点的 HDInsight 托管 Kafka 群集，然后在边缘节点上安装 Confluent 架构注册表。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: bb46bc18469638416ff76f84516498e0076c85fd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "95500316"
---
# <a name="apache-kafka-with-confluent-schema-registry-in-azure-hdinsight"></a>在 Azure HDInsight 中通过 Confluent 架构注册表 Apache Kafka

Kafka 架构注册表提供插入 Apache Kafka 客户端的序列化程序，这些客户端处理以 Avro 格式发送的 Kafka 消息的消息架构存储和检索。 它被 Confluent 为 OSS 项目，但现在已在 [Confluent 社区许可证](https://www.confluent.io/blog/license-changes-confluent-platform/)下。 架构注册表还提供以下用途：

* 为制造商和使用者存储和检索架构。
* 在主题上强制/full 兼容性。
* 减小发送到 Kafka 的有效负载的大小。

在 HDInsight 托管的 Kafka 群集中，架构注册表通常部署在边缘节点上，以允许从头节点进行计算。

下面是有关如何在 HDInsight 群集上部署架构注册表的典型体系结构。 架构注册表对其执行的操作公开 REST API。  生成者和使用者可以与 VNet 中的架构注册表或使用 [KAFKA REST 代理](rest-proxy.md)交互。

![HDInsight Kafka 架构注册表](./media/schema-registry/pic1.png)

## <a name="deploy-an-hdinsight-managed-kafka-with-confluent-schema-registry"></a>使用 Confluent 架构注册表部署 HDInsight 托管的 Kafka

在本部分中，我们将在虚拟网络中部署一个包含边缘节点的 HDInsight 托管 Kafka 群集，然后在边缘节点上安装 Confluent 架构注册表。  

1. 选择下面的“部署到 Azure”按钮以登录到 Azure，并打开资源管理器模板。

    [![部署到 Azure](./media/schema-registry/hdi-deploy-to-azure1.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Farnabganguly%2FKafkaschemaregistry%2Fmaster%2Fazuredeploy.json)

1. 在 "自定义部署" 模板上，按如下所述填充字段：

    |属性 |说明 |
    |---|---|
    |订阅|从下拉列表中选择用于此群集的 Azure 订阅。|
    |资源组|从下拉列表中选择现有资源组，或选择“新建”  。|
    |区域|从下拉列表中，选择在其中创建群集的区域。|
    |群集名称|输入任何全局唯一的名称。 或者原样保留以使用默认名称。|
    |群集登录用户名|提供用户名，默认值为 **admin**。|
    |群集登录密码|提供密码。|
    |SSH 用户名|提供用户名，默认值为 **sshuser**。|
    |SSH 密码|提供密码。|

    将其他字段保留原样。 选择“查看 + 创建”以继续。

1. 查看部署详细信息，然后选择 " **创建** " 以初始化部署。 部署可能需要45分钟才能完成。

## <a name="configure-the-confluent-schema-registry"></a>配置 Confluent 架构注册表

在本部分中，我们将配置在边缘节点上安装的 Confluent Kafka 架构注册表。 Confluent 架构注册表位于  `/etc/schema-registry/schema-registry.properties` ，启动和停止服务可执行文件的机制位于  `/usr/bin/` 文件夹中。

架构注册需要知道 Zookeeper 服务可以与 HDInsight Kafka 群集进行交互。 按照以下步骤获取 Zookeeper 仲裁的详细信息。

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md) 连接到群集边缘节点。 编辑以下命令，将 CLUSTERNAME 替换为群集的名称，然后输入该命令：

    ```cmd
    ssh sshuser@schema-registry.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 设置密码变量。 将 PASSWORD 替换为群集登录密码，然后输入以下命令：

    ```bash
    export password='PASSWORD'
    ```

1. 提取具有正确大小写格式的群集名称。 执行以下命令：

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

1. 提取 Kafka Zookeeper 主机。 执行以下命令：

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKAZKHOSTS
    ```

    记下此值，因为稍后将用到它。

1. 提取 Kafka 代理主机。 执行以下命令：

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKABROKERS
    ```

1. 在编辑模式下打开架构注册表属性文件。 执行以下命令：

    ```bash
    sudo nano /etc/schema-registry/schema-registry.properties
    ```

    1. 将的值更新为 `kafkastore.connection.url` 前面标识的 Zookeeper 字符串。
    1. 将的值更新 `debug` 为 `true` 。

    Properties 文件现在看起来类似于：  

    ```
    listeners=http://0.0.0.0:8081
    kafkastore.connection.url=zk1-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181,zk2-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181
    kafkastore.topic=_schemas
    debug=true
    ```

1. 要保存文件，请使用 **Ctrl + X**、**Y**，并按 **Enter**。

1. 启动架构注册表并使其指向使用更新后的架构注册表属性文件。 执行以下命令：

    ```bash
    cd /bin
    sudo schema-registry-start /etc/schema-registry/schema-registry.properties
    ```

1. 在一个 SSH 会话中运行架构注册表时，启动另一个 SSH 窗口。  在主题 "Kafka" 下注册新版本的架构，并记下输出：

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}'
    ```

    ```output
       HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:33:04 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. 在主题 "Kafka" 下注册新版本的架构，并记下输出：

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}' \
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:18 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. 列出所有主题并检查输出：

    ```cmd
    curl -X GET -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        http://localhost:8081/subjects
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:39 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 27
    Server: Jetty(9.2.24.v20180105)

    ["Kafka-value","Kafka-key"]
    ```

你可能想要尝试此处列出的其他一些 [高级命令](https://docs.confluent.io/1.0/schema-registry/docs/intro.html#quickstart)。

## <a name="send-and-consume-avro-data-from-kafka-using-schema-registry"></a>使用架构注册表从 Kafka 发送和使用 Avro 数据

在本部分中，我们将从标准输入中读取数据，并将其写入格式为 Kafka 的主题。 接下来，我们将使用使用者和 Avro 格式化程序从 Kafka 主题读取数据，以将这些数据转换为可读格式。

1. 创建 Kafka 主题 `agkafkaschemareg` 。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic agkafkaschemareg --zookeeper $KAFKAZKHOSTS
    ```

1. 使用 **Kafka Avro 控制台** 生成者创建一个架构，将该架构分配给该主题，然后开始以 Avro 格式将数据发送到该主题。 请确保已成功创建上一步中的 Kafka 主题，并且 **$KAFKABROKERS** 具有值。

    我们要发送的架构是一个密钥：值对

    ```
    Value
    {
      "type": "record",
      "name": "example_schema",
      "namespace": "com.example",
      "fields": [
        {
          "name": "cust_id",
          "type": "int",
          "doc": "Id of the customer account"
        },
        {
          "name": "year",
          "type": "int",
          "doc": "year of expense"
        },
        {
          "name": "expenses",
          "type": {
            "type": "array",
            "items": "float"
          },
          "doc": "Expenses for the year"
        }
      ],
      "doc:": "A basic schema for storing messages"
    }
    ```

    使用以下命令启动 **Kafka Avro 控制台制造** 者：

    ```bash
    /usr/bin/kafka-avro-console-producer     --broker-list $KAFKABROKERS     --topic agkafkaschemareg     --property parse.key=true --property key.schema='{"type" : "int", "name" : "id"}'     --property value.schema='{ "type" : "record", "name" : "example_schema", "namespace" : "com.example", "fields" : [ { "name" : "cust_id", "type" : "int", "doc" : "Id of the customer account" }, { "name" : "year", "type" : "int", "doc" : "year of expense" }, { "name" : "expenses", "type" : {"type": "array", "items": "float"}, "doc" : "Expenses for the year" } ], "doc:" : "A basic schema for storing messages" }'
    ```

1. 当制造者准备好接受消息时，请开始发送预定义 Avro 架构格式的消息。 使用 Tab 键可在键和值之间创建间距。

    ```
    1 TAB {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2 TAB {"cust_id":3535353, "year":2011, "expenses":[761.35, 92.18, 14.41]}
    3 TAB {"cust_id":7979797, "year":2011, "expenses":[4489.00]}
    ```

1. 尝试在控制台生成器中输入随机非架构数据，以查看生成者现在如何允许任何不符合预定义 Avro 架构的数据。

    ```
    1       {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2       {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]}
    org.apache.kafka.common.errors.SerializationException: Error deserializing json {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]} to Avro of schema {"type":"record","name":"example_schema","namespace":"com.example","fields":[{"name":"cust_id","type":"int","doc":"Id of the customer account"},{"name":"year","type":"int","doc":"year of expense"},{"name":"expenses","type":{"type":"array","items":"float"},"doc":"Expenses for the year"}],"doc:":"A basic schema for storing messages"}
    Caused by: org.codehaus.jackson.JsonParseException: Unexpected character ('"' (code 34)): was expecting comma to separate OBJECT entries
     at [Source: java.io.StringReader@3561c410; line: 1, column: 35]
            at org.codehaus.jackson.JsonParser._constructError(JsonParser.java:1433)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportError(JsonParserMinimalBase.java:521)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportUnexpectedChar(JsonParserMinimalBase.java:442)
            at org.codehaus.jackson.impl.ReaderBasedParser.nextToken(ReaderBasedParser.java:406)
            at org.apache.avro.io.JsonDecoder.getVaueAsTree(JsonDecoder.java:549)
            at org.apache.avro.io.JsonDecoder.doAction(JsonDecoder.java:474)
            at org.apache.avro.io.parsing.Parser.advance(Parser.java:88)
            at org.apache.avro.io.JsonDecoder.advance(JsonDecoder.java:139)
            at org.apache.avro.io.JsonDecoder.readInt(JsonDecoder.java:166)
            at org.apache.avro.io.ValidatingDecoder.readInt(ValidatingDecoder.java:83)
            at org.apache.avro.generic.GenericDatumReader.readInt(GenericDatumReader.java:511)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:182)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.readField(GenericDatumReader.java:240)
            at org.apache.avro.generic.GenericDatumReader.readRecord(GenericDatumReader.java:230)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:174)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:144)
            at io.confluent.kafka.formatter.AvroMessageReader.jsonToAvro(AvroMessageReader.java:213)
            at io.confluent.kafka.formatter.AvroMessageReader.readMessage(AvroMessageReader.java:200)
            at kafka.tools.ConsoleProducer$.main(ConsoleProducer.scala:59)
            at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
    
    ```

1. 在另一个屏幕中，启动 Kafka Avro 控制台使用者

    ```bash
    sudo /usr/bin/kafka-avro-console-consumer --bootstrap-server $KAFKABROKERS --topic agkafkaschemareg --from-beginning
    ```

    应会看到以下输出：

    ```output
    {"cust_id":1313131,"year":2012,"expenses":[1313.13,2424.24]}
    {"cust_id":7979797,"year":2011,"expenses":[4489.0]}
    {"cust_id":3535353,"year":2011,"expenses":[761.35,92.18,14.41]}
    ```

## <a name="next-steps"></a>后续步骤

* [使用 Apache Kafka 制造者和使用者 Api](apache-kafka-producer-consumer-api.md)