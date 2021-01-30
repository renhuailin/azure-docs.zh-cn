---
title: 获取成本分析并为 Azure Batch 设置预算
description: 了解如何获取成本分析、设置预算并降低用于运行 Batch 工作负荷的基础计算资源和软件许可证的成本。
ms.topic: how-to
ms.date: 01/29/2021
ms.openlocfilehash: d1fc2d15a7037e56a8056efa67d2017badb77ffd
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091321"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>获取成本分析并为 Azure Batch 设置预算

本主题将帮助你了解可能与 Azure Batch 关联的成本、如何设置批处理池或帐户的预算，以及降低批处理工作负荷成本的方法。

## <a name="understand-costs-associated-with-batch-resources"></a>了解与 Batch 资源关联的成本

如果 Azure Batch 使用的是基础计算资源和用于运行批处理工作负荷的软件许可证，则不会产生任何费用。 可能是由于 Vm) 的虚拟机 (Vm、从 VM 传输数据或存储在云中的任何输入或输出数据导致的。

### <a name="virtual-machines"></a>虚拟机

虚拟机是用于 Batch 处理的最重要资源。 Batch 的 VM 使用成本是根据类型、数量和使用持续时间计算的。 VM 计费选项包括[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)或[预留](../cost-management-billing/reservations/save-compute-costs-reservations.md)（提前支付）。 根据计算工作负荷的不同，这两种支付选项有不同的优点，并且会以不同的方式影响帐单。

使用 [虚拟机配置](nodes-and-pools.md#virtual-machine-configuration) 创建的池中的每个 VM 都有一个使用 Azure 托管磁盘的关联 OS 磁盘。 Azure 托管磁盘有额外的成本，其他磁盘性能层也有不同的成本。

### <a name="storage"></a>存储

使用[应用包](batch-application-packages.md)将应用部署到 Batch 节点 (VM) 时，还需要为应用包使用的 Azure 存储资源付费。 你还将对任何输入或输出文件（如资源文件和其他日志数据）的存储进行计费。

通常，与 Batch 关联的存储数据的成本要比计算资源的成本低得多。

### <a name="networking-resources"></a>网络资源

Batch 池使用网络资源，其中某些资源具有相关的成本。 具体而言，对于虚拟机配置池，使用标准负载均衡器，这需要静态 IP 地址。 Batch 使用的负载均衡器对在 "用户订阅" 模式下配置的 [帐户](accounts.md#batch-accounts) 可见，但在批处理服务模式下是可见的。

标准负载均衡器会对与 Batch 池 Vm 一起传递的所有数据产生费用。 选择从池节点检索数据的批处理 Api (例如获取任务/节点文件) 、任务应用程序包、资源/输出文件和容器映像也会产生费用。

### <a name="additional-services"></a>其他服务

根据要将哪些服务用于 Batch 解决方案，可能会产生额外的费用。 若要确定每个附加服务的成本，请参阅[定价计算器](https://azure.microsoft.com/pricing/calculator/)。 通常用于可能有关联成本的批处理的服务包括：

- Application Insights
- 数据工厂
- Azure Monitor
- 虚拟网络
- 带有图形应用的 VM

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>查看成本分析和创建池的预算

在 Azure 门户中，可以为批处理池或批处理帐户创建预算和支出警报。 预算和警报可用于向利益干系人通知超支的风险，不过，可能会有延迟的支出，并稍微超出预算。

> [!NOTE]
> 此示例中的池使用 **虚拟机配置**，建议对大多数池使用该配置，并根据虚拟机定价结构收费。 使用 **云服务配置** 的池会根据云服务定价结构收费。

### <a name="view-cost-analysis-for-a-batch-pool"></a>查看 Batch 池的成本分析

1. 在 "Azure 门户中，键入或选择" **成本管理 + 计费** "。
1. 在 " **计费范围** " 部分中选择你的订阅。
1. 在“成本管理”下，选择“成本分析”。
1. 选择“添加筛选器”。 在第一个下拉箭头中，选择 "**资源**"
1. 在第二个下拉列表中，选择 Batch 池。 选择该池后，你将看到池的成本分析，类似于此处显示的示例。
    ![显示 Azure 门户中池的成本分析的屏幕截图。](./media/batch-budget/pool-cost-analysis.png)

生成的成本分析显示了池的成本，以及此成本来自哪些资源。 在此示例中，池中使用的 VM 是成本最高的资源。

### <a name="create-a-budget-for-a-batch-pool"></a>创建 Batch 池的预算

1. 在 " **成本分析** " 页上，选择 " **预算：无**"。
1. 选择 " **创建新的预算 >**。
1. 使用生成的窗口来配置专用于池的预算。 有关详细信息，请参阅 [教程：创建和管理 Azure 预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md)。

## <a name="minimize-costs-associated-with-azure-batch"></a>最大程度地降低与 Azure Batch 相关的成本

根据你的方案，你可能希望尽可能降低成本。 请考虑使用这些策略中的一个或多个，以最大程度地提高工作负荷效率并降低潜在成本。

### <a name="use-low-priority-virtual-machines"></a>使用低优先级虚拟机

[低优先级 vm](batch-low-pri-vms.md) 利用 Azure 中的过剩计算能力，降低 Batch 工作负荷的成本。 当你在池中指定低优先级 VM 时，Batch 使用此盈余来运行工作负荷。 使用低优先级 Vm 而不是专用 Vm 时，可以节省大量成本。

### <a name="select-a-standard-virtual-machine-os-disk-type"></a>选择标准虚拟机 OS 磁盘类型

Azure 提供多个 [VM 操作系统磁盘类型](../virtual-machines/disks-types.md)。 大多数 VM 系列大小都支持高级和标准存储。 为池选择“s”VM 大小时，Batch 会配置高级 SSD OS 磁盘。 选择“非 s”VM 大小时，会使用更经济的标准 HDD 磁盘类型。 例如，将高级 SSD OS 磁盘用于 `Standard_D2s_v3`，将标准 HDD OS 磁盘用于 `Standard_D2_v3`。

高级 SSD OS 磁盘更昂贵，但性能更高。 具有高级磁盘的 Vm 的启动速度可能比具有标准 HDD OS 磁盘的 Vm 快。 使用 Batch 时，操作系统磁盘通常不会很多地使用，因为应用程序和任务文件位于 VM 的临时 SSD 磁盘上。 因此，你通常可以选择 "非 s" VM 大小，以避免支付在指定了 "VM 大小" 时预配的高级 SSD 的增加成本。

### <a name="purchase-reservations-for-virtual-machine-instances"></a>为虚拟机实例购买预留

如果你想要使用批处理长时间，则可以使用 [Azure 保留](../cost-management-billing/reservations/save-compute-costs-reservations.md) 来实现工作负载，从而降低 vm 的成本。 预留费率比即用即付费率低得多。 不使用保留的虚拟机实例按现用现付费率进行收费。 购买预订时，将应用预订折扣。

### <a name="use-automatic-scaling"></a>使用自动缩放

[自动缩放](batch-automatic-scaling.md)根据当前作业的需求来动态缩放 Batch 池中的 VM 数。 通过根据作业的生存期扩展池，自动缩放可确保仅在有作业要执行时才扩展和使用 Vm。 如果作业已完成，或者没有作业，Vm 将自动缩小以节省计算资源。 借助缩放，可以只使用所需的资源，从而降低 Batch 解决方案的总体成本。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 成本管理 + 计费](../cost-management-billing/cost-management-billing-overview.md)
- 了解如何 [使用 Batch 的低优先级 vm](batch-low-pri-vms.md)。
