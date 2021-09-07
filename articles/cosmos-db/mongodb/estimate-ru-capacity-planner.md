---
title: 使用 Azure Cosmos DB 容量规划器估算成本 - 适用于 Mongo DB 的 API
description: 可以使用 Azure Cosmos DB Capacity Planner 估算你的工作负荷所需的吞吐量（RU/秒）和成本。 本文介绍如何使用容量规划器来估算使用适用于 MongoDB 的 Azure Cosmos DB API 时所需的吞吐量和成本。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.author: dech
ms.openlocfilehash: a6c430462185ebac38c196d5aa80fc0d676f4911
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030351"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner---azure-cosmos-db-api-for-mongodb"></a>使用 Azure Cosmos DB 容量规划器估算 RU/秒 - 适用于 MongoDB 的 Azure Cosmos DB API
[!INCLUDE[appliesto-sql-api](../includes/appliesto-mongodb-api.md)]

> [!NOTE]
> 如果你计划将数据迁移到 Azure Cosmos DB，但只知道现有分片和复制数据库群集中的 vCore 和服务器数量，则还请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
>

针对工作负载为数据库和集合配置适当数量的预配吞吐量（也称为[请求单位 [RU/秒]](../request-units.md)）对于优化成本和性能非常重要。 本文介绍如何使用 Azure Cosmos DB [容量规划器](https://cosmos.azure.com/capacitycalculator/)来估算使用适用于 MongoDB 的 Azure Cosmos DB API 时工作负载所需的 RU/秒和成本。 如果使用的是 SQL API，请参阅如何[配合使用容量计算器与 SQL API](../estimate-ru-with-capacity-planner.md) 一文。

[!INCLUDE [capacity planner modes](../includes/capacity-planner-modes.md)]

## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a><a id="basic-mode"></a>使用基本模式估算预配吞吐量和成本
若要使用基本模式针对你的工作负荷进行快速估算，请导航到 [Capacity Planner](https://cosmos.azure.com/capacitycalculator/)。 根据工作负载输入以下参数：

|**输入**  |**说明**  |
|---------|---------|
| API |选择 MongoDB API |
|区域数量|适用于 MongoDB 的 Azure Cosmos DB API 面向所有 Azure 区域提供。 选择你的工作负荷所需的区域数目。 可以将任意数量的区域与你的帐户相关联。 有关更多详细信息，请参阅[全局分布](../distribute-data-globally.md)。|
|多区域写入|如果你启用[多区域写入](../distribute-data-globally.md#key-benefits-of-global-distribution)，则你的应用程序可以在任何 Azure 区域中进行读取和写入。 如果你禁用多区域写入，则你的应用程序可以将数据写入到单个区域。 <br/><br/> 如果你预计在不同区域中会有需要低延迟写入的主动-主动工作负荷，请启用多区域写入。 例如，在不同区域中大量向数据库写入数据的 IOT 工作负荷。 <br/><br/> 多区域写入可保证 99.999% 的读取和写入可用性。 与单一写入区域相比，多区域写入需要更多的吞吐量。 若要了解详细信息，请参阅[单一写入区域与多个写入区域的 RU 有何不同](../optimize-cost-regions.md)一文。|
|事务存储中存储的数据总量 |单个区域中的事务存储中存储的预估数据总量 (GB)。|
|使用分析存储| 如果要使用 [Synapse 分析存储](../synapse-link.md)，请选择“打开”。 输入“存储在分析存储中的总数据”，其表示在单个区域的分析存储中存储的数据估值 (GB)。  |
|项大小|文档的估计大小，范围为 1 KB 到 2 MB。 |
|查找数/秒 |每个区域每秒的预期查找操作数。 |
|Inserts/sec |每个区域每秒的预期插入操作数。 |
|更新次数/秒 |每个区域每秒的预期更新操作数。 选择自动编制索引时，该更新操作的“RU/秒”估计值被当作每次更新更改的一个属性来计算。 |
|删除数/秒 |每个区域每秒的预期删除操作数。 |

填写所需的详细信息后，选择“计算”。 “成本预估”选项卡将显示存储和预配吞吐量的总成本。 可以在此选项卡中展开“显示详细信息”链接，获取不同 CRUD 和查询请求所需的吞吐量的明细。 每次更改任何字段的值后，请选择“计算”来重新计算估计成本。

:::image type="content" source="./media/estimate-ru-capacity-planner/basic-mode-mongodb-api.png" alt-text="Capacity Planner 基本模式" border="true":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a><a id="advanced-mode"></a>使用高级模式估算预配吞吐量和成本

高级模式允许你提供会影响 RU/秒估计值的更多设置。 若要使用此选项，请导航到 [Capacity Planner](https://cosmos.azure.com/capacitycalculator/) 并使用你用于 Azure 的帐户登录该工具。 登录选项位于右侧边角。

登录后，与基本模式中的字段相比，你可以看到更多字段。 根据你的工作负载输入其他参数。

|**输入**  |**说明**  |
|---------|---------|
|API|Azure Cosmos DB 是一项多模型和多 API 服务。 选择“MongoDB API”。 |
|区域数量|适用于 MongoDB 的 Azure Cosmos DB API 面向所有 Azure 区域提供。 选择你的工作负荷所需的区域数目。 你可以将任意数量的区域与你的 Cosmos 帐户相关联。 有关更多详细信息，请参阅[全局分布](../distribute-data-globally.md)。|
|多区域写入|如果你启用[多区域写入](../distribute-data-globally.md#key-benefits-of-global-distribution)，则你的应用程序可以在任何 Azure 区域中进行读取和写入。 如果你禁用多区域写入，则你的应用程序可以将数据写入到单个区域。 <br/><br/> 如果你预计在不同区域中会有需要低延迟写入的主动-主动工作负荷，请启用多区域写入。 例如，在不同区域中大量向数据库写入数据的 IOT 工作负荷。 <br/><br/> 多区域写入可保证 99.999% 的读取和写入可用性。 与单一写入区域相比，多区域写入需要更多的吞吐量。 若要了解详细信息，请参阅[单一写入区域与多个写入区域的 RU 有何不同](../optimize-cost-regions.md)一文。|
|默认一致性|适用于 MongoDB 的 Azure Cosmos DB API 支持 5 种一致性级别，使开发人员可在一致性、可用性和延迟之间进行权衡。 若要了解详细信息，请参阅[一致性级别](../consistency-levels.md)一文。 <br/><br/> 默认情况下，适用于 MongoDB 的 API 使用会话一致性，这可以保证能够在会话中读取自己的写入。 <br/><br/> 与会话一致性、一致前缀一致性和最终一致性相比，选择强一致性或有限过期一致性将会使读取所需的 RU/秒翻倍。 多区域写入强一致性不受支持，系统将自动默认选择单区域写入强一致性。 |
|索引策略| 如果选择“关闭”选项，则不会为任何属性编制索引。 这将导致最低的写入 RU 开销。 如果只计划在每次查询时使用 _id 字段和分片键进行查询（每次查询都是使用这两项），请关闭索引策略。<br/><br/> 如果选择“自动”选项，则适用于 MongoDB 的 API 的 3.6 及更高版本将自动为 _id 字段编制索引。 选择自动索引时，等同于设置通配符索引（其中，每个属性都会自动编制索引）。 将通配符索引用于所有字段能够实现灵活高效的查询。<br/><br/>如果选择“自定义”选项，你可设置要通过多键索引或复合索引编制索引的属性数。 可以稍后在表单中输入编制索引的属性的数量。 若要了解详细信息，请参阅适用于 MongoDB 的 API 中的[索引管理](../mongodb-indexing.md)。|
|事务存储中存储的数据总量 |单个区域中的事务存储中存储的预估数据总量 (GB)。|
|使用分析存储| 如果要使用 [Synapse 分析存储](../synapse-link.md)，请选择“打开”。 输入“存储在分析存储中的总数据”，其表示在单个区域的分析存储中存储的数据估值 (GB)。  |
|工作负荷模式|如果工作负载量固定不变，请选择“稳定”。 <br/><br/> 如果工作负载量随时间推移而变化，请选择“可变”。  例如，在特定的一天或一个月内变化。 如果选择可变工作负载选项，则下列设置可用：<ul><li>高峰时间百分比：一个月内工作负荷需要峰值（最高）吞吐量的时间百分比。 </li></ul> <br/><br/> 例如，如果你的工作负荷在工作日的上午 9 点到下午 6 点的营业时间内有大量活动，则高峰时间的百分比为：45 个高峰小时 / 730 小时 / 月 = ~6%。<br/><br/>使用高峰和非高峰时间间隔，你能够相应地[以编程方式缩放预配吞吐量](../set-throughput.md#update-throughput-on-a-database-or-a-container)，从而优化成本。|
|项大小|文档的大小，范围为 1 KB 到 2 MB。 可以为多个示例项添加估算。 <br/><br/>你还可以 **上传示例 (JSON)** 文档以获得更准确的估计。<br/><br/>如果你的工作负荷在同一容器中有多个类型的项（包含不同的 JSON 内容），则可以上传多个 JSON 文档并获得估计值。 可以使用“添加新项”按钮添加多个示例 JSON 文档。|
| 操作类型 | 操作的类型，例如查找、聚合、修改等  。  |
| 请求单位 (RU) 按调用收费 | 执行所选操作类型的 RU/秒费用估算。 |
| 每个区域的调用/秒 | 每个区域每秒执行的所选操作类型数。 |

还可以使用“保存估计值”按钮下载包含当前估计值的 CSV 文件。

:::image type="content" source="./media/estimate-ru-capacity-planner/advanced-mode-mongodb-api.png" alt-text="Capacity Planner 高级模式" border="true":::

容量规划器中显示的价格根据吞吐量和存储的公共定价费率进行评估。 所有价格显示为美元。 若要按区域查看所有费率，请参阅 [Azure Cosmos DB 定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。  

## <a name="next-steps"></a>后续步骤

* 若只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
* 详细了解 [Azure Cosmos DB 的定价模型](../how-pricing-works.md)。
* 创建新的 [Cosmos 帐户、数据库和容器](../create-cosmosdb-resources-portal.md)。
* 了解如何[优化预配吞吐量成本](../optimize-cost-throughput.md)。
* 了解如何[借助保留容量优化成本](../cosmos-db-reserved-capacity.md)。
* 正在尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以根据有关现有数据库群集的信息进行容量计划。
