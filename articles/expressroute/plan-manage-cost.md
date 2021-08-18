---
title: 规划和管理 Azure ExpressRoute 成本
description: 了解如何使用 Azure 门户中的成本分析来规划和管理 Azure ExpressRoute 的成本。
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 05/18/2021
ms.openlocfilehash: c4a2113a5aa95c1bacf7be0f4b376b9377412371
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111971701"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>规划和管理 Azure ExpressRoute 的成本

本文介绍如何规划和管理 Azure ExpressRoute 的成本。 首先，在为服务添加任何资源并估算成本之前，先使用 Azure 定价计算器来帮助规划 ExpressRoute 的成本。 接下来，在添加 Azure 资源时，查看预估成本。 

开始使用 Azure ExpressRoute 资源后，使用成本管理功能来设置预算和监视成本。 还可以查看预测的成本并确定支出趋势，以确定可能需要实施措施的领域。Azure ExpressRoute 的成本只是 Azure 账单中每月成本的一部分。 尽管本文介绍了如何为 Azure ExpressRoute 规划和管理成本，但用户需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。

## <a name="prerequisites"></a>先决条件

成本管理中的成本分析支持大多数 Azure 帐户类型，但不支持所有帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="local-vs-standard-vs-premium"></a>本地与标准与Premium

Azure ExpressRoute 有三个不同的线路 SKU：[本地](./expressroute-faqs.md#expressroute-local)、标准和[高级](./expressroute-faqs.md#expressroute-premium)  。 为 ExpressRoute 的使用付费的方式根据这三个 SKU 类型而有所不同。 如果使用本地 SKU，将自动按照“不限流量”套餐收费。 如果使用标准和高级 SKU，可以选择按“数据流量”套餐或“不限流量”套餐缴费。 除使用 Global Reach 附加产品的情况外，所有流入数据均免费。 务必要了解哪些 SKU 类型和数据套餐最适合你的工作负载，最能为你的工作负载优化成本和预算。

## <a name="estimate-costs-before-using-azure-expressroute"></a>使用 Azure ExpressRoute 之前估算成本

创建 Azure ExpressRoute 线路之前，使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)估算成本。 

1. 首先在左侧选择“网络”，然后选择“Azure ExpressRoute” 。 

1. 根据对等互连位置，选择相应的区域。 请参阅 [ExpressRoute 连接提供商](./expressroute-locations-providers.md#partners)，在下拉列表中选择合适的区域。 

1. 然后选择要为之估算的 SKU、线路速度和数据套餐。   

1. 在“其他出站数据传输”中，输入估算的一个月中可能耗用的出站数据量 (GB)。 

1. 最后，可以在估算中加入 Global Reach 附加产品。

以下屏幕截图显示了使用计算器进行成本估算：

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 计算器中的 ExpressRoute 成本估算":::

有关详细信息，请参阅 [Azure ExpressRoute 定价](https://azure.microsoft.com/pricing/details/expressroute/)。

### <a name="expressroute-gateway-estimated-cost"></a>ExpressRoute 网关成本估算

如果要使用 ExpressRoute 网关将虚拟网络链接到 ExpressRoute 线路，请使用以下步骤来估算网关使用成本。

1. 首先在左侧选择“网络”，然后选择“VPN 网关” 。 

1. 为网关选择区域，然后将“类型”更改为“ExpressRoute 网关” 。

1. 从下拉列表中选择“网关类型”。

1. 在“网关时间”中输入所需值。 （720 小时 = 30 天）

## <a name="understand-the-full-billing-model-for-expressroute"></a>了解 ExpressRoute 的完整计费模式

Azure ExpressRoute 在 Azure 基础结构上运行，部署新资源时，会随 ExpressRoute 产生成本。 需要了解的是，其他基础结构可能会产生成本。 对已部署的资源进行更改时需要管理该成本。 

### <a name="costs-that-typically-accrue-with-expressroute"></a>通常会随 ExpressRoute 产生的成本

#### <a name="expressroute"></a>ExpressRoute

创建 ExpressRoute 线路时，你可能会选择创建 ExpressRoute 网关，用于将虚拟网络链接到线路。 ExpressRoute 网关按每小时的费率加上 ExpressRoute 线路的费用来收费。 请参阅 [ExpressRoute 定价](https://azure.microsoft.com/pricing/details/expressroute)并选择“ExpressRoute 网关”，查看不同网关 SKUS 的费率。

入站数据传输包含在三个 SKU 的 ExpressRoute 线路的每月成本中。 出站数据传输仅包含在不限流量套餐中。 对于数据流量套餐，出站数据传输根据[对等互连位置](expressroute-locations-providers.md#partners)的区域编号按 GB 计费。

#### <a name="expressroute-direct"></a>ExpressRoute Direct

ExpressRoute Direct 每月收取端口费，其中包括本地和标准 SKU ExpressRoute 线路的线路费。 对于高级 SKU 线路，需支付额外的线路费。 出站数据传输根据对等互连位置的区域编号按 GB 计费。 出站数据计费仅适用于标准和高级 SKU。
 
#### <a name="expressroute-global-reach"></a>ExpressRoute Global Reach

ExpressRoute Global Reach 是一个附加产品，你可以为 ExpressRoute 和 ExpressRoute Direct 启用它，从而将 ExpressRoute 线路链接在一起。 入站和出站数据传输根据对等互连位置的区域编号按 GB 计费。

### <a name="costs-might-accrue-after-resource-deletion"></a>删除资源后可能会产生成本

如果在删除 ExpressRoute 线路后仍留有 ExpressRoute 网关，依然会收取相关费用，直到网关被删除为止。

### <a name="using-azure-prepayment-credit"></a>使用 Azure 预付款额度

可以使用 Azure 预付款（之前称为货币承诺）额度支付 ExpressRoute 费用。 但是，不能使用 Azure 预付款额度来支付第三方产品和服务的费用（包括 Azure 市场中的第三方产品和服务）。

## <a name="monitor-costs"></a>监视成本

将 Azure 资源用于 ExpressRoute 时，会产生成本。 Azure 资源使用情况单位成本因时间间隔（秒、分钟、小时和天）或（字节、MB 等）的单位使用情况而异。开始使用 ExpressRoute 之后便会产生成本，可以在[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中查看成本。

使用成本分析时，可以在关系图和表中查看不同时间间隔的 ExpressRoute 线路成本。 有些示例是按天、当前、上个月和年划分的。 还可以对比预算和预测的成本来查看成本。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势。 你会看到可能出现超支的地方。 如果已创建预算，还可以轻松查看超支的地方。

查看成本分析中的 ExpressRoute 成本：

1. 登录到 Azure 门户。

1. 转到到“订阅”，从列表中选择订阅，然后在菜单中选择“成本分析” 。 选择“范围”，在成本分析中切换到不同的范围。 默认情况下，服务的成本显示在第一个圆环图中。

    最初打开成本分析时，会显示实际的每月成本。 下面是显示所有每月使用成本的示例。

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="示例显示累计订阅成本":::
    

1.  若要缩小范围以查看单项服务（如 Expressroute）的成本，请选择“添加筛选器”，然后选择“服务名称” 。 然后选择“ExpressRoute”。

    下面的示例只显示了“ExpressRoute”的成本。

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="示例显示 ExpressRoute 累积成本":::

在前面的示例中，你将看到该服务的当前成本。 还显示了按 Azure 区域（位置）的成本和按资源组的 ExpressRoute 成本。 从这里，你可以自行探索成本。

## <a name="create-budgets-and-alerts"></a>创建预算和警报

可以创建[预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本，并创建[警报](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果希望增加监视中的精度，可以在 Azure 中使用筛选器为特定资源或服务创建预算。 筛选器可帮助确保不会意外创建会产生额外成本的新资源。 有关创建预算时筛选器选项的详细信息，请参阅[对选项进行分组和筛选](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

还可以将[成本数据导出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)到存储帐户。 当你或其他人需要进行有关成本的额外数据分析时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 可以按每天、每周或每月计划导出成本，并设置自定义的日期范围。 建议导出成本数据来检索成本数据集。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure ExpressRoute 的定价方式。 请参阅 [Azure ExpressRoute 概述定价](https://azure.microsoft.com/pricing/details/expressroute/)。
- 了解[如何通过 Azure 成本管理优化云投资](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何[防止意外成本](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 参与[成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)引导式学习课程。