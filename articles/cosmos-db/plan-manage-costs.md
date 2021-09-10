---
title: 计划和管理 Azure Cosmos DB 的成本
description: 了解如何使用 Azure 门户中的成本分析来计划和管理 Azure Cosmos DB 的成本。
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: f954be4b5ce82bf84bc99fcdab253c48fccf9933
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226991"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>计划和管理 Azure Cosmos DB 的成本
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

本文介绍如何计划和管理 Azure Cosmos DB 的成本。 首先，使用 Azure Cosmos DB 容量计算器来估计工作负荷成本，然后再创建任何资源。 然后，可以查看预估成本并开始创建资源。

开始使用 Azure Cosmos DB 资源后，使用成本管理功能来设置预算和监视成本。 还可以查看预测的成本并确定支出趋势，以确定可能需要实施措施的领域。Azure Cosmos DB 的成本只是 Azure 账单中每月成本的一部分。 尽管本文介绍如何计划和管理 Azure Cosmos DB 的成本，但 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）都需要付费。

## <a name="prerequisites"></a>先决条件

### <a name="provisioned-throughput-or-serverless"></a>预配吞吐量或无服务器

Azure Cosmos DB 支持两种类型的容量模式：[预配吞吐量](set-throughput.md)和[无服务器](serverless.md)。 在这两种模式下，使用 Azure Cosmos DB 的付费方式大不相同，因此请务必选择最适合你工作负荷的模式。 有关如何进行此选择的指南和建议，请参阅《[如何在预配吞吐量和无服务器之间进行选择](throughput-serverless.md)》一文。

### <a name="cost-analysis"></a>成本分析

成本管理中的成本分析支持大多数 Azure 帐户类型，但不支持所有帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="estimate-costs-before-using-azure-cosmos-db"></a>使用 Azure Cosmos DB 之前估算成本

Azure Cosmos DB 提供了两种不同的容量模式：预配吞吐量和无服务器。 在这两种模式下，你可以执行完全相同的数据库操作，但这些操作的收费方式不同。

### <a name="capacity-planning"></a>容量计划

作为估算成本的辅助方法，可以进行容量计划，以便迁移到 Azure Cosmos DB。 如果计划从现有数据库群集迁移到 Azure Cosmos DB，则可以根据有关现有数据库群集的信息进行容量计划。
* 如果只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md) 

![将具有 3 个四核 SKU 副本的副本集迁移到 Azure Cosmos DB](media/convert-vcore-to-request-unit/one-replica-set.png)

* 如果知道当前数据库工作负荷的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)

### <a name="estimate-provisioned-throughput-costs"></a>估算预配吞吐量成本

如果计划在预配吞吐量模式下使用 Azure Cosmos DB，请使用 [Azure Cosmos DB 容量计算器](https://cosmos.azure.com/capacitycalculator/)来估算成本，然后再在 Azure Cosmos 帐户中创建资源。 容量计算器用于估算所需的吞吐量和工作负荷的成本。 容量计算器目前仅适用于 SQL API、Cassandra API 和 API for MongoDB。

根据工作负荷为 Azure Cosmos 数据库和容器配置适当数量的预配吞吐量（也称为[请求单位 (RU/s)](request-units.md)），对于优化成本和性能非常重要。 要获取成本预算，必须输入详细信息，例如 API 类型、区域数、项大小、每秒读/写请求数、存储的总数据量。 要了解有关容量计算器的详细信息，请参阅《[估算](estimate-ru-with-capacity-planner.md)》一文。

以下屏幕截图显示了使用容量计算器进行吞吐量和成本估算：

:::image type="content" source="./media/estimate-ru-with-capacity-planner/basic-mode-sql-api.png" alt-text="Capacity Planner 基本模式" border="true":::

### <a name="estimate-serverless-costs"></a><a id="estimating-serverless-costs"></a> 估算无服务器成本

如果计划在无服务器模式下使用 Azure Cosmos DB，则需要估算每月可能会消耗多少个[请求单位](request-units.md)和多大 GB 存储。 用户可以通过评估每月发出的数据库操作数量来估算所需的请求单位数，再用该数量乘以相应的 RU 成本。 下表列出了常用数据库操作的估计 RU 费用：

| Operation | 估计成本 | 注释 |
| --- | --- | --- |
| 创建项 | 5 RU | 索引属性少于 5 个时 1 KB 项的平均成本 |
| 更新项 | 10 RU | 索引属性少于 5 个时 1 KB 项的平均成本 |
| 按 ID 和分区键读取各项（点读取） | 1 RU | 1 KB 项的平均成本 |
| 删除项 | 5 RU | |
| 执行查询 | 10 RU | 充分利用[索引](index-overview.md)并返回不超过 100 个结果的查询的平均成本 |

> [!IMPORTANT] 
> 请注意上表中的“说明”。 如需更准确地估算实际操作成本，可以使用 [Azure Cosmos DB 仿真器](local-emulator.md)和[度量准确的操作 RU 成本](find-request-unit-charge.md)。 尽管 Azure Cosmos DB 模拟器不支持无服务器模式，但它会报告数据库操作的标准 RU 费用，并可用于此项估算。

在计算出每月可能消耗的请求单位和存储空间 (GB) 总数后，可通过以下公式返回成本估算值：（[请求单位数] / 1,000,000 * $0.25）+（[存储空间 GB] * $0.25）。

> [!NOTE]
> 上面示例中所示的成本仅用于演示目的。 有关最新定价信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。

## <a name="understand-the-full-billing-model"></a>了解完整的计费模式

Azure Cosmos DB 在 Azure 基础结构上运行，部署新资源时，该基础结构会随之产生成本。 用户务必了解，是否可能产生其他基础结构成本。

### <a name="how-youre-charged-for-azure-cosmos-db"></a>Azure Cosmos DB 的收费方式

创建或使用 Azure Cosmos DB 资源时，可能需要按以下计量标准付费：

* 数据库操作 - 你将根据预配或消耗的请求单位（RU/秒）付费：
  * 标准（手动）预配吞吐量 - 你将根据在容器或数据库上预配的 RU/秒按小时付费。
  * 自动缩放预配吞吐量 - 你将根据系统在每小时纵向扩展到的最大每秒 RU 数付费。

* 消耗的存储 - 你将根据数据和索引在给定的小时内消耗的存储空间总量 (GB) 付费。

如果使用备份存储、分析存储、可用性区域、多区域写入等 Azure Cosmos DB 功能，则会产生额外的费用。 在计费周期结束时，将对每个计量标准的费用求和。 帐单或发票显示的费用是所有 Azure Cosmos DB 成本的一部分。 每个计量标准都有单独的一行项目。 有关详细信息，请参阅[定价模型](how-pricing-works.md)一文。

### <a name="using-azure-prepayment"></a>使用 Azure 预付款

可以使用 Azure 预付款额度支付 Azure Cosmos DB 费用。 但是，不能使用 Azure 预付款额度来支付第三方产品和服务（包括 Azure 市场中的）的费用。

## <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 门户中查看估计成本

在 Azure 门户中开始使用 Azure Cosmos DB 资源时，可以看到估计的成本。 请按照以下步骤查看成本估计：

1. 登录 Azure 门户，并导航到 Azure Cosmos 帐户。
1. 转到“概述”部分。
1. 查看底部的“成本”图表。 该图显示在可配置时间段内的当前成本估计值：
1. 创建一个新容器，例如图容器。
1. 输入工作负载所需的吞吐量，例如 400 RU/s。 输入吞吐量值后，可以看到价格估算，如以下屏幕截图所示：

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Azure 门户中的成本估算":::

如果你的 Azure 订阅有支出限制，Azure 将阻止超过额度金额的支出。 创建和使用 Azure 资源时，将使用额度。 达到额度限制时，将在该计费周期的剩余时间内禁用部署的资源。 你无法更改额度限制，但可以删除该限制。 有关支出限制的详细信息，请参阅 [Azure 支出限制](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

可以使用 Azure 预付款（之前称为货币承诺）额度支付 Azure Cosmos DB 费用。 但是，不能使用 Azure 预付款额度来支付第三方产品和服务的费用（包括 Azure 市场中的第三方产品和服务）。

## <a name="monitor-costs"></a>监视成本

在 Azure Cosmos DB 中使用资源时，会产生成本。 资源使用的单位成本随时间间隔（秒、分钟、小时和天）或请求的单位使用量而变化。 一旦开始使用 Azure Cosmos DB，就会产生成本，在 Azure 门户的[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)窗格中可以看到它们。

使用成本分析时，可以在图表中查看不同时间间隔的 Azure Cosmos DB 成本。 有些示例是按天、当月、上个月以及按年来统计的。 还可以对比预算和预测的成本来查看成本。 切换到时间更长的视图有助于确定支出趋势，以及了解可能超支的领域。 如果已创建预算，还可以轻松查看超支的地方。

查看成本分析中的 Azure Cosmos DB 成本：

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户中打开范围，并在菜单中选择“成本分析”。 例如，转到“订阅”，从列表中选择一项订阅，然后在菜单中选择“成本分析” 。 选择“范围”，在成本分析中切换到不同的范围。

1. 默认情况下，所有服务的成本显示在第一个圆环图中。 选择图表中标记为“Azure Cosmos DB”的区域。

1. 要缩小查看单项服务的成本，请选择“添加筛选器”，然后选择“服务名称” 。 然后，从列表中选择“Azure Cosmos DB”。 下面是一个仅显示 Azure Cosmos DB 成本的示例：

   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="通过“成本分析”窗格监视成本":::

在上面的示例中，可以看到 2 月份 Azure Cosmos DB 的当前成本。该图表还包含按位置和按资源组列出的 Azure Cosmos DB 成本。

## <a name="create-budgets"></a>创建预算

可以创建[预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本，并创建[警报](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果希望增加监视中的精度，可以在 Azure 中使用筛选器为特定资源或服务创建预算。 筛选器可帮助确保不会意外创建会产生额外成本的新资源。 有关创建预算时筛选器选项的详细信息，请参阅[对选项进行分组和筛选](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

还可以将[成本数据导出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)到存储帐户。 当你或其他人需要进行有关成本的额外数据分析时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 可以按每天、每周或每月计划导出成本，并设置自定义的日期范围。 建议导出成本数据来检索成本数据集。

## <a name="other-ways-to-manage-and-reduce-costs"></a>管理和降低成本的其他方式

下面是可以用来降低成本的一些最佳做法：

* [优化预配吞吐量成本](optimize-cost-throughput.md) - 此文详细说明了优化吞吐量成本的最佳做法。 其中介绍了何时根据工作负载类型在容器级别或在数据库级别预配吞吐量。

* [优化请求成本](optimize-cost-reads-writes.md) - 此文介绍了如何将读取和写入请求转换为请求单位，以及如何优化这些请求的成本。

* [优化存储成本](optimize-cost-storage.md) - 存储成本按消耗量计收。 了解如何使用更改源和生存时间等功能根据项大小和索引策略优化存储成本。

* [优化多区域成本](optimize-cost-regions.md) - 如果你有一个或多个未充分利用的读取区域，则可以采取措施来最大限度地利用读取区域中的 RU，具体方式是从读取区域使用更改源或将其移动到另一个次要区域（如果已过度利用）。

* [优化开发/测试成本](optimize-dev-test.md) - 了解如何使用本地仿真器、Azure Cosmos DB 免费层、Azure 免费帐户和其他几个选项来优化开发成本。

* [使用预留容量优化成本](cosmos-db-reserved-capacity.md) - 了解如何使用预留容量，通过承诺一年或三年的 Azure Cosmos DB 资源预留量来节省资金。

## <a name="next-steps"></a>后续步骤

有关 Azure Cosmos DB 中定价方式的详细信息，请参阅以下文章：

* 正在尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以根据有关现有数据库群集的信息进行容量计划。
    * 如果只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md) 
    * 如果知道当前数据库工作负荷的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)
* [Azure Cosmos DB 中的定价模型](how-pricing-works.md)
* 了解[如何通过 Azure 成本管理优化云投资](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
* 详细了解如何通过[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
* 了解如何[防止意外成本](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
* 参与[成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)引导式学习课程。