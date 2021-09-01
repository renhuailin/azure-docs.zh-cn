---
title: 使用第三方监视工具监视 Azure Cosmos DB
description: 本文介绍监视第三方工具如何有助于监视 Cosmos DB。
author: manishmsfte
ms.author: mansha
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/28/2021
ms.openlocfilehash: b78d6a4c92ad96c15d55332aac320bad09c58c1e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777863"
---
# <a name="monitoring-azure-cosmos-db-using-third-party-solutions"></a>使用第三方解决方案监视 Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

除了 Azure Monitor，还可以使用第三方监视解决方案来监视 Cosmos DB 实例。

> [!IMPORTANT] 
> 本文中提到的解决方案仅供参考，所有权由各解决方案所有者拥有。 建议用户进行全面评估，然后选择最适合的解决方案。

## <a name="datadog"></a>Datadog
{支持：SQL API、Azure Cosmos DB API for MongoDB、Gremlin API、Cassandra API 和 Table API}

[Datadog](https://www.datadoghq.com/) 是一个完全统一的平台，涵盖基础设施监视、应用程序性能监视、日志管理、用户体验监视等。 Datadog 将公司堆栈中每个工具和服务的数据汇集在一起，为故障排除、优化性能和跨团队协作提供单一的事实来源。
Datadog 中所有内容都在同一组标记下进行组织，因此与特定问题相关的所有数据将自动关联。 通过消除盲点，Datadog 可以降低被忽略的错误的风险，减轻正在进行的服务维护的负担，并加速数字化转型。

Datadog 从 CosmosDB 收集 40 多个不同的仪表和计数指标，包括每个区域的总可用存储、所创建的 SQL 数据库的数量等。 这些指标通过 Datadog Azure 集成收集，在平台中显示的速度比行业中的其他工具快 40%。 Datadog 还提供适用于 CosmosDB 的开箱即用仪表板，让用户可立即深入了解 CosmosDB 实例的性能。 用户可以可视化平台级指标（例如消耗的请求单位总数）和 API 级指标（例如创建的 Cassandra 密钥空间数），以更好地了解他们的 CosmosDB 使用情况。

许多 Cosmos DB 客户都在使用 Datadog，其中包括
- 马士基
- PWC 
- PayScale 
- AllScripts 
- 赫斯特



:::image type="content" source="./media/monitor-solutions/datadog-demo.gif" alt-text="Datadog 演示" border="false":::
图：Datadog 工作中

有用的链接：
- [定价详细信息](https://www.datadoghq.com/pricing/)
- [开始使用 14 天试用版](https://www.datadoghq.com/free-datadog-trial/)


## <a name="dynatrace"></a>Dynatrace
{支持：SQL API 和 Azure Cosmos DB API for MongoDB}

[Dynatrace](https://www.dynatrace.com/platform/) 为云提供软件智能，以抑制云复杂性并加速数字化转型。 借助大规模自动且智能的可观测性，Dynatrace 一体式软件智能平台可针对应用程序性能和安全性、底层基础结构和所有用户体验提供精确答案，使团队能够自动执行云操作、更快地发布更好的软件，并提供卓越的数字体验。  
Dynatrace 使用 Mongo API 收集和传送 CosmosDB 指标，其中包括调用数和响应时间——所有这些指标都根据聚合、命令、读取和写入操作进行可视化。  它还告知你在环境中执行的确切数据库语句。  最后，借助 [Davis AI Engine](https://www.dynatrace.com/davis)，它可以精确检测哪条数据库语句是降级的根本原因，并可以发现标识为根本原因的数据库。

:::image type="content" source="./media/monitor-solutions/dynatrace-demo.gif" alt-text="Dynatrace 的不同屏幕，用于提供 Cosmos DB 的监视信息" border="false":::
图：Dynatrace 工作中

### <a name="useful-links"></a>有用的链接

- [免费试用 15 天的 Dynatrace](https://www.dynatrace.com/trial)
- [从 Azure 市场启动](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dynatrace.dynatrace-managed)
- [有关如何使用 Azure Monitor 监视 Cosmos DB 的文档](https://www.dynatrace.com/support/help/technology-support/cloud-platforms/microsoft-azure-services/set-up-integration-with-azure-monitor/?_ga=2.184080354.559899881.1623174355-748416177.1603817475)
- [Cosmos DB - Dynatrace 集成的详细信息](https://www.dynatrace.com/news/blog/azure-services-explained-part-4-azure-cosmos-db/?_ga=2.185016301.559899881.1623174355-748416177.1603817475)
- [用于 Azure 数据库的 Dynatrace 监视功能](https://www.dynatrace.com/technologies/azure-monitoring/azure-database-performance/)
- [Dynatrace for Azure 解决方案概述](https://www.dynatrace.com/technologies/azure-monitoring/)
- [解决方案合作伙伴](https://www.dynatrace.com/partners/solution-partners/)

## <a name="next-steps"></a>后续步骤
- [监视 Cosmos DB 数据参考](./monitor-cosmos-db-reference.md)
