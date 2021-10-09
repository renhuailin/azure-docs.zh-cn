---
title: 在 PIM 中激活 Azure AD 角色 - Azure Active Directory | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中激活 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/10/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 677de5e28ed4dcae5081f362804ef18e0971c322
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639691"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>在 PIM 中激活我的 Azure AD 角色

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 简化了企业管理以特权身份访问 Azure AD 和其他 Microsoft 联机服务（如 Microsoft 365 或 Microsoft Intune）中的资源的方式。  

如果你符合管理角色的资格，则必须在需要执行特权操作时激活角色分配 。 例如，如果你偶尔管理 Microsoft 365 功能，则组织的特权角色管理员可能不会让你成为永久性的全局管理员，因为该角色也影响其他服务。 但是，他们会让你符合 Azure AD 角色（例如 Exchange Online 管理员）的资格。 可以在需要权限时，请求暂时分配该角色，并将在预定的时段内拥有管理员控制权。

本文面向需要在 Privileged Identity Management 中激活其 Azure AD 角色的管理员。

## <a name="activate-a-role"></a>激活角色

需要充当某个 Azure AD 角色时，可在 Privileged Identity Management 中通过打开“我的角色”请求激活。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。 有关如何将 Privileged Identity Management 磁贴添加到仪表板的信息，请参阅[开始使用 Privileged Identity Management](pim-getting-started.md)。

1. 选择“我的角色”，然后选择“Azure AD 角色”，查看符合条件的 Azure AD 角色的列表。 

    ![显示可以激活的角色的“我的角色”页](./media/pim-how-to-activate-role/my-roles.png)

1. 在“Azure AD 角色”列表中，找到要激活的角色。

    ![Azure AD 角色 - 我的合格角色列表](./media/pim-how-to-activate-role/activate-link.png)

1. 选择“激活”打开“激活”窗格。

    ![Azure AD 角色 - 激活页面包含持续时间和范围](./media/pim-how-to-activate-role/activate-page.png)

1. 选择“需要进行附加验证”，并按照说明提供任何其他安全验证。 在每个会话中只需执行身份验证一次。

    ![用于提供安全验证（例如 PIN 码）的屏幕](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 多重身份验证后，选择“激活，然后继续”。

    ![在激活角色之前使用 MFA 验证身份](./media/pim-how-to-activate-role/activate-role-mfa-banner.png)

1. 如果要指定缩小的范围，请选择“范围”以打开筛选器窗格。 在筛选器窗格中，可以指定需要访问的 Azure AD 资源。 最佳做法是请求访问所需的最少资源。

1. 根据需要指定自定义的激活开始时间。 Azure AD 角色将在选定时间后激活。

1. 在“原因”框中，输入该激活请求的原因。

1. 选择“激活”。

    如果[角色需要审批](pim-resource-roles-approval-workflow.md)才能激活，则浏览器右上角会显示一条通知，告知你请求正在等待审批。

    ![“激活请求正在等待审批”通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="activate-a-role-using-graph-api"></a>使用 Graph API 激活角色

### <a name="get-all-eligible-roles-that-you-can-activate"></a>获取可激活的所有符合条件的角色

当用户通过组成员身份获取其角色资格时，此 Graph 请求不会返回其资格。

#### <a name="http-request"></a>HTTP 请求

````HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleEligibilityScheduleRequests/filterByCurrentUser(on='principal')  
````

#### <a name="http-response"></a>HTTP 响应

为了节省空间，我们将只显示一个角色的响应，但会列出你可以激活的所有符合条件的角色分配。

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#Collection(unifiedRoleEligibilityScheduleRequest)", 
    "value": [ 
        { 
            "@odata.type": "#microsoft.graph.unifiedRoleEligibilityScheduleRequest", 
            "id": "<request-ID-GUID>", 
            "status": "Provisioned", 
            "createdDateTime": "2021-07-15T19:39:53.33Z", 
            "completedDateTime": "2021-07-15T19:39:53.383Z", 
            "approvalId": null, 
            "customData": null, 
            "action": "AdminAssign", 
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
                "startDateTime": "2021-07-15T19:39:53.3846704Z", 
                "recurrence": null, 
                "expiration": { 
                    "type": "noExpiration", 
                    "endDateTime": null, 
                    "duration": null 
                } 
            }, 
            "ticketInfo": { 
                "ticketNumber": null, 
                "ticketSystem": null 
            } 
        },
} 
````

### <a name="activate-a-role-assignment-with-justification"></a>激活具有理由的角色分配

#### <a name="http-request"></a>HTTP 请求

````HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests 

{ 
    "action": "SelfActivate", 
    "justification": "adssadasasd", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "directoryScopeId": "/", 
    "principalId": "<principal-ID-GUID>" 
} 
````

#### <a name="http-response"></a>HTTP 响应

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentScheduleRequests/$entity", 
    "id": "f1ccef03-8750-40e0-b488-5aa2f02e2e55", 
    "status": "PendingApprovalProvisioning", 
    "createdDateTime": "2021-07-15T19:51:07.1870599Z", 
    "completedDateTime": "2021-07-15T19:51:17.3903028Z", 
    "approvalId": "<approval-ID-GUID>", 
    "customData": null, 
    "action": "SelfActivate", 
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
        "startDateTime": null, 
        "recurrence": null, 
        "expiration": { 
            "type": "afterDuration", 
            "endDateTime": null, 
            "duration": "PT5H30M" 
        } 
    }, 
    "ticketInfo": { 
        "ticketNumber": null, 
        "ticketSystem": null 
    } 
} 
````

## <a name="view-the-status-of-activation-requests"></a>查看激活请求的状态

可以查看等待激活的请求的状态。

1. 打开 Azure AD Privileged Identity Management。

1. 选择“我的请求”，查看你的 Azure AD 角色和 Azure 资源角色请求列表。

    ![显示挂起的请求的“我的请求 - Azure AD”页](./media/pim-how-to-activate-role/my-requests-page.png)

1. 向右滚动以查看“请求状态”列。

## <a name="cancel-a-pending-request-for-new-version"></a>取消新版本的挂起请求

如果不需要激活需要审批的角色，随时可以取消等待中的请求。

1. 打开 Azure AD Privileged Identity Management。

1. 选择“我的请求”。

1. 针对想要取消的角色，选择“取消”链接。

    选择“取消”会取消该请求。 若要再次激活该角色，必须提交新的激活请求。

   ![突出显示“取消”操作的“我的请求”列表](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot-portal-delay"></a>排查门户延迟问题

### <a name="permissions-arent-granted-after-activating-a-role"></a>激活角色后，未授予权限

在 Privileged Identity Management 中激活角色时，激活可能不会立即传播到需要特权角色的所有门户。 有时，即使更改已传播，门户中的 Web 缓存也可能会导致更改不能立即生效。 如果激活延迟，请从尝试执行该操作的门户注销，然后重新登录。 在 Azure 门户中，PIM 会自动注销并重新登录。

## <a name="next-steps"></a>后续步骤

- [查看 Azure AD 角色的审核历史记录](pim-how-to-use-audit-log.md)
