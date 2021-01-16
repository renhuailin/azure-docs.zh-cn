---
title: 计划和管理成本
titleSuffix: Azure Machine Learning
description: 使用 Azure 门户中的成本分析来计划和管理 Azure 机器学习成本。 了解更多节约成本的技巧，以降低构建 ML 模型时的成本。
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 81d9a6bcbd910a60e1f659e430bbe527eb3077c6
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251737"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>计划和管理 Azure 机器学习成本

本文介绍如何计划和管理 Azure 机器学习成本。 首先，请在添加任何资源以前使用 Azure 定价计算器来帮助计划成本。 接下来，在添加 Azure 资源时，查看预估成本。 最后，在使用托管 Azure 机器学习计算群集训练模型时，请运用成本节省技巧。

开始使用 Azure 机器学习资源后，使用成本管理功能来设置预算和监视成本。 另外，还应查看预测的成本并识别支出趋势，以确定可能需要实施措施的领域。

请知晓，Azure 机器学习的成本只是 Azure 账单中每月成本的一部分。 如果在使用其他 Azure 服务，需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。 本文介绍如何为 Azure 机器学习计划和管理成本。 在熟悉了如何管理 Azure 机器学习成本之后，应用类似的方法来管理订阅中使用的所有 Azure 服务的成本。

训练机器学习模型时，通过使用托管 Azure 机器学习计算群集来利用更多节省成本的技巧：

* 配置训练群集以实现自动缩放
* 设置订阅和工作区上的配额
* 针对“训练运行”设置终止策略
* 使用低优先级虚拟机 (VM)
* 使用 Azure 虚拟机预留实例

## <a name="prerequisites"></a>先决条件

成本分析支持各种 Azure 帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 

若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="estimate-costs-before-using-azure-machine-learning"></a>使用 Azure 机器学习之前估计成本

在 Azure 机器学习帐户中创建资源之前，使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)估算成本。 在左侧，选择“AI + 机器学习”，然后选择“Azure 机器学习”，开始操作 。  

以下屏幕截图显示了使用计算器进行成本估算：

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 计算器中的成本估算":::

向工作区添加新资源时，请返回到此计算器，并在此处添加相同资源来更新成本估算。

有关详细信息，请参阅 [Azure 机器学习定价](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>了解 Azure 机器学习的完整计费模式

Azure 机器学习在 Azure 基础结构上运行，该基础结构在部署新资源时 Azure 机器学习与进行计费。 了解其他基础结构可能会产生成本，这一点很重要。 你需要在对已部署的资源进行更改时管理该成本。 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>通常 Azure 机器学习的成本

为 Azure 机器学习工作区创建资源时，还会创建其他 Azure 服务的资源。 它们是：

* [Azure 容器注册表](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 基本帐户
* [Azure 块 Blob 存储](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (常规用途 v1) 
* [密钥保管库](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>删除资源后可能会产生成本

当你在 Azure 门户中或使用 Azure CLI 删除 Azure 机器学习工作区时，以下资源将继续存在。 它们会持续产生成本，直到你将其删除。

* Azure 容器注册表
* Azure 块 Blob 存储
* 密钥保管库
* Application Insights

若要删除工作区以及这些从属资源，请使用 SDK：

```python
ws.delete(delete_dependent_resources=True)
```

如果在工作区中创建 Azure Kubernetes Service (AKS) ，或将任何计算资源附加到工作区，则必须在 [Azure 门户](https://portal.azure.com)中单独删除这些资源。

### <a name="using-monetary-credit-with-azure-machine-learning"></a>使用货币信用额度 Azure 机器学习

你可以通过 EA 货币承诺信用额度支付 Azure 机器学习费用。 但是，不能使用 EA 货币承诺信用额度来支付第三方产品和服务（包括来自 Azure Marketplace 的产品和服务）的费用。


## <a name="create-budgets"></a>创建预算

你可以创建 [预算](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 来管理成本，并创建 [警报](../cost-management/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ，以自动通知利益干系人支出异常和超支风险的利益干系人。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果你希望在监视中出现更多粒度，则可以在 Azure 中创建针对特定资源或服务的筛选器。 筛选器可帮助确保不会意外地创建新资源，从而降低成本。 有关创建预算时筛选器选项的详细信息，请参阅 [组和筛选选项](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

你还可以将 [成本数据导出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 到存储帐户。 当你需要或其他人进行额外的数据分析以获得成本时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 您可以按每天、每周或每月计划导出您的成本，并设置自定义的日期范围。 建议使用导出成本数据来检索成本数据集。

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>管理和降低 Azure 机器学习成本的其他方式

使用这些提示来包含机器学习计算资源的成本。

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>使用 Azure 机器学习计算群集 (AmlCompute)

随着数据的不断变化，需要快速且精简的模型训练和重训练流程来维持模型准确。 但是，持续训练需要付出一定代价，尤其是 GPU 上的深度学习模型。 

Azure 机器学习用户可以使用托管 Azure 机器学习计算群集（也称为 AmlCompute）。 AmlCompute 支持多种 GPU 和 CPU 选项。 AmlCompute 是由 Azure 机器学习代表你的订阅在内部进行托管的。 它在 Azure IaaS 云规模上提供相同的企业级安全性、合规性和治理功能。

由于这些计算池位于 Azure IaaS 基础结构内，因此，你可以按照与基础结构的其余部分相同的安全性和合规性要求来部署、缩放和管理训练。  这些部署在你的订阅中发生，遵循你的治理规则。 了解有关 [Azure 机器学习计算](how-to-create-attach-compute-cluster.md)的详细信息。

### <a name="configure-training-clusters-for-autoscaling"></a>配置训练群集以实现自动缩放

根据工作负载要求自动缩放群集有助于降低成本，这样就可以只使用所需的内容。

AmlCompute 群集的设计旨在根据工作负载动态进行缩放。 群集可以纵向扩展到所配置的最大节点数。 每次运行完成后，群集将释放节点并缩放到所配置的最小节点计数。

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

还可以配置节点在纵向缩减之前处于空闲状态的时间量。 默认情况下，纵向缩减之前的空闲时间设置为 120 秒。

+ 如果执行迭代较少的试验，请缩短此时间以节省成本。
+ 如果迭代较多的开发/测试试验，可能需要增加此时间，这样就不用在每次更改训练脚本或环境后为纵向扩展或纵向缩减付费。

可以使用 [AmlCompute SDK 类](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py)、[AmlCompute CLI](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute) 以及 [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable) 来配置 AmlCompute 群集，以适应 Azure 门户中不断变化的工作负载要求。

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>设置资源上的配额

AmlCompute 附带一个[配额（或限制）配置](how-to-manage-quotas.md#azure-machine-learning-compute)。 此配额根据 VM 系列（例如 Dv2 系列、NCv3 系列）而定，且因每个订阅的区域而异。 订阅从较小的默认值开始以便于你上手，但使用此设置可以控制在订阅中逐渐增加的 Amlcompute 资源量。 

对于订阅内的每个工作区，还需配置[按 VM 系列的工作区级别配额](how-to-manage-quotas.md#workspace-level-quotas)。 这样，你就可以对每个工作区可能产生的成本进行更精细的控制并限制某些 VM 系列。 

若要在工作区级别设置配额，请使用 [Azure 门户](https://portal.azure.com)。  选择订阅中的任何工作区，然后在左窗格中选择“使用量 + 配额”。 然后选择“配置配额”选项卡以查看配额。 你需要订阅范围的权限来设置配额，因为这是一个会影响多个工作区的设置。

### <a name="set-run-autotermination-policies"></a>设置“运行自动终止”策略 

在某些情况下，应对“训练运行”进行配置以限制其持续时间或提前终止。 例如，当使用 Azure 机器学习的内置超参数优化或自动化机器学习时。

以下是可执行的几个选择：
* 在 RunConfiguration 中定义名为 `max_run_duration_seconds` 的参数，用于控制一个运行可在所选计算（本地或远程云计算）上持续的最长持续时间。
* 对于[超参数优化](how-to-tune-hyperparameters.md#early-termination)，请在老虎机策略、中间值停止策略或截断选择策略中定义提前终止策略。 若要进一步控制超参数整理，请使用 `max_total_runs` 或 `max_duration_minutes` 等参数。
* 对于[自动化机器学习](how-to-configure-auto-train.md#exit)，请使用 `enable_early_stopping` 标志设置类似的终止策略。 另外，请使用诸如 `iteration_timeout_minutes` 和 `experiment_timeout_minutes` 等属性来控制运行的最长持续时间或整个试验的最长持续时间。

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> 使用低优先级 Vm

Azure 允许在虚拟机规模集、Batch 和机器学习服务中将未利用的多余容量作为低优先级 VM 来使用。 这些容量分配是可预先抢占的，其价格比专用 VM 低。 通常，建议为 Batch 工作负载使用低优先级 VM。 如果可通过重新提交（对于 Batch 推理）或通过重启（对于具有检查点的深度学习培训）从中断中恢复，也应使用它们。

低优先级 VM 具有与专用配额值（按 VM 系列）不同的单一配额。 了解[有关 AmlCompute 配额的详细信息](how-to-manage-quotas.md)。

 低优先级 VM 不适用于计算实例，因为后者需要支持交互式笔记本体验。

### <a name="use-reserved-instances"></a>使用预留实例

节省计算资源成本的另一种方法是 Azure 虚拟机预留实例。 此产品按一年或三年的期限提供。 其折扣最高可达即用即付价格的 72%，并直接应用于每月 Azure 帐单。

Azure 机器学习计算本身就支持预留实例。 如果你购买了一年或三年的预留实例，将自动根据你的 Azure 机器学习托管计算来应用折扣。


## <a name="next-steps"></a>后续步骤

- 了解 [如何通过 Azure 成本管理优化云投资](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何 [防止意外成本](../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 采用 [成本管理](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引导式学习课程。