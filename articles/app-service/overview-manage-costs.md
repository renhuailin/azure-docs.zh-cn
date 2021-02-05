---
title: 计划管理应用服务的成本
description: 了解如何使用 Azure 门户中的成本分析来规划和管理 Azure App Service 的成本。
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: 3df08705859678525526f8fef198826f58249d8b
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573358"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>规划和管理 Azure App Service 的成本

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

本文介绍了如何为 Azure App Service 规划和管理成本。 首先，使用 Azure 定价计算器来帮助规划应用服务成本，然后再添加任何服务资源来估算成本。 接下来，当你添加 Azure 资源时，请查看预估成本。 开始使用应用服务资源后，使用 [成本管理](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 功能来设置预算和监视成本。 你还可以查看预测成本并确定支出趋势，以确定你可能想要采取的措施。Azure App Service 的成本仅是 Azure 帐单中每月费用的一部分。 尽管本文介绍了如何规划和管理应用服务的成本，但你需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。

## <a name="relevant-costs-for-app-service"></a>应用服务的相关成本

应用服务在计算成本的 Azure 基础结构上运行。 了解其他基础结构可能会产生成本，这一点很重要。 在对已部署的资源进行更改时，必须管理该成本。

### <a name="costs-that-accrue-with-azure-app-service"></a>Azure App Service 的成本

根据你在应用服务中使用的功能，可能会创建以下经济资源的资源：

- **应用服务计划**  承载应用服务应用程序所必需的。
- **隔离层**  应用服务环境需要 [虚拟网络](../virtual-network/index.yml) 。
- **备份**  需要一个 [存储帐户](../storage/index.yml) 才能进行备份。
- **诊断日志**  你可以选择 [存储帐户](../storage/index.yml) 作为日志记录选项，或与 [Azure Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md)集成。
- **应用服务证书**  在 Azure 中购买的证书必须在 [Azure Key Vault](../key-vault/index.yml)维护。

应用服务的其他成本资源 (参阅 [应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)) 详细信息：

- [应用服务域](manage-custom-dns-buy-domain.md)  如果你启用自动续订，则你的订阅将在每年的域注册中收费。
- [应用服务证书](configure-ssl-certificate.md#import-an-app-service-certificate)  购买时的一次性充电。 如果有多个子域要保护，则可以通过购买一个通配符证书而不是多个标准证书来降低成本。
- [基于 IP 的证书绑定](configure-ssl-bindings.md#create-binding)  在应用级别的证书上配置绑定。 将为每个绑定累算费用。 对于 **标准** 层和更高版本，不会对首个基于 IP 的绑定收费。

### <a name="costs-that-might-accrue-after-resource-deletion"></a>删除资源后可能会产生的成本

删除应用服务计划中的所有应用后，该计划将继续根据其配置的定价层和实例数进行计费。 若要避免产生不必要的费用，请删除该计划或将其缩小到 **免费** 层。

删除 Azure App Service 资源后，相关 Azure 服务中的资源可能会继续存在。 它们会持续产生成本，直到你将其删除。 例如：

- 为 **隔离** 层应用服务计划创建的虚拟网络
- 你创建用于存储备份或诊断日志的存储帐户
- 你创建的用于存储应用服务证书 Key Vault
- 记录为装运诊断日志创建的分析命名空间
- 尚未过期的应用服务的[实例或标记保留](#azure-reservations)

### <a name="using-monetary-credit-with-azure-app-service"></a>使用货币信用额度 Azure App Service

你可以通过 Azure 预付款支付 Azure App Service 费用 (之前称为货币承诺) 信用额度。 但是，不能使用 Azure 预付款信用额度来支付第三方产品和服务（包括来自 Azure Marketplace 的产品和服务）的费用。

## <a name="estimate-costs"></a>估算成本

预先评估和优化应用服务成本的简单方法是使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)。

若要使用定价计算器，请单击 "**产品**" 选项卡中的 "**应用服务**"。然后，向下滚动到 "使用计算器"。 以下屏幕截图是一个示例，不反映当前定价。

![示例显示 Azure 定价计算器中的预估成本](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 门户中查看估计成本

当你创建应用服务应用程序或应用服务计划时，你可以查看预估成本。

创建应用并查看预计价格：

1. 在 "创建" 页上，向下滚动到 " **应用服务计划**"，然后单击 " **新建**"。
1. 指定一个名称，然后单击 **"确定"**。
1. 在 " **Sku 和大小**" 旁边，单击 " **更改大小**"。
1. 查看摘要中显示的预计价格。 以下屏幕截图是一个示例，不反映当前定价。

    ![查看门户中每个定价层的预估成本](media/overview-manage-costs/pricing-estimates.png)

如果你的 Azure 订阅有支出限制，Azure 将阻止你支出支出。 创建和使用 Azure 资源时，将使用信用额度。 达到信用额度限制时，将在该计费周期的剩余时间内禁用部署的资源。 你无法更改信用额度，但可以将其删除。 有关支出限制的详细信息，请参阅 [Azure 支出限制](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="optimize-costs"></a>优化成本

在基本级别上，应用服务应用由托管这些应用的应用服务计划进行收费。 与应用服务部署相关联的成本取决于以下几个主要因素：

- **定价层**  但也称为应用服务计划的 SKU。 更高的层提供更多的 CPU 内核、内存、存储或功能或它们的组合。
- **实例计数**  专用层 (基本和更高的) 可以横向扩展，每个扩展的实例都可以累算费用。
- **邮戳费**  在隔离层中，无论承载多少应用或辅助实例，都将在应用服务环境中对其计费。

应用服务计划可以托管多个应用。 根据你的部署，你可以将托管多个应用的成本保存在一个应用服务计划中， (例如，在更少的应用服务计划中托管你的应用) 。

有关详细信息，请参阅 [应用服务计划概述](overview-hosting-plans.md)

### <a name="non-production-workloads"></a>非生产工作负荷

若要在降低或降低成本的同时测试应用服务或解决方案，可以首先使用共享实例上托管的两个入门级定价层（" **免费** " 和 " **共享**"）。 若要在具有更高性能的专用实例上测试应用，可以升级到支持 Windows 和 Linux 应用的 **基本** 层。 

> [!NOTE]
> **Azure 开发/测试定价**  为了测试需要更高层 (除 **隔离**) 之外的所有层的预生产工作负荷，Visual Studio 订阅者还可以使用 [Azure 开发/测试定价](https://azure.microsoft.com/pricing/dev-test/)，提供了大量折扣。
>
> **免费** 和 **共享** 层以及 Azure 开发/测试定价折扣均不享有财务支持的 SLA。

### <a name="production-workloads"></a>生产工作负荷

生产工作负荷附带专用 **标准** 定价层或更高版本的建议。 虽然这一价格提高了更高的级别，但它还提供了更多的内存和存储和更高性能的硬件，从而提高了每个计算实例的应用密度。 这会转换为相同数量的应用程序的实例计数，从而降低成本。 事实上， **高级 V3** (最高的非 **隔离** 层) 是大规模提供应用的最经济高效的方式。 为了增加成本，你可以获取有关 [高级 V3 预留](#azure-reservations)的深度折扣。

> [!NOTE]
> **Premium V3** 支持 Windows 容器和 Linux 容器。 

选择所需的定价层后，应将空闲实例降到最低。 在扩展部署中，你可以在未充分利用的计算实例上浪费资金。 应该 [配置自动缩放](../azure-monitor/platform/autoscale-get-started.md)，在 **标准** 层和更高版本中可用。 通过创建横向扩展计划以及基于指标的向外扩展规则，你只需为在给定时间内真正需要的实例付费。

### <a name="azure-reservations"></a>Azure 预留

如果你计划使用一年或更长时间的已知的最小计算实例数，则应通过在1年或3年的增量中保留这些实例，充分利用 **高级 V3** 层并降低实例的成本。 每月节省的成本最多可为每个实例55%。 可以执行两种类型的保留：

- **Windows (或平台不可知)**  可应用于订阅中的 Windows 或 Linux 实例。
- **Linux 专用**  仅适用于你的订阅中的 Linux 实例。

预订实例定价适用于你的订阅中的适用实例，最大数量为你保留的实例数。 预订实例是计费的，不依赖于特定计算实例。 如果你运行的实例数少于在预留期内的任何时间预留的实例数，则仍需为保留实例付费。 如果在预留期间运行的实例数超出了预留时间，则会支付额外实例的正常应计成本。

 (应用服务环境) 的 **隔离** 层还支持1年和3年的保留期，价格将降低。 有关详细信息，请参阅 [如何将预订折扣应用于 Azure App Service](../cost-management-billing/reservations/reservation-discount-app-service.md)。

## <a name="monitor-costs"></a>监视成本

在应用服务中使用 Azure 资源时，会产生成本。 Azure 资源使用情况单位成本因时间间隔 (秒、分钟、小时和天) 而异。 应用服务使用开始后，会立即产生成本，你可以在 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中查看成本。

使用成本分析时，可以在关系图和表中查看不同时间间隔的应用服务成本。 有些示例是按天、当前和上个月和年。 你还可以查看预算和预测成本的成本。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势。 你会看到超支可能发生的位置。 如果已创建预算，还可以轻松地查看超出的位置。
    
若要查看成本分析中的应用服务成本：

1. 登录到 Azure 门户。
2. 在 Azure 门户中打开范围，并在菜单中选择 " **成本分析** "。 例如，在 " **订阅**" 中，从列表中选择一个订阅，然后在菜单中选择 "  **成本分析** "。 选择 " **作用域** " 可切换到成本分析中的其他作用域。
3. 默认情况下，服务的成本显示在第一个圆环图中。 选择图表中标记为 "应用服务" 的区域。

最初打开成本分析时，会显示实际的每月成本。 下面是显示所有每月使用成本的示例。

![显示订阅的累计成本的示例](media/overview-manage-costs/all-costs.png)

若要缩小单个服务（如应用服务）的成本，请选择 " **添加筛选器** "，然后选择 " **服务名称**"。 然后选择 " **应用服务**"。

下面是一个示例，显示了应用服务的成本。

![显示 ServiceName 的累计成本的示例](media/overview-manage-costs/service-specific-costs.png)

在前面的示例中，您将看到该服务的当前成本。 还显示了按资源组列出的 Azure 区域 (位置) 和应用服务成本的成本。 从这里，你可以自行探索成本。

## <a name="create-budgets"></a>创建预算

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

你可以创建 [预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 来管理成本，并创建 [警报](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ，以自动通知利益干系人支出异常和超支风险的利益干系人。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果你希望在监视中出现更多粒度，则可以在 Azure 中创建针对特定资源或服务的筛选器。 筛选器可帮助确保不会意外地创建新的资源，从而降低额外的费用。 有关创建预算时可用的筛选器选项的详细信息，请参阅 [组和筛选选项](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

你还可以将 [成本数据导出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 到存储帐户。 当你需要或其他人进行更多数据分析以获得成本时，这会很有帮助。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 您可以按每天、每周或每月计划导出您的成本，并设置自定义的日期范围。 建议使用导出成本数据来检索成本数据集。

## <a name="next-steps"></a>后续步骤

- 了解有关如何使用 Azure 存储的详细信息。 请参阅[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)。
- 了解 [如何通过 Azure 成本管理优化云投资](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何 [防止意外成本](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 采用 [成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引导式学习课程。

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->