---
title: 了解预配如何与 Azure Active Directory 中的 Azure Monitor 日志集成。
description: 了解预配如何与 Azure Active Directory 中的 Azure Monitor 日志集成。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f656f55b0c74103053f110b8c66645d353db9fbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561562"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>了解预配如何与 Azure Monitor 日志集成

预配与 Azure Monitor 日志和 Log Analytics 集成。 借助 Azure 监视，可以创建工作簿（也称为仪表板）、将预配日志存储 30 天以上，以及创建自定义查询和警报等。 本文介绍预配日志与 Azure Monitor 日志的集成方式。 若要详细了解预配日志的一般工作原理，请参阅[预配日志](../reports-monitoring/concept-provisioning-logs.md)。

## <a name="enabling-provisioning-logs"></a>启用预配日志

你应该已熟悉 Azure 监视和 Log Analytics。 如果不熟悉，请直接了解相关信息，然后再返回了解应用程序预配日志。 若要详细了解 Azure 监视，请参阅 [Azure Monitor 概述](../../azure-monitor/overview.md)。 若要详细了解 Azure Monitor 日志和 Log Analytics，请参阅 [Azure Monitor 中的日志查询概述](../../azure-monitor/logs/log-query-overview.md)。

配置 Azure 监视后，可以为应用程序预配启用日志。 选项位于“诊断设置”页上。

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="访问诊断设置" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="启用应用程序预配日志" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> 如果最近刚预配了工作区，则可能需要一些时间才能向其发送日志。 如果收到错误消息，指出订阅未注册为使用 microsoft.insights，请在几分钟后再返回查看。
 
## <a name="understanding-the-data"></a>了解数据
预配发送给日志查看器的基础数据流几乎完全相同。 Azure Monitor 日志获取与 Azure 门户 UI 和 Azure API 几乎相同的流。 下表列出的日志字段仅有少数差异。 若要详细了解这些字段，请参阅[列出 provisioningObjectSummary](/graph/api/provisioningobjectsummary-list?preserve-view=true&tabs=http&view=graph-rest-beta)。

|Azure Monitor 日志   |Azure 门户 UI   |Azure API |
|----------|-----------|------------|
|errorDescription |reason |resultDescription |
|status |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Azure Monitor 工作簿

Azure Monitor 工作簿为数据分析提供了一个灵活的画布。 它们还提供在 Azure 门户中创建丰富的可视化报表的功能。 若要了解详细信息，请参阅 [Azure Monitor 工作簿概述](../../azure-monitor/visualize/workbooks-overview.md)。

应用程序预配附带一组预构建的工作簿。 可以在“工作簿”页上找到它们。 若要查看数据，需确保已填充所有筛选器（timeRange、jobID、appName）。 还需要确保已预配应用，否则日志中不会包含任何数据。

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="应用程序预配工作簿" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="应用程序预配仪表板" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>自定义查询

可以在 Azure 仪表板上创建自定义查询并显示数据。 若要了解操作方法，请参阅[创建和共享 Log Analytics 数据的仪表板](../../azure-monitor/logs/get-started-queries.md)。 此外，请务必查看 [Azure Monitor 中的日志查询概述](../../azure-monitor/logs/log-query-overview.md)。

下面是一些用于开始使用应用程序预配的示例。

根据用户在源系统中的 ID 查询其日志：
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

按 ErrorCode 汇总计数：
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

按操作汇总每天的事件计数：
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

采用 100 个事件和项目关键属性：
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>自定义警报

通过 Azure Monitor，可以配置自定义警报，以便可以收到有关与预配相关的关键事件的通知。 例如，你可能希望收到有关故障高峰的警报。 或者收到有关禁用高峰或删除高峰的警报。 可能希望收到的警报的另一个示例是缺少预配，这表明出现了问题。

若要了解有关警报的详细信息，请参阅[借助 Azure Monitor 警报对事件作出响应](../../azure-monitor/alerts/tutorial-response.md)。

出现故障高峰时发出警报。 将 jobID 替换为应用程序的 jobID。

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="出现故障高峰时发出警报。" lightbox="media/application-provisioning-log-analytics/alert1.png":::

可能存在导致预配服务停止运行的问题。 使用以下警报来检测在给定时间间隔内没有预配事件的情况。

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="可能存在导致预配服务停止运行的问题。" lightbox="media/application-provisioning-log-analytics/alert2.png":::

出现禁用或删除高峰时发出警报。

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="出现禁用或删除高峰时发出警报。" lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>社区参与

我们正在为应用程序预配查询和仪表板采用一种开源的、基于社区的方法。 如果你构建了你认为其他人会觉得有用的查询、警报或工作簿，请确保将其发布到 [AzureMonitorCommunity GitHub 存储库](https://github.com/microsoft/AzureMonitorCommunity)。 然后向我们发送带有链接的电子邮件。 我们会在审阅后将其发布到服务，以便其他人也能从中受益。 可以通过 provisioningfeedback@microsoft.com 联系我们。

## <a name="next-steps"></a>后续步骤

- [Log Analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Azure Monitor 日志查询入门](../../azure-monitor/logs/get-started-queries.md)
- [在 Azure 门户中创建和管理器警报组](../../azure-monitor/alerts/action-groups.md)
- [安装和使用用于 Azure Active Directory 的日志分析视图](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [预配日志 API](/graph/api/resources/provisioningobjectsummary?preserve-view=true&view=graph-rest-beta)