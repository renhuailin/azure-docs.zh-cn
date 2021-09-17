---
title: 可用性集概述
description: 了解 Azure 中的可用性集
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: f1580c69a4fc1671d4dbe86ede05392b32534db6
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693676"
---
# <a name="availability-sets-overview"></a>可用性集概述

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM

本文概述了 Azure 虚拟机 (VM) 的可用性功能。

## <a name="what-is-an-availability-set"></a>什么是可用性集？ 

可用性集是 VM 的逻辑分组，可让 Azure 了解应用程序的构建方式，以便提供冗余和可用性。 建议在可用性集内创建两个或多个 VM，提供高度可用的应用程序，并满足 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 的要求。 可用性集本身是免费的，只需为创建的每个 VM 实例付费。

## <a name="how-do-availability-sets-work"></a>可用性集如何工作？
基础 Azure 平台为可用性集中的每个虚拟机分配一个更新域和一个容错域 。 每个可用性集最多可配置三个容错域和二十个更新域。 更新域表示虚拟机组以及可同时重新启动的基础物理硬件。 如果单个可用性集中配置了超过 5 个虚拟机，第 6 个虚拟机放置在第 1 个虚拟机所在的更新域中，第 7 个虚拟机放置在第 2 个虚拟机所在的更新域中，依此类推。 在计划内维护期间，更新域的重启顺序可能不会按序进行，但一次只重启一个更新域。 重启的更新域有 30 分钟的时间进行恢复，此时间过后，就会在另一更新域上启动维护操作。

容错域定义一组共用一个通用电源和网络交换机的虚拟机。 默认情况下，可用性集中配置的虚拟机隔离在最多三个容错域中。 虽然将虚拟机置于可用性集中并不能让应用程序免受特定于操作系统或应用程序的故障的影响，但可以限制潜在物理硬件故障、网络中断或电源中断的影响。

:::image type="content" source="./media/virtual-machines-common-manage-availability/ud-fd-configuration.png" alt-text="图表显示分成容错域的以及那些容错域内的各种计算群集，我们有多个更新域":::

VM 也与磁盘容错域保持一致。 这种一致性可确保附加到 VM 的所有托管磁盘都在同一容错域内。 

在托管可用性集中，只能创建带托管磁盘的 VM。 托管磁盘容错域的数目因区域而异 - 每个区域两个或三个托管磁盘容错域。 

:::image type="content" source="./media/virtual-machines-common-manage-availability/md-fd-updated.png" alt-text="图表显示磁盘容错域和 VM 如何保持一致。":::

## <a name="next-steps"></a>后续步骤
现在即可开始使用这些可用性和冗余功能构建 Azure 环境。 有关最佳实践的信息，请参阅 [Azure 可用性的最佳实践](/azure/architecture/checklist/resiliency-per-service)。

