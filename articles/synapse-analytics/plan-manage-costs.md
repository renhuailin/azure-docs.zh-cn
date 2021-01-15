---
title: 计划管理 Azure Synapse Analytics 的成本
description: 了解如何使用 Azure 门户中的成本分析来规划和管理 Azure Synapse Analytics 的成本。
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: ab772043c681684836e3c488419584d94dd0b45a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220637"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>规划和管理 Azure Synapse Analytics 的成本

本文介绍如何为 Azure Synapse Analytics 规划和管理成本。 首先，使用 Azure 定价计算器来帮助规划 Azure Synapse 成本，然后再添加任何服务资源来估算成本。 接下来，当你添加 Azure Synapse 资源时，请查看预估成本。

开始使用 Azure Synapse 资源后，使用成本管理功能来设置预算和监视成本。 你还可以查看预测成本并确定支出趋势，以确定你可能想要采取的措施。Azure Synapse 的成本仅是 Azure 帐单中每月费用的一部分。 尽管本文介绍了如何为 Azure Synapse 规划和管理成本，但你需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。

## <a name="prerequisites"></a>先决条件

成本管理中的成本分析支持大多数 Azure 帐户类型，但不支持所有帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，至少需要一个 Azure 帐户的读取权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 之前估计成本

添加 Azure Synapse 分析之前，请使用 [azure 定价计算器](https://azure.microsoft.com/pricing/calculator/) 估算成本。

Azure Synapse 具有不同费用的各种资源，如以下成本估算所示。 

![示例显示 Azure 定价计算器中的预估成本](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>了解 Azure Synapse Analytics 的完整计费模式

Azure Synapse 在 Azure 基础结构上运行，当你部署新资源时，会在 azure 基础结构上累算费用和 Azure Synapse。 了解其他基础结构可能会产生成本，这一点很重要。 你需要在对已部署的资源进行更改时管理该成本。 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>通常通过 Azure Synapse Analytics 进行计费的成本

为 Azure Synapse 创建资源时，还会创建其他 Azure 服务的资源。 它们包括：

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>删除资源后可能会产生成本

删除 Azure Synapse 资源后，以下资源可能会继续存在。 它们会持续产生成本，直到你将其删除。

- Data Lake Storage Gen2

### <a name="using-monetary-credit-with-azure-synapse"></a>使用 Azure Synapse 的货币信用额度 

可以通过 EA 货币承诺信用额度支付 Azure Synapse 费用。 但是，不能使用 EA 货币承诺信用额度来支付第三方产品和服务（包括来自 Azure Marketplace 的产品和服务）的费用。

## <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 门户中查看估计成本

为 Azure Synapse Analytics 创建资源时，可以看到估计的成本。 工作区具有通过工作区创建的无服务器 SQL 池。 在运行查询之前，无服务器 SQL 池不会产生费用。 需要在工作区中创建其他资源，例如专用的 SQL 池和无服务器 Apache Spark 池。

若要创建 <ResourceName> 并查看预计价格：

1. 导航到 Azure 门户中的服务。
2. 创建资源。
3. 查看摘要中显示的预计价格。
4. 完成资源的创建。

![显示创建资源时预估成本的示例](./media/plan-manage-costs/create-workspace-cost.png)


如果你的 Azure 订阅有支出限制，Azure 将阻止你支出支出。 创建和使用 Azure 资源时，将使用信用额度。 达到信用额度限制时，将在该计费周期的剩余时间内禁用部署的资源。 你无法更改信用额度，但可以将其删除。 有关支出限制的详细信息，请参阅 [Azure 支出限制](../cost-management-billing/manage/spending-limit.md)。

## <a name="monitor-costs"></a>监视成本

使用 Azure Synapse 资源时，会产生成本。 Azure 资源使用情况单位成本按时间间隔 (秒、分钟、小时和天) 或单位使用情况变化 (字节、mb 等） ) 。在开始使用 Azure Synapse 中的资源时，将立即开始使用 Azure 中的资源时，会产生成本，你可以在 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中看到成本。

使用成本分析时，可以在关系图和表中查看不同时间间隔的 Azure Synapse 分析成本。 有些示例是按天、当前和上个月和年。 你还可以查看预算和预测成本的成本。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势。 你会看到超支可能发生的位置。 如果已创建预算，还可以轻松地查看超出的位置。

若要查看成本分析中的 Azure Synapse 成本：

1. 登录到 Azure 门户。
2. 在 Azure 门户中打开作用域，然后在 "" 菜单中选择 " **成本分析** "。 例如，在 " **订阅**" 中，从列表中选择一个订阅，然后在菜单中选择 "  **成本分析** "。 选择 " **作用域** " 可切换到成本分析中的其他作用域。
3. 默认情况下，服务的成本显示在第一个圆环图中。 选择图表中标记为 "Azure Synapse" 的区域。

最初打开成本分析时，会显示实际的每月成本。 下面是显示所有每月使用成本的示例。

![显示订阅的累计成本的示例](./media/plan-manage-costs/actual-monthly-costs.png)

- 若要缩小单个服务（如 Azure Synapse）的成本，请选择 " **添加筛选器** "，然后选择 " **服务名称**"。 然后，选择 " **Azure Synapse Analytics**"。

下面是一个示例，显示了 Azure Synapse 的费用。

![显示 ServiceName 的累计成本的示例](./media/plan-manage-costs/filtered-monthly-costs.png)

在前面的示例中，您将看到该服务的当前成本。 还显示了按资源组列出的 Azure 区域 (位置) 和 Azure Synapse 成本）的成本。 从这里，你可以自行探索成本。

## <a name="create-budgets"></a>创建预算

你可以创建 [预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 来管理成本，并创建 [警报](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ，以自动通知利益干系人支出异常和超支风险的利益干系人。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果你希望在监视中出现更多粒度，则可以在 Azure 中创建针对特定资源或服务的筛选器。 筛选器可帮助确保不会意外地创建新资源，从而降低成本。 有关创建预算时筛选器选项的详细信息，请参阅 [组和筛选选项](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

你还可以将 [成本数据导出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 到存储帐户。 当你需要或其他人进行额外的数据分析以获得成本时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 您可以按每天、每周或每月计划导出您的成本，并设置自定义的日期范围。 建议使用导出成本数据来检索成本数据集。


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>为 Azure Synapse 管理和降低成本的其他方式 

### <a name="serverless-sql-pool"></a>无服务器 SQL 池

若要了解有关无服务器 SQL 池成本的详细信息，请参阅 [Azure Synapse Analytics 中无服务器 sql 池的成本管理](./sql/data-processed.md)

### <a name="dedicated-sql-pool"></a>专用 SQL 池

可以通过在资源不使用时暂停资源来控制专用 SQL 池的成本。 例如，如果晚上和周末不使用数据库，那么可以在这些时间暂停数据库的使用，然后在白天时恢复使用。 有关详细信息，请参阅 [通过 Azure 门户在专用 SQL 池中暂停和恢复计算](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

<!-- ### Serverless Apache Spark pool -->

### <a name="data-integration---pipelines-and-data-flows"></a>数据集成-管道和数据流 

若要了解有关数据集成成本的详细信息，请参阅 [计划和管理 Azure 数据工厂的成本](../data-factory/plan-manage-costs.md)

## <a name="next-steps"></a>后续步骤

- 了解 [如何通过 Azure 成本管理优化云投资](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何 [防止意外成本](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 采用 [成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引导式学习课程。
- 了解[Azure 机器学习](../machine-learning/concept-plan-manage-cost.md)的计划和管理成本