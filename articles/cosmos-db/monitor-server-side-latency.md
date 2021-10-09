---
title: 如何监视 Azure Cosmos DB 中的操作的服务器端延迟
description: 了解如何监视 Azure Cosmos DB 帐户或容器中的操作的服务器延迟。 Azure Cosmos DB 帐户的所有者可了解 Azure Cosmos 帐户的服务器端延迟问题。
ms.service: cosmos-db
ms.topic: how-to
ms.author: esarroyo
author: StefArroyo
ms.date: 09/16/2021
ms.openlocfilehash: bd1dcc24533e7d537a350d7f8fd516e697b3fdb5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593812"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>如何监视 Azure Cosmos DB 容器或帐户中的操作的服务器端延迟
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

适用于 Azure Cosmos DB 的 Azure Monitor 提供一个指标视图，用于监视帐户和创建仪表板。 默认情况下，系统会收集 Azure Cosmos DB 指标，此功能不需要你显式启用或配置任何设置。 服务器端延迟指标用于查看操作的服务器端延迟。 Azure Cosmos DB 通过直接连接为点读取/写入操作提供少于 10 ms 的 SLA。 对于点读写操作，SLA 按照 [SLA 文档](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)中的详细信息进行计算。

如果看到点操作的异常高的延迟，可以监视服务器端延迟，点操作有：

* 在直接连接模式下具有分区键和 ID 的 GET 或 SET 操作
* 读或写操作
* 查询

你可以查找诊断日志以查看返回的数据的大小。 如果看到查询操作的一直持续的较高延迟，应该在诊断日志中查找所使用的更高[吞吐量或 RU/s](cosmosdb-monitor-logs-basic-queries.md)。 服务器端延迟显示将数据返回到客户端之前在后端基础结构上花费的时间。 请务必查看此指标，排除所有后端延迟问题。

## <a name="view-the-server-side-latency-metric"></a>查看服务器端延迟指标

1. 登录到 [Azure 门户](https://portal.azure.com/)。
   
1. 在左侧导航栏中选择“监视”，然后选择“指标”。 

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Azure Monitor 中的“指标”窗格" border="true":::

1. 在“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。    对于“资源类型”，请选择“Azure Cosmos DB 帐户”，选择一个现有的 Azure Cosmos 帐户，然后选择“应用”。  
   
   :::image type="content" source="./media/monitor-account-key-updates/select-account-scope.png" alt-text="选择要查看指标的帐户范围" border="true":::

1. 接下来，从可用指标的列表中选择“服务器端延迟”指标。 若要详细了解此列表中的所有可用指标，请参阅[按类别划分的指标](monitor-cosmos-db-reference.md)一文。 在此示例中，让我们选择“服务器端延迟”和“平均”作为聚合值。 除这些详细信息外，还可以选择指标的“时间范围”和“时间粒度”。  可以查看过去最长 30 天的指标。  应用筛选器后，系统会根据该筛选器显示图表。 可以查看所选时间段内每分钟的服务器端延迟。  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="从 Azure 门户中选择服务器端延迟指标" border="true":::

## <a name="filters-for-server-side-latency"></a>服务器端延迟的筛选器

还可以按特定的 CollectionName、ConnectionMode、DatabaseName、OperationType、Region 和 PublicAPIType 筛选指标并显示相应的图表。      

若要筛选指标，请选择“添加筛选器”，并选择所需的属性（例如 PublicAPIType），然后选择值“sql”。 为 OperationType 添加另一个筛选器。 然后，图将显示所选时间段内不同操作的服务器端延迟。 不会记录通过存储过程执行的操作，因此 OperationType 指标下不会显示这些操作。

每个操作的服务器端延迟指标如下图所示的那样显示：

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="服务器端延迟指标的筛选器" border="true":::

也可以使用“应用拆分”选项对指标进行分组。  

## <a name="next-steps"></a>后续步骤

* 使用 Azure 中的[诊断设置](cosmosdb-monitor-resource-logs.md)监视 Azure Cosmos DB 数据。
* [审核 Azure Cosmos DB 控制平面操作](audit-control-plane-logs.md)
