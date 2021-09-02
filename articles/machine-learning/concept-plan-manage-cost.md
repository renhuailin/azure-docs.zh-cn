---
title: 计划管理成本
titleSuffix: Azure Machine Learning
description: 使用 Azure 门户中的成本分析来计划和管理 Azure 机器学习成本。 了解更多节约成本的技巧，以降低构建 ML 模型时的成本。
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization, devx-track-azurecli
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: e48cdb3792a314166a29ced4d3828ba77de46621
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742063"
---
# <a name="plan-to-manage-costs-for-azure-machine-learning"></a>计划管理 Azure 机器学习的成本

本文介绍如何计划和管理 Azure 机器学习成本。 首先，请在添加任何资源以前使用 Azure 定价计算器来帮助计划成本。 接下来，在添加 Azure 资源时，查看预估成本。 

开始使用 Azure 机器学习资源后，使用成本管理功能来设置预算和监视成本。 另外，还应查看预测的成本并识别支出趋势，以确定可能需要实施措施的领域。

请知晓，Azure 机器学习的成本只是 Azure 账单中每月成本的一部分。 如果在使用其他 Azure 服务，需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。 本文介绍如何为 Azure 机器学习计划和管理成本。 在熟悉了如何管理 Azure 机器学习成本之后，应用类似的方法来管理订阅中使用的所有 Azure 服务的成本。

有关优化成本的详细信息，请参阅[如何在 Azure 机器学习中管理和优化成本](how-to-manage-optimize-cost.md)。

## <a name="prerequisites"></a>先决条件

成本管理中的成本分析支持大多数 Azure 帐户类型，但不支持所有帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 

若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="estimate-costs-before-using-azure-machine-learning"></a>使用 Azure 机器学习之前估计成本

- 在 Azure 机器学习工作区创建资源之前，请使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)估算成本。
在左侧，选择“AI + 机器学习”，然后选择“Azure 机器学习”，开始操作 。  

以下屏幕截图显示了使用计算器进行成本估算：

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="显示 Azure 定价计算器中的预估成本的示例。":::

向工作区添加新资源时，请返回到此计算器，并在此处添加相同资源来更新成本估算。

有关详细信息，请参阅 [Azure 机器学习定价](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>了解 Azure 机器学习的完整计费模式

Azure 机器学习在 Azure 基础结构上运行，部署新资源时，Azure 基础结构会随 Azure 机器学习的使用产生成本。 需要了解的是，其他基础结构可能会产生成本。 对已部署的资源进行更改时需要管理该成本。 


### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>会随 Azure 机器学习的使用而产生的成本

为 Azure 机器学习工作区创建资源时，会同时创建其他 Azure 服务的资源。 它们是：

* [Azure 容器注册表](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)基本帐户
* [Azure 块 Blob 存储](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)（常规用途 v1）
* [密钥保管库](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

创建[计算实例](concept-compute-instance.md)时，VM 将保持打开状态，以供你操作。  [设置计划](how-to-create-manage-compute-instance.md#schedule)以自动启动和停止计算实例（预览版），以节省非计划使用成本。
 
### <a name="costs-might-accrue-before-resource-deletion"></a>删除资源后可能会产生成本

在 Azure 门户中或使用 Azure CLI 删除 Azure 机器学习工作区之前，以下子资源均是累计的常见成本，即使你在工作区中未处于活动状态亦会如此。 如果你计划稍后返回 Azure 机器学习工作区，这些资源可能会持续产生成本。

* VM
* 负载均衡器
* 虚拟网络
* 带宽

每个 VM 在运行时均按小时计费。 成本取决于 VM 规范。 仍可通过负载均衡器对正在运行但未主动处理数据集的 VM 进行计费。 对于每个计算实例，系统将每天计费一个负载均衡器。 计算群集的每个 50 节点都将有一个计费的标准负载均衡器。 每个负载均衡器每天计费约 0.33 美元。 若要避免计算已停止计算实例和计算群集上的负载均衡器成本，请删除计算资源。 每个订阅和每个区域将按一个虚拟网络计费。 虚拟网络不能跨区域或订阅。 在 vNet 设置中设置专用终结点可能还会产生费用。 带宽按使用情况收；传输的数据越多，收取的费用就越多。

### <a name="costs-might-accrue-after-resource-deletion"></a>删除资源后可能会产生成本

在 Azure 门户中或使用 Azure CLI 删除某个 Azure 机器学习工作区后，以下资源会继续存在。 它们会持续产生成本，直到将其删除。

* Azure 容器注册表
* Azure 块 Blob 存储
* 密钥保管库
* Application Insights

要同时删除工作区和这些从属资源，请使用 SDK：

```python
ws.delete(delete_dependent_resources=True)
```

如果在工作区中创建 Azure Kubernetes 服务 (AKS)，或将任何计算资源附加到工作区，需要在 [Azure 门户](https://portal.azure.com)中单独删除这些资源。

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>将 Azure 预付款信用额度与 Azure 机器学习结合使用

可以使用 Azure 预付额度支付 Azure 机器学习费用。 但是，不能使用 Azure 预付款额度来支付第三方产品和服务（包括 Azure 市场中的）的费用。

## <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 门户中查看估计成本

<!-- Note for Azure service writer: If your service shows estimated costs when a user is creating resources in the Azure portal, at a minimum, insert this section as a brief walkthrough that steps through creating a Azure Machine Learning resource where the estimated cost is shown to the user, updated for your service. Add a screenshot where the estimated costs or subscription credits are shown.

If your service doesn't show costs as they create a resource or if estimated costs aren't shown to users before they use your service, then omit this section.

For example, you might start with the following (modify for your service):
-->

为 Azure 机器学习创建计算资源时，可以看到估计成本。

若要创建计算实例并查看估计价格，请执行以下操作：

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com)
1. 在左侧选择“计算”。
1. 在顶部工具栏中，选择“+ 新建”。
1. 查看显示的每个可用虚拟机大小的估计价格。
1. 完成资源创建过程。


:::image type="content" source="media/concept-plan-manage-cost/create-resource.png" alt-text="显示创建计算实例时的估计成本的示例。" lightbox="media/concept-plan-manage-cost/create-resource.png" :::

如果你的 Azure 订阅有支出限制，Azure 将阻止超过额度金额的支出。 创建和使用 Azure 资源时，将使用额度。 达到额度限制时，将在该计费周期的剩余时间内禁用部署的资源。 你无法更改额度限制，但可以删除该限制。 有关支出限制的详细信息，请参阅 [Azure 支出限制](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="monitor-costs"></a>监视成本

将 Azure 机器学习与 Azure 资源一起使用时，会产生成本。 Azure 资源使用情况的单位成本因时间间隔（秒、分钟、小时和天）或单位使用情况（字节、MB 等）而异。一旦开始使用 Azure 机器学习，就会产生费用，你可以在[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中看到这些费用。

使用成本分析时，可以在图表中查看不同时间间隔的 Azure 机器学习成本。 有些示例是按天、当前、上个月和年划分的。 还可以对比预算和预测的成本来查看成本。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势。 你会看到可能出现超支的地方。 如果已创建预算，还可以轻松查看超支的地方。

若要在成本分析中查看 Azure 机器学习，请执行以下操作：

1. 登录到 Azure 门户。
2. 在 Azure 门户中打开范围，并在菜单中选择“成本分析”。 例如，转到“订阅”，从列表中选择一项订阅，然后在菜单中选择“成本分析” 。 选择“范围”，在成本分析中切换到不同的范围。
3. 默认情况下，服务的成本显示在第一个圆环图中。 选择图表中标记为“Azure 机器学习”的区域。

最初打开成本分析时，会显示实际的每月成本。 下面是显示所有每月使用成本的示例。

:::image type="content" source="media/concept-plan-manage-cost/all-costs.png" alt-text="显示订阅累计成本的示例。" lightbox="media/concept-plan-manage-cost/all-costs.png" :::


若要缩小单个服务（如 Azure 机器学习）的成本，请选择“添加筛选器”，然后选择“服务名称” 。 然后选择“Azure 机器学习”。

下面的示例只显示了 Azure 机器学习的成本。

:::image type="content" source="media/concept-plan-manage-cost/vm-specific-cost.png" alt-text="显示 ServiceName 累积成本的示例。" lightbox="media/concept-plan-manage-cost/vm-specific-cost.png" :::

<!-- Note to Azure service writer: The image shows an example for Azure Storage. Replace the example image with one that shows costs for your service. -->

在前面的示例中，可查看服务的当前成本。 还显示了 Azure 区域（位置）的成本和资源组的 Azure 机器学习成本。 从这里，你可以自行探索成本。
## <a name="create-budgets"></a>创建预算

可以创建[预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本，并创建[警报](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果希望增加监视中的精度，可以在 Azure 中使用筛选器为特定资源或服务创建预算。 筛选器可帮助确保不会意外创建会产生额外成本的新资源。 有关创建预算时筛选器选项的详细信息，请参阅[对选项进行分组和筛选](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

还可以将[成本数据导出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)到存储帐户。 当你或其他人需要进行有关成本的额外数据分析时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 可以按每天、每周或每月计划导出成本，并设置自定义的日期范围。 建议导出成本数据来检索成本数据集。

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>管理和降低 Azure 机器学习成本的其他方式

使用以下技巧来帮助管理和优化计算资源成本。

- 配置训练群集以实现自动缩放
- 设置订阅和工作区上的配额
- 针对“训练运行”设置终止策略
- 使用低优先级虚拟机 (VM)
- 将计算实例计划为自动关闭并启动
- 使用 Azure 虚拟机预留实例
- 本地训练
- 并行化训练
- 设置数据保留和删除策略
- 将资源部署到同一区域
- 如果你不打算在不久的将来使用这些实例和群集，请将其删除。

有关详细信息，请参阅[管理和优化 Azure 机器学习成本](how-to-manage-optimize-cost.md)。

## <a name="next-steps"></a>后续步骤

- [管理和优化 Azure 机器学习成本](how-to-manage-optimize-cost.md)。
- [在组织范围内管理 Azure 机器学习的预算、成本和配额](/azure/cloud-adoption-framework/ready/azure-best-practices/optimize-ai-machine-learning-cost)
- 了解[如何通过 Azure 成本管理优化云投资](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何[防止意外成本](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 参与[成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)引导式学习课程。
