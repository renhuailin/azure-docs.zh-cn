---
title: 用于 MongoDB 的 Azure Cosmos DB API 简介
description: 了解如何使用 Azure Cosmos DB，通过 Azure Cosmos DB 的用于 MongoDB 的 API 来存储和查询大量数据。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: bce5b5874b12638d1f2d628e23812676053006a4
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035195"
---
# <a name="azure-cosmos-db-api-for-mongodb"></a>用于 MongoDB 的 Azure Cosmos DB API
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

通过用于 MongoDB 的 Azure Cosmos DB API，可以轻松使用 Cosmos DB，就像它是 MongoDB 数据库一样。 可以利用 MongoDB 体验，并通过将应用程序指向用于 MongoDB 的 API 帐户的连接字符串，继续使用自己的常用 MongoDB 驱动程序、SDK 和工具。

## <a name="why-choose-the-api-for-mongodb"></a>为什么选择用于 MongoDB 的 API

与 MongoDB Atlas 等服务产品相比，用于 MongoDB 的 API 在 [Azure Cosmos DB](../introduction.md) 的基础构建了许多附加的优点：

* **即时可伸缩性**：通过启用 [自动缩放](../provision-throughput-autoscale.md)功能，数据库可以在无预热的情况下纵向扩展/缩减。
* **自动且透明的分片**：用于 MongoDB 的 API 会为你管理所有基础结构。 这包括分片和分片数，而不同于 MongoDB Atlas 等其他 MongoDB 产品/服务，后者在水平缩放时需要指定并管理分片。 这使你有更多的时间来专注于为用户开发应用程序。
* **“5 个 9”的可用性**：[99.999% 的可用性](../high-availability.md)可以轻松配置，确保数据始终可供你查看。  
* **经济高效、精细且无限制的可伸缩性**：与其他 MongoDB 服务产品不同，分片集合可以缩放到任何大小。 用于 MongoDB 的 API 的用户目前正在运行存储空间超过 600TB 的数据库。 缩放是以一种经济高效的方式完成的，因为与其他 MongoDB 服务产品不同，Cosmos DB 平台由于规模经济和资源治理，可以按小到 VM 的百分之一的增量进行缩放。
* **无服务器部署**：不同于 MongoDB Atlas，用于 MongoDB 的 API 是提供 [无服务器容量模式](../serverless.md)的云本机数据库。 对于[无服务器](../serverless.md)，只需按操作付费，而无需为不使用的数据库付费。
* **免费层**：使用 Azure Cosmos DB 免费层，你可以在帐户中永久获得每秒前 1000 RU 的免费吞吐量和 25 GB 的免费存储（适用于帐户级别）。
* **升级只需几秒钟时间**：所有 API 版本都包含在一个代码库中，这使更改版本就像 [拨动开关](upgrade-mongodb-version.md)一样简单，并且没有故障时间。
* **任意规模的实时分析 (HTAP)** ：用于 MongoDB 的 API 能够为商业智能等用例针对数据库数据实时运行复杂的分析查询，而不会影响数据库。 因为使用云本机分析列式存储，并且没有 ETL 管道，所以查询既快速又便宜。 详细了解 [Azure Synapse Link](../synapse-link.md)。

> [!NOTE]
> [你可以免费使用用于 MongoDB 的 Azure Cosmos DB API 的免费层！](../free-tier.md)。 使用 Azure Cosmos DB 免费层，你将在帐户中获得每秒前 1000 RU 的免费吞吐量和 25 GB 的免费存储（适用于帐户级别）。


## <a name="how-the-api-works"></a>API 的工作方式

用于 MongoDB 的 Azure Cosmos DB API 实现 MongoDB 线路协议。 此实现允许与本机 MongoDB 客户端 SDK、驱动程序和工具进行透明兼容。 Azure Cosmos DB 不托管 MongoDB 数据库引擎。 任何与所使用的 API 版本兼容的 MongoDB 客户端驱动程序都应能够进行连接，而无需特殊配置。

MongoDB 的功能兼容性：

用于 MongoDB 的 Azure Cosmos DB API 与以下 MongoDB 服务器版本兼容：
- [4.0 版](feature-support-40.md)
- [版本 3.6](feature-support-36.md)
- [版本 3.2](feature-support-32.md)

用于 MongoDB 的 API 的所有版本都在同一代码库上运行，这使得升级可以在几秒钟内完成，而不会造成停机。 Azure Cosmos DB 只需轻击一些功能标志，即可从一个版本切换到另一个版本。  功能标志还继续支持 3.2 和 3.6 等旧版 API。 你可以选择最适合自己的服务器版本。

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="Azure Cosmos DB 的 API for MongoDB" border="false":::

## <a name="what-you-need-to-know-to-get-started"></a>开始使用时需了解的内容

* 无需为群集中的虚拟机付费。 [定价](../how-pricing-works.md)基于在每个数据库或集合的基础上配置的吞吐量（以请求单位 (RU) 进行度量）。 每秒前 1000 个 RU 在[免费层](../free-tier.md)是免费的。

* 部署用于 MongoDB 的 Azure Cosmos DB API 的方式有以下三种：
     * [预配吞吐量](../set-throughput.md)：设置每秒钟 RU 的数量，并手动对其进行更改。 此模型最适合一致的工作负载。
     * [自动缩放]../(provision-throughput-autoscale.md)：设置所需吞吐量的上限。 吞吐量会即时缩放，以满足你的需求。 此模型最适合经常更改的工作负载，可以优化成本。
     * [无服务器](../serverless.md)：仅为某一时间段使用的吞吐量付费。 此模型最适合开发/测试工作负载。 

* 分片群集的性能取决于创建集合时选择的分片键。 请谨慎选择分片键，确保数据在分片之间均匀分布。

### <a name="capacity-planning"></a>容量计划

正在尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量规划。
* 若只知道现有数据库群集中的 vcore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
* 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](../estimate-ru-with-capacity-planner.md)

## <a name="quickstart"></a>快速入门

* [迁移现有的 MongoDB Node.js Web 应用](create-mongodb-nodejs.md)。
* [使用 Azure Cosmos DB 的用于 MongoDB 的 API 和 .NET SDK 生成 Web 应用](create-mongodb-dotnet.md)
* [使用 Azure Cosmos DB 的用于 MongoDB 的 API 和 Java SDK 生成控制台应用](create-mongodb-java.md)
* [使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
* [使用 Azure Cosmos DB 容量规划器估算请求单位](../estimate-ru-with-capacity-planner.md)

## <a name="next-steps"></a>后续步骤

* 正在尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量规划。
    * 若只知道现有数据库群集中的 vcore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
    * 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-capacity-planner.md)
* 在[将 MongoDB 应用程序连接到 Azure Cosmos DB](connect-mongodb-account.md) 教程中了解如何获取帐户连接字符串信息。
* 在[将 Studio 3T 与 Azure Cosmos DB 配合使用](connect-using-mongochef.md)教程中了解如何在 Studio 3T 中创建 Cosmos 数据库与 MongoDB 应用之间的连接。
* 在[将 MongoDB 数据导入 Azure Cosmos DB](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) 教程中了解如何将数据导入 Cosmos 数据库。
* 使用 [Robo 3T](connect-using-robomongo.md) 连接到 Cosmos 帐户。
* 了解如何[配置全局分布式应用的读取首选项](tutorial-global-distribution-mongodb.md)。
* 在[故障排除指南](error-codes-solutions.md)中查找常见错误的解决方案


<sup>注意：本文介绍了可与 MongoDB 数据库实现网络协议兼容的 Azure Cosmos DB 功能。Microsoft 不会运行 MongoDB 数据库来提供此服务。Azure Cosmos DB 并不隶属于 MongoDB, inc.</sup>