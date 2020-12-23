---
title: 计划管理 Azure ExpressRoute 的成本
description: 了解如何使用 Azure 门户中的成本分析来规划和管理 Azure ExpressRoute 的成本。
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: 38b6aa35c1b12e3fdaed4a60cd6e241a05d42efe
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501380"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>规划和管理 Azure ExpressRoute 的成本

本文介绍如何为 ExpressRoute 规划和管理成本。 首先，使用 Azure 定价计算器来帮助规划 ExpressRoute 成本，然后再添加任何服务资源来估算成本。 添加 Azure 资源时，请查看预估成本。 

开始使用 ExpressRoute 资源后，使用成本管理功能来设置预算和监控成本。 你还可以查看预测成本并确定支出趋势，以确定你可能想要采取的措施。 

请记住，ExpressRoute 的成本仅是 Azure 帐单中每月成本的一部分。 尽管本文介绍了如何为 ExpressRoute 规划和管理成本，但你需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。

## <a name="prerequisites"></a>先决条件

成本管理中的成本分析支持大多数 Azure 帐户类型，但不支持所有帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，至少需要一个 Azure 帐户的读取权限。 

若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="local-vs-standard-vs-premium"></a>Local 与 Standard 与 Premium

ExpressRoute 有三个不同的线路 SKU： [*本地*](./expressroute-faqs.md#expressroute-local)、 *标准* 和 [*高级*](./expressroute-faqs.md#expressroute-premium)。 你为 ExpressRoute 使用付费的方式因这三个 SKU 类型的不同而异。 使用本地 SKU，你将使用无限制数据计划自动收费。 使用标准和高级 SKU，可以在按流量计费的数据计划或无限制数据计划之间进行选择。 除使用 Global Reach 外接程序外，所有入站数据都是免费的。 务必要了解哪些 SKU 类型和数据计划最适合你的工作负荷，以便优化成本和预算。

## <a name="estimate-costs"></a>估算成本

在创建 ExpressRoute 线路之前，使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/) 估算成本。 

1. 在左侧选择 " **网络**"，然后选择 " **Azure ExpressRoute** " 开始。 

1. 根据对等互连位置，选择相应的 *区域* 。 请参阅 [ExpressRoute 连接提供商](./expressroute-locations-providers.md#partners) ，在下拉范围中选择适当的 *区域* 。 

1. 然后选择要估计的 *SKU*、 *线路速度* 和 *数据计划* 。 

1. 在 " *额外出站数据传输*" 中，输入一个月中可能使用的出站数据量（GB）。 

1. 最后，可以将 *Global Reach 外接程序* 添加到估计。

以下屏幕截图显示了使用计算器进行成本估算：

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 计算器中的 ExpressRoute 成本估算":::

有关详细信息，请参阅 [Azure ExpressRoute 定价](https://azure.microsoft.com/pricing/details/expressroute/)。

### <a name="expressroute-gateway-estimated-cost"></a>ExpressRoute 网关预估成本

如果正在使用 ExpressRoute 网关将虚拟网络链接到 ExpressRoute 线路，请使用以下步骤估算网关使用情况的成本。

1. 在左侧选择 " **网络**"，然后选择 " **VPN 网关** "。 

1. 为网关选择 *区域* ，然后将 *类型* 更改为 **ExpressRoute 网关**。

1. 从下拉项中选择 *网关类型* 。

1. 输入 " *网关时间*"。  (720 小时 = 30 天) 

## <a name="understand-the-full-billing-model-for-expressroute"></a>了解 ExpressRoute 的完整计费模式

在部署新资源时，ExpressRoute 在 Azure 基础结构上运行，该基础结构会产生成本和 ExpressRoute。 了解其他基础结构可能会产生成本，这一点很重要。 你需要在对已部署的资源进行更改时管理该成本。 

### <a name="costs-that-typically-accrue-with-expressroute"></a>通常通过 ExpressRoute 累积的成本

创建 ExpressRoute 线路时，可以选择创建 ExpressRoute 网关，将虚拟网络链接到线路。 网关按小时费率收费，并按 ExpressRoute 线路的成本收费。 请参阅 [expressroute 定价](https://azure.microsoft.com/en-us/pricing/details/expressroute) 并选择 Expressroute 网关，查看不同网关 sku 的费率。
 
### <a name="costs-might-accrue-after-resource-deletion"></a>删除资源后可能会产生成本

如果在删除 ExpressRoute 线路后有 ExpressRoute 网关，则仍需支付费用，直到将其删除。

### <a name="using-monetary-credit"></a>使用货币信用额度

可以通过 EA 货币承诺信用额度支付 ExpressRoute 费用。 但是，不能使用 EA 货币承诺信用额度来支付第三方产品和服务（包括来自 Azure Marketplace 的产品和服务）的费用。

## <a name="monitor-costs"></a>监视成本

通过 ExpressRoute 使用 Azure 资源时，会产生成本。 Azure 资源使用情况单位成本按时间间隔 (秒、分钟、小时和天) 或单位使用情况变化 (字节、mb 等）。在 ExpressRoute 使用开始后，就会立即 ) ，此时将产生成本，你可以在 [成本分析](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中查看成本。

使用成本分析时，可以在关系图和表中查看不同时间间隔的 ExpressRoute 线路开销。 有些示例是按天、当前和上个月和年。 你还可以查看预算和预测成本的成本。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势。 你会看到超支可能发生的位置。 如果已创建预算，还可以轻松地查看超出的位置。

若要查看成本分析中的 ExpressRoute 成本：

1. 登录到 Azure 门户。

1. 单击 " **订阅**"，从列表中选择一个订阅，然后在菜单中选择 "  **成本分析** "。 选择 " **作用域** " 可切换到成本分析中的其他作用域。 默认情况下，服务的成本显示在第一个圆环图中。

    最初打开成本分析时，会显示实际的每月成本。 下面是显示所有每月使用成本的示例。

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="显示订阅的累计成本的示例":::
    

1.  若要缩小单个服务（如 Expressroute）的成本，请选择 " **添加筛选器** "，然后选择 " **服务名称**"。 然后选择 " **ExpressRoute**"。

    下面是一个示例，显示了 ExpressRoute 的成本。

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="显示 ExpressRoute 累计成本的示例":::

在前面的示例中，您将看到该服务的当前成本。 还显示了按资源组列出的 Azure 区域 (位置) 和 ExpressRoute 成本）的成本。 从这里，你可以自行探索成本。

## <a name="create-budgets-and-alerts"></a>创建预算和警报

你可以创建 [预算](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 来管理成本，并创建 [警报](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ，以自动通知利益干系人支出异常和超支风险的利益干系人。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果你希望在监视中出现更多粒度，则可以在 Azure 中创建针对特定资源或服务的筛选器。 筛选器可帮助确保不会意外地创建新资源，从而降低成本。 有关创建预算时筛选器选项的详细信息，请参阅 [组和筛选选项](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

你还可以将 [成本数据导出](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 到存储帐户。 当你需要或其他人进行额外的数据分析以获得成本时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 您可以按每天、每周或每月计划导出您的成本，并设置自定义的日期范围。 建议使用导出成本数据来检索成本数据集。

## <a name="next-steps"></a>后续步骤

- 详细了解如何使用 Azure ExpressRoute。 请参阅 [Azure ExpressRoute 概述定价](https://azure.microsoft.com/en-us/pricing/details/expressroute/)。
- 了解 [如何通过 Azure 成本管理优化云投资](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过 [成本分析](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何 [防止意外成本](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 采用 [成本管理](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引导式学习课程。
