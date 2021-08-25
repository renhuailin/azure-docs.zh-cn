---
title: 使用 Azure Monitor 日志分析活动日志 | Microsoft Docs
description: 了解如何使用 Azure Monitor 日志分析 Azure Active Directory 活动日志
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/19/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 453c2815f2f4243a7101ba5ce8dd1076d088133c
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122514990"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>使用 Azure Monitor 日志分析 Azure AD 活动日志

[将 Azure AD 活动日志与 Azure Monitor 日志集成](howto-integrate-activity-logs-with-log-analytics.md)之后，可以使用 Azure Monitor 日志的强大功能来深入了解自己的环境。 此外，可以安装[用于 Azure AD 活动日志的 Log Analytics 视图](howto-install-use-log-analytics-views.md)，访问有关环境中审核和登录事件的预建报表。

本文介绍如何在 Log Analytics 工作区中分析 Azure AD 活动日志。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>先决条件 

若要按照文中内容操作，需要：

* 在 Azure 订阅中创建 Log Analytics 工作区。 了解如何[创建 Log Analytics 工作区](../../azure-monitor/logs/quick-create-workspace.md)。
* 首先，完成[将 Azure AD 活动日志路由到 Log Analytics 工作区](howto-integrate-activity-logs-with-log-analytics.md)的相关步骤。
*  [访问](../../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions) Log Analytics 工作区
* Azure Active Directory 中的以下角色（如果要通过 Azure Active Directory 门户访问 Log Analytics）
    - 安全管理员
    - 安全读取者
    - 报表读取者
    - 全局管理员
    
## <a name="navigate-to-the-log-analytics-workspace"></a>导航到 Log Analytics 工作区

1. 登录 [Azure 门户](https://portal.azure.com)。 

2. 选择“Azure Active Directory”，然后从“监视”部分选择“日志”以打开 Log Analytics 工作区。 随即工作区打开，并含有默认查询。

    ![默认查询](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>查看 Azure AD 活动日志的架构

日志将推送到工作区中的“AuditLogs”和“SigninLogs”表。 查看这些表的架构：

1. 在上一节中的默认查询视图中，选择“架构”并展开工作区。 

2. 展开“日志管理”部分，然后展开“AuditLogs”或“SigninLogs”以查看日志架构  。

## <a name="query-the-azure-ad-activity-logs"></a>查询 Azure AD 活动日志

现在工作区中已有日志，可以对其运行查询。 例如，若要获取最近一周使用最多的应用程序，将默认查询替换为以下查询并选择“运行”

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

若要获取最近一周发生最多的审核事件，可使用以下查询：

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Azure AD 活动日志数据警报

还可以针对查询设置警报。 例如，若要配置为当最近一周使用的应用程序超过 10 个时发出警报：

1. 在工作区中，选择“设置警报”以打开“创建规则”页面。

    ![设置警报](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. 选择警报中创建的默认“警报条件”，将默认指标中的“阈值”更新为 10。

    ![警报条件](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. 为警报输入名称和描述，然后选择严重级别。 示例中可将其设置为“信息性”。

4. 选择“操作组”，信号发生时将向其发出警报。 可以选择通过电子邮件或短信来通知团队，或使用 webhook、Azure functions 或逻辑应用来自动执行此操作。 详细了解如何[在 Azure 门户中创建和管理警报组](../../azure-monitor/alerts/action-groups.md)。

5. 配置警报后，选择“创建警报”来启用它。 

## <a name="use-pre-built-workbooks-for-azure-ad-activity-logs"></a>将预建的工作簿用于 Azure AD 活动日志

这些工作簿提供与涉及审核、登录和预配事件的常见方案相关的多个报表。 还可使用上一节中所述步骤，针对报表中提供的任何数据设置警报。

* 预配分析：此[工作簿](../app-provisioning/application-provisioning-log-analytics.md)显示与审核预配活动相关的报表，例如，预配的新用户数和预配失败情况、更新的用户数和更新失败情况以及取消预配的用户数和相应失败情况。    
* **登录事件**：此工作簿显示与监视登录活动最相关的报表，例如，分别按应用程序、用户、设备统计的登录情况，以及随时间推移跟踪登录数的摘要视图。
* 条件访问见解：使用条件访问见解和报告[工作簿](../conditional-access/howto-conditional-access-insights-reporting.md)，可以了解条件访问策略在一定时间段内在组织中的影响。 

## <a name="next-steps"></a>后续步骤

* [Azure Monitor 日志查询入门](../../azure-monitor/logs/get-started-queries.md)
* [在 Azure 门户中创建和管理器警报组](../../azure-monitor/alerts/action-groups.md)
* [安装和使用用于 Azure Active Directory 的日志分析视图](howto-install-use-log-analytics-views.md)