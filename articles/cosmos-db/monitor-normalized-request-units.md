---
title: 监视 Azure Cosmos 容器或帐户的规范化 RU/秒
description: 了解如何在 Azure Cosmos DB 中监视某个操作的规范化请求单位使用量。 Azure Cosmos DB 帐户的所有者可以了解哪些操作消耗了较多的请求单位。
ms.service: cosmos-db
ms.topic: how-to
ms.author: esarroyo
author: StefArroyo
ms.date: 01/07/2021
ms.openlocfilehash: d3fe29f94cf874d7ede2b5d7f85199c2d9df65f2
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113359652"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>如何监视 Azure Cosmos 容器或帐户的规范化 RU/秒
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

适用于 Azure Cosmos DB 的 Azure Monitor 提供了用来监视帐户和创建仪表板的指标视图。 默认情况下，系统会收集 Azure Cosmos DB 指标，此功能不需要你显式启用或配置任何设置。

“规范化 RU 消耗”指标用于查看分区键范围在流量方面的饱和程度。 Azure Cosmos DB 在所有分区键范围之间平均分布吞吐量。 此指标提供分区键范围的最大吞吐量利用率的每秒视图。 使用此指标可计算给定容器在各分区键范围的 RU/秒利用率。 通过此指标，如果看到在 Azure Monitor 中所有分区键范围的请求单位利用率很高，则应增大吞吐量来满足工作负载的需要。 示例 - 规范化利用率定义为所有分区键范围的 RU/秒利用率的最大值。 例如，假设最大吞吐量为 20,000 RU/秒，并且有两个分区键范围（P_1 和 P_2），每个都可扩展到 10,000 RU/秒。 在给定的秒数内，如果 P_1 已使用 6000 RU，而 P_2 已使用 8000 RU，则标准化利用率为 MAX(6000 RU / 10,000 RU, 8000 RU / 10,000 RU) = 0.8。

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>规范化 RU/秒较高时会发生的事情和应该做的事情

给定分区键范围的规范化 RU/秒消耗达到 100% 时，如果客户端仍在 1 秒的时间范围向该特定分区键范围发出请求，则会收到速率限制错误。 客户端应等待建议时长，然后重试该请求。 使用 SDK 可轻松地处理这种情况，方法是通过适当地等待来重试预配置的时间。  不一定是仅因为规范化 RU 已达到100%，就会看到 RU 速率限制错误。 原因是规范化 RU 是一个表示所有分区键范围的最大利用率的值，一个分区键范围可能处于忙碌状态，但其他分区键范围可正常为这些请求提供服务。 例如，如果某个操作（例如存储过程）消耗了分区键范围上的所有 RU/秒，则会导致规范化 RU/秒消耗出现短期峰值。 在这种情况下，如果请求率较低或对不同分区键范围内的其他分区发出请求，则不会有任何即时速率限制错误。 

有了 Azure Monitor 指标，就可使用“总请求数”指标来查找 SQL API 每个状态代码的操作。 稍后，你可以通过 429 状态代码对这些请求进行筛选，并按 **操作类型** 来划分这些请求。  

若要查找速率受限的请求，建议使用诊断日志获取此信息。

如果多个分区键范围的规范化 RU/秒消耗持续达到 100% 峰值或接近 100%，建议增大吞吐量。 可使用 Azure Monitor 指标和 Azure Monitor 诊断日志查明哪些操作繁忙及其峰值利用率是多少。

综上所述，“规范化 RU 消耗”指标用于查看哪个分区键范围的利用率较大。 这使你可将吞吐量向某个分区键范围倾斜。 你稍后可以跟进，查看 Azure Monitor 日志中的 **PartitionKeyRUConsumption** 日志，了解哪些逻辑分区键使用量较大。 这将指向分区键选择中的更改或应用程序逻辑中的更改。 若要解决速率限制，请分布数据的负载（比如跨多个分区），或者按需增加吞吐量。 

## <a name="view-the-normalized-request-unit-consumption-metric"></a>查看规范化请求单位消耗指标

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 在左侧导航栏中选择“监视”，然后选择“指标”。 

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Azure Monitor 中的“指标”窗格":::

3. 在“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。    对于“资源类型”，请选择“Azure Cosmos DB 帐户”，选择一个现有的 Azure Cosmos 帐户，然后选择“应用”。  

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="选择一个 Azure Cosmos 帐户来查看指标":::

4. 接下来，可以从可用指标列表中选择一个指标。 可以选择特定于请求单位、存储、延迟、可用性、Cassandra 和其他方面的指标。 若要详细了解此列表中的所有可用指标，请参阅[按类别列出的指标](monitor-cosmos-db-reference.md)一文。 在此示例中，我们选择“规范化 RU 消耗”指标，并选择“最大值”作为聚合值。

   除了这些详细信息之外，还可以选择指标的时间范围和时间粒度。 可以查看过去最长 30 天的指标。  应用筛选器后，系统会根据该筛选器显示图表。

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="从 Azure 门户中选择指标":::

### <a name="filters-for-normalized-request-unit-consumption"></a>针对规范化请求单位消耗的筛选器

还可以按特定的 **CollectionName**、**DatabaseName**、**PartitionKeyRangeID** 和 **区域** 筛选所显示的指标和图表。 若要筛选指标，请选择“添加筛选器”，并选择所需的属性（例如 **CollectionName**）和所关注的相应值。 然后，图中会显示在所选时间段内针对该容器消耗的规范化 RU 消耗单位。  

可以使用“应用拆分”选项对指标进行分组。 对于共享吞吐量数据库，“规范化 RU”指标仅以数据库粒度显示数据，不会显示每个集合的任何数据。 因此按集合名称应用拆分时，将看不到共享吞吐量数据库的任何数据。

而是显示每个容器的规范化请求单位消耗指标，如下图所示：

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="对规范化请求单位消耗指标应用筛选器":::

## <a name="next-steps"></a>后续步骤

* 使用 Azure 中的[诊断设置](cosmosdb-monitor-resource-logs.md)监视 Azure Cosmos DB 数据。
* [审核 Azure Cosmos DB 控制平面操作](audit-control-plane-logs.md)
