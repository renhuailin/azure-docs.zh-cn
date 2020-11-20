---
title: 规划和管理 Azure Cosmos DB 的成本
description: 了解如何使用 Azure 门户中的成本分析来规划和管理 Azure Cosmos DB 的成本。
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: a73d155d20d09573ad5e1b2597678291aea4c92b
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980569"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>规划和管理 Azure Cosmos DB 的成本
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

本文介绍如何计划和管理 Azure Cosmos DB 的成本。 首先，使用 Azure Cosmos DB 容量计算器来估计工作负荷成本，然后再创建任何资源。 稍后，你可以查看预估成本并开始创建资源。

开始使用 Azure Cosmos DB 资源后，使用成本管理功能来设置预算和监视成本。 你还可以查看预测成本并确定支出趋势，以确定你可能想要采取的措施。Azure Cosmos DB 的成本仅是 Azure 帐单中每月费用的一部分。 尽管本文介绍了如何规划和管理 Azure Cosmos DB 的成本，但你需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）计费。

## <a name="prerequisites"></a>先决条件

### <a name="provisioned-throughput-or-serverless"></a>预配吞吐量或无服务器

Azure Cosmos DB 支持两种类型的容量模式： [预配吞吐量](set-throughput.md) 和 [无服务器](serverless.md)。 在这两种模式下，为 Azure Cosmos DB 使用付费的方式会有所不同，因此请务必选择最适合工作负荷的模式。 有关如何进行此选择的指南和建议，请参阅 [如何在预配吞吐量和无服务器之间进行选择](throughput-serverless.md) 。

### <a name="cost-analysis"></a>成本分析

成本管理中的成本分析支持大多数 Azure 帐户类型，但不支持所有帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，至少需要一个 Azure 帐户的读取权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="estimating-provisioned-throughput-costs-before-using-azure-cosmos-db"></a>使用 Azure Cosmos DB 估算预配的吞吐量成本

如果计划在预配的吞吐量模式下使用 Azure Cosmos DB，请使用 [Azure Cosmos DB 容量计算器](https://cosmos.azure.com/capacitycalculator/) 来估算成本，然后再在 Azure Cosmos 帐户中创建资源。 容量计算器用于估算所需的吞吐量和工作负荷的成本。 将 Azure Cosmos 数据库和容器配置为具有适当数量的预配吞吐量，或 [请求单位 (RU/秒) ](request-units.md)，以优化成本和性能。 需要输入详细信息，例如 API 类型、区域数、项大小、每秒读/写请求数、存储的总数据量以获取成本估算。 若要了解有关容量计算器的详细信息，请参阅 [估计](estimate-ru-with-capacity-planner.md) 文章。

以下屏幕截图显示了使用容量计算器的吞吐量和成本估计：

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Azure Cosmos DB 容量计算器中的成本估算":::

## <a name="estimating-serverless-costs-before-using-azure-cosmos-db"></a><a id="estimating-serverless-costs"></a> 使用 Azure Cosmos DB 之前估计无服务器成本

如果打算在无服务器模式下使用 Azure Cosmos DB，则需要估计每月可能会消耗多少个 [请求单位](request-units.md) 和存储 GB 的存储。 您可以通过计算每月发出的数据库操作的数量来估算所需的请求单位数，并按相应的 RU 成本乘以它们的数量。 下表列出了常见数据库操作的估计 RU 费用：

| Operation | 估计成本 | 说明 |
| --- | --- | --- |
| 创建项 | 5 RU | 要编制索引的属性少于5的 1 KB 项的平均开销 |
| 更新项 | 10 RU | 要编制索引的属性少于5的 1 KB 项的平均开销 |
| 按 ID 和分区键读取单个项 (点读取)  | 1 RU | 1 KB 项的平均开销 |
| 删除项 | 5 RU | |
| 执行查询 | 10 RU | 充分利用 [索引](index-overview.md) 并返回100结果的查询的平均开销 |

> [!IMPORTANT] 
> 注意上表中的说明。 若要更准确地估计操作的实际成本，可以使用 [Azure Cosmos 模拟器](local-emulator.md) 并 [度量操作的确切 RU 成本](find-request-unit-charge.md)。 尽管 Azure Cosmos 模拟器不支持无服务器的支持，但它会报告数据库操作的标准 RU 费用，并可用于此估算。

计算出每个月可能消耗的请求单位和存储空间总数后，以下公式将返回您的成本估算值： **( [请求单位数]/1000000 * $0.25) + ( [GB 存储空间] * $0.25)**。

> [!NOTE]
> 上面示例中所示的成本仅用于演示目的。 有关最新定价信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。

## <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 门户中查看估计成本

开始使用 Azure 门户中 Azure Cosmos DB 资源时，可以看到估计的成本。 使用以下步骤查看成本估算：

1. 登录到 Azure 门户并导航到 Azure Cosmos 帐户。
1. 请参阅 **概述** 部分。
1. 检查底部的 **成本** 图表。 此图显示了在可配置时间段内的当前成本估计值：
1. 创建新的容器，如图形容器。
1. 输入工作负载所需的吞吐量，例如 400 RU/秒。 输入吞吐量值后，可以看到价格估算，如以下屏幕截图所示：

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Azure 门户中的成本估算":::

如果你的 Azure 订阅有支出限制，Azure 将阻止你支出支出。 创建和使用 Azure 资源时，将使用信用额度。 达到信用额度限制时，将在该计费周期的剩余时间内禁用部署的资源。 你无法更改信用额度，但可以将其删除。 有关支出限制的详细信息，请参阅 [Azure 支出限制](https://docs.microsoft.com/azure/billing/billing-spending-limit)。

你可以通过 Azure 企业协议货币承诺信用额度支付 Azure Cosmos DB 费用。 但是，不能使用货币承诺信用额度来支付第三方产品和服务（包括来自 Azure Marketplace 的产品和服务）的费用。

## <a name="monitor-costs"></a>监视成本

在 Azure Cosmos DB 中使用资源时，会产生成本。 资源使用情况单位成本因时间间隔 (秒、分钟、小时和天) 或请求单位使用情况而异。 一旦开始使用 Azure Cosmos DB，就会产生成本，你可以在 Azure 门户的 " [成本分析](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) " 窗格中看到它们。

使用成本分析时，可以在关系图和表中查看不同时间间隔的 Azure Cosmos DB 成本。 一些示例如下：日、当前、上个月和年。 还可以对比预算和预测的成本来查看成本。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势并了解超支可能发生的位置。 如果您已经创建了预算，还可以轻松地查看他们超出的位置。 

查看成本分析中的 Azure Cosmos DB 成本：

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户中打开范围，并在菜单中选择 " **成本分析** "。 例如，在 " **订阅**" 中，从列表中选择一个订阅，然后在菜单中选择 "  **成本分析** "。 选择 " **作用域** " 可切换到成本分析中的其他作用域。

1. 默认情况下，在第一个圆环图中显示所有服务的成本。 选择图表中标记为 "Azure Cosmos DB" 的区域。

1. 若要降低单个服务（如 Azure Cosmos DB）的成本，请选择 " **添加筛选器** "，然后选择 " **服务名称**"。 然后从列表中选择 " **Azure Cosmos DB** "。 下面是一个示例，显示只是 Azure Cosmos DB 的成本：
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="通过成本分析窗格监视成本":::

在前面的示例中，你将看到2月 Azure Cosmos DB 的当前成本。此图表还包含按位置和按资源组 Azure Cosmos DB 成本。

## <a name="create-budgets"></a>创建预算

你可以创建 [预算](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 来管理成本，并创建 [警报](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ，以自动通知利益干系人支出异常和超支风险的利益干系人。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果你希望在监视中出现更多粒度，则可以在 Azure 中创建针对特定资源或服务的筛选器。 筛选器可帮助确保不会意外地创建新资源，从而降低成本。 有关创建预算时筛选器选项的详细信息，请参阅 [组和筛选选项](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

你还可以将 [成本数据导出](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 到存储帐户。 当你需要或其他人进行额外的数据分析以获得成本时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 您可以按每天、每周或每月计划导出您的成本，并设置自定义的日期范围。 建议使用导出成本数据来检索成本数据集。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，详细了解 Azure Cosmos DB 中定价的工作方式：

* [Azure Cosmos DB 中的定价模型](how-pricing-works.md)
* [在 Azure Cosmos DB 中优化预配的吞吐量成本](optimize-cost-throughput.md)
* [优化 Azure Cosmos DB 中的查询成本](./optimize-cost-reads-writes.md)
* [优化 Azure Cosmos DB 中的存储成本](optimize-cost-storage.md)
* 了解 [如何通过 Azure 成本管理优化云投资](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
* 详细了解如何通过 [成本分析](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
* 了解如何 [防止意外成本](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
* 采用 [成本管理](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引导式学习课程。
