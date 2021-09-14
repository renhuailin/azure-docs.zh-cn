---
title: 获取 Azure Batch 的成本分析并为其设置预算
description: 了解如何针对那些用于运行 Batch 工作负荷的基础计算资源和软件许可证获取成本分析、设置预算和降低成本。
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: a590d8687c51b1693494c11d95de720f7f2c7eb0
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538842"
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

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>查看池的成本分析并为其创建预算

在 Azure 门户中，可以针对 Batch 池或 Batch 帐户创建预算和支出警报。 存在任何透支风险时，预算和警报可用于通知利益干系人，不过，支出警报可能会有所延迟，并可能存在略微超出预算的情况。

> [!NOTE]
> 此示例中的池使用虚拟机配置，这是针对大多数池建议的配置，根据虚拟机定价结构收费。 使用云服务配置的池根据云服务定价结构收费。

### <a name="view-cost-analysis-for-a-batch-pool"></a>查看 Batch 池的成本分析

1. 在 Azure 门户中，键入或选择“成本管理 + 计费”。
1. 在“计费范围”部分选择你的订阅。
1. 在“成本管理”下，选择“成本分析”。
1. 选择“添加筛选器”。 在第一个下拉列表中，选择“资源”
1. 在第二个下拉列表中，选择 Batch 池。 选择该池后，你将看到该池的成本分析，如以下示例所示。
    ![显示 Azure 门户中池成本分析的屏幕截图。](./media/batch-budget/pool-cost-analysis.png)

生成的成本分析显示了池的成本，以及此成本来自哪些资源。 在此示例中，池中使用的 VM 是成本最高的资源。

### <a name="create-a-budget-for-a-batch-pool"></a>为 Batch 池创建预算

1. 在“成本分析”页中，选择“预算: 无” 。
1. 选择“创建新预算 >”。
1. 使用出现的窗口专门为池配置预算。 有关详细信息，请参阅[教程：创建和管理 Azure 预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md)。

## <a name="minimize-costs-associated-with-azure-batch"></a>最大程度地降低 Azure Batch 相关的成本

你可能想要根据自己的情况尽可能地降低成本。 请考虑使用以下一种或多种策略来最大程度地提高工作负载效率并降低潜在成本。

### <a name="use-low-priority-virtual-machines"></a>使用低优先级虚拟机

[低优先级 VM](batch-low-pri-vms.md) 通过利用 Azure 中的盈余计算容量来降低 Batch 工作负载的成本。 当你在池中指定低优先级 VM 时，Batch 使用此盈余来运行工作负荷。 使用低优先级 VM 而不是专用 VM 可能会节省大量成本。

### <a name="use-ephemeral-os-disks"></a>使用临时 OS 磁盘

虚拟机配置池可使用[临时 OS 磁盘](create-pool-ephemeral-os-disk.md)来避免产生与托管磁盘相关的额外费用，临时磁盘会在 VM 缓存或临时 SSD 上创建 OS 磁盘。

### <a name="purchase-reservations-for-virtual-machine-instances"></a>为虚拟机实例购买预留容量

如果你打算长时间使用 Batch，可以通过对工作负载使用 [Azure 预留](../cost-management-billing/reservations/save-compute-costs-reservations.md)来节省 VM 成本。 预留费率比即用即付费率低得多。 没有预留容量的虚拟机实例将按即用即付费率收费。 购买预留容量时，将应用预留折扣。

### <a name="use-automatic-scaling"></a>使用自动缩放

[自动缩放](batch-automatic-scaling.md)根据当前作业的需求来动态缩放 Batch 池中的 VM 数。 自动缩放根据作业的生存期缩放池，确保仅在需要执行某个作业时才扩展和使用 VM。 如果该作业已完成或者没有任何作业，则 VM 会自动缩减以节省计算资源。 借助缩放，可以只使用所需的资源，从而降低 Batch 解决方案的总体成本。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 成本管理 + 计费](../cost-management-billing/cost-management-billing-overview.md)
- 了解如何[将低优先级 VM 与 Batch 配合使用](batch-low-pri-vms.md)。
