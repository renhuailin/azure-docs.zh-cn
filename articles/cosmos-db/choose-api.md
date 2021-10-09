---
title: 在 Azure Cosmos DB 中选择 API
description: 了解如何根据你的工作负载需求，在 Azure Cosmos DB 中的 SQL/Core、MongoDB、Cassandra、Gremlin 和表 API 之间进行选择。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/12/2021
ms.openlocfilehash: 6d13f5f79da2906d1b994d4ab83e06d0593eaefe
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586628"
---
# <a name="choose-an-api-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中选择 API
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 是一种用于新式应用开发的完全托管的 NoSQL 数据库。 Azure Cosmos DB 提供自动管理、更新和修补，使你无需进行数据库管理。 它还通过经济高效的无服务器和自动缩放选项处理容量管理，这些选项可响应应用程序的需求，使容量与需求相匹配。

## <a name="apis-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 API

Azure Cosmos DB 提供多个数据库 API，包括 Core (SQL) API、MongoDB API、Cassandra API、Gremlin API 和表 API。 借助这些 API，可以使用文档、键-值、图和列系列数据模型来为真实数据建模。 应用程序可以通过这些 API 将 Azure Cosmos DB 视为如同其他各种数据库技术一样，并消除管理和缩放方法所产生的开销。 借助这些 API，Azure Cosmos DB 可帮助你使用过去你在数据建模和查询时所用的生态系统、工具与技能。

所有 API 都提供存储和吞吐量的自动缩放、灵活性及性能保证。 不存在最好的 API，你可以选择其中任何一个 API 来生成应用程序。 本文将帮助你根据工作负载和团队要求选择 API。

## <a name="considerations-when-choosing-an-api"></a>选择 API 时的注意事项

Core (SQL) API 已原生随附在 Azure Cosmos DB 中。

适用于 MongoDB、Cassandra、Gremlin 和表的 API 实现开源数据库引擎的线路协议。 如果满足以下条件，则这些 API 最适用：

* 如果你已有 MongoDB、Cassandra 或 Gremlin 应用程序。
* 如果你不想重写整个数据访问层。
* 如果你要为数据库使用开源开发人员生态系统、客户端驱动程序、专业知识和资源。
* 如果你需要使用 Azure Cosmos DB 的关键功能，比如全局分发、存储和吞吐量的弹性缩放、性能、低延迟、运行事务和分析工作负载的能力，以及使用一个完全托管的平台。
* 如果你要在多云环境中开发新式应用。

可以使用这些 API 生成新应用程序，或迁移现有数据。 若要运行迁移的应用，请更改应用程序的连接字符串，然后像往常一样继续运行即可。 迁移现有应用时，请确保评估这些 API 的功能支持。

必须根据工作负载选择符合你的要求的 API。 下图显示的流程图说明了在生成新应用或将现有应用迁移到 Azure Cosmos DB 时如何选择适当的 API：

:::image type="content" source="./media/choose-api/choose-api-decision-tree.png" alt-text="用于在 Azure Cosmos DB 中选择 API 的决策树。" lightbox="./media/choose-api/choose-api-decision-tree.png":::

## <a name="coresql-api"></a>Core (SQL) API

此 API 以文档格式存储数据。 它提供最佳的端到端体验，因为我们可以完全控制接口、服务和 SDK 客户端库。 为 Azure Cosmos DB 推出的任何新功能首先会在 SQL API 帐户中提供。 Azure Cosmos DB SQL API 帐户支持使用结构化查询语言 (SQL) 语法来查询项，这是用户最熟悉的且最流行的 JSON 对象查询语言之一。 有关详细信息，请参阅[Azure Cosmos DB SQL API](/learn/modules/intro-to-azure-cosmos-db-core-api/) learn 模块和 [SQL 查询入门](sql-query-getting-started.md)一文。

如果你要从 Oracle、DynamoDB、HBase 等其他数据库进行迁移，并想要使用现代化技术来生成应用，则 SQL API 是建议的选项。 SQL API 支持分析，并在操作工作负载与分析工作负载之间提供性能隔离。

### <a name="capacity-planning-for-migration-to-api-for-mongodb"></a>针对迁移到 API for MongoDB 的容量计划

尝试为从现有数据库群集迁移到 Azure Cosmos DB SQL API 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量规划。
* 如果只知道现有分片和复制数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md)。
* 如果你知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量规划工具估算请求单位](estimate-ru-with-capacity-planner.md)。

## <a name="api-for-mongodb"></a>适用于 MongoDB 的 API

此 API 通过 BSON 格式将数据存储在文档结构中。 它与 MongoDB 线路协议兼容；但是，它不使用任何本机 MongoDB 相关代码。 若要使用更广泛的 MongoDB 生态系统和技能，且不影响使用 Azure Cosmos DB 功能（例如缩放、高可用性、异地复制、多个写入位置、自动和透明分片管理、操作存储与分析存储之间的透明复制等），那么此 API 是一个很好的选择。

你只需要更改连接字符串，就可以将现有 MongoDB 应用与 MongoDB API 一起使用。 可以使用本机 MongoDB 工具（例如 mongodump 和 mongorestore）或 Azure 数据库迁移工具来移动任何现有数据。 MongoDB shell、[MongoDB Compass](mongodb/connect-using-compass.md) 和 [Robo3T](mongodb/connect-using-robomongo.md) 等工具可以像在本机 MongoDB 中那样运行查询和处理数据。

适用于 MongoDB 的 API 与 MongoDB 服务器版本 4.0、3.6 和 3.2 兼容。 建议使用服务器版本 4.0，因为它提供最佳性能和完整功能支持。 有关详细信息，请参阅[适用于 MongoDB 的 API](mongodb/mongodb-introduction.md) 一文。

### <a name="capacity-planning-for-migration-to-api-for-mongodb"></a>针对迁移到 API for MongoDB 的容量计划

尝试为从现有数据库群集迁移到 Azure Cosmos DB API for MongoDB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量规划。
* 如果只知道现有数据库群集中 vCore 和服务器的数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md)。
* 如果你知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量规划工具估算请求单位](./mongodb/estimate-ru-capacity-planner.md)。

## <a name="cassandra-api"></a>Cassandra API

此 API 在面向列的架构中存储数据。 Apache Cassandra 提供高度分布式的水平缩放方法来存储大量数据，并针对面向列的架构提供灵活的方法。 Azure Cosmos DB 中的 Cassandra API 符合这种与分布式 NoSQL 数据库接轨的理念。 Cassandra API 是与 Apache Cassandra 兼容的线路协议。 如果你希望利用 Azure Cosmos DB 的弹性和完全托管功能，并且仍使用 Apache Cassandra 的大部分原生功能、工具和生态系统，则应考虑 Cassandra API。 使用 Cassandra API，意味着无需管理 OS、Java VM、垃圾回收器、读/写性能、节点、群集等。

可以使用 Apache Cassandra 客户端驱动程序连接到 Cassandra API。 通过 Cassandra API，可以使用 Cassandra 查询语言 (CQL)、CQL shell 等工具和熟悉的 Cassandra 客户端驱动程序来与数据交互。 Cassandra API 目前仅支持 OLTP 方案。 借助 Cassandra API，还可以使用 Azure Cosmos DB 的独特功能，例如更改源。 有关详细信息，请参阅 [Cassandra API](cassandra-introduction.md) 一文。

## <a name="gremlin-api"></a>Gremlin API

此 API 可让用户发出图查询并将数据存储为边和顶点。 对于涉及动态数据、存在复杂关系的数据、过于复杂以致无法在关系数据库中建模的数据的方案，以及在你想要使用现有 Gremlin 生态系统和技能的情况下，请使用此 API。 Azure Cosmos DB 的 Gremlin API 将图数据库算法的强大功能与高度可缩放的托管基础结构结合在一起。 它针对与缺乏灵活性和关系型方法相关的最常见数据问题提供了独特且灵活的解决方法。 Gremlin API 目前仅支持 OLTP 方案。

Azure Cosmos DB 的 Gremlin API 基于 [Apache TinkerPop](https://tinkerpop.apache.org/) 图计算框架。 Gremlin API 使用同一图形查询语言来引入和查询数据。 它使用 Azure Cosmos DB 的分区策略从图形数据库引擎执行读/写操作。 Gremlin API 具有对开源 Gremlin 的线路协议支持，因此你可以使用开源 Gremlin SDK 来生成应用程序。 对于复杂的分析图方案，Azure Cosmos DB Gremlin API 还可与 Apache Spark 和 [GraphFrames](https://github.com/graphframes/graphframes) 配合工作。 有关详细信息，请参阅 [Gremlin API](graph-introduction.md) 一文。

## <a name="table-api"></a>表 API

此 API 以键/值格式存储数据。 如果你当前正在使用 Azure 表存储，可能会发现在延迟、缩放、吞吐量、全局分发、索引管理、低查询性能等方面存在一些限制。 表 API 克服了这些限制，如果你要利用 Azure Cosmos DB 的功能，建议迁移应用。 表 API 仅支持 OLTP 场景。

为 Azure 表存储编写的应用程序只需经过少量的代码更改即可迁移到表 API，然后便可利用高级功能。 有关详细信息，请参阅[表 API](table/introduction.md) 一文。

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB SQL API 入门](create-sql-api-dotnet.md)
* [Azure Cosmos DB 的用于 MongoDB 的 API 入门](mongodb/create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API 入门](cassandra/manage-data-dotnet.md)
* [Azure Cosmos DB Gremlin API 入门](create-graph-dotnet.md)
* [Azure Cosmos DB 表 API 入门](create-table-dotnet.md)
* 尝试为迁移到 Azure Cosmos DB 进行容量规划？ 可以使用有关现有数据库群集的信息进行容量规划。
    * 若只知道现有数据库群集中的 vcore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md) 
    * 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)
