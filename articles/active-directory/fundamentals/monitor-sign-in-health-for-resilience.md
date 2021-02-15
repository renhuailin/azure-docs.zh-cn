---
title: 在 Azure Active Directory 中监视应用程序登录运行状况以实现复原
description: 创建查询和通知以监视应用程序的登录运行状况。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/10/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40bfa27dba905cb2e9a363c7739f0a43e7c2afdf
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101134"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>监视应用程序登录运行状况以实现复原

若要提高基础结构的复原能力，请为关键应用程序设置应用程序登录运行状况的监视，以便在发生影响的事件时收到警报。 为帮助您完成这项工作，您可以基于登录运行状况工作簿配置警报。 

管理员可以通过此工作簿监视租户中的应用程序的身份验证请求。 它提供以下主要功能：

* 将工作簿配置为监视所有或单个具有近乎实时数据的应用。

* 配置警报，以便在身份验证模式发生更改时通知你，以便调查和采取措施。

* 比较一段时间内的趋势，例如 week 周，这是工作簿的默认设置。

> [!NOTE]
> 若要查看所有可用工作簿以及使用它们的先决条件，请参阅 [如何使用报表 Azure Monitor 工作簿](../reports-monitoring/howto-use-azure-monitor-workbooks.md)。

在影响事件期间，可能会发生两种情况：

* 由于用户无法登录，应用程序的登录数可能会删除急剧。

* 登录失败的次数可能会增加。 

本文逐步讲解如何设置登录运行状况工作簿，以监视用户的登录中断。

## <a name="prerequisites"></a>先决条件 

* Azure AD 租户。

* Azure AD 租户具有全局管理员或安全管理员角色的用户。

* Azure 订阅中用于将日志发送到 Azure Monitor 日志的 Log Analytics 工作区。 

   * 了解如何 [创建 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

* 与 Azure Monitor 日志集成 Azure AD 日志

   * 了解如何将 [Azure AD 登录日志与 Azure Monitor 流相集成。](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

 

## <a name="configure-the-app-sign-in-health-workbook"></a>配置应用登录运行状况工作簿 

若要访问工作簿，请打开 **Azure 门户**，选择 " **Azure Active Directory**"，然后选择 " **工作簿**"。

你将看到使用中的工作簿、条件性访问和故障排除。 "应用登录运行状况" 工作簿将显示在 "使用情况" 部分中。

使用工作簿后，该工作簿可能出现在 "最近修改的工作簿" 部分。

![在 Azure 门户中显示工作簿库的屏幕截图。](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


应用登录运行状况工作簿使你能够直观显示你的登录所发生的情况。 

默认情况下，该工作簿提供两个图形。 以下关系图比较了你的应用程序在 () 的情况下发生的情况，而不是一周之前的同一时间段。 蓝线是最新的，橙色线条是上一周。

![显示登录运行状况图形的屏幕截图。](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**第一个关系图是 (成功用户) 数的每小时使用情况**。 将当前的成功用户数与典型的使用时间进行比较，可帮助你发现可能需要调查的可能的使用情况。 成功使用率的丢弃方法有助于检测失败率不能太高的性能和利用率问题。 例如，如果用户无法访问你的应用程序来尝试登录，则不会出现任何故障，只会出现故障。 可在以下部分中找到此数据的示例查询。

第二个关系图为每小时失败率。 失败率峰值可能表明身份验证机制存在问题。 仅当用户可以尝试进行身份验证时，才能测量失败率。 如果用户无法获取进行尝试的权限，则故障不会显示。

可以配置警报，以便在使用情况或失败率超过指定阈值时通知特定组。 可在以下部分中找到此数据的示例查询。

 ## <a name="configure-the-query-and-alerts"></a>配置查询和警报

您可以在 Azure Monitor 中创建警报规则，并可以定期自动运行已保存的查询或自定义的日志搜索。

按照以下说明，根据图形中反映的查询创建电子邮件警报。 下面的示例脚本将在以下情况下发送电子邮件通知

* 从两天前的同一小时起，成功的使用会下降90%，如前一部分中的每小时使用情况图中所示。 

* 在两天前，失败率增加了90%，如前一部分中的每小时失败率关系图所示。 

 若要配置基础查询并设置警报，请完成以下步骤。 你将使用示例查询作为你的配置的基础。 此部分的末尾显示了查询结构的说明。

有关如何使用 Azure Monitor 创建、查看和管理日志警报的详细信息，请参阅 [管理日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)。

 
1. 在工作簿中，选择 " **编辑**"，然后选择位于关系图右侧上方的 " **查询" 图标** 。   

   [![显示编辑工作簿的屏幕截图。](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   此时将打开查询日志。

  [![显示查询日志的屏幕截图。](./media/monitor-sign-in-health-for-resilience/query-log.png)](/media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. 为新的 Kusto 查询复制以下示例脚本之一。

**用于删除使用情况的 Kusto 查询**

```Kusto

let thisWeek = SigninLogs

| where TimeGenerated > ago(1h)

| project TimeGenerated, AppDisplayName, UserPrincipalName

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize users = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

let lastWeek = SigninLogs

| where TimeGenerated between((ago(1h) - totimespan(2d))..(now() - totimespan(2d)))

| project TimeGenerated, AppDisplayName, UserPrincipalName

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize usersPriorWeek = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

thisWeek

| join

(

 lastWeek

)

on rn

| project TimeGenerated, users, usersPriorWeek, difference = abs(users - usersPriorWeek), max = max_of(users, usersPriorWeek)

| where (difference * 2.0) / max > 0.9

```

 

**Kusto 查询以提高失败率**


```kusto

let thisWeek = SigninLogs

| where TimeGenerated > ago(1 h)

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h)

| project TimeGenerated, failureRate = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rn = row_number();

let lastWeek = SigninLogs

| where TimeGenerated between((ago(1 h) - totimespan(2d))..(ago(1h) - totimespan(2d)))

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h)

| project TimeGenerated, failureRatePriorWeek = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rn = row_number();

thisWeek

| join (lastWeek) on rn

| project TimeGenerated, failureRate, failureRatePriorWeek

| where abs(failureRate – failureRatePriorWeek) > **THRESHOLD VALUE**

```

3. 将查询粘贴到窗口中，然后选择 " **运行**"。 确保查看下图中显示的已完成消息，并显示该消息。

   [![显示运行查询结果的屏幕截图。](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. 突出显示该查询，然后选择 "+ **新建警报规则**"。 
 
   [![显示 "新建警报规则" 屏幕的屏幕截图。](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. 配置警报条件。 在 "条件" 部分中， **只要平均自定义日志搜索大于逻辑定义的计数**，请选择该链接。 在 "配置信号逻辑" 窗格中，滚动到 "警报逻辑"

   [![显示 "配置警报" 屏幕的屏幕截图。](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **阈值**：0。 此值将对任何结果发出警报。

   * **评估期 (分钟)**：60。 此值将在一小时内查看

   * **频率 (分钟)**：60。 此值将计算周期设置为上一小时的每小时一次。

   * 选择“完成”。

6. 在 " **操作** " 部分中，配置以下设置：  

    [![显示 "创建警报规则" 页的屏幕截图。](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * 在 " **操作**" 下，选择 " **选择操作组**"，并添加要向其通知警报的组。

   * 在 " **自定义操作** " 下，选择 **电子邮件警报**。

   * 添加 **主题行**。

7. 在 " **警报规则详细信息**" 下，配置以下设置：

   * 添加描述性名称和说明。

   * 选择要向其添加警报的 **资源组** 。

   * 选择警报的默认 **严重性** 。

   * 选择 " **创建时启用警报规则** " （如果希望立即显示），或选择 " **取消警报**"。

8. 选择“创建警报规则”。

9. 选择 " **保存**"，输入查询的名称， **另存为包含警报类别的查询**。 然后再次选择 " **保存** "。  

   [![显示 "保存查询" 按钮的屏幕截图。](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)



### <a name="refine-your-queries-and-alerts"></a>优化查询和警报
修改查询和警报以获得最大效率。

* 请确保测试警报。

* 修改警报敏感度和频率，以便获取重要通知。 如果管理员获取的内容太多并遗漏了重要的内容，则管理员可能会 desensitized 警报。 

* 确保将警报来自其管理员电子邮件客户端的电子邮件添加到 "允许发件人" 列表。 否则，你可能会因电子邮件客户端上的垃圾邮件筛选器而错过通知。 

* Azure Monitor 中的警报查询只能包含过去48小时的结果。 [这是设计的当前限制](https://github.com/MicrosoftDocs/azure-docs/issues/22637)。

## <a name="create-processes-to-manage-alerts"></a>创建用于管理警报的进程

设置查询和警报后，创建用于管理警报的业务流程。

* 谁将监视工作簿和何时？
* 生成警报时，谁会进行调查？

* 通信需求是什么？ 谁将创建通信，谁将接收这些通信？

* 如果发生中断，需要触发哪些业务流程？

## <a name="next-steps"></a>后续步骤

[了解有关工作簿的详细信息](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)

 

 

 
