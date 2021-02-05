---
title: 规划和管理 Azure API 管理的成本
description: 了解如何使用 Azure 门户中的成本分析来规划和管理 Azure API 管理的成本。
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 1ebb89ae318e57f1d4e0708a08019515ca43158d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581323"
---
# <a name="plan-and-manage-costs-for-api-management"></a>规划和管理 API 管理的成本

本文介绍如何为 Azure API 管理规划和管理成本。 首先，使用 Azure 定价计算器来帮助规划 API 管理成本，然后再添加任何服务资源来估算成本。 开始使用 API 管理资源后，使用成本管理功能来设置预算和监视成本。 你还可以查看预测成本并确定支出趋势，以确定你可能想要采取的措施。 

API 管理的成本仅是 Azure 帐单中每月成本的一部分。 尽管本文介绍了如何为 API 管理规划和管理成本，但你需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。

## <a name="prerequisites"></a>先决条件

成本管理中的成本分析支持大多数 Azure 帐户类型，但不支持所有帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，至少需要一个 Azure 帐户的读取权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="estimate-costs-before-using-api-management"></a>使用 API 管理之前估计成本

在添加 API 管理之前，请使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/) 来估算成本。 

1. 搜索 " *api 管理*"，或选择 "**集成**  >  **api 管理**"。
1. 选择 " **查看** "，为 API 管理服务实例添加默认成本估算。

> [!NOTE]
> 此示例中显示的成本仅用于演示目的。 有关最新定价信息，请参阅 [API 管理定价](https://azure.microsoft.com/pricing/details/api-management/) 。

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="估计开发人员层的成本":::

* 默认的成本估算基于 **开发人员**[服务层](api-management-features.md)中具有1个 [容量单位](api-management-capacity.md)的 API 管理服务实例。 开发人员层适用于非生产用例和评估。 它不受服务级别协议的支持。

* 若要估算额外容量单元或不同服务层的成本，请从 " **单位** 和 **层** " 下拉列表中选择其他选项。

* 根据功能可用性和服务层，可能需要支付额外费用才能使用 [自承载网关](self-hosted-gateway-overview.md)。

有关其他定价和功能的详细信息，请参阅：

* [API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)
* [Azure API 管理层的基于功能的比较](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>使用资金额度和 API 管理

你可以通过 Azure 预付款 (之前称为货币承诺) 来支付 API 管理费用。 但是，不能使用 Azure 预付款信用额度来支付第三方产品和服务（包括来自 Azure Marketplace 的产品和服务）的费用。

## <a name="monitor-costs"></a>监视成本

将 Azure 资源用于 API 管理时，会产生成本。 Azure 资源使用情况单位成本因时间间隔 (秒、分钟、小时和天) 或 (字节、mb 等) 的单位使用情况而异。 一旦开始使用 API 管理，就会产生成本，你可以在 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中查看成本。

使用成本分析时，可以在关系图和表中查看不同时间间隔的 API 管理成本。 有些示例是按天、当前和上个月和年。 你还可以查看预算和预测成本的成本。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势。 你会看到超支可能发生的位置。 如果已创建预算，还可以轻松地查看超出的位置。

> [!NOTE]
> 此示例中显示的成本仅用于演示目的。 根据资源使用情况和当前定价，你的成本将有所不同。

查看成本分析中的 API 管理成本：

1. 登录到 [Azure 门户](https://azure.microsoft.com)。
1. 打开 " **成本管理 + 计费** " 窗口，从菜单中选择 " **成本管理** "，然后选择 **计费范围**。 例如，从列表中选择一个订阅。
1. 从菜单中选择 " **成本管理** "，然后选择 " **成本分析**"。
1. 默认情况下，将在第一个圆环图中显示所有服务的每月费用。 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="订阅的每月成本":::

1. 若要降低单个服务（例如 API 管理）的成本，请选择 " **添加筛选器** "，然后选择 " **服务名称**"。 然后选择 " **API 管理**"。

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="示例显示 API 管理的累计成本":::

在前面的示例中，您将看到该服务的当前成本。 还显示了按资源组列出的 Azure 区域 (位置) 和 API 管理成本）的成本。 从这里，你可以自行探索成本。

## <a name="create-budgets"></a>创建预算

你可以创建 [预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 来管理成本，并创建 [警报](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ，以自动通知利益干系人支出异常和超支风险的利益干系人。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果你希望在监视中出现更多粒度，则可以在 Azure 中创建针对特定资源或服务的筛选器。 筛选器可帮助确保不会意外地创建新资源，从而降低成本。 有关创建预算时筛选器选项的详细信息，请参阅 [组和筛选选项](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

你还可以将 [成本数据导出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 到存储帐户。 当你需要其他人对成本进行额外的数据分析时，这会很有帮助。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 您可以按每天、每周或每月计划导出您的成本，并设置自定义的日期范围。 建议使用导出成本数据来检索成本数据集。

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>其他管理和降低 API 管理成本的方法

### <a name="choose-tier"></a>选择层

查看 [AZURE API 管理层基于功能的比较](api-management-features.md) ，以帮助确定适用于你的方案的服务层。 不同的服务层支持为各种用例设计的特性和功能的组合，但成本不同。 

* **消耗** 服务层提供不会产生固定成本的轻型无服务器选项。 将根据特定阈值上对服务的 API 调用数进行计费。 容量还会根据服务负载自动进行缩放。
* **开发人员**、**基本**、**标准** 和 **高级** API 管理层将产生每月费用，并为评估和生产工作负荷提供更大的吞吐量和更丰富的功能集。 随时[升级](upgrade-and-scale.md)到不同的服务层。

### <a name="scale-using-capacity-units"></a>使用容量单位进行缩放

除了消耗服务层，API 管理通过添加或删除 [*容量单元*](api-management-capacity.md)来支持扩展。 随着 API 管理实例的负载增加，增加容量单位比升级到更高的服务层更经济。 最大单位数取决于服务层。

每个容量单位都有特定的请求处理功能，该功能取决于服务层。 例如，"基本" 级别的单位为每秒大约1000个请求的预计最大吞吐量。 

当你按比例添加或删除单位、容量和成本。 例如，两个单元的标准级别提供大约每秒大约2000个请求的吞吐量。 由于请求或响应的大小、连接模式、发出请求的客户端的数量和其他因素，实际吞吐量可能会有所不同。

[监视](api-management-howto-use-azure-monitor.md) api 管理实例的容量指标，以帮助确定是否缩放或升级 api 管理实例以适应更多负载。

## <a name="next-steps"></a>后续步骤

- 了解 [如何通过 Azure 成本管理优化云投资](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何 [防止意外成本](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 采用 [成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引导式学习课程。
- 了解 API 管理 [容量](api-management-capacity.md)。
- 请参阅使用 [Azure 门户](upgrade-and-scale.md)缩放和升级 API 管理的步骤，并了解自动 [缩放](api-management-howto-autoscale.md)。
