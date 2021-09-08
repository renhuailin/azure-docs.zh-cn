---
title: Azure Cosmos DB 中的开发和测试优化
description: 本文介绍 Azure Cosmos DB 为服务的免费开发和测试提供多个选项的情况。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 60f586e3f2ead0bde743f75d7810bf2d43c737af
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123028587"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中优化开发和测试成本
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

本文介绍免费使用 Azure Cosmos DB 进行开发和测试的各种选项，以及在开发或测试帐户中进行成本优化的各种技巧。

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB 模拟器（可以本地下载的版本）

[Azure Cosmos DB 模拟器](local-emulator.md)是一个本地的可下载版本，模拟 Azure Cosmos DB 云服务。 即使没有网络连接，也可以编写并测试使用 Azure Cosmos DB API 的代码，不需支付任何费用。 Azure Cosmos DB 模拟器提供了一个用于开发的具有云服务高保真特性的本地环境。 可以在本地开发和测试应用程序，无需创建 Azure 订阅。 做好将应用程序部署到云的准备以后，即可更新连接到云中的 Azure Cosmos DB 终结点所需的连接字符串，不需进行其他的修改。 也可在 Azure DevOps 中[通过 Azure Cosmos DB 模拟器生成任务设置 CI/CD 管道](tutorial-setup-ci-cd.md)，以便运行测试。 若要开始操作，可以参阅 [Azure Cosmos DB 模拟器](local-emulator.md)一文。

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB 免费层

使用 Azure Cosmos DB 免费层，可以轻松上手、开发和测试应用程序，甚至免费运行小型生产工作负载。 在帐户上启用免费层后，一开始你将在该帐户中免费获得 1000 RU/s 的吞吐量和 25 GB 的存储空间。

免费层在帐户的生命周期内无限期地持续有效，并且附带常规 Azure Cosmos DB 帐户的所有[优点和功能](introduction.md#key-benefits)，包括无限制的存储和吞吐量（RU/秒）、SLA、高可用性以及在所有 Azure 区域进行统包式全局分布等。 你可以使用 Azure 门户、CLI、PowerShell 和资源管理器模板来创建免费层帐户。 若要了解详细信息，请参阅如何[创建免费层帐户](free-tier.md)一文和[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。

## <a name="try-azure-cosmos-db-for-free"></a>免费试用 Azure Cosmos DB

[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 是一种免费体验，使你无需注册 Azure 帐户或使用信用卡即可在云中试用 Azure Cosmos DB。 Azure Cosmos DB 试用帐户只能使用有限的一段时间，目前为 30 天。 可以随时续订。 通过 Azure Cosmos DB 试用帐户，可以轻松地评估 Azure Cosmos DB，生成和测试应用程序，或者使用快速入门或教程。 你还可以创建演示、执行单元测试，甚至创建多区域帐户并在其上运行应用，而无需支付任何费用。 在 Azure Cosmos DB 试用帐户中，你可以有一个具有最多 25 个容器和 20,000 RU/秒吞吐量的共享吞吐量数据库，或一个具有最多 5000 RU/秒的容器。 若要开始操作，请参阅[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 页。

## <a name="azure-free-account"></a>Azure 免费帐户

Azure Cosmos DB 包含在 [Azure 免费帐户](https://azure.microsoft.com/free)中，该帐户提供 Azure 信用额度和资源，可以免费使用特定的一段时间。 特别对于 Azure Cosmos DB，此免费帐户全年提供 25 GB 的存储和 400 RU 的预配吞吐量。 任何开发人员都可以通过此体验轻松地测试 Azure Cosmos DB 的功能，或者将其与其他 Azure 服务集成，没有任何费用。 Azure 免费帐户提供 200 USD 的信用额度，可以在头 30 天使用。 在选择升级之前，即使开始使用服务也不会收费。 若要开始操作，请访问 [Azure 免费帐户](https://azure.microsoft.com/free)页。

## <a name="azure-cosmos-db-serverless"></a>Azure Cosmos DB 无服务器

[Azure Cosmos DB 无服务器](serverless.md)让你以一种基于消耗的方式使用 Azure Cosmos 帐户。在这种方式下，你只需为数据库操作所消耗的请求单位和数据所消耗的存储空间付费。 在无服务器模式下使用 Azure Cosmos DB 时不涉及最低费用。 因为它消除了预配容量的概念，所以它最适合于开发或测试活动，尤其是在数据库大部分时间处于空闲状态的情况下。

## <a name="use-shared-throughput-databases"></a>使用共享吞吐量数据库

在[共享吞吐量数据库](set-throughput.md#set-throughput-on-a-database)中，数据库中的所有容器共享数据库的已预配吞吐量（RU/秒）。 例如，如果为数据库预配了 400 RU/秒并且有四个容器，则所有四个容器共享此 400 RU/秒的吞吐量。 在开发或测试环境中，对每个容器的访问可能不是很频繁，因此，需求低于最小值 400 RU/秒，将容器置于共享吞吐量数据库中有助于优化成本。

例如，假设你的开发或测试帐户有四个容器。 如果创建具有专用吞吐量（最小为 400 RU/秒）的四个容器，则总吞吐量将为 1600 RU/秒。 相反，如果创建一个共享吞吐量数据库（至少 400 RU/秒）并将容器置于其中，则总吞吐量将只有 400 RU/秒。 通常情况下，共享吞吐量数据库非常适合不需要在任何单个容器上保证吞吐量的方案。  详细了解[共享吞吐量数据库](set-throughput.md#set-throughput-on-a-database)。

## <a name="next-steps"></a>后续步骤

可以按照以下文章的说明，开始使用模拟器或免费的 Azure Cosmos DB 帐户：

* 详细了解[了解 Azure Cosmos DB 帐单](understand-your-bill.md)
* 详细了解 [Azure Cosmos DB 无服务器](serverless.md)
* 详细了解如何[优化吞吐量成本](optimize-cost-throughput.md)
* 详细了解如何[优化存储成本](optimize-cost-storage.md)
* 详细了解如何[优化读取和写入成本](optimize-cost-reads-writes.md)
* 详细了解如何[优化查询成本](./optimize-cost-reads-writes.md)
* 详细了解[优化多区域 Azure Cosmos 帐户的成本](optimize-cost-regions.md)
* 正在尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量规划。
    * 若只知道现有数据库群集中的 vcore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md) 
    * 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)