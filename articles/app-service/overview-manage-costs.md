---
title: 计划管理应用服务的成本
description: 了解如何使用 Azure 门户中的成本分析来计划和管理 Azure 应用服务的成本。
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 06/23/2021
ms.openlocfilehash: 3f2ae25c3f2e1076cf714aa56f7ecdf867d6dd8f
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112554182"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>计划和管理 Azure 应用服务的成本

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

本文介绍如何计划和管理 Azure 应用服务的成本。 首先，使用 Azure 定价计算器来帮助计划应用服务成本，然后再添加任何服务资源来估算成本。 接下来，在添加 Azure 资源时，查看预估成本。 在开始使用应用服务资源之后，请使用[成本管理](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)功能来设置预算和监视成本。 你还可以查看预测成本并确定支出趋势，以确定你可能想要对其采取措施的区域。Azure 应用服务的成本仅是 Azure 帐单中每月成本的一部分。 尽管本文介绍了如何为应用服务计划和管理成本，但用户需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。

## <a name="understand-the-full-billing-model-for-azure-app-service"></a>了解 Azure 应用服务的完整计费模式

Azure 应用服务在 Azure 基础结构上运行，部署新资源时，该基础结构会随之产生成本。 用户务必了解，是否可能产生其他基础结构成本。

### <a name="how-youre-charged-for-azure-app-service"></a>Azure 应用服务的收费方式

创建或使用应用服务资源时，将按以下计量标准付费：

- 根据应用服务计划的定价层收取每小时费率（按比例分配到第二小时）。
- 根据分配 VM 实例的时间量，向计划中每个横向扩展实例收取费用。 

应用服务的其他成本资源包括（请参阅[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)以了解详情）：

- [应用服务域](manage-custom-dns-buy-domain.md) 如果启用自动续订，则按年收取域注册的订阅费用。
- [应用服务证书](configure-ssl-certificate.md#import-an-app-service-certificate) 购买时的一次性付费。 如果有多个子域要保护，则可以购买一个通配符证书而不是多个标准证书，以此来降低成本。
- 基于 [IP 的证书绑定](configure-ssl-bindings.md#create-binding) 在应用级别的证书上配置绑定。 每个绑定都累计成本。 对于“标准”层和更高版本，不会对首个基于 IP 的绑定收费。

在计费周期结束时，每个 VM 实例的费用。 帐单或发票显示的费用是所有应用服务成本的一部分。 每个计量标准都有单独的一行项目。

### <a name="other-costs-that-might-accrue-with-azure-app-service"></a>Azure 应用服务可能产生的其他成本

根据在应用服务中使用的功能，可能会创建以下会产生成本的资源：

- 隔离层  应用服务环境需要[虚拟网络](../virtual-network/index.yml)并单独收费。
- 备份  需要一个[存储帐户](../storage/index.yml)进行备份并单独收费。
- 诊断日志  用户可以选择[存储帐户](../storage/index.yml)作为日志记录选项，或与 [Azure Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) 集成。 这些服务单独收费。
- 应用服务证书  在 Azure 中购买的证书必须在 [Azure Key Vault](../key-vault/index.yml) 中维护（单独收费）。

### <a name="costs-that-might-accrue-after-resource-deletion"></a>删除资源后可能会累计的成本

删除应用服务计划中的所有应用后，该计划将继续根据其配置的定价层和实例数进行计费。 要避免产生不必要的费用，请删除该计划或将其降级到“免费”层。

删除 Azure 应用服务资源后，相关 Azure 服务中的资源可能会继续存在。 它们会持续产生成本，直到将其删除。 例如：

- 为“隔离”层应用服务计划创建的虚拟网络
- 为了存储备份或诊断日志而创建的存储帐户
- 为了存储应用服务证书而创建的密钥保管库
- 为了交付诊断日志而创建的 Log Analytic 命名空间
- 尚未过期的应用服务的[实例或标记预留](#azure-reservations)

### <a name="using-azure-prepayment-with-azure-app-service"></a>对 Azure 应用服务使用 Azure 预付款

可以使用 Azure 预付款额度支付 Azure 应用服务费用。 但是，不能使用 Azure 预付额度来支付第三方产品和服务（包括 Azure 市场中的第三方产品和服务）的费用。

## <a name="estimate-costs"></a>估算成本

要预先评估和优化应用服务成本，一种简单方法是使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)。

若要使用定价计算器，请单击“产品”选项卡中的“应用服务”。然后，向下滚动以使用计算器。  以下屏幕截图只是一个示例，并不反映当前定价。

![显示 Azure 定价计算器中的预估成本的示例](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 门户中查看估计成本

创建“应用服务”应用或“应用服务”计划时，可以查看预估成本。

要创建应用并查看预估价格，请执行以下步骤：

1. 在“创建”页面上，向下滚动到“应用服务计划”，然后单击“新建”。 
1. 指定名称，然后单击“确定”。
1. 在“SKU 和大小”旁边，单击“更改大小”。 
1. 查看摘要中显示的估计价格。 以下屏幕截图只是一个示例，并不反映当前定价。

    ![查看门户中每个定价层的预估成本](media/overview-manage-costs/pricing-estimates.png)

如果你的 Azure 订阅有支出限制，Azure 将阻止超过额度金额的支出。 创建和使用 Azure 资源时，将使用额度。 达到额度限制时，将在该计费周期的剩余时间内禁用部署的资源。 你无法更改额度限制，但可以删除该限制。 有关支出限制的详细信息，请参阅 [Azure 支出限制](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="optimize-costs"></a>优化成本

在基本级别上，“应用服务”应用按照托管这些应用的应用服务计划进行收费。 与应用服务部署相关联的成本取决于以下几个主要因素：

- 定价层  也称为应用服务计划的 SKU。 更高层，提供的 CPU 内核、内存、存储或功能或它们的组合就越多。
- 实例计数  专用层（基本层和更高层）可以横向扩展，每个扩展的实例都会累计成本。
- 印花费  在隔离层中，无论托管多少应用或工作器实例，都将在应用服务环境中累计固定费用。

一个应用服务计划可以托管多个应用。 根据部署，用户可以将托管多个应用的成本保存在一个应用服务计划中（例如，在更少的应用服务计划中托管应用）。

有关详细信息，请参阅[应用服务计划概述](overview-hosting-plans.md)

### <a name="non-production-workloads"></a>非生产工作负荷

要在累计低成本或最低成本的同时测试应用服务或解决方案，可以首先使用共享实例上托管的两个入门级定价层（“免费”和“共享”）。  要在具有更高性能的专用实例上测试应用，可以升级到同时支持 Windows 和 Linux 应用的“基本”层。 

> [!NOTE]
> Azure 开发/测试定价  为了测试需要更高层（除“隔离”层之外的所有层）的生产前工作负荷，Visual Studio 订阅者还可以使用 [Azure 开发/测试定价](https://azure.microsoft.com/pricing/dev-test/)，该定价提供了大量折扣。 
>
> “免费”和“共享”层以及 Azure 开发/测试定价折扣均不提供财务支持的 SLA。 

### <a name="production-workloads"></a>生产工作负荷

生产工作负荷附带专用“标准”定价层或更高层的建议。 虽然更高层价格会增加，但它还提供了更多的内存、存储和更高性能的硬件，从而提高了每个计算实例的应用密度。 对于相同数量的应用，这会降低实例数量，从而降低成本。 实际上，“高级 V3”（最高的非隔离层）是大规模提供应用的最经济高效的方式。  要进一步节省成本，可以获取[高级 V3 预留](#azure-reservations)的大幅折扣。

> [!NOTE]
> “高级 V3”同时支持 Windows 容器和 Linux 容器。 

选择所需的定价层后，应将空闲实例数降到最低。 在横向扩展部署中，未充分利用的计算实例可能会浪费成本。 应该[配置自动扩展](../azure-monitor/autoscale/autoscale-get-started.md)，“标准”层和更高层中提供了这一功能。 通过创建横向扩展日程安排以及基于指标的横向扩展规则，用户只需为在给定时间内真正需要的实例付费。

### <a name="azure-reservations"></a>Azure 预留

如果你计划在一年或更长时间内使用某个已知最小数量的计算实例，则应在 1 年或 3 年的增量中保留这些实例，从而充分利用“高级 V3”层并大幅降低实例的成本。 每个实例每月最多可以节省 55% 的成本。 可以采取两种类型的预留：

- Windows（或平台不可知）  可应用于订阅中的 Windows 或 Linux 实例。
- Linux 专用  仅适用于订阅中的 Linux 实例。

预留实例定价适用于订阅中的适用实例，最大数量是预留的实例数。 预留实例要进行计费，不与任何特定计算实例绑定。 如果在预留期内的任何时间点运行的实例数少于预留的实例数，则仍需为预留实例付费。 如果在预留期内的任何时间点运行的实例数超过了预留的实例数，则需要支付额外实例的正常应计成本。

“隔离”层（应用服务环境）还支持 1 年和 3 年的预留期，定价会降低。 有关详细信息，请参阅[如何将预留折扣应用于 Azure 应用服务](../cost-management-billing/reservations/reservation-discount-app-service.md)。

## <a name="monitor-costs"></a>监视成本

将 Azure 资源用于应用服务时，会产生成本。 Azure 资源使用的单位成本随时间间隔（秒、分钟、小时和天）而变化。 开始使用应用服务之后便会产生成本，可以在[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中查看成本。

使用成本分析时，可以在关系图和表中查看不同时间间隔的应用服务成本。 有些示例是按天、当前、上个月和年划分的。 还可以对比预算和预测的成本来查看成本。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势。 你会看到可能出现超支的地方。 如果已创建预算，还可以轻松查看超支的地方。
    
要在成本分析中查看应用服务成本，请执行以下操作：

1. 登录到 Azure 门户。
2. 在 Azure 门户中打开范围，并在菜单中选择“成本分析”。 例如，转到“订阅”，从列表中选择一项订阅，然后在菜单中选择“成本分析” 。 选择“范围”，在成本分析中切换到不同的范围。
3. 默认情况下，服务的成本显示在第一个圆环图中。 选择图表中标记为“应用服务”的区域。

最初打开成本分析时，会显示实际的每月成本。 下面是显示所有每月使用成本的示例。

![显示订阅累计成本的示例](media/overview-manage-costs/all-costs.png)

若要缩小范围以查看单项服务（如应用服务）的成本，请选择“添加筛选器”，然后选择“服务名称” 。 然后选择“应用服务”。

下面的示例只显示了“应用服务”的成本。

![显示 ServiceName 累积费用的示例](media/overview-manage-costs/service-specific-costs.png)

在前面的示例中，你将看到该服务的当前成本。 还显示了按 Azure 区域（位置）的成本和按资源组的应用服务成本。 从这里，你可以自行探索成本。

## <a name="create-budgets"></a>创建预算

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

可以创建[预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本，并创建[警报](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果希望增加监视中的精度，可以在 Azure 中使用筛选器为特定资源或服务创建预算。 筛选器可帮助确保不会意外创建会产生额外成本的新资源。 有关创建预算时可用的筛选器选项的详细信息，请参阅[对选项进行分组和筛选](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

还可以将[成本数据导出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)到存储帐户。 当你或其他人需要进行有关成本的更多数据分析时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 可以按每天、每周或每月计划导出成本，并设置自定义的日期范围。 建议导出成本数据来检索成本数据集。

## <a name="next-steps"></a>后续步骤

- 了解 Azure 存储的定价方面的详细信息。 请参阅[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)。
- 了解[如何通过 Azure 成本管理优化云投资](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何[防止意外成本](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 参与[成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)引导式学习课程。

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->