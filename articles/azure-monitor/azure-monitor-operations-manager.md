---
title: 面向现有 Operations Manager 客户的 Azure Monitor
description: 面向现有 Operations Manager 用户的指南，介绍如何在转换到云的过程中，将对某些工作负载的监视转换到 Azure Monitor。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: ccc46073ef81977f5cd467c84dc96946980cc8c8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714472"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>面向现有 Operations Manager 客户的 Azure Monitor
本文为当前使用 [System Center Operations Manager](/system-center/scom/welcome) 并计划转换到 [Azure Monitor](overview.md) 的客户提供指导，告诉他们如何将商业应用程序和其他资源迁移到 Azure。 它假定你的最终目标是完全转换到云，并在不影响业务和 IT 操作要求的情况下，尽可能用 Azure Monitor 代替 Operations Manager 功能。 

当 Azure Monitor 和 Operations Manager 增加功能时，本文提出的具体建议也会随之改变。 不过，基本策略将保持一致。

> [!IMPORTANT]
> 实施此处介绍的几项 Azure Monitor 功能会产生成本，因此在跨整个环境进行部署之前，应评估其价值。

## <a name="prerequisites"></a>先决条件
本文假定你已使用 [Operations Manager](/system-center/scom)，并且至少对 [Azure Monitor](overview.md) 有基本的了解。 有关两者的完整比较，请参阅[云监视指南：监视平台概述](/azure/cloud-adoption-framework/manage/monitor/platform-overview)。 这篇文章详细介绍了两者之间的具体功能差异，可帮助你了解此处提出的一些建议。 


## <a name="general-strategy"></a>常规策略
由于平台本质上的不同，没有任何迁移工具可将资产从 Operations Manager 转换为 Azure Monitor。 你的迁移将构成一个[标准的 Azure Monitor 实施](deploy.md)，而你将继续使用 Operations Manager。 当你根据对不同应用程序和组件的要求自定义 Azure Monitor，并且它获得更多功能时，你就可以开始停用 Operations Manager 中的各种管理包和代理。

本文推荐的常规策略与[云监视指南](/azure/cloud-adoption-framework/manage/monitor/)推荐的策略相同，后者建议使用[混合云监视](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring)策略，该策略可让你逐步转换到云。 尽管某些功能可能会重叠，但该策略允许你在逐渐熟悉新平台的过程中维护现有的业务流程。 只有在 Azure Monitor 可以取而代之的情况下，才能弃用 Operations Manager 功能。 使用多个监视工具确实会增加复杂性，但它可以让你利用 Azure Monitor 监视下一代云工作负载的能力，同时保留 Operations Manager 监视可能在本地或其他云中的服务器软件和基础结构组件的能力。 


## <a name="components-to-monitor"></a>要监视的组件
它有助于对需要监视的各种工作负载进行分类，以便为每种工作负载确定不同的监视策略。 当你从旧版企业应用程序转换到云中的新式应用程序时，需要监视环境中的各种层，[云监视指南：制定监视策略](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling)对此进行了详细分类。

在云出现之前，你使用 Operations Manager 监视所有层。 当你开始向基础结构即服务 (IaaS) 转换时，你继续使用 Operations Manager 来监视虚拟机，但开始使用 Azure Monitor 来监视云资源。 当你进一步转换到使用平台即服务 (PaaS) 的新式应用程序时，你可以更多地关注 Azure Monitor，并开始停用 Operations Manager 功能。


![云模型](/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

这些层可以简化为以下类别（详见本文的其余部分）。 虽然环境中的监视工作负载可能无法做到全都恰好归入这些类别之一，但每一个工作负载都应足够接近某个特定类别，才适用常规建议。

**商业应用程序。** 提供业务特定功能的应用程序。 它们可能是内部的，也可能是外部的，通常使用自定义代码在内部开发。 旧版应用程序通常托管在运行 Windows 或 Linux 的虚拟机或物理计算机上，而较新的应用程序将基于 Azure 中的应用程序服务，例如 Azure Web 应用和 Azure Functions。

**Azure 服务。** Azure 中支持已迁移到云的商业应用程序的资源。 其中包括 Azure 存储、Azure SQL 和 Azure IoT 等服务。 同时还包括 Azure 虚拟机，因为它们像其他 Azure 服务一样受到监视，但在这些虚拟机的来宾操作系统上运行的应用程序和软件需要获得比主机更多的监视。

**服务器软件。** 在虚拟机和物理计算机上运行的软件，用于支持商业应用程序或为业务提供常规功能的打包应用程序。 示例包括 Internet Information Services (IIS)、SQL Server、Exchange 和 SharePoint。 同时还包括虚拟机和物理计算机上的 Windows 或 Linux 操作系统。

**本地基础结构。** 需要监视的特定于本地环境的组件。 其中包括物理服务器、存储和网络组件等资源。 这些是迁移到云时虚拟化的组件。

## <a name="sample-walkthrough"></a>示例演练
下面是一个从 Operations Manager 迁移到 Azure Monitor 的假设演练。 这并不代表实际迁移的全部复杂性，但至少提供了基本的步骤和顺序。 以下部分更详细地说明了其中每个步骤。

在将任何组件迁移到 Azure 之前，你的环境基于本地或托管服务提供商的虚拟机和物理计算机。 它依靠 Operations Manager 来监视环境（例如物理服务器和网络）中的商业应用程序、服务器软件和其他基础结构组件。 你为服务器软件（例如 IIS、SQL Server 和各种供应商软件）使用标准管理包，并根据具体要求调整这些管理包。 你为商业应用程序和其他无法使用现有管理包监视的组件创建自定义管理包，并配置 Operations Manager 以支持你的业务流程。

你的 Azure 迁移之旅从 IaaS 开始，以将支持商业应用程序的虚拟机迁移到 Azure。 这些应用程序及其所依赖的服务器软件的监视要求不会改变，你可以继续在这些服务器上将 Operations Manager 与现有管理包结合使用。 

一旦创建了 Azure 订阅，就会为 Azure 服务启用 Azure Monitor。 它会自动收集平台指标和活动日志，你可以配置要收集的资源日志，以便使用日志查询以交互方式分析所有可用遥测数据。 你可以在虚拟机上启用 VM 见解，以便共同分析整个环境中的监视数据，发现虚拟机与进程之间的关系。 你可以通过在本地物理计算机和虚拟机上启用支持 Azure Arc 的服务器，将 Azure Monitor 的使用扩展到这些物理计算机和虚拟机。 

为每个商业应用程序启用 Application Insights。 它标识每个应用程序的不同组件，开始收集使用情况和性能数据，并标识代码中发生的任何错误。 你可以创建可用性测试来主动测试外部应用程序，并提醒你任何性能或可用性问题。 虽然 Application Insights 为你提供了 Operations Manager 中没有的强大功能，但你仍要继续依赖为商业应用程序开发的自定义管理包，因为它们包括 Azure Monitor 尚未涵盖的监视方案。 

随着你对 Azure Monitor 的熟悉，你开始创建能够取代某些管理包功能的警报规则，并开始改进业务流程，以使用新的监视平台。 这使你可以开始从 Operations Manager 管理组中删除计算机和管理包。 你继续对关键的服务器软件和本地基础结构使用管理包，同时密切留意 Azure Monitor 中的新功能，这些功能将取代其他功能。

## <a name="monitor-azure-services"></a>监视 Azure 服务
Azure 服务实际上需要 Azure Monitor 来收集遥测数据，并且在你创建 Azure 订阅时就启用了 Azure Monitor。 系统会自动为订阅收集[活动日志](essentials/activity-log.md)，并从你创建的任何 Azure 资源中自动收集[平台指标](essentials/data-platform-metrics.md)。 你可以立即开始使用[指标资源管理器](essentials/metrics-getting-started.md)，它与操作控制台中的性能视图类似，但它提供数据的交互式分析和[高级聚合](essentials/metrics-charts.md)。 [创建指标警报](alerts/alerts-metric.md)，以便在值超过阈值时收到通知，或者[向 Azure 仪表板添加图表](essentials/metrics-charts.md#pinning-to-dashboards)，以提高可见性。

[![指标资源管理器](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

为每个 Azure 资源[创建一个诊断设置](essentials/diagnostic-settings.md)，以将指标和[资源日志](essentials/resource-logs.md)发送到 Log Analytics 工作区，这些指标和日志提供了有关每个资源的内部操作的详细信息。 这可以为资源提供所有可用的遥测数据，并允许你使用 [Log Analytics](logs/log-analytics-overview.md) 和 Operations Manager 中没有的高级查询语言，以交互方式分析日志和性能数据。 你还可以创建[日志查询警报](alerts/alerts-log-query.md)，这类警报可以使用复杂的逻辑来确定警报条件并关联多个资源中的数据。

[![日志分析](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

Azure Monitor 中的[见解](monitor-reference.md)与管理包类似，因为它们为特定的 Azure 服务提供独特的监视。 见解目前可用于多种服务，包括网络、存储和容器，适用的服务还在不断增加。

[![见解示例](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


见解基于 Azure Monitor 中的[工作簿](visualize/workbooks-overview.md)，后者将指标和日志查询合并为丰富多样的交互式报表。 创建你自己的工作簿，以合并来自多个服务的数据，此操作类似于在操作控制台中创建自定义视图和报表。

### <a name="azure-management-pack"></a>Azure 管理包
[Azure 管理包](https://www.microsoft.com/download/details.aspx?id=50013)允许 Operations Manager 根据一组特定的监视方案来发现 Azure 资源并监视其运行状况。 此管理包确实需要你对 Azure 中的每个资源进行额外的配置，但在改进业务流程以重点使用 Azure Monitor 之前，它可能有助于在操作控制台中监视 Azure 资源。

[![Azure 管理包](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 如果你想在操作控制台中监视某些 Azure 资源，并将某些基本警报与现有流程集成，则可以选择使用 Azure 管理包。 它实际上使用 Azure Monitor 收集的数据。 不过，如果要对 Azure 资源进行长期完整的监视，你应该使用 Azure Monitor。 


## <a name="monitor-server-software-and-local-infrastructure"></a>监视服务器软件和本地基础结构
将计算机迁移到云时，对其软件的监视要求不会改变。 你不再需要监视其物理组件，因为它们已虚拟化，但是，来宾操作系统及其工作负载无论在什么环境下都具有相同的要求。

[VM 见解](vm/vminsights-overview.md)是 Azure Monitor 用于监视虚拟机及其来宾操作系统和工作负载的主要功能。 与 Operations Manager 类似，VM 见解使用代理从虚拟机的来宾操作系统收集数据。 这与管理包通常用于分析和警报的性能和事件数据相同。 但是，没有预先存在的规则来识别这些虚拟机中运行的商业应用程序和服务器软件的问题，并就这些问题发出警报。 你必须创建自己的警报规则，以针对任何检测到的问题主动收到通知。

[![VM 见解性能](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

Azure Monitor 也不衡量虚拟机上运行的不同应用程序和服务的运行状况。 当某个值低于阈值时，指标警报可以自动解决，但 Azure Monitor 目前既无法为虚拟机上运行的应用程序和服务定义运行状况条件，也未提供运行状况汇总来对相关组件的运行状况进行分组。

> [!NOTE]
> [用于 VM 见解的新来宾运行状况功能](vm/vminsights-health-overview.md)现在处于公共预览状态，它根据一组性能指标的运行状况状态发出警报。 但是，此功能最初仅适用于与来宾操作系统相关的一组特定性能计数器，而不适用于虚拟机中运行的应用程序或其他工作负载。
> 
> [![VM 见解来宾运行状况](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

监视混合环境中虚拟机上的软件通常需要结合使用 VM 见解和 Operations Manager，这取决于每个虚拟机的要求以及围绕 Azure Monitor 开发操作流程的成熟度。 两个平台都使用 Microsoft 管理代理（在 Azure Monitor 中称为 Log Analytics 代理），因此，两个平台可以同时监视一个虚拟机。

> [!NOTE]
> 将来，VM 见解会转换为 [Azure Monitor 代理](agents/azure-monitor-agent-overview.md)，后者目前处于公共预览状态。 它将与 Microsoft Monitoring Agent 兼容，因此，这两个平台仍然能够监视同一个虚拟机。

继续使用 Operations Manager 来获得 Azure Monitor 尚不能提供的功能。 这包括用于关键服务器软件（如 IIS、SQL Server 或 Exchange）的管理包。 你也可能有为本地基础结构开发的自定义管理包，这些管理包是 Azure Monitor 无法提供的。 如果 Operations Manager 已紧密集成到你的操作流程中，则在实现服务操作的现代化并能够使用 Azure Monitor 和其他 Azure 服务对其进行扩充或取代之前，你也可以继续使用 Operations Manager。 

使用用于 VM 的 Azure Monitor 来增强当前的监视功能，即使它不能立即取代 Operations Manager。 Azure Monitor 独有的功能示例包括：

- 发现和监视虚拟机与其外部依赖项之间的关系。
- 在交互式图表和工作簿中查看多个虚拟机上的聚合性能数据。
- 使用[日志查询](logs/log-query-overview.md)以交互方式分析来自虚拟机的遥测数据以及来自其他 Azure 资源的数据。
- 根据多个虚拟机之间的复杂逻辑创建[日志警报规则](alerts/alerts-log-query.md)。

[![VM 见解映射](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

除了 Azure 虚拟机，VM 见解还可以使用[启用了 Azure Arc 的服务器](../azure-arc/servers/overview.md)来监视本地和其他云中的计算机。 使用启用了 Azure Arc 的服务器，你可以按照管理本机 Azure 虚拟机的方式，管理在 Azure 外部、在企业网络上或其他云提供商中托管的 Windows 和 Linux 计算机。



## <a name="monitor-business-applications"></a>监视商业应用程序
通常，你需要使用自定义管理包、Operations Manager 以及每个虚拟机上安装的代理来监视商业应用程序。 Azure Monitor 中的 Application Insights 监视基于 Web 的应用程序（无论它们是在 Azure、其他云中还是在本地），因此，它可以用于所有应用程序，无论它们是否已迁移到 Azure。

如果你对商业应用程序的监视仅限于 Operations Manager 中的 [.NET 应用性能模板]()提供的功能，那么你很有可能在不损失任何功能的情况下迁移到 Application Insights。 事实上，Application Insights 将包含大量附加功能，包括：

- 自动发现和监视应用程序组件。
- 收集详细的应用程序使用情况和性能数据，例如响应时间、故障率和请求速率。
- 收集浏览器数据，例如页面视图和加载性能。
- 检测异常并深入研究堆栈跟踪和相关请求。
- 使用[分布式跟踪](app/distributed-tracing.md)和[智能检测](app/proactive-diagnostics.md)等功能执行高级分析。
- 使用[指标资源管理器](essentials/metrics-getting-started.md)以交互方式分析性能数据。
- 使用[日志查询](logs/log-query-overview.md)以交互方式分析收集的遥测数据以及为 Azure 服务和 VM 见解收集的数据。

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

不过，在某些情况下，你可能需要在 Application Insights 之外继续使用 Operations Manager，直到你能够实现所需的功能。 你可能需要继续使用 Operations Manager 的示例包括：

-  [可用性测试](app/monitor-web-app-availability.md)用于对应用程序的可用性和响应能力进行监视并发出警告，它需要来自 Web 测试代理的 IP 地址的传入请求。 如果你的策略不允许这种访问，你可能需要继续使用 Operations Manager 中的 [Web 应用程序可用性监视](/system-center/scom/web-application-availability-monitoring-template)。
- 在 Operations Manager 中，你可以为可用性测试设置任何轮询间隔，许多客户每 60-120 秒检查一次。 Application Insights 的最小轮询间隔为 5 分钟，这对于某些客户而言可能太长。
- 通过收集应用程序生成的事件并在本地代理上运行脚本，可在 Operations Manager 中执行大量监视任务。 这些都不是 Application Insights 中的标准选项，因此，你可能需要通过一些自定义操作来满足你的业务需求。 这可能包括使用 Log Analytics 工作区中存储的事件数据的自定义警报规则，以及使用[混合 runbook 辅助角色](../automation/automation-hybrid-runbook-worker.md)在虚拟机来宾操作系统中启动的脚本。
- 根据应用程序编写语言的不同，你可能只能使用[可与 Application Insights 一起使用的工具](app/platforms.md)。

按照本指南其他部分的基本策略，继续为商业应用程序使用 Operations Manager，但也利用 Application Insights 提供的附加功能。 当你能够用 Azure Monitor 取代其关键功能时，就可以开始停用自定义管理包。


## <a name="next-steps"></a>后续步骤

- 有关 Azure Monitor 和 System Center Operations Manager 的详细比较，以及有关设计和实施混合监视环境的更多详细信息，请参阅[云监视指南](/azure/cloud-adoption-framework/manage/monitor/)。
- 详细了解如何[在 Azure Monitor 中监视 Azure 资源](essentials/monitor-azure-resource.md)。
- 详细了解如何[在 Azure Monitor 中监视虚拟机](vm/monitor-vm-azure.md)。
- 详细了解 [VM 见解](vm/vminsights-overview.md)。
- 详细了解 [Application Insights](app/app-insights-overview.md)。
