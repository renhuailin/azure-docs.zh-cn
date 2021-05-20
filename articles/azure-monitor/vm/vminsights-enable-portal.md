---
title: 在 Azure 门户中为单个虚拟机或虚拟机规模集启用 Azure Monitor
description: 了解如何使用 Azure 门户在单个 Azure 虚拟机或虚拟机规模集上启用 VM 见解。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 076fcab7b0747a7993407edd65f9d08efc27309f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035579"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>在 Azure 门户中为单个虚拟机或虚拟机规模集启用 Azure Monitor
本文介绍如何使用 Azure 门户为虚拟机或虚拟机规模集启用 VM 见解。 此过程可用于以下项：

- Azure 虚拟机
- Azure 虚拟机规模集
- 与 Azure Arc 连接的混合虚拟机

## <a name="prerequisites"></a>先决条件

- [创建和配置 Log Analytics 工作区](./vminsights-configure-workspace.md)。 或者可以在此过程期间创建新工作区。
- 请查看[支持的操作系统](./vminsights-enable-overview.md#supported-operating-systems)，确保要启用的虚拟机或虚拟机规模集的操作系统受到支持。 

## <a name="enable-vm-insights"></a>启用 VM 见解

从 Azure 门户，选择“虚拟机”、“虚拟机规模集”或“服务器 - Azure Arc”，然后从列表中选择资源  。 在菜单的“监视”部分，选择“见解”，然后选择"启用"  。 以下示例显示了 Azure 虚拟机，但 Azure 虚拟机规模集或 Azure Arc 的菜单是类似的。

![为 VM 启用 VM 见解](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

如果虚拟机未连接到 Log Analytics 工作区，系统将提示你选择一个工作区。 如果之前尚未[创建工作区](../logs/quick-create-workspace.md)，则对于在订阅中部署虚拟机或虚拟机规模集的位置，可以选择默认值。 如果工作区不存在，则将创建并配置此工作区。 如果选择现有工作区，将为 VM 见解配置该工作区（如果尚未配置）。

> [!NOTE]
> 如果选择的工作区尚未为 VM 见解配置，则 VMInsights 管理包将添加到此工作区。 这将应用于已连接到工作区的任何代理，无论是否为 VM 见解启用了该代理都是如此。 性能数据将通过这些虚拟机收集，并存储在 InsightsMetrics 表中。

![选择工作区](media/vminsights-enable-portal/select-workspace.png)

在执行配置时，会收到状态消息。

>[!NOTE]
>如果对虚拟机规模集使用手动升级模型，请升级实例以完成设置。 可以从“设置”部分的“实例”页启动升级。

![启用 VM 见解监视部署处理](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>后续步骤

* 请参阅[使用 VM 见解地图](vminsights-maps.md)，查看已发现的应用程序依赖项。 
* 请参阅[查看 Azure VM 性能](vminsights-performance.md)，了解瓶颈、整体利用率和 VM 的性能。
