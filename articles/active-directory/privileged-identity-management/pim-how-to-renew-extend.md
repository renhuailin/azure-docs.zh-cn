---
title: 在 PIM 中续订 Azure AD 角色分配 - Azure Active Directory | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中延期或续订 Azure Active Directory 角色分配。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 08/06/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b061c98b0e961d60d8edd9b0275c70ce3b005044
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647929"
---
# <a name="extend-or-renew-azure-ad-role-assignments-in-privileged-identity-management"></a>在 Privileged Identity Management 中延期或续订 Azure AD 角色分配

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 提供控件来管理 Azure AD 角色的访问权限和分配生命周期。 管理员可以使用开始和结束日期时间属性分配角色。 当分配结束时间即将到来时，Privileged Identity Management 会向受影响的用户或组发送电子邮件通知。 此外，它还向 Azure AD 管理员发送电子邮件通知，确保能够保持相应的访问权限。 即使访问权限未延期，分配也可以续订，并在长达 30 天内以过期状态保持可见。

## <a name="who-can-extend-and-renew"></a>谁可以延期和续订？

只有全局管理员或特权角色管理员才能延期或续订 Azure AD 角色分配。 受影响的用户或组可以请求延期即将过期的角色，以及请求续订已过期的角色。

## <a name="when-are-notifications-sent"></a>何时发送通知？

对于在 14 天内过期的角色，Privileged Identity Management 会在过期前的一天向管理员和受影响用户或组发送电子邮件通知。 分配正式过期后，它会发送另一封电子邮件。

当即将过期或已过期的角色的用户或组请求延期或续订时，管理员会收到通知。 当某个管理员批准或拒绝了请求时，将会向所有其他管理员通知此决定。 然后，发出请求的用户或组会收到决定结果。

## <a name="extend-role-assignments"></a>延期角色分配

以下步骤概述了请求、解决或管理角色分配延期或续订的过程。

### <a name="self-extend-expiring-assignments"></a>自我延期即将过期的分配

分配给某个角色的用户或组可以直接从“我的角色”页上的“符合条件”或“活动”选项卡将即将过期的角色分配延期，也可以在“Azure AD 角色”下或 Privileged Identity Management 门户的顶层“我的角色”页执行此操作。  用户或组可以请求将那些在接下来的 14 天过期的符合条件且处于活动状态的角色分配延期。

![Azure AD 角色 -“我的角色”页，其中列出了带有“操作”列的符合条件的角色](./media/pim-how-to-renew-extend/pim-extend-link-in-portal.png)

如果分配结束日期和时间在 14 天内，则“延期”按钮在用户界面中将变为活动的链接。 以下示例假设当前日期为 3 月 27 日。

![带有“激活”或“延期”链接的“操作”列](./media/pim-how-to-renew-extend/pim-extend-within-fourteen.png)

若要请求延期此角色分配，请选择“延期”打开请求窗体。

![包含“原因”框的“延期角色分配”窗格](./media/pim-how-to-renew-extend/extend-role-assignment-request.png)

输入延期请求的原因，然后选择“延期”。

>[!NOTE]
>我们建议详细说明为何有必要延期，以及要同意延期多久（如果知道此信息）。

管理员在收到电子邮件通知后会查看延期请求。 如果已提交延期请求，门户中会显示一条 Azure 通知。

![说明已存在挂起的角色分配扩展的通知](./media/pim-how-to-renew-extend/extend-notification.png)

转到“挂起的请求”页查看请求状态或取消请求。

![Azure AD 角色 -“待定的请求”页，其中列出了任何待处理的请求和一个“取消”链接](./media/pim-how-to-renew-extend/pending-requests.png)

### <a name="admin-approved-extension"></a>管理员批准的延期

当用户或组提交延期角色分配的请求时，管理员会收到一封电子邮件通知，其中包含原始分配的详细信息，以及请求的原因。 此通知还包含一个直接链接，让管理员批准或拒绝该请求。

除了使用电子邮件中的链接以外，管理员还可以通过转到 Privileged Identity Management 管理门户，并从左窗格中选择“审批请求”来批准或拒绝请求。

![Azure AD 角色 -“审批请求”页，其中列出了请求以及用于批准或拒绝的链接](./media/pim-how-to-renew-extend/extend-admin-approve-list.png)

当管理员选择“批准”或“拒绝”时，将显示请求的详细信息，同时会显示一个字段，让管理员提供审核日志的业务理由。 

![使用请求者原因、分配类型、开始时间、结束时间和原因审批角色分配请求](./media/pim-how-to-renew-extend/extend-admin-approve-form.png)

批准角色分配延期请求时，管理员可以选择新的开始日期、结束日期和分配类型。 如果管理员希望提供受限的访问权限来完成特定的任务（例如，一天的访问权限），则可能需要更改分配类型。 在此示例中，管理员可将分配从“符合条件”更改为“活动”。 这意味着，他们可为请求者提供访问权限，而无需让请求者激活。

### <a name="admin-initiated-extension"></a>管理员发起的延期

如果分配到某个角色的用户未请求角色分配延期，管理员可以代表该用户延期分配。 角色分配的管理延期不需要审批，但在完成角色延期后，系统会向其他所有管理员发送通知。

若要将角色分配延期，请浏览到 Privileged Identity Management 中的角色或分配视图。 找到需要延期的分配。 在操作列中选择“延期”。

![Azure AD 角色 -“分配”页，其中列出了符合条件的角色以及用于延期的链接](./media/pim-how-to-renew-extend/extend-admin-extend.png)

## <a name="extend-role-assignments-using-graph-api"></a>使用图形 API 扩展角色分配

使用图形 API 扩展活动分配。

#### <a name="http-request"></a>HTTP 请求

````HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests 
 
{ 
    "action": "AdminExtend", 
    "justification": "abcde", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "directoryScopeId": "/", 
    `"principalId": "<principal-ID-GUID>", 
    "scheduleInfo": { 
        "startDateTime": "2021-07-15T19:15:08.941Z", 
        "expiration": { 
            "type": "AfterDuration", 
            "duration": "PT3H" 
        } 
    } 
}
````

#### <a name="http-response"></a>HTTP 响应

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentScheduleRequests/$entity", 
    "id": "<assignment-ID-GUID>", 
    "status": "Provisioned", 
    "createdDateTime": "2021-07-15T20:26:44.865248Z", 
    "completedDateTime": "2021-07-15T20:26:47.9434068Z", 
    "approvalId": null, 
    "customData": null, 
    "action": "AdminExtend", 
    "principalId": "<principal-ID-GUID>", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "directoryScopeId": "/", 
    "appScopeId": null, 
    "isValidationOnly": false, 
    "targetScheduleId": "<schedule-ID-GUID>", 
    "justification": "test", 
    "createdBy": { 
        "application": null, 
        "device": null, 
        "user": { 
            "displayName": null, 
            "id": "<user-ID-GUID>" 
        } 
    }, 
    "scheduleInfo": { 
        "startDateTime": "2021-07-15T20:26:47.9434068Z", 
        "recurrence": null, 
        "expiration": { 
            "type": "afterDuration", 
            "endDateTime": null, 
            "duration": "PT3H" 
        } 
    }, 
    "ticketInfo": { 
        "ticketNumber": null, 
        "ticketSystem": null 
    } 
} 
````

## <a name="renew-role-assignments"></a>续订角色分配

续订已过期角色分配的过程虽然在概念上与请求延期的过程类似，但两者确实存在差异。 分配和管理员可根据需要，使用以下步骤来续订对已过期角色的访问权限。

### <a name="self-renew"></a>自我续订

不再能够访问资源的用户可以访问最长 30 天的已过期分配历史记录。 为此，他们可以浏览到左窗格中的“我的角色”，并在“Azure AD 角色”部分选择“已过期的角色”选项卡。 

![“我的角色”页 -“已过期的角色”选项卡](./media/pim-how-to-renew-extend/renew-from-myroles.png)

显示的角色列表默认为“符合条件的角色”。 选择“符合条件的”或“活动的”已分配角色。

若要请求续订列表中的任何角色分配，请选择“续订”操作。 然后提供请求原因。 建议提供持续时间和任何附加的上下文或业务理由，以便管理员决定是批准还是拒绝。

![“续订角色分配”窗格，其中显示“原因”框](./media/pim-how-to-renew-extend/renew-request-form.png)

提交请求后，系统会通知管理员收到了一个续订角色分配的待处理请求。

### <a name="admin-approves"></a>管理员审批

Azure AD 管理员可以通过电子邮件通知中的链接，或者通过在 Azure 门户中访问 Privileged Identity Management 并在 PIM 中选择“审批请求”，来访问续订请求。

![Azure AD 角色 -“审批请求”页，其中列出了请求以及用于批准或拒绝的链接](./media/pim-how-to-renew-extend/extend-admin-approve-list.png)

当管理员选择“批准”或“拒绝”时，将显示请求的详细信息，同时会显示一个字段，让管理员提供审核日志的业务理由。

![使用请求者原因、分配类型、开始时间、结束时间和原因审批角色分配请求](./media/pim-how-to-renew-extend/extend-admin-approve-form.png)

批准续订角色分配的请求时，管理员必须输入新的开始日期、结束日期以及分配类型。

### <a name="admin-renew"></a>管理员续订

他们也可以在 Azure AD 角色的“已过期角色”选项卡中续订已过期的角色分配。 若要查看所有已过期角色分配的列表，请在“分配”屏幕上选择“已过期角色”。 

![Azure AD 角色 -“分配”页，其中列出了已过期的角色以及用于续订的链接](./media/pim-how-to-renew-extend/renew-from-assignments-pane.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中批准或拒绝 Azure AD 角色的请求](azure-ad-pim-approval-workflow.md)
- [在 Privileged Identity Management 中配置 Azure AD 角色设置](pim-how-to-change-default-settings.md)
