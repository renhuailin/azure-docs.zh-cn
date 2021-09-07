---
title: 使用 Azure Cosmos DB 容量规划器估算成本 - SQL API
description: 可以使用 Azure Cosmos DB Capacity Planner 估算你的工作负荷所需的吞吐量（RU/秒）和成本。 本文介绍如何使用容量规划器来估算使用 SQL API 时所需的吞吐量和成本。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.author: dech
ms.openlocfilehash: 41059de2efd316c492933209f412e9b4512a0de9
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113508"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner---sql-api"></a>使用 Azure Cosmos DB 容量规划器估算吞吐量（RU/秒）- SQL API
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!NOTE]
> 如果你计划将数据迁移到 Azure Cosmos DB，但只知道现有分片和复制数据库群集中的 vCore 和服务器数量，则还请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
>

针对你的工作负荷为 Azure Cosmos 数据库和容器配置适当数量的预配吞吐量（也称为[请求单位（RU/秒）](../request-units.md)）对于优化成本和性能非常重要。 本文介绍如何使用 Azure Cosmos DB [容量规划器](https://cosmos.azure.com/capacitycalculator/)来估算使用 SQL API 时你的工作负载所需的吞吐量（RU/秒）和成本。 如果使用适用于 MongoDB 的 API，请参阅如何[配合使用容量计算器与 MongoDB](../mongodb/estimate-ru-capacity-planner.md) 一文。

[!INCLUDE [capacity planner modes](../includes/capacity-planner-modes.md)]

## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a><a id="basic-mode"></a>使用基本模式估算预配吞吐量和成本
若要使用基本模式针对你的工作负荷进行快速估算，请导航到 [Capacity Planner](https://cosmos.azure.com/capacitycalculator/)。 根据你的工作负荷输入以下参数：

|**输入**  |**说明**  |
|---------|---------|
| API |选择 SQL（核心）API |
|区域数量|Azure Cosmos DB 面向所有 Azure 区域提供。 选择你的工作负荷所需的区域数目。 你可以将任意数量的区域与你的 Cosmos 帐户相关联。 有关更多详细信息，请参阅 Azure Cosmos DB 中的[全局分布](../distribute-data-globally.md)。|
|多区域写入|如果你启用[多区域写入](../distribute-data-globally.md#key-benefits-of-global-distribution)，则你的应用程序可以在任何 Azure 区域中进行读取和写入。 如果你禁用多区域写入，则你的应用程序可以将数据写入到单个区域。 <br/><br/> 如果你预计在不同区域中会有需要低延迟写入的主动-主动工作负荷，请启用多区域写入。 例如，在不同区域中大量向数据库写入数据的 IOT 工作负荷。 <br/><br/> 多区域写入可保证 99.999% 的读取和写入可用性。 与单一写入区域相比，多区域写入需要更多的吞吐量。 若要了解详细信息，请参阅[单一写入区域与多个写入区域的 RU 有何不同](../optimize-cost-regions.md)一文。|
|事务存储中存储的数据总量 |单个区域中的事务存储中存储的预估数据总量 (GB)。|
|使用分析存储| 若要使用分析存储，请选择“开”。 输入“存储在分析存储中的总数据”，其表示在单个区域的分析存储中存储的数据估值 (GB)。  |
|项大小|数据项（如文档）的估计大小，范围从 1 KB 到 2 MB。 |
|Queries/sec |每个区域每秒的预期查询数。 用于运行查询的平均 RU 费用预计为 10 个 RU。 |
|点读取数/秒 |每个区域每秒的预期点读取操作数。 点读取指对单项 ID 和分区键执行键/值查找。 若要了解有关点读取的详细信息，请参阅[读取数据的选项](../optimize-cost-reads-writes.md#reading-data-point-reads-and-queries)一文。 |
|创建数/秒 |每个区域每秒的预期创建操作数。 |
|更新次数/秒 |每个区域每秒的预期更新操作数。 选择自动编制索引时，该更新操作的“RU/秒”估计值被当作每次更新更改的一个属性来计算。 |
|删除数/秒 |每个区域每秒的预期删除操作数。 |

填写所需的详细信息后，选择“计算”。 “成本预估”选项卡将显示存储和预配吞吐量的总成本。 可以在此选项卡中展开“显示详细信息”链接，获取不同 CRUD 和查询请求所需的吞吐量的明细。 每次更改任何字段的值后，请选择“计算”来重新计算估计成本。

:::image type="content" source="../media/estimate-ru-with-capacity-planner/basic-mode-sql-api.png" alt-text="Capacity Planner 基本模式" border="true":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a><a id="advanced-mode"></a>使用高级模式估算预配吞吐量和成本

高级模式允许你提供会影响 RU/秒估计值的更多设置。 若要使用此选项，请导航到 [Capacity Planner](https://cosmos.azure.com/capacitycalculator/) 并使用你用于 Azure 的帐户登录该工具。 登录选项位于右侧边角。

登录后，与基本模式中的字段相比，你可以看到更多字段。 根据你的工作负载输入其他参数。

|**输入**  |**说明**  |
|---------|---------|
|API|Azure Cosmos DB 是一项多模型和多 API 服务。 选择 SQL（核心）API。 |
|区域数量|Azure Cosmos DB 面向所有 Azure 区域提供。 选择你的工作负荷所需的区域数目。 你可以将任意数量的区域与你的 Cosmos 帐户相关联。 有关更多详细信息，请参阅 Azure Cosmos DB 中的[全局分布](../distribute-data-globally.md)。|
|多区域写入|如果你启用[多区域写入](../distribute-data-globally.md#key-benefits-of-global-distribution)，则你的应用程序可以在任何 Azure 区域中进行读取和写入。 如果你禁用多区域写入，则你的应用程序可以将数据写入到单个区域。 <br/><br/> 如果你预计在不同区域中会有需要低延迟写入的主动-主动工作负荷，请启用多区域写入。 例如，在不同区域中大量向数据库写入数据的 IOT 工作负荷。 <br/><br/> 多区域写入可保证 99.999% 的读取和写入可用性。 与单一写入区域相比，多区域写入需要更多的吞吐量。 若要了解详细信息，请参阅[单一写入区域与多个写入区域的 RU 有何不同](../optimize-cost-regions.md)一文。|
|默认一致性|Azure Cosmos DB 支持 5 种一致性级别，允许开发人员在一致性、可用性和延迟之间进行权衡。 若要了解详细信息，请参阅[一致性级别](../consistency-levels.md)一文。 <br/><br/> 默认情况下，Azure Cosmos DB 使用会话一致性，这可以保证能够在会话中读取你自己的写入。 <br/><br/> 与会话一致性、一致前缀一致性和最终一致性相比，选择强一致性或有限过期一致性将会使读取所需的 RU/秒翻倍。 多区域写入强一致性不受支持，系统将自动默认选择单区域写入强一致性。 |
|索引策略|默认情况下，Azure Cosmos DB 会为所有项中的 [所有属性编制索引](../index-policy.md)，以便进行灵活且高效的查询（映射到 **自动** 索引编制策略）。 <br/><br/> 如果选择“关”，则不会为任何属性编制索引。 这将导致最低的写入 RU 开销。 如果你预计仅执行[点读取](/dotnet/api/microsoft.azure.cosmos.container.readitemasync)（键值查找）和/或写入而不执行查询，请选择“关”策略。 <br/><br/> 如果选择“自动”，Azure CosmosDB 会在所有项写入时自动对其进行索引。 <br/><br/> 自定义索引编制策略允许你从索引中纳入或排除特定属性，以降低写入吞吐量和存储空间。 若要了解详细信息，请参阅[索引编制策略](../index-overview.md)和[示例索引编制策略](how-to-manage-indexing-policy.md#indexing-policy-examples)这两篇文章。|
|事务存储中存储的数据总量 |单个区域中的事务存储中存储的预估数据总量 (GB)。|
|使用分析存储| 若要使用分析存储，请选择“开”。 输入“存储在分析存储中的总数据”，其表示在单个区域的分析存储中存储的数据估值 (GB)。  |
|工作负荷模式|如果工作负载量固定不变，请选择“稳定”。 <br/><br/> 如果工作负载量随时间推移而变化，请选择“可变”。  例如，在特定的一天或一个月内变化。 如果选择可变工作负载选项，则下列设置可用：<ul><li>高峰时间百分比：一个月内工作负荷需要峰值（最高）吞吐量的时间百分比。 </li></ul> <br/><br/> 例如，如果你的工作负荷在工作日的上午 9 点到下午 6 点的营业时间内有大量活动，则高峰时间的百分比为：45 个高峰小时 / 730 小时 / 月 = ~6%。<br/><br/>使用高峰和非高峰时间间隔，你能够相应地[以编程方式缩放预配吞吐量](../set-throughput.md#update-throughput-on-a-database-or-a-container)，从而优化成本。|
|项大小|数据项（例如文档）的大小，范围从 1 KB 到 2 MB。 你可以为多个示例项添加估算。 <br/><br/>你还可以 **上传示例 (JSON)** 文档以获得更准确的估计。<br/><br/>如果你的工作负荷在同一容器中有多个类型的项（包含不同的 JSON 内容），则可以上传多个 JSON 文档并获得估计值。 可以使用“添加新项”按钮添加多个示例 JSON 文档。|
| 属性数 | 每项的平均属性数。 |
|点读取数/秒 |每个区域每秒的预期点读取操作数。 点读取指对单项 ID 和分区键执行键/值查找。 点读取操作不同于查询读取操作。 若要了解有关点读取的详细信息，请参阅[读取数据的选项](../optimize-cost-reads-writes.md#reading-data-point-reads-and-queries)一文。 如果你的工作负载模式是可变的，则你可以提供高峰期和非高峰期的预期点读取操作数。 |
|创建数/秒 |每个区域每秒的预期创建操作数。 |
|更新次数/秒 |每个区域每秒的预期更新操作数。 |
|删除数/秒 |每个区域每秒的预期删除操作数。 |
|Queries/sec |每个区域每秒的预期查询数。 为准确估算，请使用查询的平均成本或输入查询在 Azure 门户中使用的查询统计信息时所使用的吞吐量（RU/秒）。 |
| 每个查询的平均 RU/秒费用 | 默认情况下，每个区域每秒的平均查询成本估值为 10 RU/秒。 你可以根据估算的查询费用及 RU/秒的收费情况来提高或降低此价格。|

还可以使用“保存估计值”按钮下载包含当前估计值的 CSV 文件。

:::image type="content" source="../media/estimate-ru-with-capacity-planner/advanced-mode-sql-api.png" alt-text="Capacity Planner 高级模式" border="true":::

Azure Cosmos DB 容量规划器中显示的价格是基于吞吐量和存储的公共定价费率进行评估的。 所有价格显示为美元。 若要按区域查看所有费率，请参阅 [Azure Cosmos DB 定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。  

## <a name="next-steps"></a>后续步骤

* 如果只知道现有分片和复制数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
* 详细了解 [Azure Cosmos DB 的定价模型](../how-pricing-works.md)。
* 创建新的 [Cosmos 帐户、数据库和容器](create-cosmosdb-resources-portal.md)。
* 了解如何[优化预配吞吐量成本](../optimize-cost-throughput.md)。
* 了解如何[借助保留容量优化成本](../cosmos-db-reserved-capacity.md)。

