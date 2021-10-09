---
title: 弹性数据映射
description: 本文解释 Azure Purview 中弹性数据映射的概念
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 08/18/2021
ms.custom: template-concept
ms.openlocfilehash: 00b6429e14dcb04a76abbc1a8d49a29b220c7c06
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607059"
---
# <a name="elastic-data-map-in-azure-purview"></a>Azure Purview 中的弹性数据映射

Azure Purview 数据映射为数据发现和数据治理奠定了基础。 它可以捕获混合、本地和多云环境中的分析、软件即服务 (SaaS) 和操作系统上存在的企业数据的相关元数据。 Purview 数据映射会通过其内置扫描和分类系统自动掌握最新动态。 在 UI 中，开发人员可以使用开源的 Apache Atlas API 进一步以编程方式来与数据映射交互。

## <a name="elastic-data-map"></a>弹性数据映射

所有 Azure Purview 帐户都有一个可弹性增长的数据映射，其起始大小为一个容量单位。 弹性数据映射根据弹性窗口中的请求负载进行纵向扩展和缩减（[查看当前限制](how-to-manage-quotas.md)）。 这些限制应涵盖大多数数据环境。 但是，如果需要更大的容量，[可以创建支持票证](#request-capacity)。

## <a name="data-map-capacity-unit"></a>数据映射容量单位

弹性数据映射附带以容量单位 (CU) 表示的操作吞吐量和存储组件。 所有 Azure Purview 帐户默认附带一个容量单位，可根据使用情况弹性增长。 每个数据映射容量单位的吞吐量限制为每秒 25 次操作，元数据存储限制为 2 GB。  

### <a name="operations"></a>Operations

操作是 Purview 数据映射的吞吐量度量。 它们包括对数据映射中存储的元数据执行的创建、读取、写入、更新和删除操作。 下面列出了一些操作示例：

- 在数据映射中创建资产
- 为资产添加关系，例如所有者、专员、父级、世系等。
- 编辑资产以添加业务元数据，例如说明、术语表术语等。
- 在搜索结果页中返回结果的关键字搜索。

### <a name="storage"></a>存储

存储是数据映射的第二个组件，包含技术、业务、操作和语义元数据。

技术元数据包括通过 Purview [扫描](concept-scans-and-ingestion.md)发现的架构、数据类型、列等。 业务元数据包括说明、术语表术语等项的自动（例如，从 Power BI 数据集提升的内容，或 SQL 表中的说明）和手动标记。 语义元数据的例子包括集合到数据源或分类的映射。 操作元数据包括数据工厂复制和数据流活动运行状态以及运行时间。

## <a name="work-with-elastic-data-map"></a>使用弹性数据映射

- 可自动缩放的弹性数据映射 – 数据映射的大小最初只是一个容量单位，但可根据负载自动缩放。 对于大多数组织而言，此功能可以实现更大的节省，同时还能以较低的价格启动数据治理项目。 此功能会影响定价。

- 增强的扫描和引入 – 可以通过扫描和引入过程来跟踪和控制数据资产、分类与世系的数量分布。 此功能会影响定价。

## <a name="scenario"></a>方案

Claudia 是 Contoso 的 Azure 管理员，她想要通过 Azure 门户预配新的 Azure Purview 帐户。 预配时，她并不知道为了支持平台的将来状态而需要的 Purview 数据映射大小。 但她知道，Purview 数据映射是按容量单位计费的，这种计费方式受存储和操作吞吐量的影响。 她想要预配最小的数据映射以保持较低的成本，并使数据映射大小根据使用情况弹性增长。  

Claudia 可以创建一个 Purview 帐户，其默认数据映射大小为 1 个容量单位，并且映射大小可自动纵向扩展和缩减。 自动缩放功能还允许根据特定时段内的间歇性或计划内数据突发来调整容量。 Claudia 遵循预配体验中的后续步骤设置网络配置并完成设置。  

在 Azure Monitor 指标页中，Claudia 可以查看数据映射存储和操作吞吐量的消耗情况。 她可以进一步设置要在存储或操作吞吐量达到特定的限制时发出的警报，以监视新 Purview 帐户的消耗量和计费情况。  

## <a name="data-map-billing"></a>数据映射计费

客户按一个容量单位（每秒 25 次操作和 2 GB）付费，额外的费用按照一小时内累积额外消耗的每一容量单位计收。 数据映射操作以每秒 25 次操作为增量缩放，元数据存储以 2 GB 大小为增量缩放。 Purview 数据映射可以在弹性窗口内自动纵向扩展和缩减（[查看当前限制](how-to-manage-quotas.md)）。 但是，若要获取下一个级别的弹性窗口，需要创建支持票证。

数据映射容量单位的操作吞吐量和存储有上限。 如果存储超过当前容量单位，则即使未使用操作吞吐量，也会向客户收取下一个容量单位的费用。 下表显示了数据映射容量单位范围。 如果数据映射容量单位的数量超过 100，请联系支持人员。

|数据映射容量单位  |吞吐量（操作次数/秒）   |存储容量 (GB)|
|----------|-----------|------------|
|1    |25      |2     |
|2    |50      |4     |
|3    |75      |6     |
|4    |100     |8     |
|5    |125     |10    |
|6    |150     |12   |
|7    |175      |14     |
|8    |200     |16    |
|9    |225      |18    |
|10    |250     |20    |
|..   |..      |..     |
|100    |2500     |200   |

### <a name="billing-examples"></a>计费示例

- 给定小时内的 Purview 数据映射操作吞吐量小于或等于 25 次操作/秒，并且存储大小为 1 GB。 将向客户计收一个容量单位的费用。

- 给定小时内的 Purview 数据映射操作吞吐量小于或等于 25 次操作/秒，并且存储大小为 3 GB。 将向客户计收两个容量单位的费用。

- 给定小时内的 Purview 数据映射操作吞吐量为 50 次操作/秒，并且存储大小为 1 GB。 将向客户计收两个容量单位的费用。

- 给定小时内的 Purview 数据映射操作吞吐量为 50 次操作/秒，并且存储大小为 5 GB。 将向客户计收三个容量单位的费用。

- 给定小时内的 Purview 数据映射操作吞吐量为 250 次操作/秒，并且存储大小为 5 GB。 将向客户计收 10 个容量单位的费用

### <a name="detailed-billing-example"></a>详细计费示例

以下数据映射示例显示了从中午 12 点到下午 6 点的六小时时段内，元数据存储消耗量不断增长且每秒操作次数可变的数据映射。 图中的红线表示此六小时时段内的每秒操作次数消耗量，蓝色虚线表示元数据存储消耗量：

:::image type="content" source="./media/concept-elastic-data-map/operations-and-metadata.png" alt-text="该图表描绘了一段时间内的操作次数和元数据增长情况。":::

每个数据映射容量单位支持每秒 25 次操作和 2 GB 元数据存储。 数据映射按小时计费。 你将按照一小时内所需的最大数据映射容量单位数付费。 有时，你可能需要在一小时内每秒完成更多次的操作，这就会增加该小时内所需的容量单位数。 而有时，每秒操作次数使用量可能较低，但仍然需要大量的元数据存储。 一小时内所需的容量单位数是由元数据存储决定的。

下表显示了此计费示例中每小时使用的每秒最大操作次数和元数据存储： 

:::image type="content" source="./media/concept-elastic-data-map/billing-table.png" alt-text="该表描绘了一段时间内的最大操作次数和元数据增长情况。":::

根据此时段内数据映射每秒操作次数和元数据存储的消耗量，在此六小时时段内，将按 20 个容量单位小时数 (1 + 3 + 4 + 3 + 6 + 3) 计收此数据映射的费用：

:::image type="content" source="./media/concept-elastic-data-map/billing-capacity-hours.png" alt-text="该表描绘了一段时间内的 CU 小时数。":::

>[!Important]
>Purview 数据映射可以在弹性窗口内自动纵向扩展和缩减（[查看当前限制](how-to-manage-quotas.md)）。 若要获取下一个级别的弹性窗口，需要创建支持票证。

## <a name="request-capacity"></a>请求容量

如果你正在使用极大的数据集或大规模的环境，因而需要更大的容量来实现弹性数据映射，可以通过[创建支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)来请求增大弹性窗口的容量。

选择“服务和订阅限制(配额)”，并通过选择要为其请求更大容量的 Purview 帐户来完成屏幕上的说明。

:::image type="content" source="./media/concept-elastic-data-map/increase-limit.png" alt-text="显示支持案例创建的屏幕，其中选择了“增加限制”选项。":::

在说明中，尽可能多地提供有关你的环境的信息，以及你希望请求的额外容量。

## <a name="summary"></a>总结

Purview 通过弹性数据映射为客户提供低成本的解决方案，让客户不受阻碍地开启数据治理历程。
Purview 数据映射可以从低至 1 个容量单位的大小开始按照即用即付模式弹性增长。
客户在预配时无需担心如何为其数据资产选择适当的数据映射大小，而且将来也无需由于大小限制而处理平台迁移。

## <a name="next-steps"></a>后续步骤

- [创建 Azure Purview 帐户](create-catalog-portal.md)
- [Purview 定价](https://azure.microsoft.com/pricing/details/azure-purview/)
