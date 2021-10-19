---
title: 使用 Azure Monitor 监视虚拟机：配置监视
description: 了解如何在 Azure Monitor 中配置对虚拟机的监视。 使用 Azure Monitor 方案监视虚拟机及其工作负载。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: ceb57d364cca4b05d170ad6e42e8894f9a1ce86e
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710242"
---
# <a name="monitor-virtual-machines-with-azure-monitor-configure-monitoring"></a>使用 Azure Monitor 监视虚拟机：配置监视
本文是[在 Azure Monitor 中监视虚拟机及其工作负载](monitor-virtual-machine.md)方案的一部分。 文中介绍了如何在 Azure Monitor 中对 Azure 虚拟机和混合虚拟机配置监视。

本文讨论了 Azure Monitor 用于监视虚拟主机及其来宾操作系统的最常见功能。 根据特定环境和业务需求，你可能不需要实现通过此配置启用的全部功能。 文中各部分介绍通过配置启用哪些功能，以及该功能是否可能导致额外费用。 这些信息将帮助你评估是否需要执行配置的每个步骤。 有关定价的详细信息，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

针对通过此配置启用的每个功能，请参阅[方案概述](monitor-virtual-machine.md)中提供的一般说明。 文中还包含指向内容的链接，这些内容提供了每个功能的详细说明，可进一步帮助你评估需求。

> [!NOTE]
> 通过配置启用的功能支持监视虚拟机上运行的工作负载。 但是，根据特定的工作负载，通常还需进行要其他配置。 有关其他配置的详细信息，请参阅[工作负载监视](monitor-virtual-machine-workloads.md)。

## <a name="configuration-overview"></a>配置概述
下表列出了此配置必须执行的各个步骤。 每个步骤都可链接到相应部分，其中包含对该配置步骤的详细说明。

| 步骤 | 说明 |
|:---|:---|
| [无配置](#no-configuration) | Azure 虚拟主机的活动日志和平台指标会自动收集，无需任何配置。 |
| [创建和准备 Log Analytics 工作区](#create-and-prepare-a-log-analytics-workspace) | 创建 Log Analytics 工作区，并为 VM 见解配置该工作区。 根据特定需求，可配置多个工作区。 |
| [将活动日志发送到 Log Analytics 工作区](#send-an-activity-log-to-a-log-analytics-workspace) | 将活动日志发送到工作区，与其他日志数据一起接受分析。 |
| [准备混合计算机](#prepare-hybrid-machines) | 混合计算机或者需要通过已安装的 Azure Arc 来启用服务器代理，以便像 Azure 虚拟机一样对其进行管理，或者必须手动为这些计算机安装代理。 |
| [在计算机上启用 VM 见解](#enable-vm-insights-on-machines) | 将计算机加入 VM 见解，这将部署所需的代理并开始从来宾操作系统收集数据。 |
| [将来宾性能数据发送到指标](#send-guest-performance-data-to-metrics) |安装 Azure Monitor 代理，以便将性能数据发送到 Azure Monitor 指标。 |

## <a name="no-configuration"></a>无配置
Azure Monitor 提供针对 Azure 虚拟机的基本级别监视，无需任何费用，也无需任何配置。 Azure 虚拟机的平台指标包括 CPU、网络和磁盘利用率等重要指标。 这些指标可在 Azure 门户中的虚拟机[概述页面](monitor-virtual-machine-analyze.md#single-machine-experience)查看。 活动日志也会自动收集，其中包括该虚拟机的最近活动，例如任何配置更改以及停止和启动的时间。

## <a name="create-and-prepare-a-log-analytics-workspace"></a>创建和准备 Log Analytics 工作区
需要至少一个 Log Analytics 工作区来支持 VM 见解，并从 Log Analytics 代理收集遥测数据。 工作区不收取任何费用，但在收集数据时，需要承担引入费用和保留费用。 有关详细信息，请参阅[通过 Azure Monitor 日志管理使用情况和成本](../logs/manage-cost-storage.md)。

很多环境为其监视的所有虚拟机及其他 Azure 资源使用单个工作区。 你甚至可以共享 [Azure 安全中心和 Azure Sentinel](monitor-virtual-machine-security.md) 使用的工作区，但很多客户选择将这两者的可用性和性能遥测数据与安全数据分隔开来。 如果你刚开始使用 Azure Monitor，请先从单个工作区开始，随着需求的发展可以考虑创建更多工作区。

有关规划工作区配置时应考虑的逻辑，请参阅[规划 Azure Monitor 日志部署](../logs/design-logs-deployment.md)，了解完整的详细信息。

### <a name="multihoming-agents"></a>多宿主代理
多宿主是指连接到多个工作区的虚拟机。 通常情况下，没有理由仅对 Azure Monitor 配置多宿主代理。 使用代理将数据发送到多个工作区很可能在每个工作区中创建重复数据，导致总费用增加。 可以使用[跨工作区查询](../logs/cross-workspace-query.md)和[工作簿](../visualizations/../visualize/workbooks-overview.md)合并来自多个工作区的数据。

不过，可能考虑多宿主的原因之一是，你的环境具有 Azure 安全中心或 Azure Sentinel，而这两者存储在独立于 Azure Monitor 的工作区中。 受各服务监视的计算机需要将数据发送到各个工作区。 Windows 代理支持此方案，因为它最多可以发送到四个工作区。 Linux 代理目前只能发送到单个工作区。 如果需要使用 Azure Monitor 以及 Azure 安全中心或 Azure Sentinel 来监视一组常用的 Linux 计算机，则这些服务需要共享同一工作区。

另一个可能考虑多宿主代理的原因是，你使用的是[混合监视模型](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring)。 在此模型中，你将同时使用 Azure Monitor 和 Operations Manager 来监视同相同的计算机。 Log Analytics 代理和适用于 Operations Manager 的 Microsoft 管理代理是同一代理。 有时在提及这两个代理时使用了不同的名称。

### <a name="workspace-permissions"></a>工作区权限
工作区访问模式定义哪些用户可以访问不同的数据集。 有关如何定义访问模式和配置权限的详细信息，请参阅[在 Azure Monitor 中管理日志数据和工作区的访问权限](../logs/manage-access.md)。 如果你刚开始使用 Azure Monitor，请考虑在创建工作区以及在稍后配置其权限时接受默认设置。

### <a name="prepare-the-workspace-for-vm-insights"></a>为 VM 见解准备工作区
在对任何虚拟机启用监视之前，请先为 VM 见解准备各个工作区。 此步骤将安装所需的解决方案，该方案支持从 Log Analytics 代理收集数据。 对于每个工作区，只需完成一次配置。 有关使用 Azure 门户及其他方法完成此配置的详细信息，请参阅[启用 VM 见解概述](vminsights-enable-overview.md)。

## <a name="send-an-activity-log-to-a-log-analytics-workspace"></a>将活动日志发送到 Log Analytics 工作区
你可以查看为 Azure 门户中的每个虚拟主机收集的平台指标和活动日志。 将此数据发送到与 VM 见解相同的 Log Analytics 工作区中，以便与针对此虚拟机收集的其他监视数据一起接受分析。 你可能在为其他 Azure 资源配置监视时已完成此任务，因为 Azure 订阅中的所有资源都使用同一个活动日志。

引入或保留活动日志数据无需任何费用。 若要详细了解如何创建诊断设置，以便将活动日志发送到 Log Analytics 工作区，请参阅[创建诊断设置](../essentials/diagnostic-settings.md)。

### <a name="network-requirements"></a>网络要求
适用于 Linux 和 Windows 的 Log Analytics 代理通过 TCP 端口 443 与 Azure Monitor 服务进行出站通信。 Dependency Agent 使用 Log Analytics 代理进行所有通信，因此不需要任何其他端口。 若要详细了解如何配置防火墙和代理，请参阅[网络要求](../agents/log-analytics-agent.md#network-requirements)。

:::image type="content" source="media/monitor-virtual-machines/network-diagram.png" alt-text="显示网络的关系图。" lightbox="media/monitor-virtual-machines/network-diagram.png":::

### <a name="gateway"></a>网关
使用 Log Analytics 网关，可以通过单个网关从本地计算机进行信道通信。 但是，不能将已启用 Azure Arc 的服务器代理与 Log Analytics 网关一起使用。 如果安全策略需要网关，则需要为本地计算机手动安装代理。 若要详细了解如何配置和使用 Log Analytics 网关，请参阅 [Log Analytics 网关](../agents/gateway.md)。

### <a name="azure-private-link"></a>Azure 专用链接
使用 Azure 专用链接，可为 Log Analytics 工作区创建专用终结点。 配置完成后，必须通过此专用终结点建立到工作区的任何连接。 专用链接使用 DNS 覆写进行工作，因此对单个代理没有配置要求。 有关专用链接的详细信息，请参阅[使用 Azure 专用链接将网络安全地连接到 Azure Monitor](../logs/private-link-security.md)。

## <a name="prepare-hybrid-machines"></a>准备混合计算机
混合计算机是任何未在 Azure 中运行的计算机。 它可以是在另一个云中运行的虚拟机，或是托管提供的虚拟机，或是在数据中心内本地运行的虚拟或物理计算机。 在混合计算机上使用[已启用 Azure Arc 的服务器](../../azure-arc/servers/overview.md)，以便像管理 Azure 虚拟机一样管理这些计算机。 可使用 Azure Monitor 中的 VM 见解，使用相同的进程对已启用 Azure Arc 的服务器启用监视，就像对 Azure 虚拟机所做的一样。 有关为 Azure 准备混合计算机的完整指南，请参阅[计划和部署已启用 Azure Arc 的服务器](../../azure-arc/servers/plan-at-scale-deployment.md)。 此任务包括启用单个计算机和使用 [Azure Policy](../../governance/policy/overview.md) 大规模启用整个混合环境。

已启用 Azure Arc 的服务器无需支付额外费用，但对于启用的不同选项可能需要收取一些费用。 有关详细信息，请参阅 [Azure Arc 定价](https://azure.microsoft.com/pricing/details/azure-arc/)。 为 VM 见解启用混合计算机后，工作区中收集的数据需要付费。

### <a name="machines-that-cant-use-azure-arc-enabled-servers"></a>计算机无法使用已启用 Azure Arc 的服务器
任何符合以下条件的混合计算机将无法使用已启用 Azure Arc 的服务器：

- 由 Azure Arc 启用的服务器代理不支持该计算机的操作系统。有关详细信息，请参阅[受支持的操作系统](../../azure-arc/servers/agent-overview.md#prerequisites)。
- 安全策略不允许计算机直接连接到 Azure。 无论是否安装了已启用 Azure Arc 的服务器，Log Analytics 代理都可以使用 [Log Analytics 网关](../agents/gateway.md)。 由 Azure Arc 启用的服务器代理必须直接连接到 Azure。

你仍可使用 Azure Monitor 监视这些计算机，但需要手动安装代理。 若要在这些混合计算机上手动安装 Log Analytics 代理和 Dependency Agent，请参阅[为混合虚拟机启用 VM 见解](vminsights-enable-hybrid.md)。

> [!NOTE]
> 已启用 Azure Arc 的服务器的专用终结点目前为公共预览版。 该终结点允许混合计算机使用虚拟网络中的专用 IP 地址安全地连接到 Azure。

## <a name="enable-vm-insights-on-machines"></a>在计算机上启用 VM 见解
在计算机上启用 VM 见解后，它会安装 Log Analytics 代理和 Dependency Agent，连接到工作区，然后开始收集性能数据。 你可以开始使用性能视图和工作簿来分析各种来宾操作系统指标的趋势，启用 VM 见解的映射功能来分析运行进程和计算机之间的依赖项，还可以收集所需数据以创建各种警报规则。

可在单个计算机上启用 VM 见解，方法与在 Azure 虚拟机和已启用 Azure Arc 的服务器上使用的方法相同。 这些方法包括通过 Azure 门户或 Azure 资源管理器模板加入单个计算机，或者使用 Azure Policy 大规模启用计算机。 不会对 VM 见解直接收取费用，但在 Log Analytics 工作区中引入和保留收集的数据需要付费。

有关为计算机启用 VM 见解的不同选项，请参阅[启用 VM 见解概述](vminsights-enable-overview.md)。 若要创建策略以实现在任何新创建的计算机上自动启用 VM 见解，请参阅[使用 Azure Policy 启用 VM 见解](vminsights-enable-policy.md)。


## <a name="send-guest-performance-data-to-metrics"></a>将来宾性能数据发送到指标
当 Azure Monitor 代理完全支持 Azure Monitor、Azure 安全中心和 Azure Sentinel 时，[Azure Monitor 代理](../agents/azure-monitor-agent-overview.md)将替代 Log Analytics 代理。 在此之前，可通过安装 Log Analytics 代理，将性能数据从计算机的来宾操作系统发送到 Azure Monitor 指标。 此配置允许使用指标资源管理器评估这些数据并使用指标警报。

Azure Monitor 代理需要至少一条数据收集规则 (DCR) 用于定义应收集哪些数据以及应向何处发送这些数据。 单个 DCR 可供同一资源组中的任何计算机使用。

使用下列数据源为每个资源组（包含要监视的计算机）创建单个 DCR： 

- 数据源类型：性能计数器
- 目标：Azure Monitor 指标

请注意不要将数据发送到日志，因为这对于 Log Analytics 代理已收集的数据而言是多余的。

可在单个计算机上安装 Azure Monitor 代理，方法与在 Azure 虚拟机和已启用 Azure Arc 的服务器上使用的方法相同。 这些方法包括使用 Azure 门户或资源管理器模板加入单个计算机，或者使用 Azure Policy 大规模启用计算机。 对于无法使用已启用 Azure Arc 的服务器的混合计算机，请手动安装代理。

若要使用 Azure 门户创建 DCR 并将 Azure Monitor 代理部署到一个或多个代理，请参阅[在 Azure 门户中创建规则和关联](../agents/data-collection-rule-azure-monitor-agent.md)。 [安装 Azure Monitor 代理](../agents/azure-monitor-agent-install.md)中还介绍了其他安装方法。 若要创建策略，以便将代理和 DCR 自动部署到任何新创建的计算机，请参阅[使用 Azure Policy 大规模部署 Azure Monitor](../deploy-scale.md#azure-monitor-agent)。

## <a name="next-steps"></a>后续步骤

* [分析针对虚拟机收集的监视数据](monitor-virtual-machine-analyze.md)
* [根据收集的数据创建警报](monitor-virtual-machine-alerts.md)
* [监视虚拟机上运行的工作负载](monitor-virtual-machine-workloads.md)