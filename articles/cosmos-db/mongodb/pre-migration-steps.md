---
title: 将数据迁移到 Azure Cosmos DB's API for MongoDB 的迁移前步骤
description: 本文档概述将数据从 MongoDB 迁移到 Cosmos DB 的先决条件。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 05/17/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: c871dbdc8d668b7f4e6f9c84d7b94e292d7f8d00
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777874"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>将数据从 MongoDB 迁移到 Azure Cosmos DB's API for MongoDB 的迁移前步骤
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> 在执行迁移前步骤之前，请完整阅读本指南。
>

本 MongoDB 迁移前指南是 MongoDB 迁移系列教程的一部分。 关键的 MongoDB 迁移步骤包括迁移前步骤、迁移步骤和[迁移后步骤](post-migration-optimization.md)，如下所示。

![迁移步骤示意图。](./media/pre-migration-steps/overall-migration-steps.png)

## <a name="overview-of-pre-migration"></a>迁移前步骤概述

在实际迁移任何数据之前，提前做出特定的迁移规划和决策至关重要。 此初始决策过程称作“迁移前步骤”。 迁移前步骤的目标是 (1) 确保根据应用程序的迁移后要求设置 Azure Cosmos DB；(2) 规划如何执行迁移。

按如下所述执行全面的迁移前步骤
* [发现现有的 MongoDB 资源并创建一个项目来跟踪这些资源](#pre-migration-discovery)
* [评估现有的 MongoDB 资源是否已做好数据迁移的准备](#pre-migration-assessment)
* [将现有的 MongoDB 资源映射到新的 Azure Cosmos DB 资源](#pre-migration-mapping)
* [在开始执行全规模的数据迁移之前规划迁移过程的端到端后勤工作](#execution-logistics)

然后，根据迁移前计划执行迁移。

最后，执行关键的[迁移后切换和优化步骤](post-migration-optimization.md)。

上述所有步骤对于确保迁移成功都至关重要。

规划迁移时，建议尽可能地在每个资源的级别进行规划。

## <a name="pre-migration-discovery"></a>迁移前的发现

第一个迁移前步骤是资源发现。 此步骤将尝试创建 MongoDB 数据资产中现有资源的综合列表。

### <a name="create-a-data-estate-migration-spreadsheet"></a>创建数据资产迁移电子表格

使用首选的生产力软件，创建一个数据资产迁移电子表格作为迁移跟踪文档。 
   * 此电子表格旨在提高工作效率，并帮助你端到端规划迁移。
   * 此电子表格的结构由你决定。 以下要点提供了一些建议。
   * 此电子表格应采用列表形式的结构，其中记录了你的数据资产资源。
   * 每行对应于一个资源（数据库或集合）。
   * 每列对应于该资源的一个属性；目前，至少应包含名称和数据大小 (GB) 作为列，但理想情况下，还可以收集有关每个资源的 MongoDB 版本的信息，在这种情况下，还可以添加 Mongo 版本列  。 
   * 最初，你将在此电子表格中填写 MongoDB 数据资产中现有资源的列表。 在学习本指南的过程中，你会不断将此电子表格制作成一个用于端到端迁移规划的跟踪文档，并根据需要添加列。

### <a name="discover-existing-mongodb-data-estate-resources"></a>发现现有的 MongoDB 数据资产资源

使用适当的发现工具识别现有 MongoDB 数据资产中的资源（数据库或集合），并尽量全面发现。 

下面是可用于发现资源的一些工具：
   * [MongoDB Shell](https://www.mongodb.com/try/download/shell)
   * [MongoDB Compass](https://www.mongodb.com/try/download/compass)

## <a name="pre-migration-assessment"></a>迁移前的评估

其次，作为规划迁移的前奏步骤，应该评估数据资产中每个资源是否已做好迁移的准备。 

影响就绪性的主要因素是 MongoDB 版本。 Azure Cosmos DB 目前支持 MongoDB 二进制协议版本 3.2、3.6 和 4.0。 最好是在迁移规划电子表格中包含一个 MongoDB 版本列。 执行电子表格中的每个步骤，并突出显示对 Azure Cosmos DB 使用了不兼容 MongoDB 版本的任何资源。

## <a name="pre-migration-mapping"></a>迁移前的映射

发现和评估步骤完成后，方程的 MongoDB 一端的准备工作现已完成。 现在可以规划方程的 Azure Cosmos DB 一端。 如何设置和配置生产 Azure Cosmos DB 资源？ 在每个资源的级别进行规划 – 这意味着，应将以下各列添加到规划电子表格中： 
* Azure Cosmos DB 映射 
* 分片键 
* 数据模型
* 使用专用吞吐量还是共享吞吐量

以下各部分提供了更多详细信息。

### <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB’s API for MongoDB 时的注意事项

在规划 Azure Cosmos DB 数据资产之前，请确保了解以下 Azure Cosmos DB 概念：

- **容量模型**：Azure Cosmos DB 上的数据库容量基于吞吐量模型。 此模型基于[每秒请求单位数](../request-units.md)，此单位表示每秒可对集合执行的数据库操作次数。 可以在[数据库或集合级别](../set-throughput.md)分配此容量，也可以在分配模型中进行预配，或者使用[自动缩放预配的吞吐量](../provision-throughput-autoscale.md)。

- **请求单位**：在 Azure Cosmos DB 中，每个数据库操作都有关联的请求单位 (RU) 成本。 执行操作时，将从在给定的秒可用的请求单位级别中减去此成本。 如果请求所需的 RU 数超过了当前分配的每秒 RU 数，可以使用两个选项来解决此问题 - 增加 RU 数量，或等待下一秒开始，然后重试操作。

- **弹性容量**：给定集合或数据库的容量随时可以更改。 这样，数据库就能弹性适应工作负荷的吞吐量要求。

- **自动分片**：Azure Cosmos DB 提供一个仅需要分片（或分区键）的自动分区系统。 [自动分区机制](../partitioning-overview.md)在所有 Azure Cosmos DB API 之间共享，允许通过水平分配进行无缝的数据缩放和全面缩放。

### <a name="plan-the-azure-cosmos-db-data-estate"></a>规划 Azure Cosmos DB 数据资产

确定要创建哪些 Azure Cosmos DB 资源。 这意味着需要执行数据资产迁移电子表格中的每个步骤，并将每个现有 MongoDB 资源映射到新的 Azure Cosmos DB 资源。 
* 预计每个 MongoDB 数据库将成为 Azure Cosmos DB 数据库
* 预计每个 MongoDB 集合将成为 Azure Cosmos DB 集合
* 选择 Azure Cosmos DB 资源的命名约定。 阻止对数据库和集合的结构进行任何更改并保留相同的资源名称通常是很好的做法。
* 在 MongoDB 中，分片集合是可选的。 在 Azure Cosmos DB 中，每个集合都是分片的。
* 不要假设 MongoDB 集合分片键将成为 Azure Cosmos DB 集合分片键。不要假设现有的 MongoDB 数据模型/文档结构就是要在 Azure Cosmos DB 中采用的模型/结构。 
   * 分片键是用于优化 Azure Cosmos DB 可伸缩性和性能的唯一最重要设置，数据建模是第二重要的设置。 这两个设置都是不可变的，且在设置后不可更改；因此，在规划阶段对其进行优化极其重要。 有关详细信息，请参阅[不可变决策](#immutable-decisions)部分中的指导。
* Azure Cosmos DB 无法识别某些 MongoDB 集合类型，例如有上限的集合。 对于这些资源，只需创建常规的 Azure Cosmos DB 集合即可。
* Azure Cosmos DB 本身有两种集合类型 - 共享吞吐量和专用吞吐量。 使用共享吞吐量还是专用吞吐量是另一个关键的不可变决策，必须在规划阶段做出该决策。 有关详细信息，请参阅[不可变决策](#immutable-decisions)部分中的指导。

### <a name="immutable-decisions"></a>不可变决策

创建 Azure Cosmos DB 资源后无法修改或撤消以下 Azure Cosmos DB 配置选择；因此，在开始执行任何迁移之前，必须在迁移前规划期间正确做出这些选择：
* 按照[此指南](../partitioning-overview.md)选择最佳分片键。 分区（也称为分片）是迁移数据之前要考虑的一个要点。 Azure Cosmos DB 使用完全托管的分区来提高数据库中的容量，以满足存储和吞吐量要求。 此功能不需要托管或配置路由服务器。   
   * 分区功能以类似方式自动增加容量，并相应地重新均衡数据。 有关为数据选择适当分区键的详细信息和建议，请参阅[选择分区键](../partitioning-overview.md#choose-partitionkey)一文。 
* 按照[此指南](../modeling-data.md)选择数据模型
* 按照[此指南](../optimize-cost-throughput.md#optimize-by-provisioning-throughput-at-different-levels)为要迁移的每个资源选择专用吞吐量或共享吞吐量
* [此处](../how-to-model-partition-example.md)提供了真实的分片和数据建模示例用于帮助你完成决策过程

### <a name="cost-of-ownership"></a>拥有成本

* 使用 [Azure Cosmos DB 容量计算器](https://cosmos.azure.com/capacitycalculator/)估算新 Azure Cosmos DB 资源的拥有成本。

### <a name="estimating-throughput"></a>估算吞吐量

* 在 Azure Cosmos DB 中，吞吐量是提前预配的，按每秒请求单位 (RU) 数计量。 不同于 VM 或本地服务器，RU 随时可以轻松纵向扩展和缩减。 可以即时更改预配的 RU 数。 有关详细信息，请参阅 [Azure Cosmos DB 中的请求单位](../request-units.md)。

* 可以使用 [Azure Cosmos DB 容量计算器](https://cosmos.azure.com/capacitycalculator/)根据数据库帐户配置、数据量、文档大小以及每秒所需的读取和写入次数，来确定请求单位数量。

* 下面是影响所需 RU 数的关键因素：
   * **文档大小**：随着项/文档大小的增大，读取或写入该项/文档所要消耗的 RU 数也会增加。

   * **文档属性计数**：创建或更新文档所消耗的 RU 数与该文档的属性数目、复杂性和长度相关。 可以通过[限制已编制索引的属性数目](mongodb-indexing.md)，来减少写入操作的请求单位消耗量。

   * **查询模式**：查询的复杂性会影响查询消耗的请求单位数。 

* 了解查询成本的最佳方式是使用 Azure Cosmos DB 中的示例数据，[并在 MongoDB Shell 中使用 `getLastRequestStastistics` 命令运行示例查询](connect-mongodb-account.md)以获取请求开销，此命令将输出消耗的 RU 数：

    `db.runCommand({getLastRequestStatistics: 1})`

    此命令将输出类似于以下内容的 JSON 文档：

    ```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

* 也可以使用[诊断设置](../cosmosdb-monitor-resource-logs.md)来了解针对 Azure Cosmos DB 执行的查询的频率和模式。 可将诊断日志中的结果发送到存储帐户、事件中心实例或 [Azure Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)。  

## <a name="pre-migration-logistics-planning"></a>迁移前的后勤规划

最后，在获得现有数据资产的视图和新 Azure Cosmos DB 数据资产的设计后，接下来可以规划如何端到端执行迁移过程。 同样，应在每个资源的级别进行规划，在电子表格中添加列以捕获以下后勤维度。

### <a name="execution-logistics"></a>执行后勤
* 分配将每个现有资源从 MongoDB 迁移到 Azure Cosmos DB 的责任。 由你决定如何利用团队资源来引导迁移的完成。 对于小规模迁移，可以让一个团队开始整个迁移并监视其进度。 对于较大规模的迁移，可以按资源为团队成员分配迁移和监视该资源的责任。
* 分配迁移资源的责任后，现在应选择适当的迁移工具进行迁移。 对于小规模迁移，你可以使用一种迁移工具（例如 MongoDB 本机工具或 Azure DMS）一次性迁移所有资源。 对于较大规模的迁移或具有特殊要求的迁移，可以按每个资源的粒度选择迁移工具。
   * 在规划要使用的迁移工具之前，建议熟悉可用的选项。 [适用于 Azure Cosmos DB’s API for MongoDB 的 Azure 数据库迁移服务](../../dms/tutorial-mongodb-cosmos-db.md)提供一个机制来简化数据迁移。该机制提供完全托管的主机平台、迁移监视选项和自动限制处理。 下面是完整的选项列表：

   |**迁移类型**|**解决方案**|**注意事项**|
   |---------|---------|---------|
   |联机|[Azure 数据库迁移服务](../../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; 利用 Azure Cosmos DB 批量执行程序库 <br/>&bull; 适合用于大型数据集，负责复制实时更改 <br/>&bull; 仅适用于其他 MongoDB 源|
   |Offline|[Azure 数据库迁移服务](../../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; 利用 Azure Cosmos DB 批量执行程序库 <br/>&bull; 适合用于大型数据集，负责复制实时更改 <br/>&bull; 仅适用于其他 MongoDB 源|
   |Offline|[Azure 数据工厂](../../data-factory/connector-azure-cosmos-db.md)|&bull; 易于设置且支持多个源 <br/>&bull; 利用 Azure Cosmos DB 批量执行程序库 <br/>&bull; 适合用于大型数据集 <br/>&bull; 缺少检查点，这意味着，在迁移过程中出现任何问题都需要重启整个迁移过程<br/>&bull; 缺少死信队列，这意味着，出现几个有错误的文件就可能会停止整个迁移过程。 <br/>&bull; 需要编写自定义代码来增大某些数据源的读取吞吐量|
   |脱机|[现有的 Mongo 工具（mongodump、mongorestore、Studio3T）](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; 易于设置和集成 <br/>&bull; 需要对限制进行自定义处理|
    
   * 如果资源能够容许脱机迁移，请使用下图选择适当的迁移工具：

   ![脱机迁移工具。](./media/pre-migration-steps/offline-tools.png)

   * 如果资源要求联机迁移，请使用下图选择适当的迁移工具：

   ![联机迁移工具。](./media/pre-migration-steps/online-tools.png)
   
   观看此视频，了解上面提到的[迁移解决方案的概述和演示](https://www.youtube.com/watch?v=WN9h80P4QJM)。

* 为每个资源选择迁移工具后，下一步是确定要迁移的资源的优先级。 合理的优先级有助于使迁移保持符合计划。 良好的做法是优先迁移那些需要花费大部分时间来移动的资源；先迁移这些资源可以最好地保持完成进度。 此外，由于这些耗时的迁移通常涉及更多的数据，运行迁移工具时它们消耗的资源更多，因此它们更有可能在迁移管道的早期阶段暴露出任何问题。 这可以最大程度地减少由于迁移管道中出现的任何问题而导致计划改变的可能性。
* 迁移开始后，规划如何监视迁移进度。 如果你要在团队中协调数据迁移工作，请规划例行团队同步的间隔，以全面了解高优先级迁移的进展情况。
  

### <a name="supported-migration-scenarios"></a>支持的迁移方案

最佳的 MongoDB 迁移工具取决于迁移方案。 

#### <a name="types-of-migrations"></a>迁移的类型

下面显示了每种迁移方案的兼容工具：

![支持的迁移方案。](./media/pre-migration-steps/migration-tools-use-case-table.png)

#### <a name="tooling-support-for-mongodb-versions"></a>各个 MongoDB 版本支持的工具

假设你要从特定的 MongoDB 版本迁移，支持的工具如下：

![迁移工具支持的 MongoDB 版本。](./media/pre-migration-steps/migration-tool-compatibility.png)

### <a name="post-migration"></a>迁移后

在迁移前阶段，请花些时间来规划在迁移后，要执行哪些步骤进行应用迁移和优化。
* 在迁移后阶段，将执行应用程序的切换，以使用 Azure Cosmos DB 而不是现有的 MongoDB 数据资产。 
* 尽最大努力在每个资源的级别规划索引编制、全局分发、一致性和其他可变 Azure Cosmos DB 属性 - 但是，以后可以修改这些 Azure Cosmos DB 配置设置，因此今后调整这些设置是没有问题的 。 请不要让这些方面成为分析停顿的原因。 迁移后将应用这些可变配置。
* 在[此处](post-migration-optimization.md)可以找到最有参考性的迁移后指南。

## <a name="next-steps"></a>后续步骤
* [使用数据库迁移服务将 MongoDB 数据迁移到 Cosmos DB](../../dms/tutorial-mongodb-cosmos-db.md) 
* [对 Azure Cosmos 容器和数据库预配吞吐量](../set-throughput.md)
* [Azure Cosmos DB 中的分区](../partitioning-overview.md)
* [Azure Cosmos DB 中的全局分布](../distribute-data-globally.md)
* [Azure Cosmos DB 中的索引](../index-overview.md)
* [Azure Cosmos DB 中的请求单位](../request-units.md)
