---
title: 使用 Azure Synapse Link 中的 Apache Spark 2 与 Azure Cosmos DB 进行交互
description: 如何使用 Azure Synapse Link 中的 Apache Spark 与 Azure Cosmos DB 进行交互
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 9ea9d5d34d69b950e3fa80c350c43f12615660ba
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112549"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-2-in-azure-synapse-link"></a>使用 Azure Synapse Link 中的 Apache Spark 2 与 Azure Cosmos DB 进行交互

> [!NOTE]
> 有关使用 Spark 3 的 Synapse Link for Cosmos DB 的信息，请参阅本文 [Spark 3 上的 Azure Synapse Link for Azure Cosmos DB](how-to-query-analytical-store-spark-3.md)

本文将介绍如何使用 Synapse Apache Spark 2 与 Azure Cosmos DB 进行交互。 由于提供全面的 Scala、Python、SparkSQL 和 C# 支持，Synapse Apache Spark 对于 [Azure Synapse Link for Azure Cosmos DB](../../cosmos-db/synapse-link.md) 中的分析、数据工程、数据科学和数据探索方案至关重要。

与 Azure Cosmos DB 进行交互时支持以下功能：
* 利用 Synapse Apache Spark，可以近乎实时地分析通过 Azure Synapse Link 启用的 Azure Cosmos DB 容器中的数据，而不会影响事务工作负载的性能。 以下两个选项可用于查询 Spark 中的 Azure Cosmos DB [分析存储](../../cosmos-db/analytical-store-introduction.md)：
    + 加载到 Spark 数据帧
    + 创建 Spark 表
* 还可以利用 Synapse Apache Spark 将数据引入 Azure Cosmos DB。 需要注意的是，数据始终通过事务存储引入到 Azure Cosmos DB 容器中。 启用 Synapse Link 后，任何新的插入、更新和删除操作都将自动同步到分析存储。
* Synapse Apache Spark 还支持将 Azure Cosmos DB 作为源和接收器的 Spark 结构化流。 

以下各节将演练上述功能的语法。 你还可以查看 Learn 模块，了解如何[使用适用于 Azure Synapse Analytics 的 Apache Spark 查询 Azure Cosmos DB](/learn/modules/query-azure-cosmos-db-with-apache-spark-for-azure-synapse-analytics/)。 Azure Synapse Analytics 工作区中的笔势旨在提供一种简单现成的入门体验。 右键单击 Synapse 工作区的“数据”选项卡中的 Azure Cosmos DB 容器时，可以显示这些笔势。 借助笔势，可以快速生成代码，并根据需要对其进行定制。 笔势还适用于只需单击一下即可发现数据的功能。

> [!IMPORTANT]
> 应注意分析架构中可能会导致数据加载操作出现意外行为的某些约束。
> 例如，只有事务架构中的前 1000 个属性才能在分析架构中使用，无法使用含空格的属性等等。如果遇到某些意外结果，请查看[分析存储架构约束](../../cosmos-db/analytical-store-introduction.md#schema-constraints)以了解更多详细信息。

## <a name="query-azure-cosmos-db-analytical-store"></a>查询 Azure Cosmos DB 分析存储

在了解用于查询 Azure Cosmos DB 分析存储的两个可能选项（“加载到 Spark 数据帧”和“创建 Spark 表”）之前，有必要先探索其体验上的差异，以便选择适合自己需求的选项。

体验上的差异在于，Azure Cosmos DB 容器中的基础数据更改是否应自动反映在 Spark 中执行的分析中。 当注册 Spark 数据帧或针对容器的分析存储创建 Spark 表时，会将分析存储中当前的数据快照的元数据提取到 Spark 中，以便高效地执行后续分析。 请务必注意，因为 Spark 遵循延迟计算策略，所以除非在 Spark 数据帧上调用了某个操作，或者针对 Spark 表执行了 SparkSQL 查询，否则不会从基础容器的分析存储中提取实际数据。

在“加载到 Spark 数据帧”的情况下，在 Spark 会话的整个生存期中都会缓存获取的元数据，因此，在创建数据帧时，将针对分析存储的快照评估在数据帧上调用的后续操作。

而在“创建 Spark 表”的情况下，分析存储状态的元数据不会缓存在 Spark 中，而是在针对 Spark 表的每个 SparkSQL 查询执行中重新加载。

因此，可以根据是要针对分析存储的固定快照还是针对分析存储的最新快照对 Spark 分析进行评估，选择是要“加载到 Spark 数据帧”还是“创建 Spark 表”。

> [!NOTE]
> 若要查询 Mongo DB 帐户的 Azure Cosmos DB API，请详细了解分析存储中的[完全保真架构表示形式](../../cosmos-db/analytical-store-introduction.md#analytical-schema)，以及要使用的扩展属性名称。

> [!NOTE]
> 请注意，以下命令中的所有 `options` 都区分大小写。 例如，必须使用 `Gateway`，而 `gateway` 将返回一个错误。

### <a name="load-to-spark-dataframe"></a>加载到 Spark 数据帧

在此示例中，将创建一个指向 Azure Cosmos DB 分析存储的 Spark 数据帧。 然后，可以通过对数据帧调用 Spark 操作来执行其他分析。 此操作不会影响事务存储。

“Python”中的语法如下所示：
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

“Scala”中的等效语法如下所示：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>创建 Spark 表

在此示例中，将创建一个指向 Azure Cosmos DB 分析存储的 Spark 表。 然后，可以通过对表调用 SparkSQL 查询来执行其他分析。 此操作不会影响事务存储，也不会产生任何数据移动。 如果决定删除该 Spark 表，基础 Azure Cosmos DB 容器以及相应的分析存储不会受到影响。 

此方案非常方便，可以通过第三方工具重复使用 Spark 表，并提供对运行时基础数据的访问。

创建 Spark 表的语法如下所示：
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> 如果你有一些方案，其中基础 Azure Cosmos DB 容器的架构随时间而变化，且如果你希望更新的架构自动反映在针对 Spark 表的查询中，则可以通过将 Spark 表选项中的 `spark.cosmos.autoSchemaMerge` 选项设置为 `true` 来实现。


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>将 Spark 数据帧写入 Azure Cosmos DB 容器

在此示例中，会将 Spark 数据帧写入 Azure Cosmos DB 容器。 此操作将影响事务工作负载的性能，并消耗 Azure Cosmos DB 容器或共享数据库上预配的请求单位。

“Python”中的语法如下所示：
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

“Scala”中的等效语法如下所示：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>从容器加载流式处理数据帧
在此笔势中，你将使用 Spark 流式处理功能将容器中的数据加载到数据帧中。 数据将存储在连接到工作区的主数据湖帐户（和文件系统）中。 
> [!NOTE]
> 如果想在 Synapse Apache Spark 中引用外部库，请访问[此处](../spark/apache-spark-azure-portal-add-libraries.md)了解详细。 例如，如果想要将 Spark 数据帧引入到 Cosmos DB 的 API for Mongo DB 的容器中，可以在[此处](https://docs.mongodb.com/spark-connector/master/)使用用于 Spark 的 Mongo DB 连接器。

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>从 Azure Cosmos DB 容器加载流式处理数据帧
在此示例中，将使用 Spark 的结构化流式处理功能，通过 Azure Cosmos DB 中的更改源功能，将 Azure Cosmos DB 容器中的数据加载到 Spark 流式处理数据帧中。 Spark 使用的检查点数据将存储在连接到工作区的主数据湖帐户（和文件系统）中。

如果（在以下示例中）未创建文件夹 /localReadCheckpointFolder，则将自动创建该文件夹。 此操作将影响事务工作负载的性能，并消耗 Azure Cosmos DB 容器或共享数据库上预配的请求单位。

“Python”中的语法如下所示：
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

“Scala”中的等效语法如下所示：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>将流式处理数据帧写入 Azure Cosmos DB 容器
在此示例中，会将流式处理数据帧写入 Azure Cosmos DB 容器。 此操作将影响事务工作负载的性能，并消耗 Azure Cosmos DB 容器或共享数据库上预配的请求单位。 如果（在以下示例中）未创建文件夹 /localWriteCheckpointFolder，则将自动创建该文件夹。 

“Python”中的语法如下所示：

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

# If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "Gateway")\
        .start()

streamQuery.awaitTermination()
```

“Scala”中的等效语法如下所示：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

// If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "Gateway").
            start()

query.awaitTermination()
```


## <a name="next-steps"></a>后续步骤

* [Github 上有关 Azure Synapse Link 入门的示例](https://aka.ms/cosmosdb-synapselink-samples)
* [了解 Azure Synapse Link for Azure Cosmos DB 支持的功能](./concept-synapse-link-cosmos-db-support.md)
* [连接到 Synapse Link for Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* 查看 Learn 模块，了解如何[使用适用于 Azure Synapse Analytics 的 Apache Spark 查询 Azure Cosmos DB](/learn/modules/query-azure-cosmos-db-with-apache-spark-for-azure-synapse-analytics/)。
