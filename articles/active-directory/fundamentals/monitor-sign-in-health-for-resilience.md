---
title: 在 Azure Active Directory 中监视应用程序登录运行状况以提高复原能力
description: 创建查询和通知以监视应用程序的登录运行状况。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7c32940d5fc40249257d1d0bb38ef67c12dc2ef
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029670"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>监视应用程序登录运行状况以提高复原能力

若要提高基础结构的复原能力，可以为关键应用程序设置应用程序登录运行状况监视，以便在发生具有影响力的事件时收到警报。 为了帮助你完成这项工作，你可以根据登录运行状况工作簿配置警报。 

管理员可以通过此工作簿监视对租户中应用程序的身份验证请求。 它提供以下关键功能：

* 将工作簿配置为使用近实时数据监视所有应用或单个应用。

* 配置警报以在身份验证模式发生更改时通知你，以便你可以展开调查并采取措施。

* 比较一段时间内的趋势，例如周同比，这是工作簿的默认设置。

> [!NOTE]
> 若要查看所有可用工作簿以及使用它们的先决条件，请参阅[如何将 Azure Monitor 工作簿用于报表](../reports-monitoring/howto-use-azure-monitor-workbooks.md)。

发生具有影响力的事件时，可能会发生两件事：

* 由于用户无法登录，应用程序的登录次数可能会急剧下降。

* 登录失败的次数可能会增加。 

本文逐步讲解如何设置登录运行状况工作簿，以监视用户登录的中断。

## <a name="prerequisites"></a>先决条件

* Azure AD 租户。

* 对 Azure AD 租户具有全局管理员或安全管理员角色的用户。

* Azure 订阅中的 Log Analytics 工作区，用于将日志发送到 Azure Monitor 日志。 

   * 了解如何[创建 Log Analytics 工作区](../../azure-monitor/logs/quick-create-workspace.md)

* 与 Azure Monitor 日志集成的 Azure AD 日志

   * 了解如何[将 Azure AD 登录日志与 Azure Monitor 流集成。](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="configure-the-app-sign-in-health-workbook"></a>配置“应用登录运行状况”工作簿 

若要访问工作簿，请打开 Azure 门户，选择“Azure Active Directory”，然后选择“工作簿”。

“使用情况”、“条件访问”和“故障排除”下会显示工作簿。 “应用登录运行状况”工作簿出现在“使用情况”部分中。

只要使用工作簿，它就有可能出现在“最近修改的工作簿”部分中。

![显示 Azure 门户中的工作簿库的屏幕截图。](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


借助“应用登录运行状况”工作簿，可以直观显示登录情况。 

默认情况下，该工作簿提供两个图。 这些图比较了应用当前发生的情况以及一周前的同期情况。 蓝线表示当前时间，橙线表示上一周。

![显示登录运行状况图的屏幕截图。](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**第一张图显示每小时使用量（成功用户数）** 。 通过将当前的成功用户数与典型使用时间段进行比较，可以帮助你发现可能需要调查的使用量下降的情况。 成功使用率下降可以帮助检测失败率检测不到的性能和利用率问题。 例如，如果用户无法访问你的应用程序以尝试登录，那么就不会出现失败登录，而只会导致使用量下降。 在以下部分中可以找到对此数据的示例查询。

**第二张图显示每小时失败率**。 失败率峰值可能表明身份验证机制存在问题。 仅当用户可以尝试进行身份验证时，才能测量失败率。 如果用户无法获取进行尝试的权限，则不会显示失败。

你可以配置警报，以在使用量或失败率超过指定阈值时通知特定组。 在以下部分中可以找到对此数据的示例查询。

## <a name="configure-the-query-and-alerts"></a>配置查询和警报

你可以在 Azure Monitor 中创建警报规则，并且定期自动运行保存的查询或自定义日志搜索。

按照以下说明，根据图中反映的查询创建电子邮件警报。 以下示例脚本将在以下情况下发送电子邮件通知：

* 如前一部分中的每小时使用量图所示，与两天前的同一小时相比，成功使用量下降了 90%。 

* 如前一部分中的每小时失败率图所示，与两天前的同一小时相比，失败率增加了 90%。 

 若要配置基础查询并设置警报，请完成以下步骤。 你将使用示例查询作为配置的基础。 本部分的末尾对查询结构进行了说明。

有关如何使用 Azure Monitor 创建、查看和管理日志警报的详细信息，请参阅[管理日志警报](../../azure-monitor/alerts/alerts-log.md)。

1. 在工作簿中，选择“编辑”，然后选择图形右侧上方的查询图标。   

   [![显示编辑工作簿的屏幕截图。](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   查询日志打开。

   [![显示查询日志的屏幕截图。](./media/monitor-sign-in-health-for-resilience/query-log.png)](./media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. 为新的 Kusto 查询复制其中一个示例脚本。  
   * [针对失败率上升的 Kusto 查询](#kusto-query-for-increase-in-failure-rate)
   * [针对使用量下降的 Kusto 查询](#kusto-query-for-drop-in-usage)

3. 将查询粘贴到窗口中，然后选择“运行”。 确保看到下图所示的“已完成”消息，以及该消息下方的结果。

   [![显示运行查询结果的屏幕截图。](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. 突出显示查询，然后选择“+ 新建警报规则”。 
 
   [![显示新建警报规则屏幕的屏幕截图。](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. 配置警报条件。 ‎在“条件”部分中，选择“每当平均自定义日志搜索数大于逻辑定义的计数时”链接。 在配置信号逻辑窗格中，滚动到“警报逻辑”

   [![显示配置警报屏幕的屏幕截图。](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **阈值**：0。 该值将针对任何结果发出警告。

   * **评估期(分钟)** ：2880。 该值将评估期设置为一小时

   * **频率(分钟)** ：60。 该值将前一小时的评估频率设置为每小时一次。

   * 选择“完成”  。

6. 在“操作”部分中，配置以下设置：  

    [![显示“创建警报规则”页面的屏幕截图。](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * 在“操作”下，选择“选择操作组”，然后添加要向其发送警报的组。

   * 在“自定义操作”下，选择“通过电子邮件发送警报”。

   * 添加主题行。

7. 在“警报规则详细信息”下，配置以下设置：

   * 添加描述性名称和说明。

   * 选择要向其添加警报的资源组。

   * 选择警报的默认严重性。

   * 如果希望警报规则立即生效，请选择“在创建后启用警报规则”；否则选择“抑制警报”。

8. 选择“创建警报规则”。

9. 选择“保存”，输入查询的名称，另存为“查询”，类别为“警报”。 然后，再次选择“保存”。  

   [![显示保存查询按钮的屏幕截图。](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)

### <a name="refine-your-queries-and-alerts"></a>优化查询和警报

修改查询和警报，使其发挥最大效率。

* 务必测试警报。

* 修改警报敏感度和频率，以便收到重要通知。 如果管理员收到过多警报，他们可能会变得对警报不敏感，从而错过重要警报。 

* 确保将管理员电子邮件客户端中包含警报的电子邮件添加到“允许的发件人”列表。 否则，你可能会因电子邮件客户端上的垃圾邮件筛选器而错过通知。 

* Azure Monitor 中的警报查询只能包含过去 48 小时内的结果。 [这是目前设计上的限制](https://github.com/MicrosoftDocs/azure-docs/issues/22637)。

## <a name="sample-scripts"></a>示例脚本

### <a name="kusto-query-for-increase-in-failure-rate"></a>针对失败率上升的 Kusto 查询

   可根据需要调整底部的比率，表示上一小时的流量与昨天同期相比的变化百分比。 0.5 表示同期流量差异为 50%。

```kusto

let today = SigninLogs
| where TimeGenerated > ago(1h) // Query failure rate in the last hour 
| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")
// Optionally filter by a specific application
//| where AppDisplayName == **APP NAME**
| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate
| project TimeGenerated, failureRate = (failure * 1.0) / ((failure + success) * 1.0)
| sort by TimeGenerated desc
| serialize rowNumber = row_number();
let yesterday = SigninLogs
| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Query failure rate at the same time yesterday
| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")
// Optionally filter by a specific application
//| where AppDisplayName == **APP NAME**
| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate at same time yesterday
| project TimeGenerated, failureRateYesterday = (failure * 1.0) / ((failure + success) * 1.0)
| sort by TimeGenerated desc
| serialize rowNumber = row_number();
today
| join (yesterday) on rowNumber // join data from same time today and yesterday
| project TimeGenerated, failureRate, failureRateYesterday
// Set threshold to be the percent difference in failure rate in the last hour as compared to the same time yesterday
// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.
| extend day = dayofweek(now())
| where day != time(6.00:00:00) // exclude Sat
| where day != time(0.00:00:00) // exclude Sun
| where day != time(1.00:00:00) // exclude Mon
| where abs(failureRate - failureRateYesterday) > 0.5

```

### <a name="kusto-query-for-drop-in-usage"></a>针对使用量下降的 Kusto 查询

在下面的查询中，我们比较了上一个小时与昨天同期相比的流量情况。
我们不包括周六、周日和周一，因为预计在这些日子里，前一天同一时间的流量会有很大的变化。 

可根据需要调整底部的比率，表示上一小时的流量与昨天同期相比的变化百分比。 0.5 表示同期流量差异为 50%。

*你应调整这些值，以适应你的业务操作模型*。

```Kusto
 let today = SigninLogs // Query traffic in the last hour
| where TimeGenerated > ago(1h)
| project TimeGenerated, AppDisplayName, UserPrincipalName
// Optionally filter by AppDisplayName to scope query to a single application
//| where AppDisplayName contains "Office 365 Exchange Online"
| summarize users = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr) // Count distinct users in the last hour
| sort by TimeGenerated desc
| serialize rn = row_number();
let yesterday = SigninLogs // Query traffic at the same hour yesterday
| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Count distinct users in the same hour yesterday
| project TimeGenerated, AppDisplayName, UserPrincipalName
// Optionally filter by AppDisplayName to scope query to a single application
//| where AppDisplayName contains "Office 365 Exchange Online"
| summarize usersYesterday = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)
| sort by TimeGenerated desc
| serialize rn = row_number();
today
| join // Join data from today and yesterday together
(
yesterday
)
on rn
// Calculate the difference in number of users in the last hour compared to the same time yesterday
| project TimeGenerated, users, usersYesterday, difference = abs(users - usersYesterday), max = max_of(users, usersYesterday)
| extend ratio = (difference * 1.0) / max // Ratio is the percent difference in traffic in the last hour as compared to the same time yesterday
// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.
| extend day = dayofweek(now())
| where day != time(6.00:00:00) // exclude Sat
| where day != time(0.00:00:00) // exclude Sun
| where day != time(1.00:00:00) // exclude Mon
| where ratio > 0.7 // Threshold percent difference in sign-in traffic as compared to same hour yesterday

```

## <a name="create-processes-to-manage-alerts"></a>创建警报管理流程

设置查询和警报后，即可创建业务流程来管理警报。

* 谁来监视工作簿？何时？

* 生成警报时，谁进行调查？

* 通信需求是什么？ 谁来创建通信，谁来接收通信？

* 如果发生中断，需要触发哪些业务流程？

## <a name="next-steps"></a>后续步骤

[详细了解工作簿](../reports-monitoring/howto-use-azure-monitor-workbooks.md)
