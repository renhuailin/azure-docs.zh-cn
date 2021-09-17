---
title: 适用于虚拟机的 Azure 专用主机概述
description: 详细了解如何使用 Azure 专用主机部署虚拟机。
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 957bc2f34ddbc1af019afe0154d3a27ca6e3e368
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451558"
---
# <a name="azure-dedicated-hosts"></a>Azure 专用主机

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 统一规模集

Azure 专用主机是一种提供物理服务器（能够托管一个或多个虚拟机）的服务，专用于一个 Azure 订阅。 专用主机是数据中心中使用的相同物理服务器，作为资源提供。 你可以在区域、可用性区域和容错域中预配专用主机。 然后，可以按照最能满足你的需要的配置将 VM 直接放入预配的主机中。


## <a name="benefits"></a>优点

预留整个主机具有以下优势：

-   物理服务器级别的硬件隔离。 不会在你的主机上放置任何其他 VM。 专用主机部署在相同的数据中心，与其他非隔离主机共享相同的网络和底层存储基础结构。
-   控制 Azure 平台发起的维护事件。 尽管大多数维护事件对虚拟机的影响极小甚至没有任何影响，但对于某些敏感工作负荷而言，每暂停一秒都可能会造成影响。 在使用专用主机的情况下，可以选择维护时段，以降低对服务的影响。
-   借助 Azure 混合权益，你可以在 Azure 中使用自己的 Windows 和 SQL 许可证。 使用混合权益可以提供更多的优势。 有关详细信息，请参阅 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)。


## <a name="groups-hosts-and-vms"></a>组、主机和 VM

![专用主机的新资源的视图。](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

主机组是表示专用主机集合的资源。 可在区域和可用性区域中创建主机组，并向其添加主机。

主机  是映射到 Azure 数据中心内的物理服务器的资源。 创建主机时，将分配物理服务器。 主机是在主机组中创建的。 主机提供一个 SKU，用于描述可创建哪些 VM 大小。 每个主机可以托管多个不同大小的 VM，前提是这些 VM 来自相同的大小系列。


## <a name="high-availability-considerations"></a>高可用性注意事项

要实现高可用性，应部署多个 VM，并将其分散在多个主机（最少 2 个）上。 在使用 Azure 专用主机的情况下，有多个选项可用来预配基础结构，以形成故障隔离边界。

### <a name="use-availability-zones-for-fault-isolation"></a>使用可用性区域进行故障隔离

可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 在单个可用性区域中创建主机组。 创建后，所有主机都将放在该区域中。 要实现跨区域的高可用性，需要创建多个主机组（每个区域一个主机组）并相应地分散主机。

如果向可用性区域分配主机组，则必须在同一区域中创建在该主机上已经创建的所有 VM。

### <a name="use-fault-domains-for-fault-isolation"></a>使用容错域实现故障隔离

可以在特定的容错域中创建主机。 与规模集或可用性集中的 VM 一样，不同容错域中的主机将放置在数据中心内的不同物理机架上。 创建主机组时，需要指定容错域计数。 在主机组中创建主机时，需为每个主机分配容错域。 VM 不需要任何容错域分配。

容错域不同于归置。 对两个主机使用同一个容错域并不意味着它们彼此邻近。

容错域的范围限定为主机组。 您不应该对两个主机组之间的抗亲和力做出任何假设（除非它们位于不同的可用区域中）。

部署到具有不同容错域的主机的 VM 将在多个存储戳上具有其底层托管磁盘服务，以提高故障隔离保护。

### <a name="using-availability-zones-and-fault-domains"></a>使用可用性区域和容错域

可以同时使用这两项功能，以至于实现故障隔离。 在这种情况下，你将在中为每个主机组指定可用性区域和容错域计数，将容错域分配给组中的每个主机，并为每个 VM 分配一个可用性区域

[资源管理器示例模板](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md)使用区域和容错域传播主机以充分实现区域中的复原能力。


## <a name="manual-vs-automatic-placement"></a>手动放置与自动放置

在 Azure 中创建 VM 时，可以选择要使用哪个专用主机。 还可以使用相应的选项将 VM 自动放置在主机组中的现有主机上。

在创建新主机组时，请确保选择用于自动 VM 放置的设置。 在创建 VM 时，请选择该主机组，并让 Azure 为 VM 选择最佳主机。

已启用自动放置的主机组并不要求所有的 VM 均自动放置。 即使为该主机组选择了自动放置，你仍然能够显式选择主机。

### <a name="limitations"></a>限制

在使用自动 VM 放置时，有以下已知的问题和限制：

- 无法在专用主机上应用 Azure 混合权益。
- 无法重新部署 VM。
- 无法在专用主机上使用 Lsv2、NVasv4、Nvsv3、Msv2 或 M系列 VM


## <a name="virtual-machine-scale-set-support"></a>虚拟机规模集支持

利用虚拟机规模集，可以将一组虚拟机视为单一资源，并可以将其作为一个组来应用可用性、管理、缩放和业务流程策略。 现有的专用主机也可以用于虚拟机规模集。

在创建虚拟机规模集时，可以指定现有主机组，以便在专用主机上创建所有的 VM 实例。

在专用主机组中创建虚拟机规模集时，需要满足以下要求：

- 需要启用自动 VM 放置。
- 主机组的可用性设置应当与规模集相匹配。
    - 应将区域主机组（创建时未指定可用地区）用于区域规模集。
    - 主机组和规模集必须使用相同的可用性区域。
    - 主机组级别的容错域计数应当与规模集的容错域计数相匹配。 通过 Azure 门户，可以为规模集指定“最大分布”，这会将容错域计数设置为 1。
- 应该先创建具有足够容量且具有相同规模集区域设置与容错域设置的专用主机。
- 专用主机支持的 VM 大小应当与用于规模集的 VM 大小相匹配。

专用主机并非支持所有的规模集业务流程和优化设置。 请将以下设置应用于规模集：
- 不建议过度预配，该功能默认处于禁用状态。 可以启用过度预配，但如果主机组没有用于所有 VM 的足够容量（包括过度预配的实例），则规模集分配将失败。
- 使用 ScaleSetVM 业务流程模式
- 请勿将邻近放置组用于归置



## <a name="maintenance-control"></a>维护控制

可以偶尔更新支持虚拟机的基础结构，以提高可靠性、性能和安全性，并启动新功能。 只要有可能，Azure 平台就会尝试最大程度地降低平台维护的影响，但如果客户具有对维护敏感的  工作负荷，那么他们就不能容忍出于维护目的而冻结 VM 或断开 VM 连接，哪怕只有几秒。

维护控制  为客户提供了一个选项，以用于跳过在其专用主机上计划的定期平台更新，然后在 35 天滚动时段内他们所选的时间应用更新。 在维护时段，可以按任何顺序直接在主机级别应用维护。 维护时段结束后，Microsoft 继续操作，将挂起的维护按可能不符合用户定义的容错域的顺序应用于主机。

有关详细信息，请参阅[通过维护控制管理平台更新](./maintenance-control.md)。

## <a name="capacity-considerations"></a>容量注意事项

预配了专用主机之后，Azure 会将其分配给物理服务器。 这会在你需要预配 VM 时保证容量的可用性。 Azure 使用区域（地区）中的全部容量为你的主机选取物理服务器。 这也意味着，客户可以预期其专用主机占用空间能够扩展，而不必担心用尽群集中的空间。

## <a name="quotas"></a>配额

部署专用主机时会使用两种类型的配额。

1. 专用主机 vCPU 配额。 默认配额限制为每个区域 3000 vCPU。
1. VM 大小系列配额。 例如，在美国东部区域，“即用即付”订阅仅为 Dsv3 大小系列提供 10 个 vCPU 的配额。 若要部署 Dsv3 专用主机，则需要请求将配额提高到至少 64 个 vCPU，然后才能部署专用主机。

若要请求提高配额，请在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)中创建支持请求。

预配专用主机时将同时使用专用主机 vCPU 和 VM 系列 vCPU 配额，但不会使用区域 vCPU。 放置在专用主机上的 VM 不会计入 VM 系列 vCPU 配额。 如果 VM 从专用主机移到多租户环境中，则 VM 将占用 VM 系列 vCPU 配额。


![门户中的用量和配额页的屏幕截图](./media/virtual-machines-common-dedicated-hosts/quotas.png)

有关详细信息，请参阅[虚拟机 vCPU 配额](./windows/quotas.md)。

免费试用版和 MSDN 订阅不为 Azure 专用主机提供配额。

## <a name="pricing"></a>定价

无论部署多少个 VM，都会按专用主机对用户收费。 在每月帐单中，你将看到新的可计费资源类型的主机。 专用主机上的 VM 仍会显示在帐单中，但其价格为 0。

主机价格是根据 VM 系列、类型（硬件大小）和区域设置的。 主机价格与主机上支持的最大 VM 大小相关。

软件许可、存储和网络用量与主机和 VM 分开计费。 这些可计费项没有更改。

有关详细信息，请参阅 [Azure 专用主机定价](https://aka.ms/ADHPricing)。

还可以使用 [Azure 专用主机的保留实例](prepay-dedicated-hosts-reserved-instances.md)节省成本。

## <a name="sizes-and-hardware-generations"></a>大小和硬件代系

SKU 是为主机定义的，表示 VM 大小系列和类型。 可以在单个主机中混合使用多个不同大小的 VM，只要这些 VM 属于相同的大小系列即可。

类型  为硬件代系。 相同 VM 系列的不同硬件类型来自不同的 CPU 供应商，具有不同的 CPU 代系和核心数。

大小和硬件类型因区域而异。 请参阅主机[定价页](https://aka.ms/ADHPricing)来了解详细信息。

> [!NOTE]
> 预配某个专用主机后，就无法更改其大小或类型。 如果需要不同的大小或类型，需要创建新主机。

## <a name="host-life-cycle"></a>主机生命周期


Azure 将会监视和管理主机的运行状况状态。 查询主机时，将返回以下状态：

| 运行状况状态   | 说明       |
|----------|----------------|
| 主机可用     | 主机不存在已知问题。   |
| 主机正在接受调查  | 主机出现了一些问题，我们正在调查。 这是一种过渡状态，需要 Azure 尝试识别所发现的问题的范围和根本原因。 主机上运行的虚拟机可能受到了影响。 |
| 主机正在等待解除分配   | Azure 无法将主机还原到正常状态，并要求你将虚拟机重新部署到此主机外部。 如果已启用 `autoReplaceOnFailure`，则虚拟机会将服务修复  到正常的硬件上。 否则，虚拟机可能在即将发生故障的主机上运行。|
| 主机已解除分配  | 所有虚拟机都已从主机中删除。 由于硬件已退出轮换，因此不再向你收取此主机的费用。   |


## <a name="next-steps"></a>后续步骤

- 若要部署专用主机，请参阅[将 VM 和规模集部署到专用主机](./dedicated-hosts-how-to.md)。

- 此处有[示例模板](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md)，此模板使用区域和容错域来充分实现区域中的复原能力。

- 还可以使用 [Azure 专用主机的保留实例](prepay-dedicated-hosts-reserved-instances.md)节省成本。
