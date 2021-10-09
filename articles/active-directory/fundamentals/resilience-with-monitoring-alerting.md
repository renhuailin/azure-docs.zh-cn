---
title: 使用 Azure AD B2C 通过监视和分析实现复原能力 | Microsoft Docs
description: 使用 Azure AD B2C 通过监视和分析实现复原能力
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
ms.openlocfilehash: ccb3876799c0a1473f06c1838536df17eab03398
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754378"
---
# <a name="resilience-through-monitoring-and-analytics"></a>通过监视和分析实现复原能力

监视可以最大程度地提高应用程序和服务的可用性和性能。 它提供了一个全面的解决方案，用于从你的基础结构和应用程序收集、分析和处理遥测数据。 当发现你的服务或应用程序出现问题时，警报会主动通知你。 有了警报，你就可以在服务的最终用户注意到问题之前确定并解决这些问题。 [Azure AD Log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) 可帮助你分析、搜索审核日志和登录日志，以及构建自定义视图。

观看此视频，了解如何使用 Azure Monitor 在 Azure AD B2C 中设置监视和报告。

>[!Video https://www.youtube.com/embed/Mu9GQy-CbXI]

## <a name="monitor-and-get-notified-through-alerts"></a>通过警报进行监视并获得通知

监视你的系统和基础结构对于确保服务的总体运行状况至关重要。 它从业务指标的定义开始，这些指标包括新用户到达、最终用户的身份验证率和转换率，等等。 请配置要监视的此类指标。 如果为即将到来的流量激增（由于促销或假日流量）进行规划，请针对该活动修改你的估计值，并针对业务指标修改相应的基准。 在活动结束后，恢复为以前的基准。

同样，若要检测故障或性能波动，请设置一个良好的基线，然后定义警报，这是一种迅速应对新出现的问题的不可或缺的做法。

![图像显示了监视和分析组件](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>如何实施监视和警报

- **监视**：使用 [Azure Monitor](../../active-directory-b2c/azure-monitor.md) 依据关键服务级别目标 (SLO) 持续监视运行状况，并在发生重大更改时收到通知。 首先确定 Azure AD B2C 策略或某个应用程序是你的业务的关键组件（需要监视其运行状况以维护 SLO）。 确定符合 SLO 的关键指标。
例如，请跟踪以下指标，因为其突然下降会导致业务损失。

  - 请求总数：发送到 Azure AD B2C 策略的请求总数 n。

  - 成功率(%)：成功的请求数/请求总数。

  访问 [application insights](../../active-directory-b2c/analytics-with-application-insights.md) 中的[关键指标](../../active-directory-b2c/view-audit-logs.md)，其中存储了 Azure AD B2C 基于策略的日志、[审核日志](../../active-directory-b2c/analytics-with-application-insights.md)和登录日志。  

   - 可视化效果：使用 Log analytics 构建面板来直观地监视关键指标。

   - 当前期间：创建时态图表以显示请求总数和成功率 (%) 在当前期间（例如本周）的变化。

   - 以前的期间：创建时态图表以显示请求总数和成功率 (%) 在以前的某个期间（例如上周）的变化以供参考。

- 警报：使用 Log Analytics 定义当关键指标突然发生变化时会触发的[警报](../../azure-monitor/alerts/alerts-log.md)。 这些变化可能会对 SLO 产生负面影响。 警报使用各种形式的通知方法，包括电子邮件、短信和 Webhook。 首先定义一个充当阈值的条件，警报会根据该阈值触发。 例如：
  - 与请求总数的突然下降相对应的警报：当请求总数突然下降时触发警报。 例如，当请求总数与上一期间相比下降了 25％ 时，引发警报。  
  - 与成功率 (%) 的显著下降相对应的警报：当所选策略的成功率大幅下降时触发警报。
  - 收到警报后，请使用 [Log Analytics](../reports-monitoring/howto-install-use-log-analytics-views.md)、[Application Insights](../../active-directory-b2c/troubleshoot-with-application-insights.md) 和适用于 Azure AD B2C 的 [VS Code 扩展](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)来排查问题。 解决问题并部署更新的应用程序或策略后，它会继续监视关键指标，直到它们恢复到正常范围。

- 服务警报：使用 [Azure AD B2C 服务级别警报](../../service-health/service-health-overview.md)获取有关服务问题、计划内维护、运行状况公告和安全公告的通知。

- 报告：[通过使用 Log Analytics](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)，生成报告来帮助你了解用户见解、技术挑战和增长机会。
  - 运行状况仪表板：[使用 Azure 仪表板功能创建自定义仪表板](../../azure-monitor/app/tutorial-app-dashboards.md)，该功能支持添加使用 Log Analytics 查询的图表。 例如，确定成功的和失败的登录的模式、失败原因以及用于发出请求的设备的相关遥测数据。
  - 放弃 Azure AD B2C 旅程：使用[工作簿](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys)跟踪已放弃的 Azure AD B2C 旅程的列表。在已放弃的旅程中，用户已启动登录或注册旅程，但从未完成它。 其中提供有关策略 ID 的详细信息，以及用户在放弃旅程之前所采取的步骤的细节。
  - Azure AD B2C 监视工作簿：使用[监视工作簿](https://github.com/azure-ad-b2c/siem)（包括 Azure AD B2C 仪表板、多重身份验证 (MFA) 操作、条件访问报告以及按 correlationId 分类的搜索日志），以便更好地深入了解 Azure AD B2C 环境的运行状况。
  
## <a name="next-steps"></a>后续步骤

- [面向 Azure AD B2C 开发人员的复原能力资源](resilience-b2c.md)
  - [可复原的最终用户体验](resilient-end-user-experience.md)
  - [与外部进程建立可复原接口](resilient-external-processes.md)
  - [通过开发人员最佳做法实现复原能力](resilience-b2c-developer-best-practices.md)
- [在身份验证基础结构中构建复原能力](resilience-in-infrastructure.md)
- [提高应用程序中身份验证和授权的复原能力](resilience-app-development-overview.md)
