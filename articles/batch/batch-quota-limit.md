---
title: 服务配额和限制
description: 了解默认的 Azure Batch 配额、限制和约束，以及如何请求提高配额
ms.topic: conceptual
ms.date: 07/20/2021
ms.custom: seodec18
ms.openlocfilehash: ad5701c65f9f2068a1a68084b755f8d0a013b8c8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114465567"
---
# <a name="batch-service-quotas-and-limits"></a>Batch 服务配额和限制

与其他 Azure 服务一样，与 Batch 服务关联的某些资源存在限制。 其中的许多限制是 Azure 在订阅或帐户级别应用的默认配额。

设计和增加 Batch 工作负荷时，请记住这些配额。 例如，如果池没有达到指定的计算节点目标数量，那么可能是已达到 Batch 帐户的核心配额限制。

可以在单个 Batch 帐户中运行多个 Batch 工作负荷，或者在相同订阅的不同 Azure 区域的 Batch 帐户之间分散工作负荷。

如果打算在 Batch 中运行生产工作负荷，可能需要将一个或多个配额提高到默认值以上。 若要提高配额，可以免费[请求增加配额](#increase-a-quota)。

## <a name="resource-quotas"></a>资源配额

配额是一种限制，不是容量保证。 如果有大规模的容量需求，请联系 Azure 支持。

另请注意，配额不是受保证值。 配额可能因来自 Batch 服务的更改或是用于更改配额值的用户请求而异。

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>核心配额

### <a name="cores-quotas-in-batch-service-mode"></a>Batch 服务模式下的核心配额

Batch 支持的每个 VM 系列都存在核心配额。 这些核心配额显示在 Azure 门户的“配额”页面上。 可以通过提交支持请求来更新 VM 系列的配额限制，详见下面的描述。 对于专用节点，Batch 对每个 VM 序列强制实施核心配额限制，同时对整个 Batch 帐户强制实施总核心配额限制。 对于低优先级节点，Batch 仅强制实施 Batch 帐户的总核心配额，不同 VM 系列之间没有任何区别。

### <a name="cores-quotas-in-user-subscription-mode"></a>用户订阅模式中的核心配额

如果你创建了一个池分配模式设置为“用户订阅”的 [Batch 帐户](accounts.md)，则在创建池或重设池大小时，会直接在订阅中创建 Batch VM 和其他资源。 不会应用 Azure Batch 核心配额，将使用并强制实施你的订阅中为区域性计算核心、每系列计算核心和其他资源设定的配额。

若要详细了解这些配额，请参阅 [Azure 订阅和服务的限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。

## <a name="pool-size-limits"></a>池大小限制

池大小限制由 Batch 服务设置。 与[资源配额](#resource-quotas)不同，这些值无法更改。 只有具有节点间通信和自定义映像的池才具有与标准配额不同的限制。

| **资源** | **最大限制** |
| --- | --- |
| **[启用了节点间通信的池](batch-mpi.md)中的计算节点**  ||
| Batch 服务池分配模式 | 100 |
| Batch 订阅池分配模式 | 80 |
| [使用托管映像资源创建的池](batch-custom-images.md)中的计算节点<sup>1</sup> ||
| 专用节点 | 2000 |
| 低优先级节点 | 1000 |

<sup>1</sup> 适用于未启用节点间通信的池。

## <a name="other-limits"></a>其他限制

这些额外的限制是由 Batch 服务设置的。 与[资源配额](#resource-quotas)不同，这些值无法更改。

| **资源** | **最大限制** |
| --- | --- |
| 每个计算节点的[并发任务](batch-parallel-node-tasks.md)数 | 4 x 节点核心数 |
| 每个 Batch 帐户的[应用程序](batch-application-packages.md)数 | 200 |
| 每个应用程序的应用程序包数 | 40 |
| 每个池的应用程序包数 | 10 |
| 最长任务生存期 | 180 天<sup>1</sup> |
| 每个计算节点的[装载](virtual-file-mount.md)数 | 10 |
| 每个池的证书 | 12 |

<sup>1</sup> 最长任务生存期（从添加到作业时算起到任务完成时结束）为 180 天。 已完成的任务保存七天；最长生存期内未完成的任务的数据不可访问。

## <a name="view-batch-quotas"></a>查看 Batch 配额

在 [Azure 门户](https://portal.azure.com)中查看 Batch 帐户配额：

1. 选择“Batch 帐户”，然后选择所需的 Batch 帐户。
1. 在 Batch 帐户的菜单上选择“配额”。
1. 查看当前应用于 Batch 帐户的配额。

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="屏幕截图显示了 Azure 门户中的 Batch 帐户配额。":::

## <a name="increase-a-quota"></a>提高配额

可以使用 [Azure 门户](https://portal.azure.com)或使用 [Azure Quota REST API](#azure-quota-rest-api) 请求提高 Batch 帐户或订阅的配额。

可以提高哪种配额取决于批处理帐户的池分配模式。 若要请求增加配额，必须包含要增加其配额的 VM 系列。 当应用了配额增加时，它会应用于所有 VM 系列。

提交支持请求后，Azure 支持人员将与你取得联系。 配额请求可以在几分钟内完成，或在最多两个工作日内完成。

### <a name="azure-portal"></a>Azure 门户

1. 在“配额”页面上，选择“请求配额增加”。  或者，可以选择门户仪表板上的“帮助 + 支持”磁贴（或从门户右上角的问号 (?) 中），然后选择“新建支持请求”。  

1. 在“基本信息”中：

    1. 对于“问题类型”，选择“服务和订阅限制(配额)”。 
    1. 选择订阅。
    1. 对于“配额类型”，选择“批处理”。 
    1. 选择“下一步”继续。

1. 在“详细信息”中：

    1. 在“提供详细信息”部分中，指定位置、配额类型和 Batch 帐户（如果适用），然后选择要增加的配额。

       :::image type="content" source="media/batch-quota-limit/quota-increase.png" alt-text="屏幕截图显示了请求增加配额时的“配额详细信息”屏幕。":::

       配额类型包括：

       - 每个 Batch 帐户  
         使用此选项可以请求增加特定于单个 Batch 帐户的配额，包括专用配额和低优先级内核配额以及作业和池的数量。

         如果选择此选项，请指定要将此请求应用于哪个 Batch 帐户，然后选择要更新的配额。 针对每项资源，设定你要请求的新限制。

         低优先级配额是跨所有 VM 系列的单个值。 如果需要受约束的 SKU，则必须选择“低优先级核心”并包含要请求的 VM 系列。

       - 此区域中的所有帐户  
         使用此选项可请求适用于区域中所有 Batch 帐户的配额增加，如每个区域中每个订阅的 Batch 帐户数。

    1. 在“支持方法”中，根据[业务影响](https://aka.ms/supportseverity)以及首选联系方法和支持语言选择“严重性”。 

    1. 在“联系信息”中，验证并输入所需的联系方式详细信息。

1. 选择“查看 + 创建”，然后选择“创建”以提交支持请求。 

### <a name="azure-quota-rest-api"></a>Azure Quota REST API

你可以使用 Azure Quota REST API 在订阅级别或 Batch 帐户级别请求增加配额。

有关详细信息和示例，请参阅[使用 Azure 支持 REST API 请求提高配额](/rest/api/support/quota-payload#azure-batch)。

## <a name="related-quotas-for-vm-pools"></a>VM 池的相关配额

[部署在 Azure 虚拟网络中的虚拟机配置中的 Batch 池](batch-virtual-network.md)可自动分配其他 Azure 网络资源。 在包含创建 Batch 池时提供的虚拟网络的订阅中创建这些资源。

在虚拟网络中，将为每 100 个池节点创建以下资源：

- 一个[网络安全组](../virtual-network/network-security-groups-overview.md#network-security-groups)
- 一个[公共 IP 地址](../virtual-network/public-ip-addresses.md)
- 一个[负载均衡器](../load-balancer/load-balancer-overview.md)

这些资源受订阅的[资源配额](../azure-resource-manager/management/azure-subscription-service-limits.md)限制。 如果在虚拟网络中计划大型池部署，则可能需要请求为其中一个或多个资源增加配额。

## <a name="next-steps"></a>后续步骤

- 了解 [Batch 服务工作流和主要资源](batch-service-workflow-features.md)，例如池、节点、作业和任务。
- 了解 [Azure 订阅和服务的限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。