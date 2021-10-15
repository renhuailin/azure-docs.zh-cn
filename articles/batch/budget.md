---
title: 获取 Azure Batch 的成本分析并为其设置预算
description: 了解如何针对那些用于运行 Batch 工作负荷的基础计算资源和软件许可证获取成本分析、设置预算和降低成本。
ms.topic: how-to
ms.date: 10/04/2021
ms.openlocfilehash: 0e4c0b6235537d1e5c36e7adf997516d0f966d76
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532557"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>获取 Azure Batch 的成本分析并为其设置预算

本主题将帮助你了解 Azure Batch 相关的成本、如何为 Batch 池或帐户设置预算，以及降低 Batch 工作负载成本的方法。

## <a name="understand-costs-associated-with-batch-resources"></a>了解 Batch 资源相关的成本

使用 Azure Batch 本身不会产生任何费用，但是用于运行 Batch 工作负荷的底层计算资源和软件许可证可能会产生费用。 成本可能产生于池中的虚拟机 (VM)、从 VM 进行的数据传输，或云中存储的任何输入或输出数据。

### <a name="virtual-machines"></a>虚拟机

虚拟机是用于 Batch 处理的最重要资源。 Batch 的 VM 使用成本是根据类型、数量和使用持续时间计算的。 VM 计费选项包括[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)或[预留](../cost-management-billing/reservations/save-compute-costs-reservations.md)（提前支付）。 根据具体的计算工作负载，这两个支付选项各有优势，将对帐单产生不同的影响。

使用[虚拟机配置](nodes-and-pools.md#virtual-machine-configuration)在池中创建的每个 VM 都有一个使用 Azure 托管磁盘的关联 OS 磁盘。 Azure 托管磁盘有额外的成本，其他磁盘性能层也有不同的成本。

### <a name="storage"></a>存储

使用[应用包](batch-application-packages.md)将应用部署到 Batch 节点 (VM) 时，还需要为应用包使用的 Azure 存储资源付费。 你还要支付任何输入或输出文件（例如资源文件和其他日志数据）的存储费。

通常，与 Batch 关联的存储数据的成本要比计算资源的成本低得多。

### <a name="networking-resources"></a>网络资源

Batch 池使用网络资源，其中的某些资源会产生相关成本。 具体而言，对于虚拟机配置池，将使用标准负载均衡器，这需要静态 IP 地址。 Batch 使用的负载均衡器对于在用户订阅模式下配置的[帐户](accounts.md#batch-accounts)可见，但对于在 Batch 服务模式下配置的帐户不可见。

传入和传出 Batch 池 VM 的所有数据会产生标准负载均衡器费用。 从池节点检索数据的某些 Batch API（例如“获取任务/节点文件”）、任务应用程序包、资源/输出文件和容器映像也会产生费用。

### <a name="additional-services"></a>其他服务

根据要将哪些服务用于 Batch 解决方案，可能会产生额外的费用。 若要确定每个附加服务的成本，请参阅[定价计算器](https://azure.microsoft.com/pricing/calculator/)。 经常与 Batch 配合使用的、可能会产生相关成本的服务包括：

- Application Insights
- 数据工厂
- Azure Monitor
- 虚拟网络
- 带有图形应用的 VM

## <a name="view-cost-analysis-and-create-budgets"></a>查看成本分析并创建预算

借助 [Azure 成本管理](/cost-management-billing/cost-management-billing-overview.md)，你可以规划、分析和减少开支，最大程度利用云投资。 所有 Azure 服务的使用成本都可用，包括 Azure Batch。 你可以查看和筛选要查看和筛选的 Batch 成本，预测未来成本，设置支出限制并在达到这些限制时发出警报。

在 Azure 门户中，可以针对 Batch 池或 Batch 帐户创建预算和支出警报。 存在任何透支风险时，预算和警报可用于通知利益干系人，不过，支出警报可能会有所延迟，并可能存在略微超出预算的情况。

下面的屏幕截图显示了订阅的“成本分析”视图示例，筛选后只显示与所有 Batch 帐户关联的累计成本。 下图显示了所选时间段的总成本如何按已使用的服务、位置和计量分类。 尽管这是一个示例，并不能反映你看到的订阅成本，但通常情况下，为 Batch 池节点分配的虚拟机的成本最大。

:::image type="content" source="media/batch-budget/subscription-cost-analysis.png" alt-text="显示 Azure 门户中订阅的所有 Batch 帐户的成本分析的屏幕截图。":::

可以通过指定“资源”筛选器来获取更高级别的成本分析详细信息。 对于 Batch 帐户，这些值是 Batch 帐户名和池名称。 这允许你查看特定池、多个池或一个或多个帐户的成本。

### <a name="view-cost-analysis-for-a-batch-pool"></a>查看 Batch 池的成本分析

#### <a name="batch-service-pool-allocation-mode"></a>Batch 服务池分配模式

对于用 Batch 服务池分配模式创建的 Batch 帐户：

1. 在 Azure 门户中，键入或选择“成本管理 + 计费”。
1. 在“计费范围”部分选择你的订阅。
1. 在“成本管理”下，选择“成本分析”。
1. 选择“添加筛选器”。 在第一个下拉列表中，选择“资源”。
1. 在第二个下拉列表中，选择 Batch 池。 选择池时，将看到该池的成本分析。 下面的屏幕快照显示了示例数据。
   :::image type="content" source="media/batch-budget/pool-cost-analysis.png" alt-text="显示 Azure 门户中 Batch 池的成本分析的屏幕截图。":::

生成的成本分析显示了池的成本，以及此成本来自哪些资源。 在此示例中，池中使用的 VM 是成本最高的资源。

> [!NOTE]
> 此示例中的池使用虚拟机配置，这是[针对大多数池建议的配置](batch-pool-cloud-service-to-virtual-machine-configuration.md)，根据虚拟机定价结构收费。 使用 **云服务配置** 的池根据云服务定价结构收费。

[标记](../azure-resource-manager/management/tag-resources.md)可以与 Batch 帐户相关联，从而允许标记用于进一步的成本筛选。 例如，可以使用标记将项目、用户或组信息与 Batch 帐户相关联。 标记当前无法与 Batch 池关联。

#### <a name="user-subscription-pool-allocation-mode"></a>用户订阅池分配模式

对于使用用户订阅池分配模式创建的 Batch 帐户：

1. 在 Azure 门户中，键入或选择“成本管理 + 计费”。
1. 在“计费范围”部分选择你的订阅。
1. 在“成本管理”下，选择“成本分析”。
1. 选择“添加筛选器”。 在第一个下拉列表中，选择“标记”。
1. 在第二个下拉列表中，选择“poolname”。
1. 在第三个下拉列表中，选择“Batch 池”。 选择池时，将看到该池的成本分析。 下面的屏幕快照显示了示例数据。
   :::image type="content" source="media/batch-budget/user-subscription-pool.png" alt-text="显示 Azure 门户中用户订阅 Batch 池成本分析的屏幕截图。":::

请注意，如果你有兴趣查看用户订阅 Batch 帐户中所有池的成本数据，则可以在第二个下拉菜单中选择“batchaccountname”，并在第三个下拉菜单中选择 Batch 帐户的名称。 

> [!NOTE]
> 用户订阅 Batch 帐户创建的池不会显示在“资源”筛选器下，但在服务名称下筛选“虚拟机”时，其使用情况仍会显示。

### <a name="create-a-budget-for-a-batch-pool"></a>为 Batch 池创建预算

可以创建预算，并在达到不同的预算百分比时（如 60%、80% 和 100%）发出成本警告。 预算可以指定一个或多个筛选器，因此，你可以在各种粒度监视 Batch 帐户成本并发出警报。

1. 在“成本分析”页中，选择“预算: 无” 。
1. 选择“创建新预算 >”。
1. 使用出现的窗口专门为池配置预算。 有关详细信息，请参阅[教程：创建和管理 Azure 预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md)。

## <a name="minimize-costs-associated-with-azure-batch"></a>最大程度地降低 Azure Batch 相关的成本

你可能想要根据自己的情况尽可能地降低成本。 请考虑使用以下一种或多种策略来最大程度地提高工作负载效率并降低潜在成本。

### <a name="reduce-pool-node-use"></a>减少池节点使用

与使用 Batch 相关的最大成本通常来自为池节点分配的虚拟机。 对于虚拟机配置池，用于 VM 操作系统磁盘的关联托管磁盘也会显著影响成本。

评估 Batch 应用程序，以确定作业任务是否充分利用了池节点，或者池节点的空闲时间是否超过了预期时间。 可以减少分配的池节点的数量，降低池节点纵向扩展率，或提高纵向缩减率，以提高利用率。

除了自定义监视之外，[Batch 指标](batch-diagnostics.md#view-batch-metrics)还有助于标识已分配但处于空闲状态的节点。 你可以使用 Azure 门户中的 Batch 监视指标来选择适用于大多数池节点状态的指标。 例如，可以查看“空闲节点计数”和“正在运行的节点计数”以指示池节点的使用情况。

### <a name="ensure-pool-nodes-are-able-to-run-tasks"></a>确保池节点能够运行任务

为池列出的分配节点通常会产生成本，但池节点可能处于无法运行任务的状态，例如“unusable”或“starttaskfailed”。 Batch API 或指标可用于监视和检测此类 VM。 然后，可以确定这些状态的原因，并采取纠正措施来减少或消除这些不正常的节点。

### <a name="use-the-right-pool-node-vm-size"></a>使用正确的池节点 VM 大小

请确保使用适当的 VM 大小，以便 VM 在运行任务时得到充分利用，同时提供在所需时间内完成作业任务所需的性能。 在某些情况下（例如 CPU 使用率较低），池节点 VM 可能未充分利用。 可以通过选择价格较低的 VM 大小来节省成本。

若要确定 VM 利用率，可以在运行任务时登录到节点以查看性能数据，或使用[监视功能](monitoring-overview.md)（如 Application Insights）从池节点获取性能数据。

### <a name="use-pool-slots-to-reduce-node-requirements"></a>使用池槽减少节点要求

可以为一个池指定多个任务槽，以便可以在每个节点上并行运行相应的任务数。 可通过选择更大的 VM 大小和在节点上并行运行多个任务，使用池任务槽来减少池中使用的节点数，确保节点得到充分利用。 如果节点未充分利用，可以使用槽来提高利用率。 例如，对于单线程任务应用程序，每个核心可以配置一个槽。 槽数量可以比内核多。 例如，如果应用程序显著阻止等待返回对外部服务的调用，则此情况适用。

将 [`taskSchedulingPolicy`](/rest/api/batchservice/pool/add#taskschedulingpolicy) 设置为 `pack` 有助于确保尽可能利用 VM，通过缩放可以更轻松地删除未运行任何任务的节点。

### <a name="use-low-priority-virtual-machines"></a>使用低优先级虚拟机

[低优先级 VM](batch-low-pri-vms.md) 通过利用 Azure 中的盈余计算容量来降低 Batch 工作负载的成本。 当你在池中指定低优先级 VM 时，Batch 使用此盈余来运行工作负荷。 使用低优先级 VM 而不是专用 VM 可能会节省大量成本。 请记住，低优先级 VM 不适合所有工作负荷，因为可能没有可用的容量可分配，或者它们可能会被抢占。

### <a name="use-ephemeral-os-disks"></a>使用临时 OS 磁盘

默认情况下，池节点使用托管磁盘，这会产生费用。 某些 VM 大小的虚拟机配置池可使用[临时 OS 磁盘](create-pool-ephemeral-os-disk.md)来避免产生与托管磁盘相关的额外费用，临时磁盘会在 VM 缓存或临时 SSD 上创建 OS 磁盘。

### <a name="purchase-reservations-for-virtual-machine-instances"></a>为虚拟机实例购买预留容量

如果你打算长时间使用 Batch，可以通过对工作负载使用 [Azure 预留](../cost-management-billing/reservations/save-compute-costs-reservations.md)来节省 VM 成本。 预留费率比即用即付费率低得多。 没有预留容量的虚拟机实例将按即用即付费率收费。 购买预留容量时，将应用预留折扣。 通过承诺为 [VM 实例](../virtual-machines/prepay-reserved-vm-instances.md)执行一年或三年计划，对 VM 使用情况（包括[通过 Batch 池使用的 VM](../virtual-machines/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)）应用大量折扣。

必须注意，预留折扣“不用就会失效”。  如果一个小时内没有使用匹配的资源，将失去该小时的预留数量。 未使用的预留小时数无法结转，因此如果未使用，将丢失。 Batch 工作负荷通常根据负载缩放分配的 VM 数量，且负载随时间变化，甚至某个时段没有负载。 因此，需要谨慎确定预留量，因为如果 Batch VM 缩减到预留数量以下，预留小时数将丢失。

### <a name="use-automatic-scaling"></a>使用自动缩放

[自动缩放](batch-automatic-scaling.md)根据当前作业的需求来动态缩放 Batch 池中的 VM 数。 自动缩放根据作业的生存期缩放池，确保仅在需要执行某个作业时才扩展和使用 VM。 如果该作业已完成或者没有任何作业，则 VM 会自动缩减以节省计算资源。 借助缩放，可以只使用所需的资源，从而降低 Batch 解决方案的总体成本。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 成本管理 + 计费](../cost-management-billing/cost-management-billing-overview.md)。
- 了解如何[将低优先级 VM 与 Batch 配合使用](batch-low-pri-vms.md)。
