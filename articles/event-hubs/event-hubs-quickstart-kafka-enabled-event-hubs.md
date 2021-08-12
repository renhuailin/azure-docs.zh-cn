---
title: 快速入门：通过 Kafka 协议使用 Azure 事件中心进行数据流式传输
description: 快速入门：本文介绍了如何使用 Kafka 协议和 API 流式传输到 Azure 事件中心。
ms.topic: quickstart
ms.date: 05/10/2021
ms.openlocfilehash: b88515ee0d1bc46eb41585d05f61a2a246cfab44
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2021
ms.locfileid: "113288385"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>快速入门：使用 Kafka 协议通过事件中心进行数据流式传输
此快速入门介绍如何在不更改协议客户端或运行自己的群集的情况下将数据流式传输到事件中心。 你将了解如何只需更改应用程序配置，即可使用生产者和使用者与事件中心通信。 

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) 上提供了此示例

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保符合以下先决条件：

* 通读[用于 Apache Kafka 的事件中心](event-hubs-for-kafka-ecosystem-overview.md)一文。
* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Java 开发工具包 (JDK) 1.7+](/azure/developer/java/fundamentals/java-support-on-azure)。
* [下载](https://maven.apache.org/download.cgi)并[安装](https://maven.apache.org/install.html) Maven 二进制存档。
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间
当你创建事件中心命名空间时，系统会自动为该命名空间启用 Kafka 终结点。 可以从使用 Kafka 协议的应用程序，将事件流式传输到事件中心。 按照[使用 Azure 门户创建事件中心](event-hubs-create.md)中的分步说明创建事件中心命名空间。 如果使用专用群集，请参阅[在专用群集中创建命名空间和事件中心](event-hubs-dedicated-cluster-create-portal.md#create-a-namespace-and-event-hub-within-a-cluster)。

> [!NOTE]
> 基本层不支持适用于 Kafka 的事件中心。

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>在事件中心内使用 Kafka 发送和接收消息

1. 克隆[用于 Kafka 的 Azure 事件中心存储库](https://github.com/Azure/azure-event-hubs-for-kafka)。

2. 导航到 `azure-event-hubs-for-kafka/quickstart/java/producer`。

3. 在 `src/main/resources/producer.config` 中更新生产者的配置详细信息，如下所示：

    **TLS/SSL：**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    
    > [!IMPORTANT]
    > 将 `{YOUR.EVENTHUBS.CONNECTION.STRING}` 替换为事件中心命名空间的连接字符串。 有关获取连接字符串的说明，请参阅[获取事件中心连接字符串](event-hubs-get-connection-string.md)。 下面是一个配置示例：`sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

    **OAuth：**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    可以在[此处](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java)的 GitHub 上找到示例处理程序类 CustomAuthenticateCallbackHandler 的源代码。
4. 运行生产者代码并将事件流式传输到事件中心：
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. 导航到 `azure-event-hubs-for-kafka/quickstart/java/consumer`。

6. 在 `src/main/resources/consumer.config` 中更新使用者的配置详细信息，如下所示：
   
    **TLS/SSL：**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    > [!IMPORTANT]
    > 将 `{YOUR.EVENTHUBS.CONNECTION.STRING}` 替换为事件中心命名空间的连接字符串。 有关获取连接字符串的说明，请参阅[获取事件中心连接字符串](event-hubs-get-connection-string.md)。 下面是一个配置示例：`sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

    **OAuth：**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    可以在[此处](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java)的 GitHub 上找到示例处理程序类 CustomAuthenticateCallbackHandler 的源代码。

    可在[此处](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)找到 Kafka 的事件中心的所有 OAuth 示例。
7. 使用 Kafka 客户端运行使用者代码并处理来自事件中心的事件：

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

如果事件中心 Kafka 群集有事件，则现在开始从使用者接收这些事件。

## <a name="next-steps"></a>后续步骤
本文介绍了如何在不更改协议客户端或运行自己的群集的情况下，将事件流式传输到事件中心。 若要了解详细信息，请参阅[针对 Azure 事件中心的 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)。