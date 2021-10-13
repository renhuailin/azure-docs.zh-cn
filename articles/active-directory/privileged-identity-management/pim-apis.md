---
title: Privileged Identity management 中的 API 概念 - Azure AD | Microsoft Docs
description: 用于了解 Azure AD Privileged Identity Management (PIM) 中的 API 的信息。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac89554135277e0656392a7035b34aac307008b7
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668535"
---
# <a name="understand-the-privileged-identity-management-apis"></a>了解 Privileged Identity Management API

可以使用 Azure Active Directory (Azure AD) 角色的 Microsoft Graph API 和 Azure 资源角色（有时称为 Azure RBAC 角色）的 Azure 资源管理器 API 来执行 Privileged Identity Management (PIM) 任务。 本文介绍使用适用于 Privileged Identity Management 的 API 的重要概念。

有关 PIM API 的请求及其他详细信息，请查看：

- [适用于 Azure AD 角色的 PIM API 参考](/graph/api/resources/unifiedroleeligibilityschedulerequest?view=graph-rest-beta&preserve-view=true)
- [适用于 Azure 资源角色的 PIM API 参考](/rest/api/authorization/roleeligibilityschedulerequests)

> [!IMPORTANT]
> PIM API [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="pim-api-history"></a>PIM API 历史记录

在过去几年中，PIM API 已有几次迭代。 用户会发现一些功能重叠，但它们并不代表版本的线性进度。

### <a name="iteration-1--only-supports-azure-ad-roles-deprecating"></a>迭代 1–仅支持 Azure AD 角色，正在弃用

在 /beta/privilegedRoles 终结点下，Microsoft 拥有一个经典版本的 PIM API，在大多数租户中不再受支持。 我们自 5 月 31 日开始弃用对此 API 的剩余访问。

### <a name="iteration-2--supports-azure-ad-roles-and-azure-resource-roles"></a>迭代 2–支持 Azure AD 角色和 Azure 资源角色

在 /beta/privilegedAccess 终结点下，Microsoft 支持 /aadRoles 和 /azureResources。 此终结点在租户中仍可用，但 Microsoft 建议不要使用此 API 开始任何新的开发工作。 此 beta 版本 API 不会再正式发布，最终将被弃用。

### <a name="current-iteration--azure-ad-roles-in-microsoft-graph-and-azure-resource-roles-in-azure-resource-manager"></a>当前迭代–Microsoft Graph 中的 Azure AD 角色和 Azure 资源管理器中的 Azure 资源角色

目前为 beta 版本，Microsoft 在正式发布 API 之前会进行 PIM API 的最终迭代。 根据客户反馈，Azure AD PIM API 现在位于 unifiedRoleManagement API 集下，而 Azure 资源 PIM API 现在位于 Azure 资源管理器角色分配 API 下。 这些位置还提供一些其他优势，包括：

- PIM API 与适用于 Azure AD 角色和 Azure 资源角色的常规角色分配 API 的对齐。
- 减少调用其他 PIM API 以加入资源、获取资源或获取角色定义的需求。
- 支持仅限应用权限。
- 新增功能，如审批和电子邮件通知配置。

在当前迭代中，没有对 PIM 警报和特权访问组的 API 支持。

## <a name="current-permissions-required"></a>当前所需权限

### <a name="azure-ad-roles"></a>Azure AD 角色

  若要调用适用于 Azure AD 角色的 PIM 图形 API，至少需要以下权限之一：

- RoleManagement.ReadWrite.Directory
- RoleManagement.Read.Directory

  若要指定所需的权限，最容易的方式是使用 Azure AD 许可框架。

### <a name="azure-resource-roles"></a>Azure 资源角色

  适用于 Azure 资源角色的 PIM API 是在 Azure 资源管理器框架上开发的。 用户需要许可 Azure 资源管理，但不需要任何图形 API 权限。 还需要确保调用 API 的用户或服务主体在尝试管理的资源上至少具有所有者或用户访问管理员角色。

## <a name="calling-pim-api-with-an-app-only-token"></a>使用仅限应用令牌调用 PIM API

### <a name="azure-ad-roles"></a>Azure AD 角色

  PIM API 现在支持基于委托权限的仅限应用权限。

- 对于仅限应用权限，必须使用已经获得所需 Azure AD 或 Azure 角色权限许可的应用程序来调用 API。
- 对于委托的权限，必须使用用户以及应用程序令牌来调用 PIM API。 必须将用户分配到全局管理员角色或特权角色管理员角色，并确保调用 API 的服务主体在尝试管理的资源上至少具有所有者或用户访问管理员角色。

### <a name="azure-resource-roles"></a>Azure 资源角色

  适用于 Azure 资源的 PIM API 支持仅限用户和仅限应用程序调用。 只需确保服务主体在资源上具有所有者或用户访问管理员角色。

## <a name="design-of-current-api-iteration"></a>当前 API 迭代的设计

PIM API 包括两个与适用于 Azure AD 角色和 Azure 资源角色的 API 一致的类别：分配和激活 API 请求、策略设置。

### <a name="assignment-and-activation-api"></a>分配和激活 API

若要进行合格分配、限时合格/活动分配以及激活分配，PIM 提供以下实体：

- RoleAssignmentSchedule
- RoleEligibilitySchedule
- RoleAssignmentScheduleInstance
- RoleEligibilityScheduleInstance
- RoleAssignmentScheduleRequest
- RoleEligibilityScheduleRequest

这些实体与适用于 Azure AD 角色和 Azure 角色的预先存在的 roleDefinition 和 roleAssignment 实体一起工作，以允许创建端到端方案。

- 如果正在尝试创建或检索不具有计划（开始或结束时间）的永久性（活动）角色分配，则应避免使用这些 PIM 实体，并专注于 roleAssignment 实体下的读取/写入操作

- 若要创建具有或不具有过期时间的合格分配，可以在 roleEligibilityScheduleRequest 上使用写入操作

- 若要创建具有计划（开始或结束时间）的永久性（活动）分配，可以在 roleAssignmentScheduleRequest 上使用写入操作  

- 若要激活合格分配，还应使用名为 selfActivate 的已修改操作参数在 roleAssignmentScheduleRequest 上使用写入操作

每个请求对象都可以创建 roleAssignmentSchedule 或 roleEligibilitySchedule 对象。 这些对象为只读，并显示所有当前和未来分配的计划。

激活合格分配后，roleEligibilityScheduleInstance 会继续存在。 激活的 roleAssignmentScheduleRequest 将在该激活的持续时间内创建单独的 roleAssignmentSchedule 和 roleAssignmentScheduleInstance。

实例对象是当前存在的实际分配，无论是合格分配还是活动分配。 应该对实例实体使用 GET 操作来检索角色/用户的合格分配/活动分配的列表。

### <a name="policy-setting-api"></a>策略设置 API

为了管理设置，我们提供以下实体：

- roleManagementPolicy
- roleManagementPolicyAssignment

“角色管理策略”定义规则的设置。 例如，是否需要 MFA/审批、是否以及向谁发送电子邮件通知，或者是否允许永久分配。 “策略分配”将策略附加到特定角色。

使用此 API，即获取所有 roleManagementPolicyAssignments 的列表，按要修改的 roleDefinitionID 对其进行筛选，然后更新与 policyAssignment 关联的策略。

## <a name="relationship-between-pim-entities-and-role-assignment-entities"></a>PIM 实体与角色分配实体之间的关系

PIM 实体与适用于 Azure AD 角色或 Azure 角色的永久性（活动）分配的角色分配实体之间的唯一链接是 roleAssignmentScheduleInstance。 这两个实体之间是一对一的映射关系。 该映射意味着 roleAssignment 和 roleAssignmentScheduleInstance 将包括：  

- PIM 外部进行的永久性（活动）分配
- 使用 PIM 内部进行的计划的永久性（活动）分配
- 已激活的合格分配

## <a name="next-steps"></a>后续步骤

- [Azure AD Privileged Identity Management API 参考](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta&preserve-view=true)