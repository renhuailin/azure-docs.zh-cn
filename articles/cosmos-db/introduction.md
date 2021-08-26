---
title: Azure Cosmos DB 简介
description: 了解 Azure Cosmos DB。 此全球分布式多模型数据库是为了实现低延迟、弹性可伸缩性和高可用性而构建的，提供对 NoSQL 数据的本机支持。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 06/04/2021
ms.openlocfilehash: 6c55b2e5fbaadec239c1818d1bbe47443e797ddf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725459"
---
# <a name="welcome-to-azure-cosmos-db"></a>欢迎使用 Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

如今的应用程序需要具备高响应能力并始终联机。 若要实现低延迟和高可用性，需要在靠近用户的数据中心部署这些应用程序的实例。 应用程序需要实时响应高峰期的重大用量变化，存储不断增长的数据，并在数毫秒内向用户提供此数据。

Azure Cosmos DB 是一种用于新式应用开发的完全托管的 NoSQL 数据库。 个位数的毫秒响应时间以及自动和即时的可伸缩性，可保证任何规模的速度。 [SLA 支持](https://azure.microsoft.com/support/legal/sla/cosmos-db)的可用性和企业级安全性可确保业务连续性。 得益于全球各地的统包多区域数据分布、适用于常用语言的开源 API 和 SDK，应用的开发速度更快、效率更高。 作为一项完全托管的服务，Azure Cosmos DB 使用自动管理、更新和修补，使你无需进行数据库管理。 它还通过经济高效的无服务器和自动缩放选项处理容量管理，这些选项可响应应用程序的需求，使容量与需求相匹配。

可以在没有 Azure 订阅的情况下[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)（无需支付费用，也无需承诺），或者使用 [Azure Cosmos DB 免费层](free-tier.md)获取一个帐户，其中包含每秒前 1000 RU 的免费吞吐量和 25 GB 的免费存储。

> [!div class="nextstepaction"]
> [免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

> [!TIP]
> 若要详细了解 Azure Cosmos DB，请在每周四下午 1 点（太平洋时间）通过 Azure Cosmos DB Live TV 观看我们的播客。 请参阅[将来的研讨会安排以及过去的精彩内容](https://gotcosmos.com/tv)。

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB 是一种用于新式应用开发的完全托管的 NoSQL 数据库。" border="false":::

## <a name="key-benefits"></a>主要优势

### <a name="guaranteed-speed-at-any-scale"></a>保证任何规模的速度

获得无与伦比的 [SLA 支持](https://azure.microsoft.com/support/legal/sla/cosmos-db)的速度和吞吐量、快速的全局访问和即时弹性。

- [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) 对具有快速读写延迟的全局实时访问以及吞吐量和一致性提供支持
- 只需单击按钮，即可将多区域写入和数据分发到任何 Azure 区域。
- 在任何 Azure 区域独立且弹性地缩放存储和吞吐量（即使在不可预知的流量激增期间），以实现在全球范围内无限缩放。

### <a name="simplified-application-development"></a>简化应用程序开发

使用开源 API、多个 SDK、无架构数据和对操作数据的无 ETL 分析进行快速构建。

- 与新式（云原生）应用开发中使用的关键 Azure 服务深度集成，包括 Azure Functions、IoT 中心、AKS (Azure Kubernetes Service)、应用服务等。
- 从多个数据库 API 中进行选择，包括本机 Core (SQL) API、适用于 MongoDB 的 API、Cassandra API、Gremlin API 和表 API。
- 使用所选语言和适用于 .NET、Java、Node.js 和 Python 的 SDK，在 Core (SQL) API 上构建应用。 或者为任何其他数据库 API 选择的驱动程序。
- 使用 Azure Synapse Analytics 对 Azure Cosmos DB 中存储的近乎实时的操作数据运行无 ETL 分析。
- 通过更改源，可以轻松地跟踪和管理对数据库容器所做的更改以及使用 Azure Functions 创建触发事件。
- Azure Cosmos DB 的无架构服务会自动为所有数据（无论数据模型为何）编制索引，从而快速交付查询。

### <a name="mission-critical-ready"></a>关键任务就绪

保证每个应用程序的业务连续性、99.999% 的可用性和企业级安全性。

- Azure Cosmos DB 提供了全面的 [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) 套件，包括全球业界领先的可用性。
- 通过自动数据复制轻松地将数据分布到任何 Azure 区域。 使用强一致性时，多区域写入或 RPO 0 无需停机。
- 通过自行管理的密钥体验企业级静态加密。
- Azure 基于角色的访问控制可确保数据安全，并提供精细的控制。

### <a name="fully-managed-and-cost-effective"></a>完全托管和经济高效

端到端数据库管理，具有与应用程序和 TCO 需求相匹配的无服务器和自动缩放功能

- 完全托管的数据库服务。 自动、无需触控、维护、修补和更新，从而为开发人员了节省时间和资金。
- 适用于任何规模的不可预测或偶发性工作负载的经济高效选项，使开发人员无需计划或管理容量即可轻松入门。
- 无服务器模型提供尖峰工作负载自动响应服务，可按需管理流量激增。
- 自动缩放预配的吞吐量，可在保持 [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) 的同时自动并立即缩放容量以应对不可预测的工作负载。

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>受益于 Azure Cosmos DB 的解决方案

任何 [Web、移动、游戏和 IoT 应用程序](use-cases.md)，只要其需要处理大量数据和[全局规模](distribute-data-globally.md)的读写操作，各种数据的响应时间接近实时，就可以充分利用 Cosmos DB 所[保证的](https://azure.microsoft.com/support/legal/sla/cosmos-db/)高可用性、高吞吐量、低延迟以及可调的一致性。 了解如何将 Azure Cosmos DB 用于生成 [IoT 和 远程信息处理](use-cases.md#iot-and-telematics)、[零售和营销](use-cases.md#retail-and-marketing)、[游戏](use-cases.md#gaming)以及 [Web 和移动应用程序](use-cases.md#web-and-mobile-applications)。

## <a name="next-steps"></a>后续步骤

请通过阅读以下快速入门文章之一，来开始使用 Azure Cosmos DB：

- 了解在 Azure Cosmos DB 中[如何选择 API](choose-api.md)
- [Azure Cosmos DB SQL API 入门](create-sql-api-dotnet.md)
- [Azure Cosmos DB 的用于 MongoDB 的 API 入门](mongodb/create-mongodb-nodejs.md)
- [Azure Cosmos DB Cassandra API 入门](cassandra/manage-data-dotnet.md)
- [Azure Cosmos DB Gremlin API 入门](create-graph-dotnet.md)
- [Azure Cosmos DB 表 API 入门](table/create-table-dotnet.md)
- [有关 Azure Cosmos DB 的下一代应用开发的白皮书](https://azure.microsoft.com/resources/microsoft-azure-cosmos-db-flexible-reliable-cloud-nosql-at-any-scale/)

> [!div class="nextstepaction"]
> [免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
