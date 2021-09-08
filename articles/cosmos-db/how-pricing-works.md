---
title: Azure Cosmos DB 的定价模型
description: 本文介绍 Azure Cosmos DB 的定价模型，以及该模型如何简化成本管理和成本计划。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 407e5935771b6b6c0b351d75d0a0d7c734eaa556
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038020"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Azure Cosmos DB 中的定价模型
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 的定价模型可简化成本管理和计划。 使用 Azure Cosmos DB，你需要为你针对数据库执行的操作以及你的数据使用的存储付费。

- 数据库操作：数据库操作的收费方式取决于你使用的 Azure Cosmos 帐户的类型。

  - **预配的吞吐量**：[预配的吞吐量](set-throughput.md)（也称为预留吞吐量）可在任何规模提供高性能。 请以每秒[请求单位](request-units.md)数 (RU/s) 形式指定所需吞吐量，Azure Cosmos DB 会专门配备必需的资源来提供配置的吞吐量。 可以[在数据库或容器上预配吞吐量](set-throughput.md)。 根据工作负载需求，可以随时纵向扩展/缩减吞吐量或使用[自动缩放](provision-throughput-autoscale.md)（尽管数据库或容器上有一个保证 SLA 所需的最低吞吐量）。 按指定时间内最大的预配吞吐量以小时来收费。

   > [!NOTE]
   > 由于预配的吞吐量模型会将资源提供给容器或数据库，因此即使不运行任何工作负载，你也要为你已预配的吞吐量付费。

  - 无服务器：在 [无服务器](serverless.md)模式下，无需在 Azure Cosmos 帐户中创建资源时预配任何吞吐量。 在计费周期结束时，会针对你的数据库操作已使用的请求单位量计费。

- **存储**：对于一个给定小时内你的数据和索引所使用的存储总量（以 GB 为单位），将按单一费率计费。 存储按使用量计费，因此你不必提前预留任何存储。 仅为所使用的存储付费。

Azure Cosmos DB 中的定价模型在所有 API 中都是一致的。 有关详细信息，请参阅 [Azure Cosmos DB 定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)、[了解 Azure Cosmos DB 帐单](understand-your-bill.md)和 [Azure Cosmos DB 定价模型如何对客户而言更具经济效益](total-cost-ownership.md)。

如果将 Azure Cosmos DB 帐户部署到美国的某个非政府区域，则对于处于预配吞吐量模式下基于数据库和容器的吞吐量，有一个最低价格。 在无服务器模式下没有最低价格。 定价取决于所使用的区域，有关最新定价信息，请参阅 [Azure Cosmos DB 定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。

## <a name="try-azure-cosmos-db-for-free"></a>免费试用 Azure Cosmos DB

Azure Cosmos DB 免费为开发人员提供众多选项。 这些选项包括：

* Azure Cosmos DB 免费层：使用 Azure Cosmos DB 免费层，可以轻松上手、开发和测试应用程序，甚至免费运行小型生产工作负载。 在帐户上启用免费层后，在该帐户的生存期内，一开始你将在该帐户中免费获得 1000 RU/s 的吞吐量和 25 GB 的存储空间。 每个 Azure 订阅最多可以有一个免费层帐户，并且必须在创建帐户时选择加入使用。 若要了解详细信息，请参阅如何[创建免费层帐户](free-tier.md)一文。

* “Azure 免费帐户”：Azure 提供[免费层级](https://azure.microsoft.com/free/)，即提供 200 美元的 Azure 额度（可在前 30 天内使用），以及 12 个月的限量免费服务。 有关详细信息，请参阅 [Azure 免费帐户](../cost-management-billing/manage/avoid-charges-free-account.md)。 Azure Cosmos DB 是 Azure 免费帐户的一部分。 特别对于 Azure Cosmos DB，该免费帐户提供 25 GB 的存储和 400 RU 的全年预配吞吐量。

* “免费试用 Azure Cosmos DB”：Azure Cosmos DB 通过使用试用 Azure Cosmos DB 免费帐户提供限时体验。 可以通过使用快速入门和教程创建 Azure Cosmos DB 帐户、创建数据库和集合，以及运行示例应用程序。 可以运行示例应用程序，而无需订阅 Azure 帐户或使用信用卡。 [免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 提供一个月的 Azure Cosmos DB 服务，并且可以任意次数更新帐户。

* **Azure Cosmos DB 模拟器**：为方便进行开发，Azure Cosmos DB 模拟器提供了一个模拟 Azure Cosmos DB 服务的本地环境。 模拟器免费提供，并且具有对云服务的高保真度。 使用 Azure Cosmos DB 模拟器可在本地开发和测试应用程序，无需创建 Azure 订阅且不会产生任何费用。 投入生产之前，可以在本地使用模拟器开发应用程序。 如果对模拟器的应用程序功能感到满意，可切换到云中的“使用 Azure Cosmos DB 帐户”，从而大幅节省成本。 有关模拟器的详细信息，请参阅[使用 Azure Cosmos DB 进行开发和测试](local-emulator.md)一文。

## <a name="pricing-with-reserved-capacity"></a>预留容量定价

Azure Cosmos DB [预留容量](cosmos-db-reserved-capacity.md)有助于通过预付为期一年或三年的 Azure Cosmos DB 资源费用在使用预配吞吐量模式时为你省钱。 与采用一般定价相比，预付为期一年或三年的承诺费用可以享受 20-65% 的折扣，从而大幅节省成本。 Azure Cosmos DB 的保留容量帮助通过预付一年或三年的预配的吞吐量 (RU/s) 来降低成本，并且帮助针对所预配的吞吐量获取折扣。 

预留容量提供一种计费折扣，并且不会影响 Azure Cosmos DB 资源的运行时状态。 所有 API（包括 MongoDB、Cassandra、SQL、Gremlin 和 Azure表）和全球所有地区都可以一致地使用预留容量。 有关预留容量的详细信息，请参阅[使用预留容量预付 Azure Cosmos DB 资源](cosmos-db-reserved-capacity.md)一文，并可从 [Azure 门户](https://portal.azure.com/)购买预留容量。

## <a name="next-steps"></a>后续步骤

可在以下文章中了解更多关于优化 Azure Cosmos DB 资源成本的信息：

* 尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量规划。
    * 如果你只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md) 
    * 如果知道当前数据库工作负荷的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)
* 了解[开发和测试优化](optimize-dev-test.md)
* 详细了解[了解 Azure Cosmos DB 帐单](understand-your-bill.md)
* 详细了解如何[优化吞吐量成本](optimize-cost-throughput.md)
* 详细了解如何[优化存储成本](optimize-cost-storage.md)
* 详细了解如何[优化读取和写入成本](optimize-cost-reads-writes.md)
* 详细了解如何[优化查询成本](./optimize-cost-reads-writes.md)
* 详细了解如何[优化多区域 Cosmos 帐户的成本](optimize-cost-regions.md)
* 了解 [Azure Cosmos DB 预留容量](cosmos-db-reserved-capacity.md)
* 了解 [Azure Cosmos DB 模拟器](local-emulator.md)