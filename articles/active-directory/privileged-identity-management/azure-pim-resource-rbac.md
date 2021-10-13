---
title: 在 Privileged Identity Management (PIM) 中查看 Azure 资源角色的审核报告 - Azure AD | Microsoft Docs
description: 在 Azure AD Privileged Identity Management (PIM) 中查看 Azure 资源角色的活动和审核历史记录。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36f78327459c8bf6efc3a1cad9d4c5b306e4ba1f
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668307"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中查看 Azure 资源角色的活动和审核历史记录

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，可以查看组织中的 Azure 资源角色的活动、激活和审核历史记录。 这些资源包括订阅、资源组甚至虚拟机。 如果 Azure 门户中的任何资源利用了 Azure 的基于角色的访问控制 (RBAC) 功能，则可以利用 Privileged Identity Management 中的安全和生命周期管理功能。 如果审核数据的保留时间需要长于默认保留期，可以使用 Azure Monitor 将其路由到 Azure 存储帐户。 有关详细信息，请参阅[将 Azure AD 日志存档到 Azure 存储帐户](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)。

> [!NOTE]
> 如果你的组织已将管理功能外包给使用 [Azure Lighthouse](../../lighthouse/overview.md) 的服务提供商，则此处将不会显示该服务提供商授权的角色分配。

## <a name="view-activity-and-activations"></a>查看活动和激活

若要查看特定用户在各种资源中执行的操作，可以查看与给定激活时段关联的 Azure 资源活动。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure 资源” 。

1. 选择要查看其活动和激活情况的资源。

1. 选择“角色”或“成员”。

1. 选择用户。

    你将在 Azure 资源中按日期查看用户操作的摘要。 其中还显示了同一时间段内的最近角色激活。

    ![包含资源活动摘要和角色激活的用户详细信息](media/azure-pim-resource-rbac/rbac-user-details.png)

1. 单击某个特定的角色激活可查看详细信息，以及在该用户处于活动状态期间发生的相应 Azure 资源活动。

    [![所选角色激活以及活动详细信息](media/azure-pim-resource-rbac/export-membership.png "所选角色激活以及活动详细信息")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>导出具有子级的角色分配

你可能具有合规性要求，必须向审核者提供角色分配的完整列表。 可以使用 Privileged Identity Management 查询特定资源上的角色分配，这包括针对所有子资源的角色分配。 以前，管理员很难获取某个订阅的角色分配完整列表，他们必须导出每个特定资源的角色分配。 使用 Privileged Identity Management，可以查询某个订阅中所有处于活动状态和符合条件的角色分配，包括针对所有资源组和资源的角色分配。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure 资源” 。

1. 单击要为其导出角色分配情况的资源，例如订阅。

1. 选择“**成员**”。

1. 单击“导出”以打开“导出成员身份”窗格。

    [![用于导出所有成员的“导出成员身份”窗格](media/azure-pim-resource-rbac/export-membership.png "用于导出所有成员的导出成员身份页")](media/azure-pim-resource-rbac/export-membership.png)

1. 单击“导出所有成员”以在 CSV 文件中导出所有角色分配。

    ![CSV 文件中导出的角色分配在 Excel 中显示](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history&quot;></a>查看资源审核历史记录

资源审核提供资源的所有角色活动的视图。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure 资源” 。

1. 选择要查看其审核历史记录的资源。

1. 选择“资源审核”  。

1. 可以使用预定义的日期或自定义范围筛选历史记录。

    [![带筛选器的资源审核列](media/azure-pim-resource-rbac/rbac-resource-audit.png &quot;带筛选器的资源审核列表")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. 对于“审核类型”，选择“激活(已分配 + 已激活)”。

    [![按激活审核类型筛选的资源审核列表](media/azure-pim-resource-rbac/rbac-audit-activity.png "按“激活”筛选的资源审核列表")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![按激活审核类型筛选的资源审核列表](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. 在“操作”下，单击某个用户的 **（活动）** 可查看该用户在 Azure 资源中的活动详细信息。

    ![特定操作的用户活动详细信息](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit&quot;></a>查看我的审核

使用“我的审核”，可以查看你的个人角色活动。

1. 打开“Azure AD Privileged Identity Management”。 

1. 选择“Azure 资源” 。

1. 选择要查看其审核历史记录的资源。

1. 选择“我的审核”  。

1. 可以使用预定义的日期或自定义范围筛选历史记录。

    [![当前用户的审核列表](media/azure-pim-resource-rbac/my-audit-time.png &quot;当前用户的审核列表")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> 访问审核历史记录需要“全局管理员”或“特权角色管理员”角色。

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>获取审批事件的原因、审批者和票证编号

1. 使用特权角色管理员角色权限登录到 [Azure 门户](https://aad.portal.azure.com)并打开 Azure AD。
1. 选择“审核日志”  。
1. 使用“服务”  筛选器以仅显示特权身份管理服务的审核事件。 在“审核日志”  页上，你可以：

    - 请在“状态原因”  列中查看审核事件的原因。
    - 在“将成员添加到角色请求已批准”事件的“发起人(参与者)”  列中查看审批者。

    [![筛选 PIM 服务的审核日志](media/azure-pim-resource-rbac/filter-audit-logs.png "筛选 PIM 服务的审核日志")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. 选择一个审核日志事件，以在“详细信息”窗格的“活动”选项卡上查看票证编号。
  
    [![检查审核事件的票证编号](media/azure-pim-resource-rbac/audit-event-ticket-number.png "检查审核事件的票证编号")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. 可以在审核事件的“详细信息”窗格的“目标”选项卡上查看请求者（激活角色的人员）。 Azure 资源角色有三种目标类型：

    - 角色（  类型 = 角色）
    - 请求者 (Type = Other)
    - 审批者 (Type = User)

    [![检查目标类型](media/azure-pim-resource-rbac/audit-event-target-type.png "检查目标类型")](media/azure-pim-resource-rbac/audit-event-target-type.png)

通常，审批事件正上方的日志事件是“将成员添加到角色已完成”事件，其中，“发起人(参与者)”是请求者。 大多数情况下，你无需从审核角度查找审批请求中的请求者。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)
- [在 Privileged Identity Management 中批准或拒绝 Azure 资源角色的请求](pim-resource-roles-approval-workflow.md)
- [在 Privileged Identity Management 中查看 Azure AD 角色的审核历史记录](pim-how-to-use-audit-log.md)
