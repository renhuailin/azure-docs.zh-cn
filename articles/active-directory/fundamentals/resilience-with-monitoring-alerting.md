---
title: 使用 Azure AD B2C 进行监视和分析的复原能力 |Microsoft Docs
description: 通过使用 Azure AD B2C 进行监视和分析提高恢复能力
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 766fd80ae5f7450c8e45d10afa4612a788a8d5fc
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602150"
---
# <a name="resilience-through-monitoring-and-analytics"></a>通过监视和分析进行恢复

监视最大程度地提高应用程序和服务的可用性和性能。 它提供了一个全面的解决方案，用于从基础结构和应用程序收集、分析和操作遥测数据。 当发现你的服务或应用程序出现问题时，警报会主动通知你。 它们允许您在服务的最终用户通知他们之前确定和解决问题。 [Azure AD Log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) 可帮助你分析、搜索审核日志和登录日志，并生成自定义视图。

## <a name="monitor-and-get-notified-through-alerts"></a>监视警报并获得通知

监视系统和基础结构对于确保服务的总体运行状况至关重要。 它从业务指标的定义开始，如新用户到达、最终用户的身份验证率和转换。 将此类指示器配置为监视。 如果要规划即将发生的冲击，因为促销或假期流量，请专门针对业务指标的事件和相应的基准来修改估计。 事件发生后，回退到前面的基准。

同样，若要检测故障或性能中断，请设置一个良好的基线，然后定义警报，这是一种不理想的做法，可以及时应对新兴问题。

![图像显示监视和分析组件](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>如何实现监视和警报

- **监视**：使用 [Azure Monitor](https://docs.microsoft.com/azure/active-directory-b2c/azure-monitor) 持续监视 (SLO) 的关键服务级别目标的运行状况，并在发生关键更改时收到通知。 首先将 Azure AD B2C 策略或应用程序标识为业务的关键组件，需要监视其运行状况来维护 SLO。 确定与 Slo 一致的关键指标。
例如，跟踪以下指标，因为其中的突然中断会导致业务损失。

  - **请求总数**：发送给 Azure AD B2C 策略的请求总数 "n"。

  - **成功率 (% )**：成功请求数/请求总数。

  访问[application insights](https://docs.microsoft.com/azure/active-directory-b2c/analytics-with-application-insights)中的[关键指标](https://docs.microsoft.com/azure/active-directory-b2c/view-audit-logs)，其中存储了 Azure AD B2C 基于策略的日志、[审核日志](https://docs.microsoft.com/azure/active-directory-b2c/analytics-with-application-insights)和登录日志。  

   - **可视化**：使用 Log analytics 构建面板直观地监视关键指示器。

   - **当前期间**：创建时态图表，以显示当前时间段（例如当前周）内请求总数和成功率 (% ) 更改。

   - **上一阶段**：创建时态图表，以显示过去一段时间内的请求总数和成功率 (% ) 的更改，例如上星期。

- **警报**：使用 log analytics 会定义当关键指示器发生突然变化时触发的 [警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) 。 这些更改可能会对 Slo 产生负面影响。 警报使用各种形式的通知方法，包括电子邮件、短信和 webhook。 首先定义一个条件，该条件充当阈值，将触发警报。 例如：
  - 发出请求总数时发出警报：在请求总数突然下降时触发警报。 例如，与前一期间相比，请求总数存在25% 时，引发警报。  
  - 针对有效丢弃率的警报 (% ) ：当所选策略的成功率大幅下降时触发警报。
  - 收到警报后，请使用 Azure AD B2C [Log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)、 [Application Insights](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-with-application-insights#:~:text=Setup%20Application%20Insights%201%20Go%20to%20the%20Azure,left-menu%2C%20and%20click%20on%20it.%20More%20items...%20)和 [VS Code 扩展](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) 对问题进行故障排除。 解决问题并部署已更新的应用程序或策略后，它会继续监视关键指示器，直到它们返回到正常范围。

- **服务警报**：使用 [Azure AD B2C 服务级别警报](https://docs.microsoft.com/azure/service-health/service-health-overview) 获取有关服务问题、计划内维护、运行状况建议和安全建议的通知。

- **报告**： [通过使用 log analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)，生成的报告可帮助您了解用户见解、技术挑战和增长机会。
  - **运行状况仪表板**： [使用 Azure 仪表板功能创建自定义仪表](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards) 板，它支持使用 Log Analytics 查询添加图表。 例如，确定用于发出请求的设备的成功和失败登录、失败原因和遥测的模式。
  - **放弃 Azure AD B2C 旅程**：使用 [工作簿](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys) 跟踪用户启动了登录或注册旅程但从未完成的放弃 Azure AD B2C 旅程列表。 其中提供了有关策略 ID 的详细信息，以及用户在放弃旅程之前所采取的步骤细目。
  - **Azure AD B2C 监视工作簿**：使用 [监视工作簿](https://github.com/azure-ad-b2c/siem)，其中包括 Azure AD B2C 仪表板、多重身份验证 (MFA) 操作、条件性访问报表和按 correlationId 搜索日志，以便更好地深入了解 Azure AD B2C 环境的运行状况。
  
## <a name="next-steps"></a>后续步骤

- [面向 Azure AD B2C 开发人员的复原资源](resilience-b2c.md)
  - [复原最终用户体验](resilient-end-user-experience.md)
  - [具有外部进程的弹性接口](resilient-external-processes.md)
  - [通过开发人员的最佳做法实现复原](resilience-b2c-developer-best-practices.md)
- [在身份验证基础结构中构建复原能力](resilience-in-infrastructure.md)
- [在应用程序中提高身份验证和授权的复原能力](resilience-app-development-overview.md)
