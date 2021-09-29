---
title: 排查条件访问策略更改问题 - Azure Active Directory
description: 使用 Azure AD 审核日志诊断对条件访问策略进行的更改。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/09/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7683b64449a32013d7ac5e548ea9acaf85c51666
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561615"
---
# <a name="troubleshooting-conditional-access-policy-changes"></a>排查条件访问策略更改问题

在排查环境中发生条件访问策略更改的原因和方式时，Azure Active Directory (Azure AD) 审核日志是非常有价值的信息来源。

审核日志数据默认只会保留 30 天，此期限对于每家组织而言可能不够长。 组织可以通过将 Azure AD 中的诊断设置更改为以下选项，将数据存储更长的时间： 

- 将数据发送到 Log Analytics 工作区
- 将数据存档到存储帐户
- 将数据流式传输到事件中心
- 将数据发送到合作伙伴解决方案
 
可以在“Azure 门户” > “Azure Active Directory”、“诊断设置” > “编辑设置”中找到这些选项   。 如果你没有诊断设置，请按照[创建诊断设置以将平台日志和指标发送到不同的目标](../../azure-monitor/essentials/diagnostic-settings.md)一文中的说明创建诊断设置。 

## <a name="use-the-audit-log"></a>使用审核日志

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “审核日志” 。
1. 选择要查询的日期范围。
1. 选择“活动”，然后选择以下活动之一
   1. 添加条件访问策略 - 此活动列出新建的策略
   1. 更新条件访问策略 - 此活动列出已更改的策略
   1. 删除条件访问策略 - 此活动列出已删除的策略

:::image type="content" source="media/troubleshoot-policy-changes-audit-log/old-and-new-policy-properties.png" alt-text="显示条件访问策略的新旧 JSON 值的审核日志条目" lightbox="media/troubleshoot-policy-changes-audit-log/old-and-new-policy-properties.png":::

## <a name="use-log-analytics"></a>使用 Log Analytics

通过 Log Analytics，组织可以使用内置查询或自定义创建的 Kusto 查询来查询数据。有关详细信息，请参阅 [Azure Monitor 中的日志查询入门](../../azure-monitor/logs/get-started-queries.md)。

:::image type="content" source="media/troubleshoot-policy-changes-audit-log/log-analytics-new-old-value.png" alt-text="用于获取条件访问策略更新的 Log Analytics 查询，其中显示了新值和旧值位置" lightbox="media/troubleshoot-policy-changes-audit-log/log-analytics-new-old-value.png":::

启用后，在“Azure 门户” > “Azure AD” > “Log Analytics”中找到并访问 Log Analytics  。 条件访问管理员最感兴趣的表是 AuditLogs。

```kusto
AuditLogs 
| where OperationName == "Update conditional access policy"
```

在“TargetResources” > “modifiedProperties”下可以找到更改 。

## <a name="reading-the-values"></a>读取值

审核日志和 Log Analytics 中的旧值和新值采用 JSON 格式。 比较这两个值即可查看策略的更改。

旧策略示例：

```json
{
    "conditions": {
        "applications": {
            "applicationFilter": null,
            "excludeApplications": [
            ],
            "includeApplications": [
                "797f4846-ba00-4fd7-ba43-dac1f8f63013"
            ],
            "includeAuthenticationContextClassReferences": [
            ],
            "includeUserActions": [
            ]
        },
        "clientAppTypes": [
            "browser",
            "mobileAppsAndDesktopClients"
        ],
        "servicePrincipalRiskLevels": [
        ],
        "signInRiskLevels": [
        ],
        "userRiskLevels": [
        ],
        "users": {
            "excludeGroups": [
                "eedad040-3722-4bcb-bde5-bc7c857f4983"
            ],
            "excludeRoles": [
            ],
            "excludeUsers": [
            ],
            "includeGroups": [
            ],
            "includeRoles": [
            ],
            "includeUsers": [
                "All"
            ]
        }
    },
    "displayName": "Common Policy - Require MFA for Azure management",
    "grantControls": {
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [
        ],
        "operator": "OR",
        "termsOfUse": [
            "a0d3eb5b-6cbe-472b-a960-0baacbd02b51"
        ]
    },
    "id": "334e26e9-9622-4e0a-a424-102ed4b185b3",
    "modifiedDateTime": "2021-08-09T17:52:40.781994+00:00",
    "state": "enabled"
}

```

已更新的策略示例：

```json
{
    "conditions": {
        "applications": {
            "applicationFilter": null,
            "excludeApplications": [
            ],
            "includeApplications": [
                "797f4846-ba00-4fd7-ba43-dac1f8f63013"
            ],
            "includeAuthenticationContextClassReferences": [
            ],
            "includeUserActions": [
            ]
        },
        "clientAppTypes": [
            "browser",
            "mobileAppsAndDesktopClients"
        ],
        "servicePrincipalRiskLevels": [
        ],
        "signInRiskLevels": [
        ],
        "userRiskLevels": [
        ],
        "users": {
            "excludeGroups": [
                "eedad040-3722-4bcb-bde5-bc7c857f4983"
            ],
            "excludeRoles": [
            ],
            "excludeUsers": [
            ],
            "includeGroups": [
            ],
            "includeRoles": [
            ],
            "includeUsers": [
                "All"
            ]
        }
    },
    "displayName": "Common Policy - Require MFA for Azure management",
    "grantControls": {
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [
        ],
        "operator": "OR",
        "termsOfUse": [
        ]
    },
    "id": "334e26e9-9622-4e0a-a424-102ed4b185b3",
    "modifiedDateTime": "2021-08-09T17:52:54.9739405+00:00",
    "state": "enabled"
}

``` 

在以上示例中，已更新的策略未在权限授予控制机制中包括使用条款。

### <a name="restoring-conditional-access-policies"></a>还原条件访问策略

有关使用 Microsoft Graph API 以编程方式更新条件访问策略的详细信息，请参阅[条件访问：编程访问](howto-conditional-access-apis.md)一文。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure Active Directory 监视？](../reports-monitoring/overview-monitoring.md)
- [安装和使用用于 Azure Active Directory 的日志分析视图](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [条件访问：以编程方式访问](howto-conditional-access-apis.md)
