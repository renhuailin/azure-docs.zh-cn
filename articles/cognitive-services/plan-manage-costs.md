---
title: 规划管理 Azure 认知服务的成本
description: 了解如何使用 Azure 门户中的成本分析来规划和管理 Azure 认知服务的成本。
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: db99fa5caff27a24aa04e4780b25ade3f7c25496
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699923"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>规划和管理 Azure 认知服务的成本

本文介绍如何规划和管理 Azure 认知服务的成本。 首先，使用 Azure 定价计算器来帮助规划认知服务成本，然后再添加任何服务资源来估算成本。 接下来，在添加 Azure 资源时，查看预估成本。 开始使用认知服务资源（例如语音、计算机视觉、LUIS、文本分析、翻译等）之后，使用成本管理功能可设置预算和监控成本。 还可以查看预测的成本并识别支出趋势，以确定可能需要实施措施的领域。认知服务的成本只是 Azure 账单中每月成本的一部分。 尽管本文介绍的是如何规划和管理认知服务的成本，但你需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。

## <a name="prerequisites"></a>必备条件

成本管理中的成本分析支持大多数 Azure 帐户类型，但并非支持所有帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>使用认知服务之前估算成本

添加认知服务之前，请使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)估算成本。

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="用于认知服务的 Azure 定价计算器" border="true":::

向工作区添加新资源时，请返回到此计算器，并在此处添加相同资源来更新成本估算。

有关详细信息，请参阅 [Azure 认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services/)。

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>了解认知服务的完整计费模式

认知服务在 Azure 基础结构上运行，部署新资源时，该基础结构会随之[产生成本](https://azure.microsoft.com/pricing/details/cognitive-services/)。 需要了解的是，其他基础结构可能会产生成本。 对已部署的资源进行更改时需要管理该成本。 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>通常随认知服务而产生的成本

通常情况下，在部署 Azure 资源后，成本取决于你的定价层和你对终结点所做的 API 调用。 如果你正在使用的服务具有承诺层，则在你的层中检查分配的调用可能会产生超额费用。

使用下列服务时，可能会产生额外成本：

#### <a name="qna-maker"></a>QnA Maker

为 QnA Maker 创建资源时，还可能会创建其他 Azure 服务的资源。 它们包括：

- [Azure 应用服务（适用于运行时）](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure 认知搜索（适用于数据）](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>删除资源后可能会产生的成本

#### <a name="qna-maker"></a>QnA Maker

删除 QnA Maker 资源后，以下资源可能会继续存在。 它们会持续产生成本，直到将其删除。

- [Azure 应用服务（适用于运行时）](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure 认知搜索（适用于数据）](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-azure-prepayment-credit-with-cognitive-services"></a>对于认知服务使用 Azure 预付款信用额度

可以使用 Azure 预付款（之前称为货币承诺）信用额度支付认知服务费用。 但是，不能使用 Azure 预付款信用额度来支付第三方产品和服务（包括 Azure 市场中的第三方产品和服务）的费用。

## <a name="monitor-costs"></a>监视成本

<!-- Note to Azure service writer: Modify the following as needed for your service. Replace example screenshots with ones taken for your service. If you need assistance capturing screenshots, ask banders for help. -->

对于认知服务使用 Azure 资源时会产生成本。 Azure 资源使用单位成本因时间间隔（秒、分钟、小时和天）或单位使用情况（字节、MB 等）而异。 一旦开始使用一项或多项认知服务，就会产生成本，你可以在[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中查看成本。

使用成本分析时，可以图表和表格形式查看不同时间间隔的认知服务成本。 有些示例是按天、当月和上个月以及按年来统计的。 此外，还可以查看成本与预算和预测成本的对比情况。 随着时间推移，切换到较长时间的视图，可帮助你确定支出趋势。 你可看到哪些领域出现了超支。 如果已创建预算，还可以轻松查看超支的地方。

在成本分析中查看认知服务成本：

1. 登录到 Azure 门户。
2. 在 Azure 门户中打开范围，并在菜单中选择“成本分析”。 例如，转到“订阅”，从列表中选择一项订阅，然后在菜单中选择“成本分析” 。 选择“范围”，可切换到不同的成本分析范围。
3. 默认情况下，服务的成本显示在第一个圆环图中。 选择图表中标记为“认知服务”的区域。

最初打开成本分析时，会显示每月实际成本。 下面是一个显示所有每月使用成本的示例。

:::image type="content" source="./media/cost-management/all-costs.png" alt-text="显示累计订阅成本的示例":::

- 若要缩小范围来查看单项服务（例如认知服务）的成本，请选择“添加筛选器”，然后选择“服务名称” 。 然后，选择“认知服务”。

下面是一个仅显示认知服务成本的示例。

:::image type="content" source="./media/cost-management/cognitive-services-costs.png" alt-text="显示认知服务累计成本的示例":::

在前面的示例中，你可查看服务的当前成本。 还可显示按 Azure 地区（位置）统计的成本以及按资源组统计的认知服务成本。 自此开始，你可以自行探索成本信息。

## <a name="create-budgets"></a>创建预算

可以创建[预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本，并创建[警报](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果希望增加监视中的精度，可以在 Azure 中使用筛选器为特定资源或服务创建预算。 筛选器可帮助确保不会意外创建会产生额外成本的新资源。 有关创建预算时筛选器选项的详细信息，请参阅[对选项进行分组和筛选](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

还可以将[成本数据导出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)到存储帐户。 当你或其他人需要进行有关成本的额外数据分析时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 可以按每天、每周或每月计划导出成本，并设置自定义的日期范围。 建议导出成本数据来检索成本数据集。

## <a name="next-steps"></a>后续步骤

- 了解[如何通过 Azure 成本管理优化云投资](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何[防止意外成本](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 参与[成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)引导式学习课程。