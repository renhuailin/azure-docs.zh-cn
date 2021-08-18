---
title: 计划管理 Azure Synapse Analytics 的成本
description: 了解如何使用 Azure 门户中的成本分析来规划和管理 Azure Synapse Analytics 的成本。
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: 1527888458a494e0b2d7177246c991228440d255
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860527"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>计划管理 Azure Synapse Analytics 的成本

本文介绍如何计划和管理 Azure Synapse Analytics 的成本。 首先，使用 Azure 定价计算器来帮助规划 Azure Synapse Analytics 成本，然后再添加任何服务资源来估算成本。 接下来，在添加 Azure 资源时，查看预估成本。 在开始使用 Azure Synapse Analytics 资源之后，请使用“成本管理”功能来设置预算和监视成本。 还可以查看预测的成本并识别支出趋势，以确定可能需要实施措施的领域。Azure Synapse Analytics 的成本只是 Azure 账单中每月成本的一部分。 尽管本文介绍了如何为 Azure Synapse Analytics 规划和管理成本，但你需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。

## <a name="prerequisites"></a>先决条件

成本管理中的成本分析支持大多数 Azure 帐户类型，但不支持所有帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 之前估计成本

添加 Azure Synapse 分析之前，请使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)估算成本。

Azure Synapse 具有各种资源，费用各不相同，如以下成本估算所示。 

![显示 Azure 定价计算器中的预估成本的示例](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>了解 Azure Synapse Analytics 的完整计费模式

Azure Synapse 在 Azure 基础结构上运行，部署新资源时，会随 Azure Synapse 产生成本。 需要了解的是，其他基础结构可能会产生成本。 

### <a name="how-youre-charged-for-azure-synapse-analytics"></a>Azure Synapse Analytics 的收费方式

创建或使用 Azure Synapse Analytics 资源时，会根据以下计费标准收费：

- 数据浏览和数据仓储 
    - 专用的 SQL 池 - 根据 DWU 块数和运行时间收费。
    - 存储 - 根据存储的 TB 数向你收费。
    - 无服务器 SQL 池 - 按照处理的 TB 数据收费。
- Apache Spark 池 - 按实例数和运行小时数收费。
- 数据集成 
    - 编排活动运行次数 - 按活动运行次数收费。
    - 数据移动 – 对于在 Azure Integration Runtime 上运行的复制活动，按使用的 DIU 数和任务执行持续时间付费。
    - 数据流 vCore 小时数 – 对于数据流执行和调试，按计算类型、vCore 数量和任务执行持续时间付费。

在计费周期结束时，将对每个计量标准的费用求和。 帐单或发票显示的费用是所有 Azure Synapse Analytics 成本的一部分。 每个计量标准都有单独的一行项目。

### <a name="other-costs-that-might-accrue-with-azure-synapse-analytics"></a>Azure Synapse Analytics 可能产生的其他成本

为 Azure Synapse 创建资源时，还会创建其他 Azure 服务的资源。 它们包括：

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>删除资源后可能会产生成本

删除 Azure Synapse 资源后，以下资源可能会继续存在。 它们会持续产生成本，直到将其删除。

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>使用 Azure Synapse 的 Azure 预付款额度 

可以使用 Azure 预付款额度支付 Azure Synapse 费用。 但是，不能使用 Azure 预付款额度来支付第三方产品和服务的费用（包括 Azure 市场中的第三方产品和服务）。


## <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 门户中查看估计成本

为 Azure Synapse Analytics 创建资源时，可以看到估计的成本。 工作区具有通过工作区创建的无服务器 SQL 池。 在运行查询之前，无服务器 SQL 池不会产生费用。 需要在工作区中创建其他资源，例如专用的 SQL 池和无服务器 Apache Spark 池。

若要创建 Azure Synapse Analytics 工作区并查看预计价格：

1. 导航到 Azure 门户中的服务。
2. 创建资源。
3. 查看摘要中显示的估计价格。
4. 完成资源创建过程。

![显示创建资源时的估计成本的示例](./media/plan-manage-costs/create-workspace-cost.png)


如果你的 Azure 订阅有支出限制，Azure 将阻止超过额度金额的支出。 创建和使用 Azure 资源时，将使用额度。 达到额度限制时，将在该计费周期的剩余时间内禁用部署的资源。 你无法更改额度限制，但可以删除该限制。 有关支出限制的详细信息，请参阅 [Azure 支出限制](../cost-management-billing/manage/spending-limit.md)。

## <a name="monitor-costs"></a>监视成本

使用 Azure Synapse 资源时，会产生费用。 Azure 资源使用情况单位成本因时间间隔（秒、分钟、小时和天）或（字节、MB 等）的单位使用情况而异。一旦开始使用 Azure Synapse 中的资源，就会产生费用，可以在[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中看到这些费用。

使用成本分析时，可以在关系图和表中查看不同时间间隔的 Azure Synapse 分析成本。 有些示例是按天、当前、上个月和年划分的。 还可以对比预算和预测的成本来查看成本。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势。 你会看到可能出现超支的地方。 如果已创建预算，还可以轻松查看超支的地方。

在成本分析中查看 Azure Synapse 成本：

1. 登录到 Azure 门户。
2. 在 Azure 门户中打开范围（订阅或资源组），然后在菜单中选择“成本分析”。 例如，转到“订阅”，从列表中选择订阅，然后在菜单中选择“成本分析” 。 选择“范围”，在成本分析中切换到不同的范围。
3. 默认情况下，服务的成本显示在第一个圆环图中。 选择图表中标记为“Azure Synapse”的区域。

最初打开成本分析时，会显示实际的每月成本。 下面是显示所有每月使用成本的示例。

![显示订阅累计成本的示例](./media/plan-manage-costs/actual-monthly-costs.png)

- 若要缩小范围以查看单项服务（如 Azure Synapse）的成本，请选择“添加筛选器”，然后选择“服务名称” 。 接下来，选择“Azure Synapse Analytics”。

下面的示例只显示了 Azure Synapse 的费用。

![显示 ServiceName 累积费用的示例](./media/plan-manage-costs/filtered-monthly-costs.png)

在前面的示例中，你将看到该服务的当前成本。 还显示了按 Azure 区域（位置）的成本和按资源组的 Azure Synapse 成本。 从这里，你可以自行探索成本。

## <a name="create-budgets"></a>创建预算

可以创建[预算](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本，并创建[警报](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果希望增加监视中的精度，可以在 Azure 中使用筛选器为特定资源或服务创建预算。 筛选器可帮助确保不会意外创建会产生额外成本的新资源。 有关创建预算时可用的筛选选项的详细信息，请参阅[分组和筛选选项](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

还可以将[成本数据导出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)到存储帐户。 当你或其他人需要进行有关成本的额外数据分析时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 可以按每天、每周或每月计划导出成本，并设置自定义的日期范围。 建议导出成本数据来检索成本数据集。

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>管理和降低 Azure Synapse 成本的其他方式 

### <a name="serverless-sql-pool"></a>无服务器 SQL 池

若要了解有关无服务器 SQL 池成本的详细信息，请参阅 [Azure Synapse Analytics 中无服务器 SQL 池的成本管理](./sql/data-processed.md)

### <a name="dedicated-sql-pool"></a>专用 SQL 池

可以通过在资源不使用时暂停资源来控制专用 SQL 池的成本。 例如，如果晚上和周末不使用数据库，那么可以在这些时间暂停数据库的使用，然后在白天时恢复使用。 更多详细信息，请参阅[通过 Azure 门户暂停和恢复专用 SQL 池中的计算](./sql-data-warehouse/pause-and-resume-compute-portal.md?context=/azure/synapse-analytics/context/context)

### <a name="serverless-apache-spark-pool"></a>无服务器 Apache Spark 池

若要控制无服务器 Apache Spark 池的成本，请启用无服务器 Apache Spark 自动暂停功能并相应地设置超时值。  使用 Synapse Studio 进行开发时，工作室将发送“保持连接”消息，使会话保持连接状态，这也是可配置的，因此，请为自动暂停设置短超时值。  完成后，关闭会话，在达到超时值后，Apache Spark 池将自动暂停。
 
在开发过程中，创建多个不同大小的 Apache Spark 池定义。  创建 Apache Spark 池定义免费，你只需为所使用的资源付费。  Azure Synapse 中的 Apache Spark 使用量按 vCore 小时计费，按分钟计算。  例如，使用较大的池大小进行代码开发和验证，同时使用较大的池大小进行性能测试。

### <a name="data-integration---pipelines-and-data-flows"></a>数据集成 - 管道和数据流 

若要了解有关数据集成成本的详细信息，请参阅[计划和管理 Azure 数据工厂的成本](../data-factory/plan-manage-costs.md)

## <a name="next-steps"></a>后续步骤

- 了解[如何通过 Azure 成本管理优化云投资](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何[防止意外成本](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 参与[成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)引导式学习课程。
- 了解 [Azure 机器学习](../machine-learning/concept-plan-manage-cost.md)的计划和管理成本