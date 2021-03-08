---
title: Azure Monitor 文档中的新增内容
description: Azure Monitor 文档每月都有重大更新。
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/10/2021
ms.openlocfilehash: dd6c44587ce3f4e2b5de940ef831a20a4079c4ef
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051916"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Azure Monitor 文档中有哪些新增内容？

本文提供新的或已经过重大更新的 Azure Monitor 文章列表。 在每个月的第一周，本文将会刷新，以包含上个月的文章更新。

## <a name="january-2021"></a>2021 年 1 月 

### <a name="general"></a>常规 
- [Azure Monitor 常见问题解答](faq.md) - 添加了有关 Application Insights 设备信息的条目。
### <a name="agents"></a>代理  
- [收集 Windows 事件跟踪 (ETW) 事件以在 Azure Monitor 日志中进行分析](./agents/data-sources-event-tracing-windows.md) - 新文章。
- [Azure Monitor 中的数据收集规则（预览版）](./agents/data-collection-rule-overview.md) - 添加了 PowerShell 和 CLI 示例的链接。

### <a name="alerts"></a>警报  
- [配置 Azure 以使用安全导出连接 ITSM 工具](./alerts/itsm-connector-secure-webhook-connections-azure-configuration.md) - 新文章。
- [ITSMC 仪表板中的连接器状态错误](./alerts/itsmc-dashboard-errors.md) - 新文章。
- [使用 ITSMC 仪表板调查错误](./alerts/itsmc-dashboard.md) - 新文章。
- [排查 Azure 指标警报](./alerts/alerts-troubleshoot-metric.md) - 添加了针对动态阈值的部分。
- [排查 IT 服务管理连接器中的问题](./alerts/itsmc-troubleshoot-overview.md) - 新文章。

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights 遥测关联](app/correlation.md) - 添加了一个模块在 OpenCensus Python 中调用另一个模块时的跟踪关联。
- [适用于网页的 Application Insights](app/javascript.md) - 新文章。
- [单击适用于 Application Insights JavaScript SDK 的“分析自动收集”插件](app/javascript-click-analytics-plugin.md) - 新文章。
- [在不更改代码的情况下监视应用 - Azure Monitor Application Insights 自动检测功能](app/codeless-overview.md) - 添加了 Python 列。
- [适用于 Application Insights JavaScript SDK 的 React 插件](app/javascript-react-plugin.md) - 新文章。
- [遥测处理器（预览版）- 适用于 Java 的 Azure Monitor Application Insights](app/java-standalone-telemetry-processors.md) - 已重写。
- [使用 Azure Application Insights 进行使用情况分析](app/usage-overview.md) - 新文章。
- [使用 Azure Monitor 中的应用程序更改分析查找 Web 应用问题](app/change-analysis.md) - 添加了错误消息。


### <a name="insights"></a>洞察力    
- [适用于 Azure 数据资源管理器的 Azure Monitor（预览版）](insights/data-explorer.md)- 新文章。

### <a name="logs"></a>日志    
- [Azure Monitor 客户管理的密钥](./logs/customer-managed-keys.md) - 引入用户分配的托管标识。
- [Azure Monitor 日志专用群集](./logs/logs-dedicated-clusters.md) - 更新了响应代码。
- [跨服务查询 - Azure Monitor 和 Azure 数据资源管理器（预览版）](/azure/azure-monitor/platform/azure-data-explorer-monitor-cross-service-query)- 新文章。

### <a name="metrics"></a>指标
- [Azure Monitor 指标的指标聚合和显示说明](./essentials/metrics-aggregation-explained.md) - 新文章。

### <a name="platform-logs"></a>平台日志
- [Azure Monitor 资源日志支持的服务和类别](./essentials/resource-logs-categories.md) - 新文章。

### <a name="visualizations"></a>可视化效果
- [Azure Monitor 工作簿数据源](./visualize/workbooks-data-sources.md) - 添加了合并和更改分析。


## <a name="december-2020"></a>2020 年 12 月

### <a name="general"></a>常规
- [Azure Monitor 客户管理的密钥](logs/customer-managed-keys.md) - 添加了错误消息。
- [与 Azure Monitor 集成的合作伙伴](partners.md) - 添加了有关事件中心集成的部分。

### <a name="agents"></a>代理
- [使用 Azure Monitor 跨资源查询 Azure 数据资源管理器](logs/azure-monitor-data-explorer-proxy.md) -新文章。
- [Azure 监视代理概述](agents/agents-overview.md) - 添加了 Oracle 8 支持。

### <a name="alerts"></a>警报
- [排查 Azure 指标警报](alerts/alerts-troubleshoot-metric.md) - 添加了针对动态阈值的故障排除。
- [Log Analytics 中的 IT 服务管理连接器](alerts/itsmc-definition.md) - 新文章。
- [IT 服务管理连接器概述](alerts/itsmc-overview.md) - 重构了故障排除信息。
- [将 Cherwell 连接到 IT 服务管理连接器](alerts/itsmc-connections-cherwell.md) - 新文章。
- [将 Provance 连接到 IT 服务管理连接器](alerts/itsmc-connections-provance.md) - 新文章。
- [将 SCSM 连接到 IT 服务管理连接器](alerts/itsmc-connections-scsm.md) - 新文章。
- [将 ServiceNow 连接到 IT 服务管理连接器](alerts/itsmc-connections-servicenow.md) - 新文章。
- [如何手动修复 ServiceNow 同步问题](alerts/itsmc-resync-servicenow.md) - 重构了故障排除信息。




### <a name="application-insights"></a>Application Insights
- [适用于 JavaScript Web 应用的 Azure Application Insights](app/javascript.md) - 添加了连接字符串设置。
- [Azure Application Insights 标准指标](app/standard-metrics.md) -新文章。
- [Azure Monitor Application Insights Java](app/java-in-process-agent.md) - 添加了有关从应用程序发送自定义遥测数据的信息。
- [从 Application Insights 连续导出遥测数据](app/export-telemetry.md) - 添加了基于诊断设置的导出。
- [在 Azure Functions 为 .NET 和 .NET Core 应用启用 Snapshot Debugger](app/snapshot-debugger-function-app.md) - 新文章。
- [Application Insights 和 Log Analytics 使用的 IP 地址](app/ip-addresses.md) - 添加了 Azure 政府的 IP 地址。
- [排查 Azure Application Insights Profiler 的问题](app/profiler-troubleshooting.md) - 添加了有关诊断服务站点扩展的状态页的信息。
- [排查 Azure Application Insights 可用性测试](app/troubleshoot-availability.md) - 更新了 ping 测试的故障排除。
- [排查适用于 Java 的 Azure Monitor Application Insights](app/java-standalone-troubleshoot.md) - 新文章。

### <a name="containers"></a>容器
- [容器见解报表](insights/container-insights-reports.md) - 新文章。

### <a name="logs"></a>日志
- [Azure Monitor 日志专用群集](logs/logs-dedicated-clusters.md) - 添加了自动化命令、取消链接和删除的方法以及故障排除。
- [Azure Monitor 和 Azure 数据资源管理器之间的跨服务查询（预览版）](logs/azure-data-explorer-monitor-cross-service-query.md) - 新文章。
- [Azure Monitor 中的 Log Analytics 工作区数据导出功能（预览版）](logs/logs-data-export.md)- 添加了 ARM 模板。

### <a name="metrics"></a>指标
- [Azure 指标资源管理器的高级功能](essentials/metrics-charts.md) - 添加了有关资源范围选取器的信息。
- [在指标资源管理器中查看多个资源](essentials/metrics-dynamic-scope.md) - 新文章。

### <a name="networks"></a>网络
- [Azure Monitor 中的 Azure 网络分析解决方案](insights/azure-networking-analytics.md) - 添加了有关网络见解工作簿的信息。

### <a name="virtual-machines"></a>虚拟机
- [为混合环境启用 Azure Monitor](vm/vminsights-enable-hybrid.md) - 新版本的依赖项代理。


### <a name="visualizations"></a>可视化效果
- [Azure Monitor 工作簿映射可视化效果](visualize/workbooks-map-visualizations.md) - 新文章。
- [Azure Monitor 工作簿自带存储](visualize/workbooks-bring-your-own-storage.md) - 新文章。


## <a name="november-2020"></a>2020 年 11 月

### <a name="general"></a>常规
- [Azure Monitor 服务限制](service-limits.md) - 已针对 Azure Arc 支持更新。

### <a name="agents"></a>代理
- [Azure 监视代理概述](agents/agents-overview.md) - 已针对 Azure Arc 支持更新。
- [安装 Azure Monitor 代理](agents/azure-monitor-agent-install.md) - 新文章。
- [Azure Monitor 代理概述](agents/azure-monitor-agent-overview.md) - 已针对 Azure Arc 支持更新。
- [用于代理的资源管理器模板示例](agents/resource-manager-agent.md) - 已针对 Azure Arc 支持更新。

### <a name="alerts"></a>警报
- [在 Azure 门户中创建和管理操作组](alerts/action-groups.md) - 添加了 Webhook 的资源 IP 地址。

### <a name="application-insights"></a>Application Insights
- [Java 无代码应用程序监视 Azure Monitor Application Insights](app/java-in-process-agent.md) - 添加了配置示例。
- [适用于 Application Insights JavaScript SDK 的 React 插件](app/javascript-react-plugin.md) - 添加了关于使用 React 挂钩的部分。
- [从 Application Insights Java 2.x SDK 升级](app/java-standalone-upgrade-from-2x.md) - 新文章。
- [Microsoft.ApplicationInsights.SnapshotCollector 的发行说明](app/snapshot-collector-release-notes.md) - 新文章。

### <a name="autoscale"></a>自动缩放
- [Azure 中的自动缩放入门](autoscale/autoscale-get-started.md) - 添加了有关将自动缩放移动到不同区域的部分。

### <a name="data-collection"></a>数据收集
- [为 Azure Monitor 代理（预览版）配置数据收集](agents/data-collection-rule-azure-monitor-agent.md) - 更新了 Azure Arc 支持。
- [Azure Monitor 中的数据收集规则（预览版）](agents/data-collection-rule-overview.md)- 已针对 Azure Arc 支持更新。
- [用于数据收集规则的资源管理器模板示例](agents/resource-manager-data-collection-rules.md) - 新文章。

### <a name="insights-and-solutions"></a>见解和解决方案
- [使用安全导出将 Azure 连接到 ITSM 工具](alerts/it-service-management-connector-secure-webhook-connections.md) - 添加了有关连接到 ServiceNow 的部分。

### <a name="logs"></a>日志
- [集成 Log Analytics 和 Excel](logs/log-excel.md) - 新文章。
- [Log Analytics 数据安全](logs/data-security.md) - 添加了有关其他安全功能的部分。
- [Log Analytics 与 Power BI 集成](logs/log-powerbi.md) - 新文章。
- [Azure Monitor 日志记录中的标准列](logs/log-standard-columns.md) - 添加了 _SubscriptionId 列。

有关日志查询内容重构的新文章和更新的文章。

- [Log Analytics 教程](logs/log-analytics-tutorial.md)
- [Azure Monitor 中的日志查询](logs/log-query-overview.md)
- [Azure Monitor 中的 Log Analytics 概述](logs/log-analytics-overview.md)
- [Azure 数据资源管理器和 Azure Monitor 的查询示例](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [教程：在 Azure 数据资源管理器和 Azure Monitor 中使用 Kusto 查询](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)



### <a name="virtual-machines"></a>虚拟机

- [启用 VM 见解概述](vm/vminsights-enable-overview.md) - 添加支持的区域。

有关 VM 见解来宾运行状况的新文章（预览）

- [VM 见解来宾运行状况（预览）](vm/vminsights-health-overview.md)
- [VM 见解来宾运行状况警报（预览）](vm/vminsights-health-alerts.md)
- [在 VM 见解来宾运行状况中配置监视（预览）](vm/vminsights-health-configure.md)
- [使用数据收集规则在 VM 见解来宾运行状况中配置监视（预览）](vm/vminsights-health-configure-dcr.md)
- [启用 VM 见解来宾运行状况（预览）](vm/vminsights-health-enable.md)
- [VM 见解来宾运行状况疑难解答（预览）](vm/vminsights-health-troubleshoot.md)





## <a name="october-2020"></a>2020 年 10 月

### <a name="general"></a>常规
- [Azure Monitor API 停用](logs/operationalinsights-api-retirement.md) - 新文章。

### <a name="agents"></a>代理
- [Azure Monitor 监视的内容](monitor-reference.md) - 添加了有关代理的部分。

### <a name="alerts"></a>警报
- [在 Azure 门户中创建和管理操作组](alerts/action-groups.md) - 添加了有关服务标记的部分。
- [指标警报的资源管理器模板示例](alerts/resource-manager-alerts-metric.md) - 添加了内容匹配参数和测试位置。
- [排查 Azure 指标警报问题](alerts/alerts-troubleshoot-metric.md) - 添加了规则配置的最佳做法。

### <a name="application-insights"></a>Application Insights
- [适用于 Application Insights JavaScript SDK 的 Angular 插件](app/javascript-angular-plugin.md) - 新文章。
- [ASP.NET Core 应用程序的 Azure Application Insights](app/asp-net-core.md) - 添加了关于 ILogger 日志的 FAQ。
- [使用 Azure Application Insights 为 ASP.NET 配置监视](app/asp-net.md) - 重写了文章。
- [Azure Application Insights 中基于日志的指标和预先聚合的指标](app/pre-aggregated-metrics-log-metrics.md) - 添加了具有预先聚合的指标的表。
- [监视任何网站的可用性和响应能力](app/monitor-web-app-availability.md) - 添加了有关位置填充标记的部分。
- [随时随地监视 Java 应用程序 - Azure Monitor Application Insights](app/java-standalone-config.md) - 添加了配置示例。
- [随时随地监视 Java 应用程序 - Azure Monitor Application Insights](app/java-standalone-telemetry-processors.md) - 新文章。
- [使用 Azure Monitor 中的应用程序更改分析查找 Web 应用问题](app/change-analysis.md) - 添加了有关虚拟机和活动日志的部分。
  
### <a name="autoscale"></a>自动缩放
- [Azure 中的自动缩放入门](autoscale/autoscale-get-started.md) - 添加了有关将自动缩放移动到不同区域的部分。

### <a name="containers"></a>容器
- [使用容器见解配置 PV 监视](containers/container-insights-persistent-volumes.md) - 新文章。
- [如何管理容器见解代理](containers/container-insights-manage-agent.md) - 添加了对已启用 Azure Arc 的 Kubernetes 群集的支持。
- [来自容器见解的指标警报](containers/container-insights-metric-alerts.md) - 添加了对已启用 Azure Arc 的 Kubernetes 群集的支持。

### <a name="insights-and-solutions"></a>见解和解决方案
- [IT 服务管理连接器 - Azure Monitor 中的安全导出](alerts/it-service-management-connector-secure-webhook-connections.md) - 添加了有关 ServiceNow 部分。

### <a name="logs"></a>日志
- [使用逻辑应用将数据从 Log Analytics 工作区存档到 Azure 存储](logs/logs-export-logic-app.md) - 新文章。
- [Azure Monitor 中的 Log Analytics 工作区数据导出功能（预览版）](logs/logs-data-export.md)- 添加了事件中心的 REST 请求的示例正文。
- [管理 Azure Monitor 日志的使用情况和成本](logs/manage-cost-storage.md) - 添加了有关 Azure Monitor 日志和 Azure 安全中心计费之间的关系的信息。 如果使用的是每节点定价层，则添加了节点计数查询。 
- [监视 Azure Monitor 中 Log Analytics 工作区的运行状况](logs/monitor-workspace.md) - 新文章。
- [使用 Azure 数据资源管理器查询 Azure Monitor 中的数据（预览版）](logs/azure-data-explorer-monitor-proxy.md)- 新文章。
- [使用 Azure 数据资源管理器查询从 Azure Monitor 导出的数据（预览版）](logs/azure-data-explorer-query-storage.md)- 新文章。

### <a name="networks"></a>网络
- [Azure 网络监视器预览版](insights/network-insights-overview.md) - 添加了疑难解答部分。 添加了有关连接的部分。

### <a name="platform-logs"></a>平台日志
- [Azure 活动日志事件架构](essentials/activity-log-schema.md) - 添加了严重性级别的描述。

### <a name="virtual-machines"></a>虚拟机
- [更改用于 VM 的 Azure Monitor 中的分析](vm/vminsights-change-analysis.md) - 新文章。
- [启用用于 VM 的 Azure Monitor 概述](vm/vminsights-enable-overview.md) - 添加了支持的区域。
- [如何为指标更新容器见解](containers/container-insights-update-metrics.md) - 添加了对已启用 Azure Arc 的 Kubernetes 群集的支持。



## <a name="september-2020"></a>2020 年 9 月

### <a name="general"></a>常规
- [Azure Monitor常见问题解答](faq.md) - 添加了有关 OpenTelemetry 的部分。

### <a name="agents"></a>代理
- [Azure Monitor 代理概述](agents/azure-monitor-agent-overview.md) - 添加了切换到新代理的决定因素。
- [Azure 监视代理概述](agents/agents-overview.md) - 添加了对 Windows 10 的支持。

### <a name="alerts"></a>警报
- [使用 Azure 资源管理器模板创建日志警报](alerts/alerts-log-create-templates.md) - 新文章。
- [排查 Azure 指标警报问题](alerts/alerts-troubleshoot-metric.md) - 添加了有关导出指标警报规则的 ARM 模板的部分。

### <a name="application-insights"></a>Application Insights
- [新建基于工作区的 Azure Monitor Application Insights 资源](app/create-workspace-resource.md) - 删除了预览名称。
- [Azure Application Insights 中的数据保留和存储](app/data-retention-privacy.md) - 添加了对 Mac 和 Linux 数据丢失保护的新增支持的详细信息。
- [Application Insights 中的事件计数器](app/eventcounters.md) - 添加了有关默认收集的计数器的备注。
- [Azure Application Insights 中基于日志的指标和预先聚合的指标](app/pre-aggregated-metrics-log-metrics.md) - 删除了预览名称。
- [将 Azure Monitor Application Insights 经典资源迁移到基于工作区的资源](app/convert-classic-resource.md) - 新文章。
- [在任何环境中监视 Java 应用程序 - Azure Monitor Application Insights](app/java-in-process-agent.md) - 更新为代理的新预览版本。
- [使用 Azure Application Insights 为 ASP.NET 设置 Web 应用分析](app/asp-net.md) - 重写了文章。
- [Azure Application Insights 中的遥测通道](app/telemetry-channels.md) - 添加了对 Mac 和 Linux 数据丢失保护的新增支持的详细信息。
- [Azure Application Insights Snapshot Debugger 故障排除](app/snapshot-debugger-troubleshoot.md) - 向 Snapshot Debugger 故障排除添加了“SSL”部分。
- [使用 Azure Monitor 中的应用程序更改分析查找 Web 应用问题](app/change-analysis.md) - 添加了虚拟机和活动日志。


### <a name="containers"></a>容器
- [使用容器见解配置已启用 Azure Arc 的 Kubernetes 群集](containers/container-insights-enable-arc-enabled-clusters.md) - 添加了有关使用服务主体启用监视功能的指南。
- [带有容器见解的部署和 HPA 指标](containers/container-insights-deployment-hpa-metrics.md) - 新文章。

### <a name="insights-and-solutions"></a>见解和解决方案
- [适用于 Azure Cache for Redis 的 Azure Monitor](insights/redis-cache-insights-overview.md) - 删除了预览名称。
- [Azure 网络监视器（预览版）](insights/network-insights-overview.md)- 添加了“连接和流量”部分。
- [IT 服务管理连接器 - Azure Monitor 中的安全导出](alerts/it-service-management-connector-secure-webhook-connections.md) - 新文章。
- [Azure Monitor 中的 IT 服务管理连接器](alerts/itsmc-connections.md) - 有关 Cherwell 和 Provance ITSM 集成的说明。
- [使用 Azure Monitor for Key Vault 监视 Key Vault](insights/key-vault-insights-overview.md) - 删除了预览名称。

### <a name="logs"></a>日志
- [审核 Azure Monitor 日志查询中的查询](logs/query-audit.md) - 新文章。
- [Azure Monitor 客户管理的密钥](logs/customer-managed-keys.md) - 添加了客户密码箱。
- [Azure Monitor 日志专用群集](logs/logs-dedicated-clusters.md) - 新文章。
- [设计 Azure Monitor 日志部署](logs/design-logs-deployment.md) - 更新了“缩放和引入卷速率限制”部分。
- [Azure Monitor Log Analytics 中的日志查询范围](logs/scope.md) - 更新为包括基于工作区的应用程序。
- [登录 Azure Monitor](logs/data-platform-logs.md) - 更新为包括基于工作区的应用程序。
- [Azure Monitor 日志记录中的标准列](logs/log-standard-columns.md) - 更新为包括基于工作区的应用程序。
- [Azure Monitor 服务限制](service-limits.md) - 更新了针对用户查询限制的限制。
- [在 Azure Monitor Log Analytics 中使用客户管理的存储帐户](logs/private-storage.md) - 重写了文章。
- [查看和分析 Azure Log Analytics 中的数据](./logs/data-platform-logs.md) - 更新为包括基于工作区的应用程序。


### <a name="platform-logs"></a>平台日志
- [Azure 活动日志事件架构 - Azure Monitor](essentials/activity-log-schema.md) - 添加了严重性级别。
- [诊断设置的资源管理器模板示例](essentials/resource-manager-diagnostic-settings.md) - 添加了 Azure 存储帐户示例。

### <a name="visualizations"></a>可视化效果
- [Azure Monitor 工作簿图表可视化效果](visualize/workbooks-chart-visualizations.md) - 新文章。
- [Azure Monitor 工作簿复合条呈现器](visualize/workbooks-composite-bar.md) - 新文章。
- [Azure Monitor 工作簿图可视化效果](visualize/workbooks-graph-visualizations.md) - 新文章。
- [Azure Monitor 工作簿网格可视化效果](visualize/workbooks-grid-visualizations.md) - 新文章。
- [Azure Monitor 工作簿蜂巢可视化效果](visualize/workbooks-honey-comb.md) - 新文章。
- [Azure Monitor 工作簿文本可视化效果](visualize/workbooks-text-visualizations.md) - 新文章。
- [Azure Monitor 工作簿磁贴可视化效果](visualize/workbooks-tile-visualizations.md) - 新文章。
- [Azure Monitor 工作簿树可视化效果](visualize/workbooks-tree-visualizations.md) - 新文章。




## <a name="august-2020"></a>2020 年 8 月

### <a name="general"></a>常规

- [Azure Monitor 监视的内容](monitor-reference.md) - 已更新，以包含 Azure Monitor 代理。


### <a name="agents"></a>代理
- [Azure Monitor 代理概述](agents/azure-monitor-agent-overview.md) - 新文章。
- [为混合环境启用 Azure Monitor](vm/vminsights-enable-hybrid.md) - 更新了依赖项代理版本。
- [Azure 监视代理概述](agents/agents-overview.md) -- 添加了 Azure Monitor 代理并合并了 OS 支持表。


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>有关代理内容重构的新文章和更新的文章
- [启用 VM 见解概述](vm/vminsights-enable-overview.md)
- [在 Linux 计算机上安装 Log Analytics 代理](agents/agent-linux.md)
- [在 Windows 计算机上安装 Log Analytics 代理](agents/agent-windows.md)
- [Log Analytics 代理概述](agents/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [适用于 JavaScript Web 应用的 Azure Application Insights](app/javascript.md) - 添加了介绍客户端服务器相关性和 CORS 相关性配置的部分。
- [创建基于 Azure Monitor Application Insights 工作区的新资源](app/create-workspace-resource.md) - 添加了由基于工作区的应用程序提供的功能。
- [Application Insights 和 Log Analytics 使用的 IP 地址](app/ip-addresses.md) - 更新了实时指标流的 IP 地址。
- [在任何环境中监视 Java 应用程序 - Azure Monitor Application Insights](app/java-in-process-agent.md) - 为支持的自定义遥测添加了表。
- [适用于 Application Insights JavaScript SDK 的 Native React 插件](app/javascript-react-native-plugin.md) - 新文章。
- [适用于 Application Insights JavaScript SDK 的 React 插件](app/javascript-react-plugin.md) - 新文章。
- [用于创建具有 Application Insights 监视的 Azure 函数应用的资源管理器模板示例](app/resource-manager-function-app.md) - 新文章。
- [用于创建具有 Application Insights 监视的 Azure 应用服务 Web 应用的资源管理器模板示例](app/resource-manager-web-app.md) - 新文章。
- [使用 Azure Application Insights 进行使用情况分析](app/usage-overview.md) - 添加了视频。

### <a name="autoscale"></a>自动缩放
- [Azure 中的自动缩放入门](autoscale/autoscale-get-started.md) - 添加了有关路由到应用服务的正常实例的部分。

### <a name="data-collection"></a>数据收集
- [为 Azure Monitor 代理（预览版）配置数据收集](agents/data-collection-rule-azure-monitor-agent.md) - 新项目。
- [Azure Monitor（预览版）中的数据收集规则](agents/data-collection-rule-overview.md) - 新文章。


### <a name="containers"></a>容器
- [带有容器见解的部署和 HPA 指标](containers/container-insights-deployment-hpa-metrics.md) - 新文章。

### <a name="insights"></a>洞察力
- [Azure Monitor 中的监视解决方案](insights/solutions.md) - 更新了新 UI
- [Azure 中的网络性能监视器解决方案](insights/network-performance-monitor.md) - 添加了支持的工作区区域。


### <a name="logs"></a>日志
- [Azure Monitor FAQ](faq.md) - 添加了从工作区中删除数据的条目。 添加了关于 502 和 503 响应的条目。
  - [设计 Azure Monitor 日志部署](logs/design-logs-deployment.md) - 更新了“引入卷速率限制”部分。
- [管理 Azure Monitor 日志的使用情况和成本](logs/manage-cost-storage.md) - 更新了使用情况查询，使查询格式更为有效。
- [优化 Azure Monitor 中的日志查询](logs/query-optimization.md) - 为性能指标添加了特定的值。
- [诊断设置的资源管理器模板示例](essentials/resource-manager-diagnostic-settings.md) - 添加了日志查询审核日志示例。


### <a name="platform-logs"></a>平台日志
- [创建诊断设置以将平台日志和指标发送到不同目标](essentials/diagnostic-settings.md) - 添加了诊断设置的区域要求。

### <a name="visualizations"></a>可视化效果
- [Azure Monitor 工作簿概述](visualize/workbooks-overview.md) - 添加了视频。
- [将 Azure 工作簿模板移动到另一个区域](visualize/workbook-templates-move-region.md) - 新文章。
- [将 Azure 工作簿移动到另一个区域](visualize/workbooks-move-region.md) - 新文章。



## <a name="july-2020"></a>2020 年 7 月

### <a name="general"></a>常规
- [部署 Azure Monitor](deploy-scale.md) - VM 见解载入内容重构。
- [使用 Azure 专用链接将网络安全地连接到 Azure Monitor](logs/private-link-security.md) - 增加了有关限制的部分。

### <a name="alerts"></a>警报
- [Azure Monitor 警报的操作规则](alerts/alerts-action-rules.md) - 增加了 CLI 进程。
- [在 Azure 门户中创建和管理操作组](alerts/action-groups.md) - 已更新以反映 UI 中的更改。
- [Azure Monitor Log Analytics 中的示例查询](logs/example-queries.md) - 新文章。
- [排除 Azure Monitor 中的日志警报故障](alerts/alerts-troubleshoot-log.md) - 增加了有关警报规则配额的部分。
- [排除 Azure 指标警报故障](alerts/alerts-troubleshoot-metric.md) - 增加了有关尚未发出的自定义指标的警报规则部分。
- [了解指标警报在 Azure Monitor 中的工作原理](alerts/alerts-metric-overview.md) - 添加了有关选择聚合粒度的建议。

### <a name="application-insights"></a>Application Insights
- [Azure Web 应用扩展的发行说明 - Application Insights](app/web-app-extension-release-notes.md) - 新文章。
- [Application Insights 资源的资源管理器模板示例](app/resource-manager-app-resource.md) - 新文章。
- [排除 Azure Application Insights Profiler 的问题](app/profiler-troubleshooting.md) - 增加了有关在 Azure 应用服务上运行 ASP.NET Core 应用探查器时出现的 bug 的注释。 

### <a name="containers"></a>容器
- [来自容器见解的日志警报](containers/container-insights-log-alerts.md) - 新文章。
- [来自容器见解的指标警报](containers/container-insights-metric-alerts.md) - 新文章。

### <a name="logs"></a>日志
- [Azure Monitor 客户托管的密钥](logs/customer-managed-keys.md) - 增加了错误消息和用于查询的 CMK 配置部分。
- [Azure Monitor HTTP 数据收集器 API](logs/data-collector-api.md) - 增加了 Python 3 示例。
- [在 Azure Monitor 中优化日志查询](logs/query-optimization.md) - 增加了有关在使用子查询时避免多次数据扫描的部分。
- [教程：Log Analytics 查询入门](./logs/log-analytics-tutorial.md) - 增加了视频。

### <a name="platform-logs"></a>平台日志
- [创建诊断设置以将平台日志和指标发送到不同目标](essentials/diagnostic-settings.md) - 增加了视频。
- [Azure Monitor 的资源管理器模板示例](/resource-manager-samples.md) - 增加了使用日志目标类型的 ARM 示例。 

### <a name="solutions"></a>解决方案
- [Azure Monitor 中的监视解决方案](insights/solutions.md) - 增加了 CLI 进程。
- [Azure 中的 Office 365 管理解决方案](insights/solution-office-365.md) - 更改了停用日期。

### <a name="virtual-machines"></a>虚拟机

有关 VM 见解内容重构的新文章和更新的文章

- [什么是 VM 见解？](vm/vminsights-overview.md)
- [配置 VM 见解的 Log Analytics 工作区](vm/vminsights-configure-workspace.md)
- [将 Linux 计算机连接到 Azure Monitor](agents/agent-linux.md)
- [为混合环境启用 Azure Monitor](vm/vminsights-enable-hybrid.md)
- [在 Azure 门户中为单个虚拟机或虚拟机规模集启用 Azure Monitor](vm/vminsights-enable-portal.md)
- [使用 Azure Policy 启用 VM 见解](./vm/vminsights-enable-policy.md)
- [启用 VM 见解概述](vm/vminsights-enable-overview.md)
- [使用 PowerShell 启用 VM 见解](vm/vminsights-enable-powershell.md)
- [使用资源管理器模板启用 VM 见解](vm/vminsights-enable-resource-manager.md)
- [使用 PowerShell 或模板启用 VM 见解](./vm/vminsights-enable-powershell.md)


### <a name="visualizations"></a>可视化效果
- [升级 Log Analytics 仪表板可视化效果](logs/dashboard-upgrade.md) - 已更新刷新频率。
- [可视化 Azure Monitor 中的数据](visualizations.md) - 增加了视频。


## <a name="june-2020"></a>2020 年 6 月

### <a name="general"></a>常规
- [部署 Azure Monitor](deploy-scale.md) - 新文章。
- [Azure Monitor 客户管理的密钥](logs/customer-managed-keys.md) - 更新的 billingtype 属性。 添加了 PowerShell 命令。

### <a name="agents"></a>代理
- [Log Analytics 代理概述](agents/log-analytics-agent.md) - 添加了 Python 2 要求。

### <a name="alerts"></a>警报
- [如何在警报规则或操作规则的目标资源移动到其他 Azure 区域时进行更新](alerts/alerts-resource-move.md) - 新文章。
- [排查 Azure 指标警报问题](alerts/alerts-troubleshoot-metric.md) - 新文章。
- [在 Azure Monitor 中排查日志警报问题](alerts/alerts-troubleshoot-metric.md) - 新文章。
  
### <a name="application-insights"></a>Application Insights
- [适用于 JavaScript Web 应用的 Azure Application Insights](app/javascript.md) - JavaScript SDK 更新部分。 更新了代码段以报告加载失败。
- [为探查器和 Snapshot Debugger 配置 BYOS（自带存储）](app/profiler-bring-your-own-storage.md) - 新文章。
- [使用 OpenCensus Python 在 Azure Application Insights 中跟踪导入请求](app/opencensus-python-request.md) - 更新了 OpenCensus 的日志记录和配置。
- [使用 Azure Application Insights 监视实时 ASP.NET Web 应用](app/monitor-performance-live-website-now.md) - 更新了 Application Insights 状态监视器 v1 的删除日期。
- [使用 Application Insights 监视 Node.js 服务](app/nodejs.md) - 包括从早期版本和 SDK 配置迁移的多个更新
- [使用 Azure Monitor 监视 Python 应用程序（预览版）](app/opencensus-python.md) - 添加了有关配置 Azure Monitor 导出程序的部分。
- [在不更改代码的情况下监视应用 - Azure Monitor Application Insights 自动检测功能](app/codeless-overview.md) - 新文章。
- [对 JavaScript Web 应用的 SDK 加载失败进行故障排查](app/javascript-sdk-load-failure.md) - 新文章。

### <a name="containers"></a>容器
- [如何停止监视混合 Kubernetes 群集](containers/container-insights-optout-hybrid.md) - 添加了有关已启用 Arc 的 Kubernetes 的部分。
- [使用容器见解配置已启用 Azure Arc 的 Kubernetes 群集](containers/container-insights-enable-arc-enabled-clusters.md) - 新文章。
- [使用容器见解配置 Azure Red Hat OpenShift v4.x](containers/container-insights-azure-redhat4-setup.md) - 已更新先决条件。
- [设置容器见解实时数据（预览）](containers/container-insights-livedata-setup.md) - 删除了关于 Azure 美国政府不提供此功能的说明。

### <a name="insights"></a>洞察力
- [常见问题解答 - Azure 中的网络性能监视器解决方案](insights/network-performance-monitor-faq.md) - 添加了 ExpressRoute 监视器的常见问题解答。

### <a name="logs"></a>日志
- [删除和恢复 Azure Log Analytics 工作区](logs/delete-workspace.md) - 添加了 PowerShell 命令。 更新了疑难解答。
- [在 Azure Monitor 中管理 Log Analytics 工作区](logs/manage-access.md) - 在 Azure RBAC 部分添加了不允许的表的示例。
- [管理 Azure Monitor 日志的使用情况和成本](logs/manage-cost-storage.md) - 有关数据大小计算的其他详细信息。 更新了数据卷警报的配置。 有关 Azure Sentinel 收集的安全数据的详细信息。 数据上限的说明。
- [将 Azure Monitor 日志与 Azure 逻辑应用和 Power Automate 配合使用](logs/logicapp-flow-connector.md) - 添加了连接器限制。

### <a name="metrics"></a>指标
- [Azure Monitor 按资源类型支持的指标](essentials/metrics-supported.md) - 更新了 SQL Server 指标。


### <a name="platform-logs"></a>平台日志

- [诊断设置的资源管理器模板示例](essentials/resource-manager-diagnostic-settings.md) - 对活动日志诊断设置的修复。
- [使用 Azure 门户将 Azure 活动日志发送到 Log Analytics 工作区](essentials/quick-collect-activity-log-portal.md) - 新文章。
- [使用 Azure 资源管理器模板将 Azure 活动日志发送到 Log Analytics 工作区](essentials/quick-collect-activity-log-arm.md) - 新文章。

来自平台日志内容重构和整合的新文章和更新文章

- [将 Azure 资源日志存档到存储帐户](./essentials/resource-logs.md#send-to-azure-storage)
- [Azure 活动日志事件架构](essentials/activity-log-schema.md)
- [Azure 活动日志](essentials/activity-log.md)
- [Azure Monitor CLI 示例](/cli-samples.md)
- [Azure Monitor PowerShell 示例](/powershell-samples.md)
- [Azure 监视 REST API 演练](essentials/rest-api-walkthrough.md)
- [Azure 资源日志支持的服务和架构](./essentials/resource-logs-schema.md)
- [Azure 资源日志](essentials/resource-logs.md)
- [收集和分析 Azure Monitor 中的 Azure 活动日志](./essentials/activity-log.md)
- [收集 Log Analytics 工作区中的 Azure 资源日志](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [创建诊断设置以将平台日志和指标发送到不同目标](essentials/diagnostic-settings.md)
- [导出 Azure 活动日志](./essentials/activity-log.md#legacy-collection-methods)
- [Azure 平台日志概述](essentials/platform-logs-overview.md)
- [将 Azure 平台日志流式传输到事件中心](./essentials/resource-logs.md#send-to-azure-event-hubs)
- [在 Azure Monitor 中查看 Azure 活动日志事件](./essentials/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>虚拟机
- [在 Azure 门户中启用 VM 见解](./vm/vminsights-enable-portal.md) - 更新为包含 Azure Arc。
- [启用 VM 见解概述](vm/vminsights-enable-overview.md) - 更新为包含 Azure Arc。
- [什么是 VM 见解？](vm/vminsights-overview.md) - 更新为包含 Azure Arc。


### <a name="visualizations"></a>可视化效果
- [Azure Monitor 工作簿数据源](visualize/workbooks-data-sources.md) - 添加了“警报和自定义终结点”部分。
- [对 Azure Monitor 基于工作簿的见解进行故障排查](insights/troubleshoot-workbooks.md) - 新文章。
- [升级 Log Analytics 仪表板可视化效果](logs/dashboard-upgrade.md) - 新文章。



## <a name="may-2020"></a>2020 年 5 月

### <a name="general"></a>常规

- [Azure Monitor常见问题解答](faq.md) - 添加了指标部分。
- [Azure Monitor 客户托管密钥](logs/customer-managed-keys.md) - 准备正式发布的各种更改。
- [Azure Monitor 的内置策略定义](./policy-reference.md) - 新文章。
- [客户拥有的用于日志引入的存储帐户](logs/private-storage.md) - 新文章。
- [管理 Azure Monitor 日志的使用情况和成本](logs/manage-cost-storage.md) - 添加了群集按比例计费。
- [使用 Azure 专用链接将网络安全地连接到 Azure Monitor](logs/private-link-security.md) - 新文章。


#### <a name="new-resource-manager-template-samples"></a>新的资源管理器模板示例 
- [用于 Azure Monitor 的资源管理器模板示例](/resource-manager-samples.md)
- [用于操作组的资源管理器模板示例](alerts/resource-manager-action-groups.md)
- [用于代理的资源管理器模板示例](agents/resource-manager-agent.md)
- [容器见解资源管理器模板示例](containers/resource-manager-container-insights.md)
- [VM 见解资源管理器模板示例](vm/resource-manager-vminsights.md)
- [用于诊断设置的资源管理器模板示例](essentials/resource-manager-diagnostic-settings.md)
- [用于 Log Analytics 工作区的资源管理器模板示例](logs/resource-manager-workspace.md)
- [用于日志查询的资源管理器模板示例](logs/resource-manager-log-queries.md)
- [用于日志查询警报规则的资源管理器模板示例](alerts/resource-manager-alerts-log.md)
- [用于指标警报规则的资源管理器模板示例](alerts/resource-manager-alerts-metric.md)
- [用于工作簿的资源管理器模板示例](visualize/resource-manager-workbooks.md)

### <a name="agents"></a>代理
- [安装并配置 Windows Azure 诊断扩展 (WAD)](agents/diagnostics-extension-windows-install.md) - 添加了有关配置诊断的详细信息。
- [Log Analytics 代理概述](agents/log-analytics-agent.md) - 添加了受支持的 Linux 版本。

### <a name="application-insights"></a>Application Insights

- [使用 Application Insights 监视 Azure Functions 上运行的应用程序 - Azure Monitor](app/monitor-functions.md) - 新文章。
- [使用 Azure Application Insights 监视 Node.js 服务](app/nodejs.md) - 常规更新，包括有关从以前版本迁移的新部分。
- [Application Insights 和 Log Analytics 使用的 IP 地址](app/ip-addresses.md) - 添加了 Webhook 和美国政府的 IP 地址。
- [使用 Application Insights 监视 Azure Kubernetes 服务 (AKS) 上的应用程序 - Azure Monitor](app/kubernetes-codeless.md) - 新文章。
- [排除无数据问题 - 用于 .NET 的 Application Insights](app/asp-net-troubleshoot-no-data.md) - 添加了有关使用 dotnet-trace 收集日志的部分。
- [使用 Azure Monitor 中的应用程序更改分析查找 Web 应用问题](app/change-analysis.md) -“更改分析”功能中的多个更新。

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>基于工作区的应用程序预览版的新的和更新的文章
- [基于工作区的 Azure Monitor Application Insights 资源架构](app/apm-tables.md)
- [新建基于工作区的 Azure Monitor Application Insights 资源](app/create-workspace-resource.md)
- [Azure Monitor 日志查询中的 app() 表达式](logs/app-expression.md)
- [Azure Monitor Log Analytics 中的日志查询范围](logs/scope.md)
- [使用 Azure Monitor 跨资源进行查询](logs/cross-workspace-query.md)
- [Azure Monitor 日志记录中的标准属性](./logs/log-standard-columns.md)
- [Azure Monitor 日志的结构](./logs/data-platform-logs.md)





### <a name="containers"></a>容器
- [如何启用容器见解](containers/container-insights-onboard.md) - 更新了防火墙配置表。
- [如何为指标更新容器见解](containers/container-insights-update-metrics.md) - 使用托管标识收集指标的更新。
- [容器见解的监视成本](containers/container-insights-cost.md) - 新文章。
- [设置容器见解实时数据（预览）](containers/container-insights-livedata-setup.md) - 支持新的群集角色绑定。

### <a name="insights"></a>洞察力
- [适用于 Azure Cache for Redis（预览版）的 Azure Monitor](insights/redis-cache-insights-overview.md) - 新文章。
- [使用 Azure Monitor for Key Vault（预览版）监视 Key Vault](./insights/key-vault-insights-overview.md) - 新文章。

### <a name="logs"></a>日志
- [使用 PowerShell 创建和配置 Log Analytics](logs/powershell-workspace-configuration.md) - 添加了故障排除部分。
- [在 Azure 门户中创建 Log Analytics 工作区](logs/quick-create-workspace.md) - 添加了故障排除部分。
- [使用 Azure CLI 创建 Log Analytics 工作区](logs/quick-create-workspace-cli.md) - 添加了故障排除部分。
- [删除和恢复 Azure Log Analytics 工作区](logs/delete-workspace.md) - 更新了有关恢复已删除工作区的信息。
- [Azure Monitor 日志查询中的函数](logs/functions.md) - 删除了有关不包含其他函数的函数的注释。
- [Azure Monitor 日志的结构](./logs/data-platform-logs.md) - 阐明了 Application Insights 表的属性说明。
- [将 Azure Monitor 日志与 Azure 逻辑应用和 Power Automate 配合使用](logs/logicapp-flow-connector.md) - 添加了限制部分。
- [使用 PowerShell 创建和配置 Log Analytics 工作区](logs/powershell-workspace-configuration.md) - 添加了故障排除部分。


### <a name="metrics"></a>指标
- [Azure Monitor 按资源类型支持的指标](essentials/metrics-supported.md) - 阐明了来宾指标和指标路由。 

### <a name="solutions"></a>解决方案
- [使用 Azure Monitor 优化 Active Directory 环境](insights/ad-assessment.md) - 向支持的版本中添加了 Windows Server 2019。
- [使用 Azure Monitor 优化 SQL Server 环境](insights/sql-assessment.md) - 添加到了支持的 SQL Server 版本。


### <a name="virtual-machines"></a>虚拟机
- [启用 VM 见解概述](vm/vminsights-enable-overview.md) - 添加到了支持的 Ubuntu Server 版本。 为 Log Analytics 工作区添加了支持的区域。
- [如何使用 VM 见解绘制性能图表](vm/vminsights-performance.md) - 为不可用的指标添加了限制部分。

### <a name="visualizations"></a>可视化效果
- [Azure Monitor 工作簿和 Azure 资源管理器模板](visualize/workbooks-automate.md) - 添加了用于部署工作簿模板的资源管理器更新。
- [Azure Monitor 工作簿组](./visualize/workbooks-groups.md) - 新文章。
- [Azure Monitor 工作簿 - 使用 JSONPath 转换 JSON 数据](visualize/workbooks-jsonpath.md) - 新文章。


## <a name="april-2020"></a>2020 年 4 月

### <a name="general"></a>常规

- [Azure Monitor 客户托管密钥](logs/customer-managed-keys.md) - 添加了关于异步操作的部分
- [在 Azure Monitor 中管理 Log Analytics 工作区](logs/manage-access.md) - 更新了自定义日志部分。

### <a name="alerts"></a>警报

- [Azure Monitor 警报的操作规则](alerts/alerts-action-rules.md) - 添加了视频。
- [Azure 中的警报和通知监视概述](alerts/alerts-overview.md) - 添加了视频。

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights 中的应用程序映射](app/app-map.md) - 为 Java 代理添加了云角色名称配置。
- [Azure Application Insights .NET 代理 API 参考](app/status-monitor-v2-api-reference.md) - 合并了 API 参考。
- [Application Insights 和 Log Analytics 使用的 IP 地址](app/ip-addresses.md) - 更新了 App Insights 和 Log Analytics API、操作组 Webhook、Azure 美国政府版的 IP 地址。
- [在任何位置监视 Java 应用程序](app/java-standalone-config.md) - 新文章。
- [在任何环境中监视 Java 应用程序](app/java-in-process-agent.md) - 新文章。
- [监视在任何环境中运行的 Java 应用程序](app/java-standalone-arguments.md) - 新文章。
- [监视本地运行的 Java 应用程序](app/java-on-premises.md) - 新文章。
- [在 Visual Studio 中删除 Application Insights](app/remove-application-insights.md) - 新文章。
- [Azure Application Insights 中的遥测采样](app/sampling.md) - 修复了 Python 中的固定速率采样。

### <a name="containers"></a>容器

- [使用容器见解配置 Azure Red Hat OpenShift v4.x](containers/container-insights-azure-redhat4-setup.md) - 新文章。
- [如何手动修复 ServiceNow 同步问题](alerts/itsmc-resync-servicenow.md) - 新文章。
- [如何停止监视 Azure 和 Red Hat OpenShift v4 群集](containers/container-insights-optout-openshift-v4.md) - 新文章。
- [如何停止监视 Azure Red Hat OpenShift v3 群集](containers/container-insights-optout-openshift-v3.md) - 新文章。
- [如何停止监视混合 Kubernetes 群集](containers/container-insights-optout-hybrid.md) - 新文章。

### <a name="insights"></a>洞察力

- [使用用于 Key Vault 的 Azure Monitor（预览版）监视 Azure Key Vault](insights/key-vault-insights-overview.md) - 新文章。

### <a name="logs"></a>日志

- [Azure Monitor 服务限制](service-limits.md) - 添加了用户查询限制。
- [管理 Azure Monitor 日志的使用情况和成本](logs/manage-cost-storage.md) - 为日志群集添加了计费。 添加了 Kusto 查询，使那些使用旧版“按节点”定价层的客户能够确定是移到“按 GB”定价层还是“产能预留”层。

### <a name="metrics"></a>指标

- [Azure 指标资源管理器的高级功能](essentials/metrics-charts.md) - 添加了聚合部分。

### <a name="workbooks"></a>工作簿

- [Azure Monitor 工作簿和 Azure 资源管理器模板](visualize/workbooks-automate.md) - 添加了用于部署工作簿模板的资源管理器模板。

## <a name="march-2020"></a>2020 年 3 月

### <a name="general"></a>常规

- [Azure Monitor 概述](overview.md) - 添加了 Azure Monitor 概述视频。
- [Azure Monitor 客户托管密钥配置](logs/customer-managed-keys.md) - 常规更新。
- [Azure Monitor 数据参考](/azure/azure-monitor/reference/) - 新站点。

### <a name="alerts"></a>警报

- [在 Azure Monitor 中创建、查看和管理活动日志警报](alerts/alerts-activity-log.md) - 资源管理器模板的附加说明。
- [了解指标警报在 Azure Monitor 中的工作原理](alerts/alerts-metric-overview.md) - 更新了政府支持。
- [排查 Azure Monitor 警报和通知问题](alerts/alerts-troubleshoot.md) - 新文章。

### <a name="application-insights"></a>Application Insights

- [使用 PowerShell 自动化 Azure Application Insights](app/powershell.md) - 添加了 ARMClient 示例。
- [从 Application Insights 连续导出遥测数据](app/export-telemetry.md) - 添加了包含导出结构详细信息的表格。
- [为 Azure 应用服务中的 .NET 应用启用 Snapshot Debugger](app/snapshot-debugger-appservice.md) - 添加了资源管理器模板示例。
- [管理 Azure Application Insights 的用量和成本](app/pricing.md) - 添加了有关数据上限警报的信息。
- [使用 Azure Monitor 监视 Python 应用程序（预览版）](app/opencensus-python.md)- 添加了标准指标。
- [JavaScript 应用程序的源映射支持 - Azure Monitor Application Insights](app/source-map-support.md) - 新文章。

### <a name="containers"></a>容器

- [Azure Monitor 常见问题解答](faq.md) - 容器见解更新。
- [使用容器见解配置 GPU 监视](containers/container-insights-gpu-monitoring.md) - 新文章。

### <a name="insights"></a>洞察力

- [Azure 中的 Office 365 管理解决方案](insights/solution-office-365.md) - 更新了弃用日期。

### <a name="logs"></a>日志

- [在 Azure Monitor 中优化日志查询](logs/query-optimization.md) - 添加了 XML 和 JSON 分析的 CPU 条件。
- [删除和恢复 Azure Log Analytics 工作区](logs/delete-workspace.md) - 添加了故障排除方法。
- [将 Azure Monitor 日志与 Azure 逻辑应用和 Power Automate 配合使用](logs/logicapp-flow-connector.md) - 更新了新 Azure Monitor 连接器的内容。

### <a name="metrics"></a>指标

- [即将弃用 Azure 门户中的磁盘指标](essentials/portal-disk-metrics-deprecation.md) - 新文章。
- [教程 - 在 Azure Monitor 中创建指标图表](essentials/tutorial-metrics-explorer.md) - 添加了视频。

### <a name="platform-logs"></a>平台日志

- [在 Azure Monitor 中收集和分析 Azure 活动日志](./essentials/activity-log.md) - 重新撰写了该文章，以更好地解释如何使用诊断设置收集活动日志。

### <a name="virtual-machines"></a>虚拟机

- [使用 Azure Monitor 监视 Azure 虚拟机](vm/monitor-vm-azure.md) - 新文章。
- [快速入门：使用 Azure Monitor 监视 Azure 虚拟机](vm/quick-monitor-azure-vm.md) - 更新了该文章，在其中添加了 VM 见解的内容。
- [来自 VM 见解的警报](vm/vminsights-alerts.md) - 新文章。
- [启用 VM 见解概述](vm/vminsights-enable-overview.md) - 更新了代理下载链接。

VM 见解正式版常规更新

- [什么是 VM 见解？](vm/vminsights-overview.md)
- [VM 见解 (GA) 常见问题](vm/vminsights-ga-release-faq.md) 
- [使用 Azure Policy 启用 VM 见解](./vm/vminsights-enable-policy.md) 
- [如何利用 VM 见解绘制性能图表](vm/vminsights-performance.md)
- [如何从 VM 见解查询日志](vm/vminsights-log-search.md)
- [使用 VM 见解查看应用依赖项](vm/vminsights-maps.md) 

### <a name="visualizations"></a>可视化效果

- [从 Azure Monitor 可视化数据](visualizations.md) - 更新了该文章，指出即将按计划弃用视图设计器。

## <a name="february-2020"></a>2020 年 2 月

### <a name="agents"></a>代理

通过重新编写诊断扩展内容进行了多处更新。

- [Azure 监视代理概述](agents/agents-overview.md) - 重新构造了各个表格来更好地阐明每个代理的特有功能。
- [Azure 诊断扩展概述](agents/diagnostics-extension-overview.md) - 全部进行了重新编写。
- [在 Azure Monitor 中使用适用于 IIS 的 Blob 存储和适用于事件的表存储](agents/diagnostics-extension-logs.md) - 针对更新和澄清进行了常规重新编写。
- [安装并配置 Windows Azure 诊断扩展 (WAD)](agents/diagnostics-extension-windows-install.md) - 新文章。 
- [Windows 诊断扩展架构](agents/diagnostics-extension-schema-windows.md) - 已重新组织。
- [将数据从 Windows Azure 诊断扩展发送到 Azure 事件中心](agents/diagnostics-extension-stream-event-hubs.md) - 全部进行了重新编写和更新。
- [在 Azure 存储中存储和查看诊断数据](../cloud-services/diagnostics-extension-to-storage.md) - 全部进行了重新编写和更新。
- [适用于 Windows 的 Log Analytics 虚拟机扩展](../virtual-machines/extensions/oms-windows.md) - 更好地阐明了与 Log Analytics 代理的关系。
- [适用于 Linux 的 Azure Monitor 虚拟机扩展](../virtual-machines/extensions/oms-linux.md) - 更好地阐明了与 Log Analytics 代理的关系。

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights 中的连接字符串](app/sdk-connection-string.md) - 新文章。

### <a name="insights-and-solutions"></a>见解和解决方案

#### <a name="container-insights"></a>容器见解

- [将 Azure Active Directory 与 Azure Kubernetes 服务集成](../aks/azure-ad-integration-cli.md) - 添加了有关以下事项的说明：创建客户端应用程序来支持启用了 Kubernetes RBAC 的群集以支持容器见解。

#### <a name="vm-insights"></a>VM 见解

- [VM 见解（正式版）常见问题解答](vm/vminsights-ga-release-faq.md) - 对性能数据的存储方式相关内容进行了更改。

#### <a name="office-365"></a>Office 365

- [Azure 中的 Office 365 管理解决方案](insights/solution-office-365.md) - 更新了弃用日期。


### <a name="logs"></a>日志

- [优化 Azure Monitor 中的日志查询](logs/query-optimization.md) - 新文章。
- [管理 Azure Monitor 日志的使用情况和成本](logs/manage-cost-storage.md) - 改进了示例查询来帮助你更好地了解使用情况。

### <a name="metrics"></a>指标

- [可通过诊断设置导出的 Azure Monitor 平台指标](essentials/metrics-supported-export-diagnostic-settings.md) - 添加了有关针对 null 值和零值的行为更改的内容。

### <a name="visualizations"></a>可视化效果

增加了多篇新文章来提供视图设计器到工作簿转换指南。

- [Azure Monitor 视图设计器到工作簿转换指南](visualize/view-designer-conversion-overview.md) - 新文章。
- [Azure Monitor 视图设计器到工作簿转换选项](visualize/view-designer-conversion-options.md) - 新文章。
- [Azure Monitor 视图设计器到工作簿磁贴转换](visualize/view-designer-conversion-tiles.md) - 新文章。
- [Azure Monitor 视图设计器到工作簿转换摘要和访问](visualize/view-designer-conversion-access.md) - 新文章。
- [Azure Monitor 视图设计器到工作簿转换常见任务](visualize/view-designer-conversion-tasks.md) - 新文章。
- [Azure Monitor 视图设计器到工作簿转换示例](visualize/view-designer-conversion-examples.md) - 新文章。

## <a name="january-2020"></a>2020 年 1 月

### <a name="general"></a>常规

- [Azure Monitor 监视哪些内容？](monitor-reference.md) - 新文章。

### <a name="agents"></a>代理

- [使用 Azure Log Analytics 代理收集日志数据](agents/log-analytics-agent.md) - 更新了网络防火墙要求表。

### <a name="alerts"></a>警报

- [在 Azure 门户中创建和管理操作组](alerts/action-groups.md) - 删除了不再需要的 v2 功能设置。
- [使用资源管理器模板创建指标警报](alerts/alerts-metric-create-templates.md) - 添加了 *ignoreDataBefore* 参数的示例。  添加了有关多条件规则的约束。
- [使用 Log Analytics 警报 REST API](alerts/api-alerts.md) - 更正了 JSON 示例。

### <a name="application-insights"></a>Application Insights

- [Application Insights 和 Log Analytics 使用的 IP 地址](app/ip-addresses.md) - 更新了“可用性测试”部分，介绍如何使用 Azure 网络安全组添加入站端口规则以允许流量。
- [排查 Azure Application Insights Profiler 的问题](app/profiler-troubleshooting.md) - 更新了一般故障排除方法。
- [Azure Application Insights 中的遥测采样](app/sampling.md) - 根据客户的反馈更新并重新组织了该文章，以提高可读性。

### <a name="data-security"></a>数据安全

- [Azure Monitor 客户管理的密钥配置](logs/customer-managed-keys.md) - 新文章。

### <a name="insights-and-solutions"></a>见解和解决方案

#### <a name="container-insights"></a>容器见解

- [配置容器见解代理数据收集](containers/container-insights-agent-config.md) - 添加了有关在 Azure Red Hat OpenShift 上升级代理的详细信息，并添加了更多信息来区分代理的升级方法。
- [为容器见解创建性能警报](./containers/container-insights-log-alerts.md) - 修订了信息，并更新了使用工作区上下文警报针对工作区中存储的性能数据创建警报的步骤。
- [使用容器见解监视 Kubernetes](containers/container-insights-analyze.md) - 更新了概述文章，以及有关 Windows Kubernetes 群集支持的分析文章。
- [使用容器见解配置 Azure Red Hat OpenShift 群集](containers/container-insights-azure-redhat-setup.md) - 添加了有关在 Azure Red Hat OpenShift 上升级代理的详细信息，并添加了更多信息来区分代理的升级方法。
- [使用容器见解配置混合 Kubernetes 群集](containers/container-insights-hybrid-setup.md) - 更新了此文章，以反映 Kubelet cAdvisor 中添加的对安全端口 10250 的支持。
- [如何管理容器见解代理](containers/container-insights-manage-agent.md) - 更新了有关在 Azure Red Hat OpenShift 与其他类型的 Kubernetes 群集中进行指标擦除的行为和配置的详细信息。
- [配置容器见解 Prometheus 集成](containers/container-insights-prometheus-integration.md) - 更新了有关在 Azure Red Hat OpenShift 与其他类型的 Kubernetes 群集中进行指标擦除的行为和配置的详细信息。
- [如何更新指标的容器见解](containers/container-insights-update-metrics.md) - 更新了有关在 Azure Red Hat OpenShift 与其他类型的 Kubernetes 群集中进行指标擦除的行为和配置的详细信息。

#### <a name="vm-insights"></a>VM 见解

- [VM 见解（正式版）常见问题解答](vm/vminsights-ga-release-faq.md) - 添加了有关将工作区和代理升级到新版本的信息。

#### <a name="office-365"></a>Office 365

- [Azure 中的 Office 365 管理解决方案](insights/solution-office-365.md) - 添加了有关在 Azure Sentinel 中迁移到 Office 365 解决方案的详细信息和常见问题解答。 删除了“加入”部分。

### <a name="logs"></a>日志

- [管理 Azure Monitor 中的 Log Analytics 工作区](logs/manage-access.md) - 对“非操作”做了更新。
- [管理 Azure Monitor 日志的用量和成本](logs/manage-cost-storage.md) - 在“定价模型”部分添加了有关数据量计算的阐释。
- [使用 Azure 资源管理器模板创建和配置 Log Analytics 工作区](./logs/resource-manager-workspace.md) - 更新了模板，添加了新定价层。

### <a name="platform-logs"></a>平台日志

- [使用诊断设置收集 Azure 活动日志 - Azure Monitor](./essentials/activity-log.md) - 有关已更改的属性的附加信息。
- [导出 Azure 活动日志](./essentials/activity-log.md#legacy-collection-methods) - 根据 UI 变化更新了此文章。 

## <a name="december-2019"></a>2019 年 12 月

### <a name="agents"></a>代理

- [将 Linux 计算机连接到 Azure Monitor](agents/agent-linux.md) - 新文章。

### <a name="alerts"></a>警报

- [使用资源管理器模板创建指标警报](alerts/alerts-metric-create-templates.md) - 添加了自定义指标的示例。
- [在 Azure Monitor 中创建具有动态阈值的警报](alerts/alerts-dynamic-thresholds.md) - 添加了有关解释动态阈值图表的部分。
- [Azure 中的警报和通知监视概述](alerts/alerts-overview.md) - 更新了 Resource Graph 查询。
- [Azure Monitor 中的指标警报支持的资源](alerts/alerts-metric-near-real-time.md) - 对支持的指标和维度做了更新。
- [从旧的 Log Analytics 警报 API 切换到新的 Azure 警报 API](alerts/alerts-log-api-switch.md) - 添加了有关已修改的警报名称的说明。
- [了解指标警报在 Azure Monitor 中的工作原理](alerts/alerts-metric-overview.md) - 添加了支持用于大规模监视的资源类型。

### <a name="application-insights"></a>Application Insights

- [适用于辅助角色服务应用（非 HTTP 应用）的 Application Insights](app/worker-service.md) - 在 C# 代码中添加了默认的日志记录级别。 更新了包引用版本。
- [ApplicationInsights.config 参考 - Azure](app/configuration-with-applicationinsights-config.md) - 更新了示例代码。
- [使用 PowerShell 自动化 Azure Application Insights](app/powershell.md) - 对资源管理器模板做了更新。
- [创建新的 Azure Application Insights 资源](app/create-new-resource.md) - 添加了全局唯一名称的说明。
- [使用实时指标流进行诊断 - Azure Application Insights](app/live-stream.md) - 更新了 ASP.NET Core SDK 版本要求。
- [Application Insights 中的事件计数器](app/eventcounters.md) - 更新了 customMetrics 的类别和表。
- [在 Azure Application Insights 中浏览 Java 跟踪日志](app/java-trace-logs.md) - 添加了 Java 代理日志记录阈值的配置。
- [Application Insights 和 Log Analytics 使用的 IP 地址](app/ip-addresses.md) - 更新了实时指标流的 IP 地址。
- [监视 Azure 应用服务性能](app/azure-web-apps.md) - 添加了对 ASP.NET Core 3.0 的支持。 
- [使用 Azure Monitor（预览版）监视 Python 应用程序](app/opencensus-python.md) - 添加了有关将 OpenCensus Python 架构映射到 Azure Monitor 架构的阐释。
- [Azure Application Insights 发行说明](app/release-notes.md) - 添加了有关旧版本的说明。
- [Azure Application Insights 中的遥测通道](app/telemetry-channels.md) - 更新了在长时间断开连接时丢失数据的持续时间。
- [Azure Application Insights 中的遥测采样](app/sampling.md) - 更正了自定义 TelemetryInitializer 的代码片段。
- [在 Java Web 项目中排查 Application Insights 问题](app/java-troubleshoot.md) - 删除了有关 JDK 9 中不支持依赖项收集的陈述。

### <a name="insights-and-solutions"></a>见解和解决方案

- [容器见解常见问题解答](./faq.md) - 添加了有关“映像”和“名称”字段的问题。
- [Azure Monitor 中的 Azure SQL Analytics 解决方案](insights/azure-sql.md) - 更新了数据库等待托管实例支持。
- [配置容器见解代理数据收集](containers/container-insights-agent-config.md) - 添加了 enrich_container_logs 的设置。
- [使用容器见解配置混合 Kubernetes 群集](containers/container-insights-hybrid-setup.md) - 添加了故障排除部分。
- [使用 Azure Monitor 监视 Active Directory 复制状态](insights/ad-replication-status.md) - 更新了.NET Framework 先决条件。
- [Azure 中的网络性能监视器解决方案](insights/network-performance-monitor.md) - 添加了支持的区域。
- [使用 Azure Monitor 优化 Active Directory 环境](insights/ad-assessment.md) - 更新了 .NET Framework 先决条件。
- [使用 Azure Monitor 优化 SQL Server 环境](insights/sql-assessment.md) - 更新了 .NET Framework 先决条件。
- [使用 Azure Log Analytics 优化 System Center Operations Manager 环境](insights/scom-assessment.md) - 更新了 .NET Framework 先决条件。
- [Azure Log Analytics 中支持使用 IT 服务管理连接器建立的连接](alerts/itsmc-connections.md) - 将 New York 添加到了必备的客户端 ID 和客户端机密。

### <a name="logs"></a>日志

- [删除和恢复 Azure Log Analytics 工作区](logs/delete-workspace.md) - 添加了 PowerShell 方法。
- [设计 Azure Monitor 日志部署](logs/design-logs-deployment.md) - 提高了工作区的引入速率。

### <a name="metrics"></a>指标

- [可通过诊断设置导出的 Azure Monitor 平台指标](essentials/metrics-supported-export-diagnostic-settings.md) - 新文章。

### <a name="platform-logs"></a>平台日志

在根据用于通过诊断设置配置活动日志的新功能重新组织平台日志内容的过程中，我们已更新多篇文章。

- [将 Azure 资源日志存档到存储帐户](./essentials/resource-logs.md#send-to-azure-storage)
- [Azure 活动日志事件架构](essentials/activity-log-schema.md)
- [Azure Monitor 服务限制](service-limits.md)
- [收集和分析 Log Analytics 工作区中的 Azure 活动日志](./essentials/activity-log.md)
- [使用诊断设置（预览版）收集 Azure 活动日志 - Azure Monitor](./essentials/activity-log.md)
- [跨 Azure 租户将 Azure 活动日志收集到 Log Analytics 工作区中](./essentials/activity-log.md)
- [收集 Log Analytics 工作区中的 Azure 资源日志](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [使用资源管理器模板在 Azure 中创建诊断设置](./essentials/resource-manager-diagnostic-settings.md)
- [创建诊断设置以收集 Azure 中的日志和指标](essentials/diagnostic-settings.md)
- [导出 Azure 活动日志](./essentials/activity-log.md#legacy-collection-methods)
- [Azure 平台日志概述](essentials/platform-logs-overview.md)
- [将 Azure 监视数据流式传输到事件中心](essentials/stream-monitoring-data-event-hubs.md)
- [将 Azure 平台日志流式传输到事件中心](./essentials/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>快速入门和教程

- [在 Azure Monitor 中创建指标图表](essentials/tutorial-metrics-explorer.md) - 新文章。
- [从 Azure 资源收集资源日志并使用 Azure Monitor 进行分析](essentials/tutorial-resource-logs.md) - 新文章。
- [使用 Azure Monitor 监视 Azure 资源](essentials/quick-monitor-azure-resource.md) - 新文章。
   
## <a name="next-steps"></a>后续步骤

- 若要贡献 Azure Monitor 文档，请参阅[文档供稿人指南](/contribute/)。