---
title: 管理和优化成本
titleSuffix: Azure Machine Learning
description: 了解在 Azure 机器学习中构建机器学习模型时优化成本的技巧
author: luisquintanilla
ms.author: luquinta
ms.custom: subject-cost-optimization
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: 47f10b542bd2c4eb7965e6dfc25349661ca3c3d8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124805247"
---
# <a name="manage-and-optimize-azure-machine-learning-costs"></a>管理和优化 Azure 机器学习成本

了解在训练机器学习模型和将其部署到 Azure 机器学习时，如何管理和优化成本。

使用以下技巧来帮助管理和优化计算资源成本。

- 配置训练群集以实现自动缩放
- 设置订阅和工作区上的配额
- 针对“训练运行”设置终止策略
- 使用低优先级虚拟机 (VM)
- 将计算实例计划为自动关闭并启动
- 使用 Azure 虚拟机预留实例
- 在本地训练
- 并行化训练
- 设置数据保留和删除策略
- 将资源部署到同一区域

有关计划和监视成本的信息，请参阅[计划管理 Azure 机器学习成本](concept-plan-manage-cost.md)指南。

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>使用 Azure 机器学习计算群集 (AmlCompute)

随着数据的不断变化，需要快速且精简的模型训练和重训练流程来维持模型准确。 但是，持续训练需要付出一定代价，尤其是 GPU 上的深度学习模型。 

Azure 机器学习用户可以使用托管 Azure 机器学习计算群集（也称为 AmlCompute）。 AmlCompute 支持多种 GPU 和 CPU 选项。 AmlCompute 是由 Azure 机器学习代表你的订阅在内部进行托管的。 它在 Azure IaaS 云规模上提供相同的企业级安全性、合规性和治理功能。

由于这些计算池位于 Azure IaaS 基础结构内，因此，你可以按照与基础结构的其余部分相同的安全性和合规性要求来部署、缩放和管理训练。  这些部署在你的订阅中发生，遵循你的治理规则。 详细了解 [Azure 机器学习计算](how-to-create-attach-compute-cluster.md)。

## <a name="configure-training-clusters-for-autoscaling"></a>配置训练群集以实现自动缩放

根据工作负载要求自动缩放群集有助于降低成本，这样就可以只使用所需的内容。

AmlCompute 群集的设计旨在根据工作负载动态进行缩放。 群集可以纵向扩展到所配置的最大节点数。 每次运行完成后，群集将释放节点并缩放到所配置的最小节点计数。

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

还可以配置节点在纵向缩减之前处于空闲状态的时间量。 默认情况下，纵向缩减之前的空闲时间设置为 120 秒。

+ 如果执行迭代较少的试验，请缩短此时间以节省成本。
+ 如果迭代较多的开发/测试试验，可能需要增加此时间，这样就不用在每次更改训练脚本或环境后为纵向扩展或纵向缩减付费。

可以使用 [AmlCompute SDK 类](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute)、[AmlCompute CLI](/cli/azure/ml(v1)/computetarget/create#az_ml_v1__computetarget_create_amlcompute) 以及 [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable) 来配置 AmlCompute 群集，以适应 Azure 门户中不断变化的工作负载要求。

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>设置资源上的配额

AmlCompute 附带一个[配额（或限制）配置](how-to-manage-quotas.md#azure-machine-learning-compute)。 此配额根据 VM 系列（例如 Dv2 系列、NCv3 系列）而定，且因每个订阅的区域而异。 订阅从较小的默认值开始以便于你上手，但使用此设置可以控制在订阅中逐渐增加的 Amlcompute 资源量。 

对于订阅内的每个工作区，还需配置[按 VM 系列的工作区级别配额](how-to-manage-quotas.md#workspace-level-quotas)。 这样，你就可以对每个工作区可能产生的成本进行更精细的控制并限制某些 VM 系列。 

若要在工作区级别设置配额，请使用 [Azure 门户](https://portal.azure.com)。  选择订阅中的任何工作区，然后在左窗格中选择“使用量 + 配额”。 然后选择“配置配额”选项卡以查看配额。 你需要订阅范围的权限来设置配额，因为这是一个会影响多个工作区的设置。

## <a name="set-run-autotermination-policies"></a>设置“运行自动终止”策略 

在某些情况下，应对“训练运行”进行配置以限制其持续时间或提前终止。 例如，当使用 Azure 机器学习的内置超参数优化或自动化机器学习时。

以下是可执行的几个选择：
* 在 RunConfiguration 中定义名为 `max_run_duration_seconds` 的参数，用于控制一个运行可在所选计算（本地或远程云计算）上持续的最长持续时间。
* 对于[超参数优化](how-to-tune-hyperparameters.md#early-termination)，请在老虎机策略、中间值停止策略或截断选择策略中定义提前终止策略。 若要进一步控制超参数整理，请使用 `max_total_runs` 或 `max_duration_minutes` 等参数。
* 对于[自动化机器学习](how-to-configure-auto-train.md#exit)，请使用 `enable_early_stopping` 标志设置类似的终止策略。 另外，请使用诸如 `iteration_timeout_minutes` 和 `experiment_timeout_minutes` 等属性来控制运行的最长持续时间或整个试验的最长持续时间。

## <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a>使用低优先级 VM

Azure 允许在虚拟机规模集、Batch 和机器学习服务中将未利用的多余容量作为低优先级 VM 来使用。 这些容量分配是可预先抢占的，其价格比专用 VM 低。 通常，建议为 Batch 工作负载使用低优先级 VM。 如果可通过重新提交（对于 Batch 推理）或通过重启（对于具有检查点的深度学习培训）从中断中恢复，也应使用它们。

低优先级 VM 具有与专用配额值（按 VM 系列）不同的单一配额。 了解[有关 AmlCompute 配额的详细信息](how-to-manage-quotas.md)。

 低优先级 VM 不适用于计算实例，因为后者需要支持交互式笔记本体验。

## <a name="schedule-compute-instances"></a>计划计算实例

创建[计算实例](concept-compute-instance.md)时，VM 将保持启动状态，以供随时使用。  [设置计划](how-to-create-manage-compute-instance.md#schedule)来自动启动和停止计算实例（预览），以便在未计划使用实例时节省成本。

## <a name="use-reserved-instances"></a>使用预留实例

节省计算资源成本的另一种方法是 Azure 虚拟机预留实例。 此产品按一年或三年的期限提供。 其折扣最高可达即用即付价格的 72%，并直接应用于每月 Azure 帐单。

Azure 机器学习计算本身就支持预留实例。 如果你购买了一年或三年的预留实例，将自动根据你的 Azure 机器学习托管计算来应用折扣。

## <a name="train-locally"></a>在本地训练

在设计小到可在本地计算机上运行的训练作业的原型以及运行这些作业时，请考虑在本地进行训练。 使用 Python SDK，将计算目标设置为 `local` 会在本地执行脚本。 有关详细信息，请参阅[配置和提交训练运行](how-to-set-up-training-targets.md#select-a-compute-target)。

Visual Studio Code 提供了一个功能完备的环境，用于开发机器学习应用程序。 使用 Azure 机器学习 Visual Studio Code 扩展和 Docker，可在本地运行和调试。 有关详细信息，请参阅[使用 Visual Studio Code 进行交互式调试](how-to-debug-visual-studio-code.md)。

## <a name="parallelize-training"></a>并行化训练

优化成本和性能的关键方法之一是借助 Azure 机器学习中的并行运行步骤并行化工作负载。 此步骤允许使用多个较小节点并行执行任务，从而使你可以水平缩放。 并行化会产生开销。 是否选择此选项具体取决于工作负载和可实现的并行度。 有关详细信息，请参阅 [ParallelRunStep](xref:azureml.contrib.pipeline.steps.ParallelRunStep) 文档。

## <a name="set-data-retention--deletion-policies"></a>设置数据保留和删除策略

每次执行管道时，都会在每个步骤中生成中间数据集。 随着时间的推移，这些中间数据集会占用存储帐户中的空间。 请考虑设置策略，在数据的整个生命周期中管理数据，以存档和删除数据集。 有关详细信息，请参阅[通过自动执行 Azure Blob 存储访问层来优化成本](../storage/blobs/lifecycle-management-overview.md)。

## <a name="deploy-resources-to-the-same-region"></a>将资源部署到同一区域

位于不同区域的计算可能会遇到网络延迟并数据传输成本增加的问题。 Azure 网络成本源自 Azure 数据中心的出站带宽。 若要帮助降低网络成本，请将所有资源部署到该区域。 在数据所在的同一区域中预配 Azure 机器学习工作区和依赖资源可帮助降低成本并提高性能。

对于混合云场景（如使用 ExpressRoute 的场景），将所有资源移动到 Azure 以优化网络成本和延迟有时可能更具成本效益。

## <a name="next-steps"></a>后续步骤

- [计划管理 Azure 机器学习的成本](concept-plan-manage-cost.md)
- [在组织规模管理 Azure 机器学习的预算、成本和配额](/azure/cloud-adoption-framework/ready/azure-best-practices/optimize-ai-machine-learning-cost)