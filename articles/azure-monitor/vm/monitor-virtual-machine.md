---
title: 使用 Azure Monitor 监视虚拟机
description: 了解如何使用 Azure Monitor 监视虚拟机及其工作负荷的运行状况和性能。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/02/2021
ms.openlocfilehash: 44efb85ae2101ee1f35bd82b739e87103ad228bd
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681348"
---
# <a name="monitor-virtual-machines-with-azure-monitor"></a>使用 Azure Monitor 监视虚拟机
本部分介绍了如何使用 Azure Monitor 监视虚拟机及其工作负荷的运行状况和性能。 这包括如何收集关键的遥测数据来监视、分析和可视化所收集的数据以确定趋势，以及如何配置警报以在出现严重问题时获得通知。

本文介绍了该方案并提供了在 Azure Monitor 中监视虚拟机时涉及的一般概念。 如果要直接跳转到某个特定区域，请参阅下表中介绍的属于此方案的其他文章之一。

| 项目 | 说明 |
|:---|:---|
| [启用监视](monitor-virtual-machine-configure.md) | 配置 Azure Monitor 以监视虚拟机，包括启用 VM 见解以及为每台虚拟机启用监视。  |
| [分析](monitor-virtual-machine-analyze.md) | 分析 Azure Monitor 从虚拟机及其来宾操作系统和应用程序收集的监视数据，以确定趋势和关键信息。 |
| [警报](monitor-virtual-machine-alerts.md)   | 创建警报以主动识别监视数据中的严重问题。 |
| [监视安全性](monitor-virtual-machine-security.md) | 发现用于监视虚拟机安全性的 Azure 服务。 |
| [监视工作负载](monitor-virtual-machine-workloads.md) | 监视虚拟机上运行的应用程序和其他工作负荷。 |

> [!IMPORTANT]
> 此方案不包括未正式发布的功能。 公共预览版中的功能（例如[虚拟机来宾运行状况](vminsights-health-overview.md)）可能会大幅修改此处的建议。 当预览版功能正式发布时，此方案将会更新。

## <a name="types-of-machines"></a>计算机类型
此方案包括使用 Azure Monitor 监视以下类型的计算机。 此处描述的许多流程是相同的，与计算机类型无关。 对于针对不同类型的计算机的注意事项，我们根据情况提供了明确说明。 计算机类型包括： 

- Azure 虚拟机。
- Azure 虚拟机规模集。
- 混合计算机，是在其他云中运行的具有托管服务提供商的虚拟机或在本地运行的虚拟机。 它们还包括在本地运行的物理计算机。

## <a name="layers-of-monitoring"></a>监视层
虚拟机基本上有四个层需要监视。 每个层都有一组不同的遥测和监视要求。 

| 层 | 描述 |
|:---|:---|
| 虚拟主机 | Azure 中的主机虚拟机。 Azure Monitor 无法访问其他云中的主机，但必须依赖于从来宾操作系统收集的信息。 主机可用于跟踪配置更改之类的活动，但通常不用于重大警报。 |
| 来宾操作系统 | 虚拟机上运行的操作系统，即某个版本的 Windows 或 Linux。 有大量来自来宾操作系统的监视数据，例如性能数据和事件。 Azure Monitor 中的 VM 见解提供了大量逻辑，用于监视来宾操作系统的运行状况和性能。 |
| 工作负荷 | 在支持你的业务应用程序的来宾操作系统中运行的工作负荷。 Azure Monitor 针对某些工作负荷提供了预定义的监视。 对于其他工作负荷，你通常需要使用它们生成的监视数据来配置数据收集和警报。 |
| 应用程序 | 可以使用 [Application Insights](../app/app-insights-overview.md) 监视依赖于虚拟机的业务应用程序。 

:::image type="content" source="media/monitor-virtual-machines/monitoring-layers.png" alt-text="此图显示了监视层。" lightbox="media/monitor-virtual-machines/monitoring-layers.png":::

## <a name="vm-insights"></a>VM 见解
此方案侧重于 [VM 见解](../vm/vminsights-overview.md)，后者是 Azure Monitor 中用于监视虚拟机的主要功能。 VM 见解提供了以下功能：

- 启用对虚拟机来宾操作系统和工作负荷的监视时，简化了代理的加入。 
- 预定义的趋势性能图表和工作簿，可用于分析虚拟机来宾操作系统中的核心性能指标。
- 提供了依赖项映射，用于显示在每个虚拟机上运行的进程，以及与其他计算机和外部源关联的组件。

## <a name="agents"></a>代理
任何监视工具（例如 Azure Monitor）都需要使用计算机上安装的代理才能从其来宾操作系统收集数据。 Azure Monitor 当前有多个代理，用于收集不同的数据、将数据发送到不同的位置以及支持不同的功能。 VM 见解管理大多数客户会使用的代理的部署和配置。 下表介绍了各种代理，你可能需要它们支持的特定方案。 有关各种代理的详细说明和比较，请参阅 [Azure Monitor 代理概述](../agents/agents-overview.md)。

> [!NOTE]
> 在具备必需的功能后，Azure Monitor 代理将完全取代 Log Analytics 代理、诊断扩展和 Telegraf 代理。 VM 见解、Azure 安全中心和 Azure Sentinel 等功能仍然需要这些其他的代理。

- [Azure Monitor 代理](../agents/agents-overview.md#azure-monitor-agent)：支持 Azure 中的、其他云环境中的和本地的虚拟机。 将数据发送到 Azure Monitor 指标和日志。 当它完全支持 VM 见解、Azure 安全中心和 Azure Sentinel 时，它将完全替代 Log Analytics 代理和诊断扩展。
- [Log Analytics 代理](../agents/agents-overview.md#log-analytics-agent)：支持 Azure 中的、其他云环境中的和本地的虚拟机。 将数据发送到 Azure Monitor 日志。 支持 VM 见解和监视解决方案。 此代理是用于 System Center Operations Manager 的代理。
- [Dependency Agent](../agents/agents-overview.md#dependency-agent)：收集有关虚拟机上运行的进程及其依赖项的数据。 依靠 Log Analytics 代理将数据传输到 Azure 中，并支持 VM 见解、服务映射和 Wire Data 2.0 解决方案。
- [Azure 诊断扩展](../agents/agents-overview.md#azure-diagnostics-extension)：仅适用于 Azure Monitor 虚拟机。 可以将数据发送到 Azure 事件中心和 Azure 存储。

## <a name="next-steps"></a>后续步骤

[分析为虚拟机收集的监视数据](monitor-virtual-machine-analyze.md)
