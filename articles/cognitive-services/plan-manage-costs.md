---
title: 计划管理 Azure 认知服务的成本
description: 了解如何使用 Azure 门户中的成本分析来规划和管理 Azure 认知服务的成本。
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 48e111ac8b2b4fd3c1e2fee568e20699896dfff3
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705759"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>规划和管理 Azure 认知服务的成本

本文介绍如何计划和管理 Azure 认知服务的成本。 首先，使用 Azure 定价计算器来帮助规划认知服务成本，然后再添加任何服务资源来估算成本。 接下来，当你添加 Azure 资源时，请查看预估成本。 开始使用认知服务资源之后 (例如语音、计算机视觉、LUIS、文本分析、转换器等 ) ，使用成本管理功能来设置预算和监控成本。 你还可以查看预测成本并确定支出趋势，以确定你可能想要采取的措施。认知服务的成本仅是 Azure 帐单中每月成本的一部分。 尽管本文介绍了如何规划和管理认知服务的成本，但你需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。

## <a name="prerequisites"></a>先决条件

成本管理中的成本分析支持大多数 Azure 帐户类型，但不支持所有帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，至少需要一个 Azure 帐户的读取权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>使用认知服务之前估计成本

在添加认知服务之前，使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/) 估算成本。

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="用于认知服务的 Azure 定价计算器" border="true":::

向工作区添加新资源时，请返回到此计算器，并在此处添加相同资源来更新成本估算。

有关详细信息，请参阅 [Azure 认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services/)。

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>了解认知服务的完整计费模式

认知服务在 Azure 基础结构上运行，在你部署新资源时，会产生 [成本](https://azure.microsoft.com/pricing/details/cognitive-services/) 。 了解其他基础结构可能会产生成本，这一点很重要。 你需要在对已部署的资源进行更改时管理该成本。 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>通常随认知服务而产生的成本

通常情况下，在部署 Azure 资源后，成本取决于你的定价层和你对终结点所做的 API 调用。 如果你正在使用的服务具有承诺层，则在你的层中进行分配的调用可能会产生超额费用。

使用这些服务时，可能会产生额外成本：

#### <a name="qna-maker"></a>QnA Maker

为 QnA Maker 创建资源时，还可以创建其他 Azure 服务的资源。 它们包括：

- [运行时的 Azure App Service () ](https://azure.microsoft.com/pricing/details/app-service/)
- [数据) 的 Azure 认知搜索 (](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>删除资源后可能会产生的成本

#### <a name="qna-maker"></a>QnA Maker

删除 QnA Maker 资源后，以下资源可能会继续存在。 它们会持续产生成本，直到你将其删除。

- [运行时的 Azure App Service () ](https://azure.microsoft.com/pricing/details/app-service/)
- [数据) 的 Azure 认知搜索 (](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-monetary-credit-with-cognitive-services"></a>将资金额度用于认知服务

你可以通过你的 EA 货币承诺信用额度支付认知服务费用。 但是，不能使用 EA 货币承诺信用额度来支付第三方产品和服务（包括来自 Azure Marketplace 的产品和服务）的费用。

## <a name="create-budgets"></a>创建预算

你可以创建 [预算](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 来管理成本，并创建 [警报](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ，以自动通知利益干系人支出异常和超支风险的利益干系人。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果你希望在监视中出现更多粒度，则可以在 Azure 中创建针对特定资源或服务的筛选器。 筛选器可帮助确保不会意外地创建新资源，从而降低成本。 有关创建预算时筛选器选项的详细信息，请参阅 [组和筛选选项](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

你还可以将 [成本数据导出](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 到存储帐户。 当你需要或其他人进行额外的数据分析以获得成本时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 您可以按每天、每周或每月计划导出您的成本，并设置自定义的日期范围。 建议使用导出成本数据来检索成本数据集。

<!--
## Other ways to manage and reduce costs for Cognitive Services

Work with Dean to complete this section in 2021.

-->

## <a name="next-steps"></a>后续步骤

- 了解 [如何通过 Azure 成本管理优化云投资](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过 [成本分析](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何 [防止意外成本](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 采用 [成本管理](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引导式学习课程。
