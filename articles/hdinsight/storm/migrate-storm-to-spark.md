---
title: 将 Azure HDInsight 3.6 Apache Storm 迁移到 HDInsight 4.0 Apache Spark
description: 将 Apache Storm 工作负荷迁移到 Spark 流式处理或 Spark 结构化流式处理的差异和迁移流程。
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/16/2019
ms.openlocfilehash: c4f4156f80fac0c9e5eaae360aa937544d88aa9e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656790"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>将 Azure HDInsight 3.6 Apache Storm 迁移到 HDInsight 4.0 Apache Spark

本文档介绍如何将 HDInsight 3.6 上的 Apache Storm 工作负荷迁移到 HDInsight 4.0。 HDInsight 4.0 不支持 Apache Storm 群集类型，你将需要迁移到另一个流式处理数据平台。 两个合适的选项分别是 Apache Spark 流式处理和 Spark 结构化流式处理。 本文档介绍了这些平台之间的差异，并且还建议使用工作流迁移 Apache Storm 工作负荷。

## <a name="storm-migration-paths-in-hdinsight"></a>HDInsight 中的 Storm 迁移路径

如果要从 HDInsight 3.6 上的 Apache Storm 迁移，可以使用多种选项：

* HDInsight 4.0 上的 Spark 流式处理
* HDInsight 4.0 上的 Spark 结构化流式处理
* Azure 流分析

本文档提供有关从 Apache Storm 迁移到 Spark 流式处理和 Spark 结构化流式处理的指南。

:::image type="content" source="./media/migrate-storm-to-spark/storm-migration-path.png" alt-text="HDInsight Storm 迁移路径" border="false":::

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Apache Storm 和 Spark 流式处理、Spark 结构化流式处理之间的比较

Apache Storm 可以提供不同级别的有保证的消息处理。 例如，基本的 Storm 应用程序至少可以保证一次处理，而 [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) 仅可以保证一次处理。 Spark 流式处理和 Spark 结构化流式处理保证即便发生节点故障，任何输入事件也仅处理一次。 Storm 具有处理每个单个事件的模型，你还可以将微批处理模型与 Trident 一起使用。 Spark 流式处理和 Spark 结构化流式处理提供微批处理模型。

|  |暴风 |Spark 流式处理 | Spark 结构化流式处理|
|---|---|---|---|
|事件处理保证|至少一次 <br> 仅一次 (Trident) |[仅一次](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[仅一次](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|处理模型|实时 <br> 微批处理 (Trident) |微批处理 |微批处理 |
|事件时间支持|[是](https://storm.apache.org/releases/2.0.0/Windowing.html)|否|[是](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**语言**|Java 等。|Scala、Java、Python|Python、R、Scala、Java、SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Spark 流式处理与 Spark 结构化流式处理

Spark 结构化流式处理即将取代 Spark 流式处理 (DStream)。 结构化流式处理将不断得到增强和维护，而 DStream 只会保留维护模式。 在现成支持的源和接收器方面，结构化流式处理的功能不如 DStream 那样全面，因此，在选择适当的 Spark 流式处理处理选项之前，请先评估要求。

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>流式处理（单个事件）处理与微批处理

Storm 提供处理每个单个事件的模型。 这意味着所有传入的记录将在其到达时立即进行处理。 Spark 流式处理应用程序必须先等待一会，以收集事件的每个微批处理，才能发送该批处理进行处理。 与此相反，事件驱动应用程序会立即处理每个事件。 Spark 流式处理延迟一般为几秒钟。 微批处理方法的优点是数据处理效率更高和聚合计算更简单。

:::image type="content" source="./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png" alt-text="流式处理和微批处理" border="false":::

## <a name="storm-architecture-and-components"></a>Storm 体系结构和组件

Storm 拓扑由多个以有向无环图 (DAG) 形式排列的组件构成。 数据在该图中的组件之间流动。 每个组件使用一个或多个数据流，并可选择性地发出一个或多个流。

|组件 |说明 |
|---|---|
|Spout|将数据引入拓扑。 它们将一个或多个流发出到拓扑中。|
|Bolt|使用 Spout 或其他 Bolt 发出的流。 Bolt 可以选择性地将流发出到拓扑中。 Bolt 还负责将数据写入 HDFS、Kafka 或 HBase 等外部服务或存储。|

:::image type="content" source="./media/migrate-storm-to-spark/apache-storm-components.png" alt-text="Storm 组件的交互" border="false":::

Storm 包含以下三个守护程序，它们使 Storm 群集正常运行。

|守护程序 |说明 |
|---|---|
|Nimbus|与 Hadoop JobTracker 相似，它负责在群集周围分发代码，并将任务分配给计算机并监视失败。|
|Zookeeper|用于群集协调。|
|监督器|侦听分配给其计算机的工作，并根据来自 Nimbus 的指令启动和停止工作进程。 每个工作进程都执行拓扑的一个子集。 用户的应用程序逻辑（Spout 和 Bolt）在此处运行。|

:::image type="content" source="./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png" alt-text="nimbus、zookeeper 和监督程序守护程序" border="false":::

## <a name="spark-streaming-architecture-and-components"></a>Spark 流式处理体系结构和组件

以下步骤总结了组件在 Spark 流式处理 (DStream) 和 Spark 结构化流式处理中的协同工作方式：

* 启动 Spark 流式处理时，驱动程序会在执行器中启动任务。
* 执行器从流式处理数据源接收流。
* 当执行器接收数据流时，它会将流拆分为多个块，并将其保留在内存中。
* 数据块将被复制到其他执行器。
* 然后，处理后的数据将存储在目标数据存储中。

:::image type="content" source="./media/migrate-storm-to-spark/spark-streaming-to-output.png" alt-text="要输出的 spark 流式处理路径" border="false":::

## <a name="spark-streaming-dstream-workflow"></a>Spark 流式处理 (DStream) 工作流

每个批处理间隔后，将生成新的 RDD，其中包含该间隔的所有数据。 连续的 RDD 集将被收集到 DStream。 例如，如果批处理间隔为 1 秒，则 DStream 将每秒发出一个批处理，其中包含一个 RDD（包含该秒期间引入的所有数据）。 处理 DStream 时，温度事件将出现在其中一个批处理中。 Spark 流式处理应用程序处理包含事件的批处理并最终作用于每个 RDD 中存储的数据。

:::image type="content" source="./media/migrate-storm-to-spark/spark-streaming-batches.png" alt-text="Spark 流式处理的处理批次" border="false":::

有关 Spark 流式处理可用的不同转换的详细信息，请参阅 [DStream 上的转换](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams)。

## <a name="spark-structured-streaming"></a>Spark 结构化流

Spark 结构化流式处理以表的形式表示数据流，表的深度不受限制。 随着新数据的抵达，输入表会不断增大。 此输入表由一个长时间运行的查询持续处理，结果将发送到输出表。

在结构化流中，数据抵达系统后立即被引入输入表中。 可以编写针对此输入表执行操作的查询（使用数据帧和数据集 API）。

查询输出将生成一个结果表，其中包含查询的结果。 可以从外部数据存储（例如关系数据库）的结果表中绘制数据。

处理输入表中数据的时间由触发器间隔控制。 默认情况下，触发器间隔为零，因此，结构化流会在数据抵达时尽快处理数据。 在实践中，这意味着结构化流在处理完前一查询的运行之后，会立即针对所有新收到的数据启动另一个处理运行。 可将触发器配置为根据某个间隔运行，以便在基于时间的批中处理流数据。

:::image type="content" source="./media/migrate-storm-to-spark/structured-streaming-data-processing.png" alt-text="在结构化流式处理中处理数据" border="false":::

:::image type="content" source="./media/migrate-storm-to-spark/structured-streaming-model.png" alt-text="结构化流式处理的编程模型" border="false":::

## <a name="general-migration-flow"></a>一般迁移流程

对于从 Storm 到 Spark 的迁移流程，建议采用以下初始体系结构：

* Kafka 用作流式处理数据源
* Kafka 和 Storm 部署在同一虚拟网络上
* Storm 处理的数据会写入数据接收器，如 Azure 存储或 Azure Data Lake Storage Gen2。

   :::image type="content" source="./media/migrate-storm-to-spark/presumed-current-environment.png" alt-text="假定当前环境的示意图"  border="false":::

若要将应用程序从 Storm 迁移到其中一个 Spark 流式处理 API，请执行以下操作：

1. 部署新群集。 在同一虚拟网络中部署新的 HDInsight 4.0 Spark 群集，并在其上部署 Spark 流式处理或 Spark 结构化流式处理应用程序，并对其进行全面的测试。

   :::image type="content" source="./media/migrate-storm-to-spark/new-spark-deployment.png" alt-text="HDInsight 中的新 Spark 部署" border="false":::

1. 停止在旧 Storm 群集上使用。 在现有 Storm 中，停止使用流式处理数据源中的数据，并等待数据完成写入目标接收器。

   :::image type="content" source="./media/migrate-storm-to-spark/stop-consuming-current-cluster.png" alt-text="停止在当前群集上使用" border="false":::

1. 开始在新 Spark 群集上使用。 从最新部署的 HDInsight 4.0 Spark 群集开始流式传输数据。 此时，通过使用最新的 Kafka 偏移量来接管进程。

   :::image type="content" source="./media/migrate-storm-to-spark/start-consuming-new-cluster.png" alt-text="开始在新群集上使用" border="false":::

1. 根据需要删除旧群集。 切换完成并正常工作后，根据需要删除旧的 HDInsight 3.6 Storm 群集。

   :::image type="content" source="./media/migrate-storm-to-spark/remove-old-clusters1.png" alt-text="根据需要删除旧的 HDInsight 群集" border="false":::

## <a name="next-steps"></a>后续步骤

有关 Storm、Spark 流式处理和 Spark 结构化流式处理的详细信息，请参阅以下文档：

* [Apache Spark 流式处理概述](../spark/apache-spark-streaming-overview.md)
* [Apache Spark 结构化流的概述](../spark/apache-spark-structured-streaming-overview.md)
