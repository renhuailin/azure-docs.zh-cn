---
title: 了解 VM 重启
description: 了解 VM 重启 - 维护和停机
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: dda6b0c5d2087b588e69e6d5507eea4cdcbc1caf
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690853"
---
# <a name="understand-vm-reboots---maintenance-vs-downtime"></a>了解 VM 重启 - 维护和停机

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

有三种情况可能会导致 Azure 中的虚拟机受影响：计划外硬件维护、意外停机、计划内维护。

## <a name="unplanned-hardware-maintenance-event"></a>计划外硬件维护事件
当 Azure 平台预测硬件或者与物理计算机关联的任何平台组件即将发生故障时，就会发生计划外硬件维护。 当预测到故障时，平台会发出计划外硬件维护事件，以便减少对托管在该硬件上的虚拟机的影响。 Azure 使用[实时迁移](./maintenance-and-updates.md)技术将虚拟机从故障硬件迁移到正常的物理计算机。 实时迁移是一项 VM 保留操作，只能短时间暂停虚拟机。 将会保留内存、打开的文件以及网络连接，但事件前后的性能可能会降低。 在无法使用实时迁移的情况下，VM 会出现意外停机，如下所述。


## <a name="unexpected-downtime"></a>意外停机时间
意外停机指虚拟机的硬件或物理基础设施意外出现故障。 此类故障可能包括：本地网络故障、本地磁盘故障，或者其他机架级别的故障。 检测到此类故障时，Azure 平台会自动将虚拟机迁移（恢复）到同一数据中心内的正常物理机。 在修复过程中，虚拟机会经历停机（重启），在某些情况下会丢失临时驱动器。 始终会保留附加的 OS 和数据磁盘。

在发生会影响整个数据中心甚至整个区域的服务中断或灾难时（这种情况很少见），虚拟机也可能会停机。 针对这种情况，Azure 提供了保护选项，包括[可用性区域](../availability-zones/az-overview.md)和[配对区域](regions.md#region-pairs)。

## <a name="planned-maintenance-events"></a>计划内维护事件
计划内维护事件是指由 Microsoft 对底层 Azure平台进行定期更新，以改进虚拟机运行时所在的平台基础结构的总体可靠性、性能和安全性。 大多数此类更新在执行时不会影响虚拟机或云服务（请参阅[不需要重新启动的维护](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)）。 虽然 Azure 平台会尝试在所有可能的情况下都使用 VM 保留维护，但在罕见情况下，这些更新需要重启虚拟机，否则无法将所需更新应用到底层基础结构。 在这种情况下，可以在合适的时间窗口为 VM 启动维护，通过“维护-重新部署”操作来执行 Azure 计划内维护。 有关详细信息，请参阅[虚拟机的计划内维护](maintenance-and-updates.md)。

## <a name="reduce-downtime"></a>缩短停机时间
要减轻一个或多个此类事件引发的停机所造成的影响，我们建议遵循以下最佳做法以提高虚拟机的可用性：

* 使用[可用性区域](../availability-zones/az-overview.md)防范数据中心故障
* 在[可用性集](availability-set-overview.md)中配置多个虚拟机以确保冗余
* 使用[适用于 Linux 的计划事件](./linux/scheduled-events.md)或[适用于 Windows 的计划事件](./windows/scheduled-events.md)来主动响应 VM 影响事件
* 将每个应用程序层配置到不同的可用性集中
* 将[负载均衡器](../load-balancer/load-balancer-overview.md)与可用性区域或可用性集组合在一起

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 中可用性选项的详细信息，请参阅[可用性概述](availability.md)。