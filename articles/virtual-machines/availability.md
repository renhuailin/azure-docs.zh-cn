---
title: Azure 虚拟机可用性选项
description: 了解在 Azure 中运行虚拟机的可用性选项
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 77e2ef4a5af250f19e505dfb9f725aa7674f691f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694474"
---
# <a name="availability-options-for-azure-virtual-machines"></a>Azure 虚拟机可用性选项

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

本文概述了 Azure 虚拟机 (VM) 的可用性选项。

## <a name="availability-zones"></a>可用性区域

[可用性区域](../availability-zones/az-overview.md?context=/azure/virtual-machines/context/context)扩展了在保持 VM 上的应用程序和数据的可用性时的控制级别。 可用性区域是 Azure 区域中在物理上独立的区域。 每个受支持的 Azure 区域有三个可用性区域。 

每个可用性区域有独立的电源、网络和散热设备。 通过将解决方案设计为使用区域中复制的 VM，可以在数据中心服务中断时保护应用和数据。 如果一个区域发生故障，另一个区域会立即提供复制的应用和数据。 

## <a name="availability-sets"></a>可用性集
[可用性集](availability-set-overview.md)是 VM 的逻辑分组，可让 Azure 了解应用程序的构建方式，以便提供冗余和可用性。 建议在可用性集内创建两个或多个 VM，提供高度可用的应用程序，并满足 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 的要求。 可用性集本身是免费的，只需为创建的每个 VM 实例付费。


## <a name="virtual-machines-scale-sets"></a>虚拟机规模集 

使用 [Azure 虚拟机规模集](../virtual-machine-scale-sets/overview.md?context=/azure/virtual-machines/context/context)可以创建并管理一组负载均衡的 VM。 可以根据需求或定义的计划自动增减 VM 实例的数目。 规模集为应用程序提供高可用性，用于集中管理、配置和更新许多 VM。 建议在一个规模集内创建两个或多个 VM，使应用程序高度可用，并满足 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 的要求。 规模集本身是免费的，你只需为创建的每个 VM 实例付费。

规模集内的虚拟机还可以部署到单个可用性区域或按地区进行部署。 可用性区域部署选项可能因[业务流程模式](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md?context=/azure/virtual-machines/context/context)而异。

## <a name="load-balancer"></a>负载均衡器
将 [Azure 负载均衡器](../load-balancer/load-balancer-overview.md)与可用性区域或可用性集组合在一起，以获取最高的应用程序复原能力。 Azure 负载均衡器将流量分布到多个虚拟机中。 对于标准层虚拟机来说，Azure 负载均衡器已包括在内。 并非所有虚拟机层都包括 Azure 负载均衡器。 有关对虚拟机进行负载均衡的更多信息，请阅读对 [Linux](linux/tutorial-load-balancer.md) 或 [Windows](windows/tutorial-load-balancer.md) 虚拟机进行负载均衡。


## <a name="azure-storage-redundancy"></a>Azure 存储冗余
Azure 存储始终会存储数据的多个副本，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 冗余可确保即使遇到故障，存储帐户也能达到其可用性和持久性目标。

在确定最适合自己方案的冗余选项时，请考虑如何在较低成本与较高可用性之间做出取舍。 可帮助你确定应选择哪种冗余选项的因素包括：
- 如何在主要区域中复制数据
- 是否要将你的数据复制到地理上距主要区域较远的另一个区域，以防范区域性灾难
- 应用程序是否要求在主要区域出于任何原因而不可用时，能够对次要区域中复制的数据进行读取访问

有关详细信息，请参阅 [Azure 存储冗余](../storage/common/storage-redundancy.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
组织需采用业务连续性和灾难恢复 (BCDR) 策略，在发生计划内和计划外停机时确保数据的安全以及应用和工作负载的联机。

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) 有助于保持业务应用和工作负载在停机期间的正常运行，从而确保业务连续性。 Site Recovery 将在物理机和虚拟机 (VM) 上运行的工作负荷从主站点复制到辅助位置。 主站点发生停机时，可以故障转移到辅助位置，从该位置访问应用。 在主位置再次运行后，可以故障回复到该位置。

Site Recovery 可以为以下计算机管理复制：
- 在 Azure 区域之间进行复制的 Azure VM。
- 本地 VM、 Azure Stack VM 和物理服务器。

## <a name="next-steps"></a>后续步骤
- [在可用性区域中创建虚拟机](./linux/create-cli-availability-zone.md)
- [在可用性集中创建虚拟机](./linux/tutorial-availability-sets.md)
- [创建虚拟机规模集](../virtual-machine-scale-sets/quick-create-portal.md)