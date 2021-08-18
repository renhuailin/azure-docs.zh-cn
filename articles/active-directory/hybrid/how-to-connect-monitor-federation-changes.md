---
title: 在 Azure AD 中监视联合身份验证配置的更改 | Microsoft Docs
description: 本文介绍如何使用 Azure AD 监视联合身份验证配置的更改。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 7ffc5980eed3268f299ee0073cfa810c17878e53
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229702"
---
# <a name="monitor-changes-to-federation-configuration-in-your-azure-ad"></a>在 Azure AD 中监视联合身份验证配置的更改

当你使用 Azure AD 对本地环境进行联合身份验证时，需要在本地标识提供者与 Azure AD 之间建立信任关系。 

由于这种已建立的信任，Azure AD 将支持本地标识提供者在身份验证后发布的安全令牌，以授予对受 Azure AD 保护的资源的访问权限。 

因此，必须密切监视此信任（联合身份验证配置），并捕获任何异常或可疑活动。

若要监视信任关系，建议设置警报，以在联合身份验证配置更改时收到通知。


## <a name="set-up-alerts-to-monitor-the-trust-relationship"></a>设置警报以监视信任关系

按照以下步骤设置警报以监视信任关系：

1. [配置 Azure AD 审核日志](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)以流向 Azure Log Analytics 工作区。 
2. 创建基于 Azure AD 日志查询触发的[警报规则](../../azure-monitor/alerts/alerts-log.md)。 
3. [将操作组添加](../../azure-monitor/alerts/action-groups.md)到警报规则，以在符合警报条件时收到通知。  

配置环境后，数据流如下所示： 

 1. Azure AD 日志根据租户中的活动进行填充。  
 2. 日志信息将流向 Azure Log Analytics 工作区。  
 3. Azure Monitor 中的后台作业根据上述配置步骤 (2) 中的警报规则配置执行日志查询。  
    ```
     AuditLogs 
     |  extend TargetResource = parse_json(TargetResources) 
     | where ActivityDisplayName contains "Set federation settings on domain" or ActivityDisplayName contains "Set domain authentication" 
     | project TimeGenerated, SourceSystem, TargetResource[0].displayName, AADTenantId, OperationName, InitiatedBy, Result, ActivityDisplayName, ActivityDateTime, Type 
     ```
     
 4. 如果查询结果与警报逻辑相符（即结果数大于或等于 1），则操作组将启动。 假设它已启动，则步骤 5 中流继续。  
 5. 通知将发送到配置警报时选择的操作组。

 > [!NOTE]
 >  除了设置警报外，建议定期查看 Azure AD 租户中配置的域，并删除所有过时、无法识别或可疑的域。 




## <a name="next-steps"></a>后续步骤

- [将 Azure AD 日志与 Azure Monitor 日志集成](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
- [使用 Azure Monitor 创建、查看和管理日志警报](../../azure-monitor/alerts/alerts-log.md)
- [使用 Azure AD Connect 管理 AD FS 与 Azure AD 之间的信任关系](how-to-connect-azure-ad-trust.md)
- [有关保护 Active Directory 联合身份验证服务的最佳做法](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)