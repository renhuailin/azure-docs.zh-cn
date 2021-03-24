---
title: 管理资源和配额
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习资源的配额和限制，以及如何请求增大配额。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: SimranArora904
ms.author: siarora
ms.date: 12/1/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperf-fy20q4, contperf-fy21q2
ms.openlocfilehash: 4e61a15b86d1d2d05889253f615eec0865c87a70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520381"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>管理和增大 Azure 机器学习资源的配额

Azure 使用限制和配额来防止由于欺诈导致的预算超支，并遵循 Azure 容量约束。 对于生产工作负荷，在缩放时请考虑这些限制。 本文介绍：

> [!div class="checklist"]
> + 与 [Azure 机器学习](overview-what-is-azure-ml.md)相关的 Azure 资源的默认限制。
> + 创建工作区级别的配额。
> + 查看你的配额和限制。
> + 请求增大配额。
> + 专用终结点和 DNS 配额。

除了配额管理外，还可以了解如何[计划和管理 Azure 机器学习的成本](concept-plan-manage-cost.md)或了解 [Azure 机器学习的服务限制](resource-limits-quotas-capacity.md)。

## <a name="special-considerations"></a>特殊注意事项

+ 配额是一种信用限制，不附带容量保证。 如果有大规模容量需求，[请与 Azure 支持部门联系来增加你的配额](#request-quota-increases)。

+ 配额在订阅中的所有服务（包括 Azure 机器学习）之间共享。 你在评估容量时需计算所有服务中的使用量。
 
  Azure 机器学习计算是一个例外。 它具有独立于核心计算配额的配额。 

+ 默认限制因套餐类别类型（例如免费试用、即用即付）和虚拟机 (VM) 系列（例如 Dv2、F、G 等）而异。

## <a name="default-resource-quotas"></a>默认资源配额

在本部分中，你将了解以下资源的默认和最大配额限制：

+ Azure 机器学习资产
  + Azure 机器学习计算
  + Azure 机器学习管道
+ 虚拟机
+ Azure 容器实例
+ Azure 存储

> [!IMPORTANT]
> 限制随时会变化。 有关最新信息，请参阅 [Azure 机器学习中的服务限制](resource-limits-quotas-capacity.md)。



### <a name="azure-machine-learning-assets"></a>Azure 机器学习资产
每个工作区都适用以下资产限制。 

| **资源** | **最大限制** |
| --- | --- |
| 数据集 | 1 千万 |
| 运行次数 | 1 千万 |
| 模型 | 1 千万|
| Artifacts | 1 千万 |

另外，最长运行时间为 30 天，每次运行记录的指标的最大数量为 1 百万 。

### <a name="azure-machine-learning-compute"></a>Azure 机器学习计算
[Azure 机器学习计算](concept-compute-target.md#azure-machine-learning-compute-managed)为订阅中每个区域所允许的核心数（按每个 VM 系列和累积总核心数拆分）和非重复计算资源数都设置了默认配额限制。 此配额与上一部分中列出的 VM 核心配额不同，因为它只适用于 Azure 机器学习的托管计算资源。

[请求增加配额](#request-quota-increases)可提升适用于本部分中各种 VM 系列核心配额、订阅核心配额总量和资源的限额。

可用资源：
+ **每个区域的专用核心数** 的默认限制为 24 到 300 个，具体取决于订阅套餐的类型。 可以为每个 VM 系列提高每个订阅的专用核心数。 专业化 VM 系列（例如 NCv2、NCv3 或 ND 系列）最初的默认限制为零个核心。

+ **每个区域的低优先级核心数** 的默认限制为 100 到 3000 个，具体取决于订阅套餐的类型。 每个订阅的低优先级核心数可以提高，对不同的 VM 系列采用单个值。

+ **每个区域的群集数** 的默认限制为 200。 它们在训练群集和计算实例之间共享。 （就配额用途来说，可以将计算实例视为单节点群集。）

> [!TIP]
> 若要详细了解要为哪个 VM 系列请求增加配额，请查看 [Azure 中的虚拟机大小](../virtual-machines/sizes.md)。 例如，GPU VM 系列在其系列名称中以“N”开头（如 NCv3 系列）

下表显示了平台中的其他限制。 若要提出有关例外情况的请求，请通过技术支持票证与 AzureML 产品团队联系。

| 资源或操作 | **最大限制** |
| --- | --- |
| 每个资源组的工作区数 | 800 |
| 设置为未启用通信的池（即无法运行 MPI 作业）的单个 Azure 机器学习计算 (AmlCompute) 群集中的节点 | 100 个节点，但最多可配置为 65000 个节点 |
| 在 Azure 机器学习计算 (AmlCompute) 群集上运行的单个并行运行步骤中的节点 | 100 个节点，但如果将群集设置为按上述标准缩放，则最多可配置为 65000 个节点 |
| 设置为已启用通信的池的单个 Azure 机器学习计算 (AmlCompute) 群集中的节点 | 300 个节点，但最多可配置为 4000 个节点 |
| 在已启用 RDMA 的 VM 系列上设置为已启用通信的池的单个 Azure 机器学习计算 (AmlCompute) 群集中的节点 | 100 个节点 |
| 在 Azure 机器学习计算 (AmlCompute) 群集上运行的单个 MPI 中的节点 | 100 个节点，但可以增加到 300 个节点 |
| 每个节点的 GPU MPI 进程数 | 1-4 |
| 每个节点的 GPU 辅助角色数 | 1-4 |
| 作业生存期 | 21 天<sup>1</sup> |
| 低优先级节点上的作业生存期 | 7 天<sup>2</sup> |
| 每个节点的参数服务器数 | 1 |

<sup>1</sup> 最大生存期是指从运行开始到运行完成之间的持续时间。 已完成的运行无限期保留。 最长生存期内未完成的运行的数据不可访问。
<sup>2</sup> 每当存在容量约束时，低优先级节点上的作业可能会预先清空。 我们建议在作业中实施检查点。

#### <a name="azure-machine-learning-pipelines"></a>Azure 机器学习管道
[Azure 机器学习管道](concept-ml-pipelines.md)具有以下限制。

| **资源** | **限制** |
| --- | --- |
| 管道中的步骤 | 30,000 |
| 每个资源组的工作区数 | 800 |

### <a name="virtual-machines"></a>虚拟机
每个 Azure 订阅都对所有服务中的虚拟机数量进行了限制。 虚拟机核心数既有区域总数限制，又有按大小系列的区域限制。 这两种限制单独实施。

例如，假设某个订阅的美国东部 VM 核心总数限制为 30，A 系列核心数限制为 30，D 系列核心数限制为 30。 该订阅可以部署 30 个 A1 VM、30 个 D1 VM，或者两者的组合，但其总数不能超过 30 个核心。

不能将对虚拟机的限制提升至高于下表中显示的值。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="container-instances"></a>容器实例

有关详细信息，请参阅[容器实例限制](../azure-resource-manager/management/azure-subscription-service-limits.md#container-instances-limits)。

### <a name="storage"></a>存储
Azure 存储的限制是每个订阅在每个区域中的存储帐户数不能超过 250 个。 此限制包括标准和高级存储帐户。

若要提高此限制，请通过 [Azure 支持](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)提交请求。 Azure 存储团队会评审你的案例，最多可以为每个区域批准 250 个存储帐户。


## <a name="workspace-level-quotas"></a>工作区级别的配额

使用工作区级配额来管理同一订阅中多个[工作区](concept-workspace.md)之间的 Azure 机器学习计算目标分配。

默认情况下，所有工作区的配额与任何 VM 系列的订阅级配额相同。 但是，你可以在订阅中的工作区上设置各个 VM 系列的最大配额。 这使你可以共享容量，避免资源争用问题。

1. 转到你的订阅中的任何工作区。
1. 在左侧窗格中，选择“使用量 + 配额”。
1. 选择“配置配额”选项卡以查看配额。
1. 展开某个 VM 系列。
1. 在任何工作区（在该 VM 系列下列出）上设置配额限制。

不能设置负值或大于订阅级配额的值。

[![屏幕截图显示了 Azure 机器学习工作区级别的配额。](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> 需要拥有订阅级别的权限才能在工作区级别设置配额。

## <a name="view-your-usage-and-quotas"></a>查看使用情况和配额

若要查看各种 Azure 资源（例如虚拟机、存储或网络）的配额，请使用 Azure 门户：

1. 在左窗格上，选择“所有服务”，然后在“一般”类别下选择“订阅”  。

2. 从订阅列表中选择要查找其配额的订阅。

3. 选择“使用情况 + 配额”以查看当前的配额限制和使用情况。 使用筛选器选择提供者和位置。 

订阅中的 Azure 机器学习计算配额与其他 Azure 配额分开管理： 

1. 在 Azure 门户中转到 Azure 机器学习工作区。

2. 在左侧窗格的“支持 + 故障排除”部分中，选择“使用情况 + 配额”以查看当前的配额限制和使用情况 。

3. 选择订阅以查看配额限制。 筛选到你关注的区域。

4. 你可以在订阅级视图与工作区级视图之间切换。

## <a name="request-quota-increases"></a>请求增加配额

若要提高限制或配额，使其超出默认限制，可以免费[提交联机客户支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)。

不能将限制提升至高于上面的表中显示的最大值。 如果没有最大限制，则无法调整对资源的限制。

请求增大配额时，请选择所需的服务。 例如，选择 Azure 机器学习、容器实例或存储。 对于 Azure 机器学习计算，可以在按照上述步骤查看配额时选择“请求配额”按钮。

> [!NOTE]
> [免费试用版订阅](https://azure.microsoft.com/offers/ms-azr-0044p)不符合增加限制或配额的条件。 如果有免费试用版订阅，可将其升级到[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)订阅。 有关详细信息，请参阅[将 Azure 免费试用版升级到即用即付](../cost-management-billing/manage/upgrade-azure-subscription.md)和 [Azure 免费帐户常见问题解答](https://azure.microsoft.com/free/free-account-faq)。

## <a name="private-endpoint-and-private-dns-quota-increases"></a>专用终结点和专用 DNS 配额增加

可以在订阅中创建的专用终结点和专用 DNS 区域的数目存在限制。

虽然 Azure 机器学习在你的（客户）订阅中创建资源，但某些情况下，会在 Microsoft 拥有的订阅中创建资源。

 在以下方案中，你可能需要在 Microsoft 拥有的订阅中请求配额宽限：

* 采用客户管理的密钥 (CMK) 且启用了 Azure 专用链接的工作区
* 将启用了专用链接的 Azure Kubernetes 服务群集附加到你的工作区

若要针对这些方案请求宽限，请使用以下步骤：

1. [创建 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md#create-a-support-request)并在“基本信息”部分中选择以下选项：

    | 字段 | 选择 |
    | ----- | ----- |
    | 问题类型 | **技术** |
    | 服务 | **我的服务**。 然后，在下拉列表中选择“机器学习”。 |
    | 问题类型 | **工作区配置和安全性** |
    | 问题子类型 | **专用终结点和专用 DNS 区域宽限请求** |

2. 在“详细信息”部分中，使用“说明”字段提供 Azure 区域以及计划使用的方案。 如果需要为多个订阅请求增加配额，请在此字段中列出订阅 ID。

3. 选择“创建”以创建请求。

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="屏幕截图显示了专用终结点和专用 DNS 配额增大请求。":::

## <a name="next-steps"></a>后续步骤

+ [计划和管理 Azure 机器学习成本](concept-plan-manage-cost.md)
+ [Azure 机器学习服务限制](resource-limits-quotas-capacity.md)