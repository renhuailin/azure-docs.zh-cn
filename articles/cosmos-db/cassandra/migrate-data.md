---
title: 将数据迁移到 Azure Cosmos DB 中的 Cassandra API 帐户 - 教程
description: 本教程介绍如何使将数据从 Apache Cassandra 复制到 Azure Cosmos DB 的 Cassandra API 帐户中。
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 937a42d6ebdf3d2ccb87451a2db07df199655005
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586772"
---
# <a name="tutorial-migrate-your-data-to-a-cassandra-api-account"></a>教程：将数据迁移到 Cassandra API 帐户
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

作为开发人员，你可能具有在本地或在云中运行的现有 Cassandra 工作负荷，并且你可能希望将其迁移到 Azure。 可以将这类工作负荷迁移到 Azure Cosmos DB 中的 Cassandra API 帐户。 本教程对可用来将 Apache Cassandra 数据迁移到 Azure Cosmos DB 中的 Cassandra API 帐户的各种选项进行了说明。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 规划迁移
> * 迁移的先决条件
> * 使用 `cqlsh` `COPY` 命令迁移数据
> * 使用 Spark 迁移数据

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites-for-migration"></a>迁移的先决条件

* **估计吞吐量需求：** 在将数据迁移到 Azure Cosmos DB 中的 Cassandra API 帐户之前，应当估计你的工作负荷的吞吐量需求。 通常，请从 CRUD 操作所需的平均吞吐量开始，然后包括提取转换负载或高峰操作所需的额外吞吐量。 需要以下详细信息来规划迁移： 

  * **现有数据大小或估计的数据大小：** 定义最小数据库大小和吞吐量要求。 如果正在估计新应用程序的数据大小，则可以假定数据均匀分布在各行中并通过乘以数据大小来估计值。 

  * 所需的吞吐量：大概的读取（查询/获取）和写入（更新/删除/插入）吞吐率。 需要使用此值来计算所需的请求单位以及状态稳定的数据大小。  

  * 架构：通过 `cqlsh` 连接到现有的 Cassandra 群集并从 Cassandra 中导出架构： 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    确定现有工作负载的需求后，应根据收集到的吞吐量需求创建一个 Azure Cosmos DB 帐户、数据库和容器。  

  * **确定操作的 RU 费用：** 可以使用 Cassandra API 支持的 任何 SDK 来确定 RU。 此示例演示获取 RU 费用的 .NET 版本。

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **分配所需的吞吐量：** 随着吞吐量需求的增长，Azure Cosmos DB 可以自动扩展存储和吞吐量。 可以使用 [Azure Cosmos DB 请求单位计算器](https://www.documentdb.com/capacityplanner)来估计吞吐量需求。 

* 在 Cassandra API 帐户中创建表：在开始迁移数据之前，从 Azure 门户或从 `cqlsh` 预创建所有表。 如果要迁移到具有数据库级别吞吐量的 Azure Cosmos DB 帐户，请确保在创建容器时提供分区键。

* **增加吞吐量：** 数据迁移的持续时间取决于为 Azure Cosmos DB 中的表预配的吞吐量。 在迁移期间增加吞吐量。 提高吞吐量后，可避免受到速率限制，并缩短迁移时间。 完成迁移后，减少吞吐量以节约成本。 此外，我们还建议在源数据库所在的同一区域中拥有 Azure Cosmos DB 帐户。 

* 启用 TLS：Azure Cosmos DB 具有严格的安全要求和标准。 请确保在与帐户进行交互时启用 TLS。 当你将 CQL 与 SSH 配合使用时，可以选择提供 TLS 信息。

## <a name="options-to-migrate-data"></a>迁移数据的选项

你可以使用 `cqlsh` `COPY` 命令或 Spark 将数据从现有的 Cassandra 工作负载移到 Azure Cosmos DB。 

### <a name="migrate-data-by-using-the-cqlsh-copy-command"></a>使用 cqlsh COPY 命令迁移数据

[CQL COPY 命令](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html#cqlshrc)用于将本地数据复制到 Azure Cosmos DB 中的 Cassandra API 帐户。

1. 为确保 csv 文件包含正确的文件结构，请使用 `COPY TO` 命令将数据直接从源 Cassandra 表导出到 csv 文件（确保 cqlsh 使用适当凭据连接到源表）：

   ```bash
   COPY exampleks.tablename TO 'data.csv' WITH HEADER = TRUE;   
   ```

1. 现在获取 Cassandra API 帐户的连接字符串信息：

   * 登录到 [Azure 门户](https://portal.azure.com)，并转到你的 Azure Cosmos DB 帐户。

   * 打开“连接字符串”窗格。 请单击此处查看从 `cqlsh` 连接到 Cassandra API 帐户所需的所有信息。

1. 使用门户中的连接信息登录到 `cqlsh`。

1. 使用 `CQL` `COPY FROM` 命令复制 `data.csv`（仍位于安装 `cqlsh` 时所用的用户根目录中）：

   ```bash
   COPY exampleks.tablename FROM 'data.csv' WITH HEADER = TRUE;
   ```



### <a name="migrate-data-by-using-spark"></a>使用 Spark 迁移数据 

通过以下步骤使用 Spark 将数据迁移到 Cassandra API 帐户：

1. 预配 [Azure Databricks 群集](spark-databricks.md)或 [Azure HDInsight 群集](spark-hdinsight.md)。 

1. 将数据移到目标 Cassandra API 终结点。 有关使用 Azure Databricks 进行迁移的信息，请参阅[此操作指南](migrate-data-databricks.md)。

如果有数据驻留在 Azure 虚拟机或任何其他云的现有群集中，则建议使用 Spark 作业迁移数据。 若要执行此操作，必须将 Spark 设置为用于一次性引入或常规引入的中介。 你可以通过使用本地环境和 Azure 之间的 Azure ExpressRoute 连接加速此迁移。 

## <a name="clean-up-resources"></a>清理资源

不再需要资源组、Azure Cosmos DB 帐户和所有相关的资源时，可将其删除。 为此，请选择虚拟机的资源组，选择“删除”，然后确认要删除的资源组的名称。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何将数据迁移到 Azure Cosmos DB 中的 Cassandra API 帐户。 你先可了解 Azure Cosmos DB 的其他概念：

> [!div class="nextstepaction"]
> [Azure Cosmos DB 中的可优化数据一致性级别](../consistency-levels.md)
