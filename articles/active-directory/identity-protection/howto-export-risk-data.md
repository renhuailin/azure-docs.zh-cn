---
title: 导出和使用 Azure Active Directory 标识保护数据
description: 了解如何在 Azure Active Directory 标识保护中使用长期数据进行调查
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 07/30/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07ea682d65bde9748a4cd55ed066eb8f31eb418a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784083"
---
# <a name="how-to-export-risk-data"></a>操作方式：导出风险数据

Azure AD 在定义的时间段内存储报表和安全信号。 涉及到风险信息时，时间可能不够长。

| 报表/信号 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| --- | --- | --- | --- |
| 审核日志 | 7 天 | 30 天 | 30 天 |
| 登录 | 7 天 | 30 天 | 30 天 |
| Azure AD MFA 使用情况 | 30 天 | 30 天 | 30 天 |
| 有风险的登录 | 7 天 | 30 天 | 30 天 |

组织可以通过更改 Azure AD 中的诊断设置来选择将数据存储更长时间，以将 RiskyUsers 和 UserRiskEvents 数据发送到 Log Analytics 工作区、将数据存档到存储帐户、将数据流式传输到事件中心，或将数据发送到合作伙伴解决方案 。 可通过“Azure 门户” > “Azure Active Directory”、“诊断设置” > “编辑设置”找到这些选项   。 如果没有诊断设置，请按照[创建诊断设置以将平台日志和指标发送到不同目标](../../azure-monitor/essentials/diagnostic-settings.md)一文中的说明进行操作，以创建诊断设置。

>[!NOTE]
>RiskyUsers 和 UserRiskEvents 的诊断设置目前为公共预览版。

[![Azure AD 中显示现有配置的诊断设置屏幕](./media/howto-export-risk-data/change-diagnostic-setting-in-portal.png)](./media/howto-export-risk-data/change-diagnostic-setting-in-portal.png#lightbox)

## <a name="log-analytics"></a>Log Analytics

通过 Log Analytics，组织可以使用内置查询或自定义创建的 Kusto 查询来查询数据。有关详细信息，请参阅 [Azure Monitor 中的日志查询入门](../../azure-monitor/logs/get-started-queries.md)。

启用后，即可在“Azure 门户” > “Azure AD” > “Log Analytics”中找到并访问 Log Analytics  。 标识保护管理员最感兴趣的表是 AADRiskyUsers 和 AADUserRiskEvents 。

- AADRiskyUsers - 在标识保护中提供“风险用户”报告等数据。
- AADUserRiskEvents - 在标识保护中提供“风险检测”报告等数据。

[![显示了对 AADUserRiskEvents 表的查询的 Log Analytics 视图，其中显示了前 5 个事件](./media/howto-export-risk-data/log-analytics-view-query-user-risk-events.png)](./media/howto-export-risk-data/log-analytics-view-query-user-risk-events.png#lightbox)

在上图中，运行了以下查询以显示最近触发的 5 个风险检测。 

```kusto
AADUserRiskEvents
| take 5
```

另一种选择是查询 AADRiskyUsers 表以查看所有风险用户。

```kusto
AADRiskyUsers
```

> [!NOTE]
> Log Analytics 仅对流式传输的数据具有可见性。 不会显示启用从 Azure AD 发送事件之前的事件。

## <a name="storage-account"></a>存储帐户

通过将日志路由到 Azure 存储帐户，可以将日志保留更长一段时间（与默认保留期相比）。 有关详细信息，请参阅[教程：将 Azure AD 日志存档到 Azure 存储帐户](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)。

## <a name="azure-event-hubs"></a>Azure 事件中心

Azure 事件中心可以查看来自源（如 Azure AD 标识保护）的传入数据，并提供实时分析和关联。 有关详细信息，请参阅文章[教程：将 Azure Active Directory 日志流式传输到 Azure 事件中心](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)

## <a name="other-options"></a>其他选项

组织可选择[将 Azure AD 数据连接到 Azure Sentinel](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection) 进行进一步处理。

组织可使用 [Microsoft Graph API 以编程方式与风险事件交互](howto-identity-protection-graph-api.md)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure Active Directory 监视？](../reports-monitoring/overview-monitoring.md)
- [安装和使用用于 Azure Active Directory 的日志分析视图](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [从 Azure Active Directory (Azure AD) 标识保护中连接数据](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection)
- [Azure Active Directory 标识保护和 Microsoft Graph PowerShell SDK](howto-identity-protection-graph-api.md)
- [教程：将 Azure Active Directory 日志流式传输到 Azure 事件中心](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)