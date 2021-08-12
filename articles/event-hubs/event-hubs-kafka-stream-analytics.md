---
title: Azure 事件中心 - 处理 Apache Kafka 事件
description: 教程：本文介绍如何使用 Azure 流分析处理通过事件中心引入的 Kafka 事件
ms.topic: tutorial
ms.date: 05/10/2021
ms.openlocfilehash: 0a14a4f8a4e82faebe232ac072ebe61e2db427b7
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2021
ms.locfileid: "113286366"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>教程：使用 Stream analytics 处理用于事件中心的 Apache Kafka 事件 
本文介绍如何将数据流式传输到事件中心，并使用 Azure 流分析对其进行处理。 其中包括以下步骤： 

1. 创建事件中心命名空间。
2. 创建向事件中心发送消息的 Kafka 客户端。
3. 创建将数据从事件中心复制到 Azure Blob 存储的流分析作业。 

使用事件中心公开的 Kafka 终结点时，无需更改协议客户端或运行自己的群集。 Azure 事件中心支持 [Apache Kafka 版本 1.0](https://kafka.apache.org/10/documentation.html)。 及更高版本。 


## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保符合以下先决条件：

* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Java 开发工具包 (JDK) 1.7+](/azure/developer/java/fundamentals/java-support-on-azure)。
* [下载](https://maven.apache.org/download.cgi)并[安装](https://maven.apache.org/install.html) Maven 二进制存档。
* [Git](https://www.git-scm.com/)
* **Azure 存储帐户**。 如果没有帐户，[先创建一个帐户](../storage/common/storage-account-create.md)，然后再继续进行操作。 本演练中的流分析作业将输出数据存储在 Azure Blob 存储中。 


## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间
当你创建事件中心命名空间时，系统会自动为该命名空间启用 Kafka 终结点。 可以从使用 Kafka 协议的应用程序，将事件流式传输到事件中心。 按照[使用 Azure 门户创建事件中心](event-hubs-create.md)中的分步说明创建事件中心命名空间。 如果使用专用群集，请参阅[在专用群集中创建命名空间和事件中心](event-hubs-dedicated-cluster-create-portal.md#create-a-namespace-and-event-hub-within-a-cluster)。

> [!NOTE]
> 基本层不支持适用于 Kafka 的事件中心。

## <a name="send-messages-with-kafka-in-event-hubs"></a>在事件中心内使用 Kafka 发送消息

1. 将[用于 Kafka 的 Azure 事件中心存储库](https://github.com/Azure/azure-event-hubs-for-kafka)克隆到计算机。
2. 导航到文件夹：`azure-event-hubs-for-kafka/quickstart/java/producer`。 
4. 在 `src/main/resources/producer.config` 中更新生产者的配置详细信息。 指定事件中心命名空间的名称和连接字符串。 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. 导航到 `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/`，在所选编辑器中打开 TestDataReporter.java。 
6. 为以下代码行添加注释：

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. 添加以下代码行替代注释代码： 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    此代码以 JSON 格式发送事件数据。 配置流分析作业的输入时，指定 JSON 作为输入数据的格式。 
7. 运行生产者并将事件流式传输到事件中心。 在 Windows 计算机上，使用 Node.js 命令提示符时，先切换到 `azure-event-hubs-for-kafka/quickstart/java/producer` 文件夹，再运行这些命令。 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>验证事件中心是否接收了数据

1. 在“实体”下，选择“事件中心”。 确认看到名为测试的事件中心。 

    ![事件中心 - 测试](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. 确认看到消息传入事件中心。 

    ![事件中心 - 消息](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>使用流分析作业处理事件数据
在本部分中，创建 Azure 流分析作业。 Kafka 客户端将事件发送到事件中心。 创建流分析作业，该作业采用事件数据作为输入，并将其输出到 Azure Blob 存储。 如果没有 Azure 存储帐户，[请创建一个帐户](../storage/common/storage-account-create.md)。

流分析作业中的查询经过数据，而不执行任何分析。 可以创建一个查询，该查询转换输入数据，生成不同格式的或带有见解的输出数据。  

### <a name="create-a-stream-analytics-job"></a>创建流分析作业 

1. 在 [Azure 门户](https://portal.azure.com)中，选择“+ 创建资源”。
2. 在“Azure 市场”菜单中选择“Analytics”，然后选择“流分析作业”。 
3. 在“新建流分析”页上执行以下操作： 
    1. 输入作业的名称。 
    2. 选择 **订阅**。
    3. 为资源组选择“新建”，并输入名称。 也可以使用现有资源组。 
    4. 选择作业的位置。
    5. 选择“创建”  来创建作业。 

        ![新建流分析作业](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>配置作业输入

1. 在通知消息中，选择“转到资源”以查看“流分析作业”页。  
2. 选择左侧菜单上作业拓扑部分中的“输入”。
3. 选择“添加流输入”并选择“事件中心”。 

    ![添加事件中心作为输入](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. 在“事件中心输入”配置页上，执行以下操作： 

    1. 指定输入的别名。 
    2. 选择 **Azure 订阅**。
    3. 选择之前创建的事件中心命名空间。 
    4. 选择事件中心的测试。 
    5. 选择“保存”。 

        ![事件中心输入配置](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>配置作业输出 

1. 选择菜单上作业拓扑部分中的“输出”。 
2. 选择工具栏上的“+ 添加”，并选择“Blob 存储”
3. 在“Blob 存储输出设置”页上执行以下操作： 
    1. 指定输出的别名。 
    2. 选择 **Azure 订阅**。 
    3. 选择 Azure 存储帐户。 
    4. 输入存储流分析查询的输出数据的容器的名称。
    5. 选择“保存”。

        ![Blob 存储输出配置](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>定义查询
设置用于读取传入数据流的流分析作业以后，下一步是创建一个可分析实时数据的转换。 请使用[流分析查询语言](/stream-analytics-query/stream-analytics-query-language-reference)来定义转换查询。 在本演练中，定义经过数据而不执行任何转换的查询。

1. 选择“查询”。
2. 在查询窗口中，将 `[YourOutputAlias]` 替换为之前创建的输出别名。
3. 将 `[YourInputAlias]` 替换为之前创建的输入别名。 
4. 在工具栏上选择“保存”。 

    ![屏幕捕获显示了查询窗口，其中包含输入和输出变量的值。](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>运行流分析作业

1. 选择左侧菜单上的“概览”。 
2. 选择“开始”。 

    ![“开始”菜单](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. 在“启动作业”页上选择“启动”。 

    ![“启动作业”页](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. 等待，直到作业状态从“正在启动”更改为“正在运行”。 

    ![作业状态 - 正在运行](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>测试方案
1. 再次运行“Kafka 生产者”，将事件发送到事件中心。 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. 确认看到在“Azure Blob 存储”中生成“输出数据”。 看到容器中具有 100 行的 JSON 文件，类似以下示例行： 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    在此方案中，Azure 流分析作业接收来自事件中心的输入数据，并将其存储在 Azure Blob 存储中。 



## <a name="next-steps"></a>后续步骤
本文介绍了如何在不更改协议客户端或运行自己的群集的情况下，将事件流式传输到事件中心。 若要详细了解用于 Apache Kafka 的事件中心，请参阅[适用于 Azure 事件中心的 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)。