---
title: 在 Azure Cosmos DB 中选择 API
description: 了解如何根据工作负载要求在 Azure Cosmos DB 的 SQL/Core、MongoDB、Cassandra、Gremlin 和表 API 之间进行选择。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2021
ms.openlocfilehash: a4873662578dee69d4f7fe16618101a732129f58
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111561602"
---
# <a name="choose-an-api-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中选择 API

Azure Cosmos DB 是一种用于新式应用开发的完全托管的 NoSQL 数据库。 Azure Cosmos DB 使用自动管理、更新和修补，使你无需进行数据库管理。 它还通过经济高效的无服务器和自动缩放选项处理容量管理，这些选项可响应应用程序的需求，使容量与需求相匹配。

## <a name="apis-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 API

Azure Cosmos DB 提供了多个数据库 API，包括 Core (SQL) API、适用于 MongoDB 的 API、Cassandra API、Gremlin API 和表 API。 通过使用这些 API，可以使用文档、键值、图形和列系列数据模型对实际数据进行建模。 这些 API 允许应用程序像对待其他各种数据库技术一样对待 Azure Cosmos DB，而没有管理开销和缩放方法。 使用这些 API，Azure Cosmos DB 可帮助你使用已有的生态系统、工具和技能进行数据建模和查询。

所有 API 都提供存储和吞吐量的自动缩放、灵活性和性能保证。 没有一个最好的 API，你可以选择任一 API 来生成应用程序。 本文将帮助你根据工作负载和团队要求选择 API。

## <a name="considerations-when-choosing-an-api"></a>选择 API 时的注意事项

Core(SQL) API 是 Azure Cosmos DB 的原生 API。

MongoDB、Cassandra、Gremlin 和表 API 实现开源数据库引擎的线路协议。 如果满足以下条件，则这些 API 最适合：

* 如果你已有 MongoDB、Cassandra 或 Gremlin 应用程序。
* 如果你不想重写整个数据访问层。
* 如果你要为数据库使用开源开发人员生态系统、客户端驱动程序、专业知识和资源。
* 如果你需要使用 Azure Cosmos DB 的关键功能，比如全局分发、存储和吞吐量的弹性缩放、性能、低延迟、运行事务和分析工作负载的能力，以及使用一个完全托管的平台。
* 如果你要在多云环境中开发新式应用。

可以使用这些 API 生成新应用程序，或迁移现有数据。 若要运行迁移的应用，请更改应用程序的连接字符串并继续像以前那样运行。 迁移现有应用时，请确保评估这些 API 的功能支持。

根据工作负载，必须选择符合要求的 API。 下图显示了一个流程图，说明在生成新应用或将现有应用迁移到 Azure Cosmos DB 时如何选择正确的 API：

:::image type="content" source="./media/choose-api/choose-api-decision-tree.png" alt-text="用于在 Azure Cosmos DB 中选择 API 的决策树。" lightbox="./media/choose-api/choose-api-decision-tree.png":::

## <a name="coresql-api"></a>Core(SQL) API

此 API 以文档格式存储数据。 它提供最佳的端到端体验，因为我们可以完全控制接口、服务和 SDK 客户端库。 向 Azure Cosmos DB 推出的任何新功能都会首先在 SQL API 帐户中提供。 Azure Cosmos DB SQL API 帐户支持使用结构化查询语言 (SQL) 语法来查询项，该语法是查询 JSON 对象的最熟悉和最常用的查询语言之一。 有关详细信息，请参阅 [SQL 查询入门](sql-query-getting-started.md)。

如果你要从 Oracle、DynamoDB、HBase 等其他数据库迁移，并且要使用现代化技术生成应用，则建议使用 SQL API。 SQL API 支持分析，并提供操作工作负载和分析工作负载之间的性能隔离。

## <a name="api-for-mongodb"></a>适用于 MongoDB 的 API

此 API 通过 BSON 格式将数据存储在文档结构中。 它与 MongoDB 线路协议兼容；但是，它不使用任何本机 MongoDB 相关代码。 若要使用更广泛的 MongoDB 生态系统和技能，且不影响使用 Azure Cosmos DB 功能（例如缩放、高可用性、异地复制、多个写入位置、自动和透明分片管理、操作存储与分析存储之间的透明复制等），那么此 API 是一个很好的选择。

你只需要更改连接字符串，就可以将现有 MongoDB 应用与 MongoDB API 一起使用。 你可以使用本机 MongoDB 工具（例如 mongodump 和 mongorestore）或使用 Azure 数据库迁移工具迁移任何现有数据。 MongoDB shell、[MongoDB Compass](mongodb-compass.md) 和 [Robo3T](mongodb-robomongo.md) 等工具可以像使用原生 MongoDB 那样运行查询和处理数据。

Azure Cosmos DB API 与 4.0、3.6 和 3.2 MongoDB 服务器版本兼容。 建议使用服务器版本 4.0，因为它可以提供最佳性能和完整功能支持。 有关详细信息，请参阅 [MongoDB API](mongodb-introduction.md) 一文。

## <a name="cassandra-api"></a>Cassandra API

此 API 将数据存储在面向列的架构中。 Apache Cassandra 提供了一种高度分布式水平缩放方法，用于存储大量数据，同时为面向列的架构提供了一种灵活的方法。 Azure Cosmos DB 中的 Cassandra API 与这种接近分布式 NoSQL 数据库的理念一致。 Cassandra API 是与 Apache Cassandra 兼容的线路协议。 如果你希望利用 Azure Cosmos DB 的弹性和完全托管功能，并且仍使用 Apache Cassandra 的大部分原生功能、工具和生态系统，则应考虑 Cassandra API。 使用 Cassandra API，意味着无需管理 OS、Java VM、垃圾回收器、读/写性能、节点、群集等。

可以使用 Apache Cassandra 客户端驱动程序连接到 Cassandra API。 通过 Cassandra API，可以使用 Cassandra 查询语言 (CQL) 和你已经熟悉的 CQL shell、Cassandra 客户端驱动程序等工具与数据进行交互。 Cassandra API 目前仅支持 OLTP 场景。 使用 Cassandra API，你还可以使用 Azure Cosmos DB 的独特功能，如更改源。 有关详细信息，请参阅 [Cassandra API](cassandra-introduction.md) 一文。

## <a name="gremlin-api"></a>Gremlin API

使用此 API，用户可以进行图形查询，将数据存储为边缘和顶点。 对于涉及动态数据、具有复杂关系的数据、过于复杂而无法通过关系数据库进行建模的数据，以及你想要使用现有 Gremlin 生态系统和技能的场景，请使用此 API。 Azure Cosmos DB 的 Gremlin API 将图形数据库算法的强大功能与高度可缩放的托管基础结构相结合。 它为与缺乏灵活性和关系方法相关的最常见数据问题提供了独特的灵活解决方案。 Gremlin API 目前仅支持 OLTP 场景。

Azure Cosmos DB 的 Gremlin API 是基于 [Apache TinkerPop](https://tinkerpop.apache.org/) 图形计算框架的。 Gremlin API 使用同一图形查询语言来引入和查询数据。 它使用 Azure Cosmos DB 的分区策略从图形数据库引擎执行读/写操作。 Gremlin API 具有对开源 Gremlin 的线路协议支持，因此你可以使用开源 Gremlin SDK 来生成应用程序。 Azure Cosmos DB Gremlin API 还可以与 Apache Spark 和 [GraphFrames](https://github.com/graphframes/graphframes) 一起用于复杂的分析图形场景。 有关详细信息，请参阅 [Gremlin API](graph-introduction.md) 一文。

## <a name="table-api"></a>表 API

此 API 以键/值格式存储数据。 如果你当前正在使用 Azure 表存储，可能会发现在延迟、缩放、吞吐量、全局分发、索引管理、低查询性能等方面存在一些限制。 表 API 克服了这些限制，如果你要利用 Azure Cosmos DB 的功能，建议迁移应用。 表 API 仅支持 OLTP 场景。

为 Azure 表存储编写的应用程序只需更改少量代码便可迁移到表 API，并可充分利用高级功能。 有关详细信息，请参阅[表 API](table-introduction.md) 一文。

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB SQL API 入门](create-sql-api-dotnet.md)
* [Azure Cosmos DB 的用于 MongoDB 的 API 入门](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API 入门](create-cassandra-dotnet.md)
* [Azure Cosmos DB Gremlin API 入门](create-graph-dotnet.md)
* [Azure Cosmos DB 表 API 入门](create-table-dotnet.md)