---
title: 使用 Databricks 和 Spark 从 MongoDB 迁移到适用于 MongoDB 的 Azure Cosmos DB API
description: 了解如何使用 Databricks Spark 将大型数据集从 MongoDB 实例迁移到 Azure Cosmos DB。
author: nayakshweta
ms.author: shwetn
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 3af5168a664fe18f9fc57877aee864f89ffa4d03
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029903"
---
# <a name="migrate-data-from-mongodb-to-an-azure-cosmos-db-api-for-mongodb-account-by-using-azure-databricks"></a>使用 Azure Databricks 将数据从 MongoDB 迁移到适用于 MongoDB 的 Azure Cosmos DB API 帐户
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

本迁移指南属于将数据库从 MongoDB 迁移到适用于 MongoDB 的 Azure CosmosDB API 系列。 关键的迁移步骤包括[迁移前步骤](pre-migration-steps.md)、迁移步骤和[迁移后步骤](post-migration-optimization.md)，如下所示。

:::image type="content" source="./media/pre-migration-steps/overall-migration-steps.png" alt-text="迁移步骤示意图":::


## <a name="data-migration-using-azure-databricks"></a>使用 Azure Databricks 进行数据迁移

[Azure Databricks](https://azure.microsoft.com/services/databricks/) 是一种适用于 [Apache Spark](https://spark.apache.org/) 的平台即服务 (PaaS) 产品/服务。 它提供一种对大规模数据集执行脱机迁移的方法。 可以使用 Azure Databricks 将数据库从 MongoDB 脱机迁移到适用于 MongoDB 的 Azure Cosmos DB API。

在本教程中，您将学习如何执行以下操作：

- 预配 Azure Databricks 群集

- 添加依赖项

- 创建和运行 Scala 或 Python 笔记本

- 优化迁移性能

- 对迁移过程中可能会看到的速率限制错误进行故障排除

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

- [完成迁移前步骤](pre-migration-steps.md)，例如估计吞吐量和选择分区键。
- [创建 Azure Cosmos DB 的用于 MongoDB 的 API 帐户](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)。

## <a name="provision-an-azure-databricks-cluster"></a>预配 Azure Databricks 群集

可以按说明来[预配 Azure Databricks 群集](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)。 建议选择支持 Spark 3.0 的 Databricks 运行时版本 7.6。

:::image type="content" source="./media/migrate-databricks/databricks-cluster-creation.png" alt-text="创建 Databricks 新群集的示意图。":::


## <a name="add-dependencies"></a>添加依赖项

将适用于 Spark 的 MongoDB 连接器库添加到你的群集，从而连接到本机 MongoDB 和适用于 MongoDB 的 Azure Cosmos DB API 终结点。 在群集中，选择“库” > “安装新库” > “Maven”，然后添加 `org.mongodb.spark:mongo-spark-connector_2.12:3.0.1` Maven 坐标  。

:::image type="content" source="./media/migrate-databricks/databricks-cluster-dependencies.png" alt-text="添加 Databricks 群集依赖项的示意图。":::


选择“安装”，然后在安装完成后重启群集。

> [!NOTE]
> 请确保在安装适用于 Spark 的 MongoDB 连接器库之后重启 Databricks 群集。

之后，你可以创建用于迁移的 Scala 或 Python 笔记本。


## <a name="create-scala-notebook-for-migration"></a>创建用于迁移的 Scala 笔记本

在 Databricks 中创建 Scala 笔记本。 运行以下代码之前，请确保为变量输入正确的值：


```scala
import com.mongodb.spark._
import com.mongodb.spark.config._
import org.apache.spark._
import org.apache.spark.sql._

var sourceConnectionString = "mongodb://<USERNAME>:<PASSWORD>@<HOST>:<PORT>/<AUTHDB>"
var sourceDb = "<DBNAME>"
var sourceCollection =  "<COLLECTIONNAME>"
var targetConnectionString = "mongodb://<ACCOUNTNAME>:<PASSWORD>@<ACCOUNTNAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@<ACCOUNTNAME>@"
var targetDb = "<DBNAME>"
var targetCollection =  "<COLLECTIONNAME>"

val readConfig = ReadConfig(Map(
  "spark.mongodb.input.uri" -> sourceConnectionString,
  "spark.mongodb.input.database" -> sourceDb,
  "spark.mongodb.input.collection" -> sourceCollection,
))

val writeConfig = WriteConfig(Map(
  "spark.mongodb.output.uri" -> targetConnectionString,
  "spark.mongodb.output.database" -> targetDb,
  "spark.mongodb.output.collection" -> targetCollection,
  "spark.mongodb.output.maxBatchSize" -> "8000"  
))

val sparkSession = SparkSession
  .builder()
  .appName("Data transfer using spark")
  .getOrCreate()

val customRdd = MongoSpark.load(sparkSession, readConfig)

MongoSpark.save(customRdd, writeConfig)
```

## <a name="create-python-notebook-for-migration"></a>创建用于迁移的 Python 笔记本

在 Databricks 中创建 Python 笔记本。 运行以下代码之前，请确保为变量输入正确的值：


```python
from pyspark.sql import SparkSession

sourceConnectionString = "mongodb://<USERNAME>:<PASSWORD>@<HOST>:<PORT>/<AUTHDB>"
sourceDb = "<DBNAME>"
sourceCollection =  "<COLLECTIONNAME>"
targetConnectionString = "mongodb://<ACCOUNTNAME>:<PASSWORD>@<ACCOUNTNAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@<ACCOUNTNAME>@"
targetDb = "<DBNAME>"
targetCollection =  "<COLLECTIONNAME>"

my_spark = SparkSession \
    .builder \
    .appName("myApp") \
    .getOrCreate()

df = my_spark.read.format("com.mongodb.spark.sql.DefaultSource").option("uri", sourceConnectionString).option("database", sourceDb).option("collection", sourceCollection).load()

df.write.format("mongo").mode("append").option("uri", targetConnectionString).option("maxBatchSize",2500).option("database", targetDb).option("collection", targetCollection).save()
```

## <a name="optimize-the-migration-performance"></a>优化迁移性能

可以通过以下配置调整迁移性能：

- **Spark 群集中的工作器和核心数**：更多的工作器意味着执行任务的计算节点更多。

- **maxBatchSize**：`maxBatchSize` 值控制将数据保存到目标 Azure Cosmos DB 集合中的速率。 但是，如果 maxBatchSize 对于集合吞吐量太高，则可能会导致[速率限制](prevent-rate-limiting-errors.md)错误。

  你需要根据 Spark 群集中的执行程序数量调整工作器的数量和 maxBatchSize 的值，这可能是写入的每个文档的大小（这就是 RU 成本高的原因），以及目标集合吞吐量限制。

  >[!TIP]
  >maxBatchSize = 集合吞吐量/（1 个文档的 RU 成本 \* Spark 工作器数量 \* 每个工作器的 CPU 核心数）

- **MongoDB Spark 分区程序和 partitionKey**：使用的默认分区程序为 MongoDefaultPartitioner，默认 partitionKey 为 _id。 可以通过将值 `MongoSamplePartitioner` 分配给输入配置属性 `spark.mongodb.input.partitioner` 来更改分区程序。 同样地，可以通过将相应的字段名称分配给输入配置属性 `spark.mongodb.input.partitioner.partitionKey` 来更改 partitionKey。 合适的 partitionKey 有助于避免数据倾斜（为同一分区键值写入的大量记录）。

- **在数据传输过程中禁用索引：** 对于大量数据迁移，请考虑禁用索引，特别是目标集合上的通配符索引。 索引会增加写入每个文档的 RU 成本。 释放这些 RU 有助于提高数据传输速率。 你可以在数据迁移完成后启用索引。



## <a name="troubleshoot"></a>疑难解答

### <a name="timeout-error-error-code-50"></a>超时错误（错误代码 50）
对于针对适用于 MongoDB 的 Cosmos DB API 数据库的操作，可能会看到 50 错误代码。 以下情况可能会导致超时错误：

- **分配给数据库的吞吐量较低**：确保为目标集合分配足够的吞吐量。
- 存在大量数据而导致过度数据倾斜。 如果有大量的数据要迁移到给定的表中，但存在重大的数据倾斜，那么即使在你的表中预配了多个[请求单位](../request-units.md)，你仍可能会遇到速率限制。 请求单位平均分配在物理分区中，重度的数据倾斜可能会导致对单个分片的请求出现瓶颈。 数据倾斜意味着相同的分区键值有大量的记录。

### <a name="rate-limiting-error-code-16500"></a>速率限制（错误代码 16500）

对于针对适用于 MongoDB 的 Cosmos DB API 数据库的操作，可能会看到 16500 错误代码。 这些是速率限制错误，可能会在较旧的帐户或禁用了服务器端重试功能的帐户上看到。
- **启用服务器端重试**：启用服务器端重试 (SSR) 功能，并让服务器自动重试速率受限的操作。



## <a name="post-migration-optimization"></a>迁移后优化

迁移数据后，你可以连接到 Azure Cosmos DB 并管理数据。 还可以执行其他迁移后步骤，例如优化索引策略、更新默认的一致性级别或者为你的 Azure Cosmos DB 帐户配置全球分发。 有关详细信息，请参阅[迁移后优化](post-migration-optimization.md)一文。

## <a name="additional-resources"></a>其他资源

* 正在尝试为迁移到 Azure Cosmos DB 进行容量计划？
    * 若只知道现有数据库群集中的 vcore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
    * 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-capacity-planner.md)

## <a name="next-steps"></a>后续步骤

* [管理 Azure Cosmos DB 的用于 MongoDB 的 API 中的索引编制](mongodb-indexing.md)
* [查找操作的请求单位费用](find-request-unit-charge-mongodb.md)
