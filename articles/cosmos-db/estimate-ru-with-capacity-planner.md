---
title: 使用 Azure Cosmos DB Capacity Planner 估算成本
description: 可以使用 Azure Cosmos DB Capacity Planner 估算你的工作负荷所需的吞吐量（RU/秒）和成本。 本文介绍了如何使用新版本的 Capacity Planner 来估算所需的吞吐量和成本。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/29/2021
ms.author: dech
ms.openlocfilehash: 6041b58d5834afe3356778207083627d7bbe10d9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936969"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>使用 Azure Cosmos DB Capacity Planner 估算 RU/秒
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

针对你的工作负荷为 Azure Cosmos 数据库和容器配置适当数量的预配吞吐量（也称为[请求单位（RU/秒）](request-units.md)）对于优化成本和性能非常重要。 本文介绍了如何使用 Azure Cosmos DB [Capacity Planner](https://cosmos.azure.com/capacitycalculator/) 来估算你的工作负荷所需的吞吐量（RU/秒）和成本。 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>如何使用 Azure Cosmos DB Capacity Planner 评估吞吐量和成本

Capacity Planner 可在两种模式下使用。

|**模式**  |**说明**  |
|---------|---------|
|基本|提供快速、大概的 RU/秒和成本估算。 此模式为索引编制策略、一致性和其他参数采用默认的 Azure Cosmos DB 设置。 <br/><br/>当评估要在 Azure Cosmos DB 上运行的潜在工作负荷时，可以使用基本模式进行快速、大概的估算。 若要了解详细信息，请参阅如何[使用基本模式估算成本](#basic-mode)。|
|高级|提供更详细的 RU/秒和成本估算，还可以调整其他设置 - 索引编制策略、一致性级别以及影响成本和吞吐量的其他参数。 <br/><br/>当为新项目估算 RU/秒或需要更详细的估算时，请使用高级模式。 若要了解详细信息，请参阅如何[使用高级模式估算成本](#advanced-mode)。|

## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a><a id="basic-mode"></a>使用基本模式估算预配吞吐量和成本
若要使用基本模式针对你的工作负荷进行快速估算，请导航到 [Capacity Planner](https://cosmos.azure.com/capacitycalculator/)。 根据你的工作负荷输入以下参数：

|**输入**  |**说明**  |
|---------|---------|
| API |选择帐户的 API 类型。 |
|区域数量|Azure Cosmos DB 面向所有 Azure 区域提供。 选择你的工作负荷所需的区域数目。 你可以将任意数量的区域与你的 Cosmos 帐户相关联。 有关更多详细信息，请参阅 Azure Cosmos DB 中的[全局分布](distribute-data-globally.md)。|
|多区域写入|如果你启用[多区域写入](distribute-data-globally.md#key-benefits-of-global-distribution)，则你的应用程序可以在任何 Azure 区域中进行读取和写入。 如果你禁用多区域写入，则你的应用程序可以将数据写入到单个区域。 <br/><br/> 如果你预计在不同区域中会有需要低延迟写入的主动-主动工作负荷，请启用多区域写入。 例如，在不同区域中大量向数据库写入数据的 IOT 工作负荷。 <br/><br/> 多区域写入可保证 99.999% 的读取和写入可用性。 与单一写入区域相比，多区域写入需要更多的吞吐量。 若要了解详细信息，请参阅[单一写入区域与多个写入区域的 RU 有何不同](optimize-cost-regions.md)一文。|
|事务存储中存储的数据总量 |单个区域中的事务存储中存储的预估数据总量 (GB)。|
|分析存储中存储的数据总量 | 如果启用“使用分析存储”选项，则会显示此选项 。 它表示存储在单个区域的分析存储中的预估数据总量 (GB)。  |
|项大小|数据项（例如文档）的估计大小，范围为 1 KB 到 2 MB。 |
|每个区域的每秒读取数|预期的每秒读取操作数。 |
|每个区域的每秒创建数|预期的每秒创建操作数。 |
|每个区域每秒的更新数|预期的每秒更新操作数。 |
|每个区域的每秒删除数|预期的每秒删除操作数。 |

填写所需的详细信息后，选择“计算”。 “成本预估”选项卡将显示存储和预配吞吐量的总成本。 可以在此选项卡中展开“显示详细信息”链接，获取不同 CRUD 请求所需的吞吐量的明细。 每次更改任何字段的值后，请选择“计算”来重新计算估计的成本。

:::image type="content" source="./media/estimate-ru-with-capacity-planner/basic-mode.png" alt-text="Capacity Planner 基本模式" border="true":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a><a id="advanced-mode"></a>使用高级模式估算预配吞吐量和成本

高级模式允许你提供会影响 RU/秒估计值的更多设置。 若要使用此选项，请导航到 [Capacity Planner](https://cosmos.azure.com/capacitycalculator/) 并使用你用于 Azure 的帐户登录该工具。 登录选项位于右侧边角。

在登录后，与“基本”模式中的字段相比，你可以看到更多字段。 根据你的工作负荷输入更多参数。

|**输入**  |**说明**  |
|---------|---------|
|API|Azure Cosmos DB 是一项多模型和多 API 服务。 对于新工作负荷，请选择“SQL (核心) API”。 |
|区域数量|Azure Cosmos DB 面向所有 Azure 区域提供。 选择你的工作负荷所需的区域数目。 你可以将任意数量的区域与你的 Cosmos 帐户相关联。 有关更多详细信息，请参阅 Azure Cosmos DB 中的[全局分布](distribute-data-globally.md)。|
|多区域写入|如果你启用[多区域写入](distribute-data-globally.md#key-benefits-of-global-distribution)，则你的应用程序可以在任何 Azure 区域中进行读取和写入。 如果你禁用多区域写入，则你的应用程序可以将数据写入到单个区域。 <br/><br/> 如果你预计在不同区域中会有需要低延迟写入的主动-主动工作负荷，请启用多区域写入。 例如，在不同区域中大量向数据库写入数据的 IOT 工作负荷。 <br/><br/> 多区域写入可保证 99.999% 的读取和写入可用性。 与单一写入区域相比，多区域写入需要更多的吞吐量。 若要了解详细信息，请参阅[单一写入区域与多个写入区域的 RU 有何不同](optimize-cost-regions.md)一文。|
|默认一致性|Azure Cosmos DB 支持 5 种一致性级别，允许开发人员在一致性、可用性和延迟之间进行权衡。 若要了解详细信息，请参阅[一致性级别](consistency-levels.md)一文。 <br/><br/> 默认情况下，Azure Cosmos DB 使用会话一致性，这可以保证能够在会话中读取你自己的写入。 <br/><br/> 与会话一致性、一致前缀一致性和最终一致性相比，选择强一致性或有限过期一致性将会使读取所需的 RU/秒翻倍。 多区域写入强一致性不受支持，系统将自动默认选择单区域写入强一致性。 |
|索引策略|默认情况下，Azure Cosmos DB 会为所有项中的 [所有属性编制索引](index-policy.md)，以便进行灵活且高效的查询（映射到 **自动** 索引编制策略）。 <br/><br/> 如果选择“关”，则不会为任何属性编制索引。 这将导致最低的写入 RU 开销。 如果你预计仅执行[点读取](/dotnet/api/microsoft.azure.cosmos.container.readitemasync)（键值查找）和/或写入而不执行查询，请选择“关”策略。 <br/><br/> 自定义索引编制策略允许你在索引中包含或排除特定属性，以降低写入吞吐量和存储。 若要了解详细信息，请参阅[索引编制策略](index-overview.md)和[示例索引编制策略](how-to-manage-indexing-policy.md#indexing-policy-examples)这两篇文章。|
|事务存储中存储的数据总量 |单个区域中的事务存储中存储的预估数据总量 (GB)。|
|分析存储中存储的数据总量 | 如果启用“使用分析存储”选项，则会显示此选项 。 它表示存储在单个区域中的分析存储中的预估数据总量 (GB)。  |
|工作负荷模式|如果工作负荷量固定，请选择“稳定”。 <br/><br/> 如果工作负荷量随时间推移而变化，请选择“可变”。  例如，在特定的一天或一个月内变化。 <br/><br/> 如果选择了可变工作负荷选项，则下列设置可用：<ul><li>高峰时间百分比：一个月内工作负荷需要峰值（最高）吞吐量的时间百分比。 <br/><br/> 例如，如果你的工作负荷在工作日的上午 9 点到下午 6 点的营业时间内有大量活动，则高峰时间的百分比为：45 个高峰小时 / 730 小时 / 月 = ~6%。<br/><br/></li><li>高峰时每个区域的每秒读取次数 - 预计的高峰时每秒读取次数。</li><li>高峰时每个区域的每秒写入次数 - 预计的高峰时每秒写入次数。</li><li>非高峰时每个区域的每秒读取次数 - 预计的非高峰时每秒读取次数。</li><li>非高峰时每个区域的每秒写入次数 - 预计的非高峰时每秒写入次数。</li></ul>使用高峰和非高峰时间间隔，你能够相应地[以编程方式缩放预配吞吐量](set-throughput.md#update-throughput-on-a-database-or-a-container)，从而优化成本。|
|项大小|数据项（例如文档）的大小，范围为 1 KB 到 2 MB。 <br/><br/>你还可以 **上传示例 (JSON)** 文档以获得更准确的估计。<br/><br/>如果你的工作负荷在同一容器中有多个类型的项（包含不同的 JSON 内容），则可以上传多个 JSON 文档并获得估计值。 可以使用“添加新项”按钮添加多个示例 JSON 文档。|
|每个区域的每秒读取数|预期的每秒读取操作数。 |
|每个区域的每秒创建数|预期的每秒创建操作数。 |
|每个区域每秒的更新数|预期的每秒更新操作数。 |
|每个区域的每秒删除数|预期的每秒删除操作数。 |

还可以使用“保存估计值”按钮下载包含当前估计值的 CSV 文件。

:::image type="content" source="./media/estimate-ru-with-capacity-planner/advanced-mode.png" alt-text="Capacity Planner 高级模式" border="true":::

Azure Cosmos DB 容量规划器中显示的价格是基于吞吐量和存储的公共定价费率进行评估的。 所有价格显示为美元。 若要按区域查看所有费率，请参阅 [Azure Cosmos DB 定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。  

## <a name="estimating-throughput-for-queries"></a>估计查询所需的吞吐量

Azure Cosmos 容量计算器对工作负荷采取点读取（按 ID 和分区键值读取单个项，例如文档）和点写入的方式。 若要估计查询所需的吞吐量，请对 Cosmos 容器中的代表性数据集运行查询并[获取 RU 开销](find-request-unit-charge.md)。 将 RU 开销乘以你预计每秒运行的查询数，以获得所需的 RU/秒总数。 

例如，如果你的工作负荷需要一个查询 (``SELECT * FROM c WHERE c.id = 'Alice'``)，该查询每秒运行 100 次，该查询的 RU 开销为 10 RU，则你总共需要 1000 RU/秒（100 查询/秒 * 10 RU/查询）来为这些请求提供服务。 将这些 RU/秒与工作负荷中发生的任何读取或写入操作所需的 RU/秒相加。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure Cosmos DB 的定价模型](how-pricing-works.md)。
* 创建新的 [Cosmos 帐户、数据库和容器](create-cosmosdb-resources-portal.md)。
* 了解如何[优化预配吞吐量成本](optimize-cost-throughput.md)。
* 了解如何[借助保留容量优化成本](cosmos-db-reserved-capacity.md)。