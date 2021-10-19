---
title: 采用了 Azure Cosmos DB Graph API 的 Infosys 供应链可追溯性解决方案
description: Infosys 实现的供应链可追溯性关系图解决方案使用 Azure Cosmos DB Gremlin API 和其他 Azure 服务。 该解决方案为成品提供全局供应链追溯和跟踪功能。
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/07/2021
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 4ec236a57e9c8f24625d22f4af3f39299d118804
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716290"
---
# <a name="supply-chain-traceability-solution-using-azure-cosmos-db-graph-api"></a>采用了 Azure Cosmos DB Graph API 的供应链可追溯性解决方案

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

本文概述了 [Infosys 实现的可追溯性关系图解决方案](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-traceability-knowledge-graph?tab=Overview)。 这些解决方案使用 Azure Cosmos DB Gremlin API 和其他 Azure 功能，为成品提供全球供应链跟踪和跟踪功能。

阅读本文后，用户会了解：

* 供应链上下文中的可追溯性是什么意思？
* 使用 Azure 功能提供的全局可追溯性解决方案的体系结构。  
* Azure Cosmos DB 关系图数据库如何帮助在一种全球供应链中提供原始材料的复杂关系？
* Azure 集成平台服务（例如 API 管理、事件中心）如何帮助你集成各种供应链应用程序生态系统？
* 如何获取 Infosys 的帮助，以便使用此解决方案来满足您的可追溯性需求？

## <a name="overview"></a>概述

在食品供应链中，产品可追溯性是指在产品的整个生命周期内，追溯和跟踪产品的能力。 供应链包括供应、制造和分发。 可追溯性对于食物安全、品牌和监管公开至关重要。 在过去，一些组织无法在其供应链中有效地追溯和跟踪产品，从而导致成本昂贵的召回、罚款和消费者健康状况问题。

Infosys 的可追溯性解决方案是通过 Azure 功能（如应用程序服务、集成服务和数据库服务）开发的，提供了以下重要功能：

* 连接工厂、仓库/配送中心。
* 引入/处理并行库存移动事件。
* 知识图，显示原材料、批处理、成品 (FG) 托盘、托盘的多级别父/子关系、货物移动之间的关联。
* 包含搜索功能的门户，可追溯和跟踪托盘。
* 识别质量事件的影响，如受影响的原始材料批、受影响的托盘、托盘位置。

## <a name="solution-architecture"></a>解决方案体系结构

供应链可追溯性通常会在引入托盘移动、处理质量事件和跟踪/分析存储数据时共享模式。 首先，这些系统需要从工厂/仓库管理系统中引入突发的数据，而这些系统往往是跨地理区域分布的。 接下来，这些系统处理和分析流数据，以在原材料、生产批次、成品托盘和复杂的父/子关系（整合包装/重新包装）之间派生复杂的关系。 然后，系统需要存储可追溯性所需的原材料、成品、托盘之间复杂的关系。 带有搜索功能的用户门户允许用户在供应链网络中追溯和跟踪产品。

Microsoft Azure 提供可应用于可追溯性用例的多种服务，包括 Azure Cosmos DB、Azure 事件中心、Azure API 管理、Azure 应用服务、Azure SignalR、Azure Synapse Analytics 和 Power BI。

Infosys 的可追溯性解决方案提供了可用于改进追溯和跟踪功能的成熟解决方案。 下图说明了用于这种可追溯性解决方案的体系结构：

:::image type="content" source="./media/supply-chain-traceability-solution/solution-architecture.png" alt-text="供应链可追溯性解决方案体系结构" lightbox="./media/supply-chain-traceability-solution/solution-architecture.png" border="false":::

此体系结构中使用的不同 Azure 服务有助于完成以下任务：

* Azure Cosmos DB 允许弹性地提高或降低性能。 借助 Gremlin API，可以创建和查询原材料、成品和仓库之间的复杂关系。
* Azure API 管理为第三方物流提供商（简称 3PL）和仓库管理系统 (WMS) 提供了用于库存移动事件的 API。  
* Azure 事件中心提供了从 WMS 和3PL 收集大量并发事件以便进一步处理的功能。
* Azure Function 应用处理事件，并使用图形 API 将数据引入 Azure Cosmos DB。
* Azure 搜索服务允许用户执行复杂的查找，筛选托盘信息。
* Azure Databricks 读取更改源，并在 Synapse Analytics 中为 Power BI 中的用户创建模型以用于自助式服务。
* Azure Web 应用和应用服务计划可以部署用户门户。
* Azure 存储帐户可存储存档的数据，以满足长期的监管需求。

## <a name="graph-db-and-its-data-design"></a>Graph DB 及其数据设计

货物的生产和配送需要维护一组复杂的动态关系。  可追溯性关系图的自适应数据模型允许存储如下关系：从原材料接收开始，在工厂中制造成品，在供应链期间转移到不同仓库，最后转移到客户仓库。 此过程的高级可视化效果如下图所示：

:::image type="content" source="./media/supply-chain-traceability-solution/data-design-visualization.png" alt-text="供应链数据设计可视化效果" lightbox="./media/supply-chain-traceability-solution/data-design-visualization.png" border="true":::

上图显示了复杂的供应链流程的高级别视图和简单视图。 不过，由于能够从工厂和仓库实时获取重要的库存变动信息，因此可以创建一个精致的图形来连接所有这些不同的信息碎片。

1. 可追溯性过程从供应商将原材料送到工厂时开始，此时会创建关系图的初始节点（顶点）以及关系（边缘）。

1. 成品（项）通过原材料制造并打包到托盘中。

1. 然后将根据客户要求/订单，将托盘移动到工厂仓库或市场仓库。

1. 仓库可以是公司自有仓库，也可以是 3PL（第三方物流提供商）。 然后根据客户订单将托盘运送到各种其他仓库。 根据客户要求，会创建子托盘或子托盘的子项以适应订购数量。 有时会通过混合多个项来生成一个全新项。 例如，在生成各种包的整合包装方案中，有时同一项会被重新包装为更小或更大的数量，作为客户订单的一部分。

   :::image type="content" source="./media/supply-chain-traceability-solution/pallet-relationship.png" alt-text="供应链可追溯性解决方案中的托盘关系" lightbox="./media/supply-chain-traceability-solution/pallet-relationship.png" border="true":::

1. 然后托盘通过供应链网络最终到达客户仓库。 在此过程中，可以进一步细分托盘，或与其他托盘合并，以生成新的托盘来履行客户订单。

1. 最终，系统会创建一个复杂的图形，用于保存质量事件管理的重要关系信息，我们接下来将讨论这方面的内容。

   :::image type="content" source="./media/supply-chain-traceability-solution/supply-chain-object-relationship.png" alt-text="供应链对象关系完整的体系结构" lightbox="./media/supply-chain-traceability-solution/supply-chain-object-relationship.png" border="true":::

1. 这些复杂的关系对于质量事件至关重要，系统可以在其中追溯和跟踪供应链中的托盘。 关系图及其遍历提供了这方面的必需信息。 例如，如果某一原材料出现问题，关系图可以显示受影响的托盘以及当前位置。

## <a name="next-steps"></a>后续步骤

* [Infosys 可追溯性关系图解决方案](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-traceability-knowledge-graph?tab=Overview)
* 若要可视化关系图数据，请参阅 [Gremlin API 可视化解决方案](graph-visualization-partners.md)。
* 若要为关系图数据建模，请参阅 [Gremlin API 建模解决方案](graph-modeling-tools.md)。