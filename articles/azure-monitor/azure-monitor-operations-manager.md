---
title: 现有 Operations Manager 客户 Azure Monitor
description: Operations Manager 的现有用户的指南，用于将特定工作负荷的监视转移到 Azure Monitor 作为过渡到云的一部分。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: 6d92b7c2f01a7e9ef12bc2bb422cfb6ed0076f73
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039370"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>现有 Operations Manager 客户 Azure Monitor
本文为当前使用 [System Center Operations Manager](/system-center/scom/welcome) ，并在将业务应用程序和其他资源迁移到 Azure 时计划过渡到 [Azure Monitor](overview.md) 的客户提供指导。 它假定您的最终目标是完全转换到云，使用 Azure Monitor 尽可能多地替换 Operations Manager 功能，而不会影响业务和 IT 操作要求。 

本文中所述的具体建议将随着 Azure Monitor 和 Operations Manager 添加功能而更改。 不过，基本策略会保持一致。

> [!IMPORTANT]
> 实现此处所述的几个 Azure Monitor 功能是一项收费，因此在整个环境中进行部署之前，应评估其价值。

## <a name="prerequisites"></a>先决条件
本文假设你已使用 [Operations Manager](/system-center/scom) ，并且至少对 [Azure Monitor](overview.md)有基本的了解。 有关这两者之间的完整比较，请参阅 [云监视指南：监视平台概述](/azure/cloud-adoption-framework/manage/monitor/platform-overview)。 本文详细介绍了与这两者之间的特定功能差异，以帮助你了解此处所述的一些建议。 


## <a name="general-strategy"></a>常规策略
没有用于将资产从 Operations Manager 转换为 Azure Monitor 的迁移工具，因为这些平台在本质上是不同的。 当你继续使用 Operations Manager 时，迁移将改为构成 [标准 Azure Monitor 实现](deploy.md) 。 当你自定义 Azure Monitor 以满足针对不同应用程序和组件的要求，并且在获得更多功能时，你可以开始在 Operations Manager 中停用不同的管理包和代理。

本文中建议的常规策略与 [云监视指南](/azure/cloud-adoption-framework/manage/monitor/)中所述的相同，后者建议使用 [混合云监视](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) 策略，以逐步转换到云。 尽管某些功能可能会重叠，但当你更熟悉新平台时，此策略可让你维护现有的业务流程。 只是离开 Operations Manager 功能，您可以将其替换为 Azure Monitor。 使用多种监视工具增加了复杂性，但它使你能够利用 Azure Monitor 功能来监视下一代云工作负载，同时保持 Operations Manager 监视本地或其他云中的服务器软件和基础结构组件的能力。 


## <a name="components-to-monitor"></a>要监视的组件
它可以帮助对不同类型的工作负荷进行分类，以便为每个工作负荷确定不同的监视策略。 [云监视指南：构建一个监视策略](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling) ，在您的环境中提供不同层的详细细分，需要在从旧企业应用程序到云中的新式应用程序时进行监视。

在云之前，你使用 Operations Manager 来监视所有层。 当你开始使用基础结构即服务 (IaaS) 进行转换时，你将继续为虚拟机使用 Operations Manager，但会开始将 Azure Monitor 用于云资源。 随着你使用平台即服务 (PaaS) 进一步过渡到新式应用程序，你可以将更多精力放在 Azure Monitor 上，并开始停用 Operations Manager 功能。


![云模型](/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

这些层可以简化为以下类别，详见本文的其余部分。 虽然您的环境中的每个监视工作负荷可能不会整齐地纳入其中一种类别，但每个监视工作负荷都应该接近特定的类别，以便满足一般建议。

**业务应用程序。** 提供特定于业务的功能的应用程序。 它们可以是内部的，也可以是外部的，通常使用自定义代码进行开发。 你的旧应用程序通常会托管在运行 Windows 或 Linux 的虚拟机或物理计算机上，而更新的应用程序将基于 Azure 中的应用程序服务（例如 Azure Web 应用和 Azure Functions）。

**Azure 服务。** 支持已迁移到云的业务应用程序的 Azure 中的资源。 其中包括 Azure 存储、Azure SQL 和 Azure IoT 等服务。 这还包括 Azure 虚拟机，因为它们与其他 Azure 服务一样受到监视，但在这些虚拟机的来宾操作系统上运行的应用程序和软件需要更多监视主机以外的内容。

**服务器软件。** 在虚拟机和物理计算机上运行的软件，支持业务应用程序或为你的业务提供常规功能的打包应用程序。 示例包括 Internet Information Server (IIS) 、SQL Server、Exchange 和 SharePoint。 这还包括虚拟机和物理计算机上的 Windows 或 Linux 操作系统。

**本地基础结构。** 需要监视的本地环境特定的组件。 这包括物理服务器、存储和网络组件等资源。 这些是在移动到云时虚拟化的组件。

## <a name="sample-walkthrough"></a>示例演练
下面是从 Operations Manager 迁移到 Azure Monitor 的假设演练。 这并不代表实际迁移的全部复杂性，但至少提供基本步骤和序列。 以下部分更详细地介绍了其中的每个步骤。

在将任何组件移入 Azure 之前，你的环境将基于位于本地的虚拟机和物理计算机，或使用托管服务提供商。 它依赖于 Operations Manager 来监视环境（如物理服务器和网络）中的业务应用程序、服务器软件和其他基础结构组件。 为服务器软件（如 IIS、SQL Server 和各种供应商软件）使用标准管理包，并根据特定要求调整这些管理包。 你为你的业务应用程序和其他组件创建自定义管理包，这些管理包不能与现有管理包进行监视，并配置 Operations Manager 来支持你的业务流程。

迁移到 Azure 后，将启动 IaaS，将支持业务应用程序的虚拟机迁移到 Azure。 这些应用程序及其依赖的服务器软件的监视要求不会改变，你可以继续在这些服务器上使用现有管理包的 Operations Manager。 

创建 Azure 订阅后，即可为 Azure 服务启用 Azure Monitor。 它自动收集平台指标和活动日志，并配置要收集的资源日志，以便可以使用日志查询以交互方式分析所有可用的遥测。 在虚拟机上启用 VM insights，分析整个环境中的监视数据，并发现计算机与进程之间的关系。 通过启用启用了 Azure Arc 的服务器，你可以将 Azure Monitor 的使用扩展到本地物理机和虚拟机。 

为每个业务应用程序启用 Application Insights。 它标识每个应用程序的不同组件，开始收集使用情况和性能数据，并标识代码中发生的任何错误。 您可以创建可用性测试来主动测试您的外部应用程序，并向您发出任何性能或可用性问题的警报。 虽然 Application Insights 提供了你没有 Operations Manager 的强大功能，但你仍会依赖于你为业务应用程序开发的自定义管理包，因为它们包括 Azure Monitor 尚未涵盖的监视方案。 

随着您对 Azure Monitor 的熟悉，您开始创建可替换某些管理包功能的警报规则，并开始发展您的业务流程，以使用新的监视平台。 这允许你开始从 Operations Manager 管理组中删除计算机和管理包。 你将继续使用管理包来实现关键服务器软件和本地基础结构，但继续观看 Azure Monitor 中的新功能，这些功能可让你停用其他功能。

## <a name="monitor-azure-services"></a>监视 Azure 服务
Azure 服务实际需要 Azure Monitor 收集遥测数据，并在创建 Azure 订阅时启用。 系统会自动为订阅收集 [活动日志](essentials/activity-log.md) ， [平台指标](essentials/data-platform-metrics.md) 会自动从你创建的任何 Azure 资源中收集。 你可以立即开始使用 [指标资源管理器，该资源管理器](essentials/metrics-getting-started.md)与操作控制台中的性能视图相似，但它提供交互式分析和数据的 [高级聚合](essentials/metrics-charts.md) 。 创建要在值超过阈值时通知的[指标警报](alerts/alerts-metric.md)，或[将图表添加到 Azure 仪表板](essentials/metrics-charts.md#pinning-to-dashboards)以供查看。

[![指标资源管理器](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

为每个 Azure 资源[创建诊断设置](essentials/diagnostic-settings.md)，以便将指标和[资源日志](essentials/resource-logs.md)发送到 Log Analytics 工作区，其中提供了有关每个资源的内部操作的详细信息。 这为你的资源提供了所有可用的遥测，并允许你使用 [Log Analytics](logs/log-analytics-overview.md) 以交互方式使用在 Operations Manager 中没有等效项的高级查询语言来分析日志和性能数据。 你还可以创建 [日志查询警报](alerts/alerts-log-query.md)，它们可以使用复杂逻辑来确定警报条件并跨多个资源关联数据。

[![日志分析](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

Azure Monitor 中的[见解](monitor-reference.md)类似于管理包，因为它们提供特定 Azure 服务的独特监视。 见解当前可用于多个服务，包括网络、存储和容器，而另一些则是连续添加的。

[![见解示例](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


见解基于 Azure Monitor 中的 [工作簿，该工作簿](visualize/workbooks-overview.md) 将指标和日志查询合并为丰富的交互式报表。 创建你自己的工作簿以合并来自多个服务的数据，这类似于你可能在操作控制台中创建自定义视图和报表的方式。

### <a name="azure-management-pack"></a>Azure 管理包
[Azure 管理包](https://www.microsoft.com/download/details.aspx?id=50013)允许 Operations Manager 基于一组特定的监视方案来发现 Azure 资源并监视其运行状况。 此管理包确实要求你对 Azure 中的每个资源执行额外的配置，但在操作控制台中提供 Azure 资源的一些可见性可能会很有帮助，因为你要将业务流程集中到 Azure Monitor。

[![Azure 管理包](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 如果需要操作控制台中某些 Azure 资源的可见性并将一些基本警报与现有进程集成，则可以选择使用 Azure 管理包。 它实际使用 Azure Monitor 收集的数据。 你应了解 Azure Monitor，以便长期完成 Azure 资源的监视。 


## <a name="monitor-server-software-and-local-infrastructure"></a>监视服务器软件和本地基础结构
将计算机移动到云时，对其软件的监视要求不会改变。 你不再需要监视其物理组件，因为它们已经过虚拟化，但不管其环境如何，来宾操作系统及其工作负载都具有相同的要求。

[VM insights](vm/vminsights-overview.md) 是 Azure Monitor 用于监视虚拟机及其来宾操作系统和工作负载的主要功能。 与 Operations Manager 类似，VM insights 使用代理从虚拟机的来宾操作系统收集数据。 这与管理包通常用于分析和警报的性能和事件数据相同。 但未预先存在的规则用于标识和警报在这些计算机中运行的业务应用程序和服务器软件的问题。 您必须创建自己的警报规则，以主动收到任何检测到的问题的通知。

[![VM insights 性能](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

Azure Monitor 也不会度量在虚拟机上运行的不同应用程序和服务的运行状况。 当某个值低于阈值但 Azure Monitor 当前不能定义计算机上运行的应用程序和服务的运行状况条件，也不提供运行状况汇总来对相关组件的运行状况进行分组时，指标警报可以自动解决。

> [!NOTE]
> [VM insights 的新来宾健康状况功能](vm/vminsights-health-overview.md)现在处于公共预览状态，并根据一组性能指标的运行状况状态发出警报。 此操作最初限制为一组与来宾操作系统相关的特定性能计数器，而不是在虚拟机中运行的应用程序或其他工作负荷。
> 
> [![VM insights 来宾运行状况](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

监视混合环境中计算机上的软件通常会结合使用 VM insights 和 Operations Manager，具体取决于每台计算机的要求以及您在 Azure Monitor 上开发操作过程的成熟度。 Microsoft 管理代理 (称为 Azure Monitor) 中的 Log Analytics 代理，这两种平台都可以同时监视一台计算机。

> [!NOTE]
> 以后，VM insights 会转换为 [Azure Monitor 代理](agents/azure-monitor-agent-overview.md)，当前为公共预览版。 它将与 Microsoft Monitoring Agent 兼容，因此，这两个平台将继续监视相同的虚拟机。

继续使用 Operations Manager，Azure Monitor 尚未提供的功能。 这包括用于关键服务器软件（如 IIS、SQL Server 或 Exchange）的管理包。 你还可以为本地基础结构开发的自定义管理包，这些管理包无法与 Azure Monitor 联系在一起。 如果你可以过渡到现代化你的服务操作（其中 Azure Monitor 和其他 Azure 服务可以增加或替换），则还继续使用 Operations Manager。 

使用 Vm Azure Monitor 来增强当前监视，即使它不会立即替换 Operations Manager。 Azure Monitor 独有功能的示例包括以下各项：

- 发现和监视虚拟机与其外部依赖项之间的关系。
- 在交互式图表和工作簿中查看跨多个虚拟机的聚合性能数据。
- 使用 [日志查询](logs/log-query-overview.md) 可以使用其他 Azure 资源中的数据以交互方式分析虚拟机中的遥测数据。
- 基于跨多个虚拟机的复杂逻辑创建 [日志警报规则](alerts/alerts-log-query.md) 。

[![VM 见解映射](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

除了 Azure 虚拟机，VM insights 还可以使用 [启用了 Azure Arc 的服务器](../azure-arc/servers/overview.md)监视本地和其他云中的计算机。 启用 Arc 的服务器允许管理托管在 Azure 外、公司网络或其他云提供商上的 Windows 和 Linux 计算机，与管理本机 Azure 虚拟机的方式一致。



## <a name="monitor-business-applications"></a>监视业务应用程序
通常需要自定义管理包，通过 Operations Manager 来监视业务应用程序，并利用在每个虚拟机上安装的代理。 Azure Monitor 中的 Application Insights 监视基于 web 的应用程序，无论这些应用程序是在 Azure、其他云还是本地，都可用于所有应用程序，无论这些应用程序是否已迁移到 Azure。

如果你对业务应用程序的监视仅限于 Operations Manager 中的 [.net 应用性能模板]() 提供的功能，则很可能会迁移到 Application Insights，而不会丢失功能。 事实上，Application Insights 将包含大量附加功能，包括：

- 自动发现和监视应用程序组件。
- 收集详细的应用程序使用情况和性能数据，如响应时间、故障率和请求速率。
- 收集浏览器数据，如页面视图和加载性能。
- 检测异常并钻取堆栈跟踪和相关请求。
- 使用 [分布式跟踪](app/distributed-tracing.md) 和 [智能检测](app/proactive-diagnostics.md)等功能执行高级分析。
- 使用 [指标资源管理器](essentials/metrics-getting-started.md) 以交互方式分析性能数据。
- 使用 [日志查询](logs/log-query-overview.md) 以交互方式分析收集的遥测数据，以及为 Azure 服务和 VM insights 收集的数据。

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

在某些情况下，你可能需要继续使用 Operations Manager 除了 Application Insights，直到能够实现所需的功能。 可能需要继续 Operations Manager 的示例包括：

-  [可用性测试](app/monitor-web-app-availability.md)允许你监视应用程序的可用性和响应能力，并要求来自 web 测试代理的 IP 地址的传入请求。 如果策略不允许这种访问，可能需要在 Operations Manager 中继续使用 [Web 应用程序可用性监视器](/system-center/scom/web-application-availability-monitoring-template) 。
- 在 Operations Manager 可以为可用性测试设置任何轮询间隔，其中，多个客户每60-120 秒检查一次。 Application Insights 的最小轮询间隔为5分钟，某些客户可能太长。
- 通过收集应用程序生成的事件以及在本地代理上运行脚本，可执行大量 Operations Manager 监视。 这不是 Application Insights 中的标准选项，因此你可能需要自定义工作来满足你的业务需求。 这可能包括使用存储在 Log Analytics 工作区中的事件数据的自定义警报规则和使用 [混合 runbook 辅助角色](../automation/automation-hybrid-runbook-worker.md)在虚拟机来宾中启动的脚本。
- 根据你的应用程序的编写语言，你可能会受到 [可用于 Application Insights 的检测](app/platforms.md)的限制。

在本指南的其他部分中，请遵循本指南的其他部分中的基本策略，继续使用适用于你的业务应用程序的 Operations Manager，但使用 Application Insights 提供的其他功能。 由于可以用 Azure Monitor 替换关键功能，因此可以开始停用自定义管理包。


## <a name="next-steps"></a>后续步骤

- 请参阅 [云监视指南](/azure/cloud-adoption-framework/manage/monitor/) ，详细了解 Azure Monitor 和 System Center Operations Manager，并详细了解如何设计和实现混合监视环境。
- 阅读有关 [监视 Azure 资源的](essentials/monitor-azure-resource.md)详细信息 Azure Monitor。
- 阅读有关 [监视 Azure Monitor 中的 Azure 虚拟机的](vm/monitor-vm-azure.md)详细信息。
- 阅读有关 [VM 见解](vm/vminsights-overview.md)的详细信息。
- 阅读有关 [Application Insights](app/app-insights-overview.md)的详细信息。