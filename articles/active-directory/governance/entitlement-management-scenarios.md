---
title: 权利管理中的常见方案 - Azure AD
description: 了解在 Azure Active Directory 权利管理中实施常见方案时应该遵循的概要步骤。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3091afbab67f65a1850bb4ffbd73487d0e0327c9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742558"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Azure AD 权利管理中的常见方案

可以通过多种方式配置组织的权利管理。 但是，如果你刚开始使用，了解适用于管理员、目录所有者、访问包管理者、审批者和请求者的常见方案会很有帮助。

## <a name="delegate"></a>委托

### <a name="administrator-delegate-management-of-resources"></a>管理员：委托资源管理权

1. [观看视频：从 IT 委托到部门经理](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [将用户委托到目录创建者角色](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>目录创建者：委托资源管理权

- [“创建新目录”](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>目录所有者：委托资源管理权

1. [将共同所有者添加到目录](entitlement-management-catalog-create.md#add-more-catalog-owners)
1. [将资源添加到目录](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>目录所有者：委托访问包的管理

1. [观看视频：从目录所有者委托到访问包管理者](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [将用户委托到访问包管理者角色](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>管控组织中用户的访问权限

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>访问包管理者：允许组织中的员工请求资源的访问权限

1. [创建新的访问包](entitlement-management-access-package-create.md#start-new-access-package)
1. [将组、团队、应用程序或 SharePoint 站点添加到访问包](entitlement-management-access-package-create.md#resource-roles)
1. [添加请求策略以允许目录中的用户请求访问权限](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [指定过期设置](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>请求者：请求对资源的访问权限

1. [登录到“我的访问权限”门户](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 查找访问包
1. [请求访问权限](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>审批者：审批对资源的请求

1. [在“我的访问权限”门户中打开请求](entitlement-management-request-approve.md#open-request)
1. [批准或拒绝访问请求](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>请求者：查看你已有权访问的资源

1. [登录到“我的访问权限”门户](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 查看活动访问包

## <a name="govern-access-for-users-outside-your-organization"></a>管控组织外部用户的访问权限

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>管理员：与外部合作伙伴组织协作

1. [了解外部用户访问权限的工作方式](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [查看外部用户的设置](entitlement-management-external-users.md#settings-for-external-users)
1. [添加与外部组织的连接](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>访问包管理者：与外部合作伙伴组织协作

1. [创建新的访问包](entitlement-management-access-package-create.md#start-new-access-package)
1. [将组、团队、应用程序或 SharePoint 站点添加到访问包](entitlement-management-access-package-resources.md#add-resource-roles)
1. [添加请求策略以允许不在目录中的用户请求访问权限](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [指定过期设置](entitlement-management-access-package-create.md#lifecycle)
1. [复制用于请求访问包的链接](entitlement-management-access-package-settings.md)
1. 将该链接发送到外部合作伙伴联系人，以便与其用户共享

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>请求者：以外部用户身份请求资源的访问权限

1. 查找从联系人收到的访问包链接
1. [登录到“我的访问权限”门户](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [请求访问权限](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>审批者：审批对资源的请求

1. [在“我的访问权限”门户中打开请求](entitlement-management-request-approve.md#open-request)
1. [批准或拒绝访问请求](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>请求者：查看你已有权访问的资源

1. [登录到“我的访问权限”门户](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 查看活动访问包

## <a name="day-to-day-management"></a>日常管理

### <a name="access-package-manager-update-the-resources-for-a-project"></a>访问包管理者：更新项目的资源

1. [观看视频：日常管理：事情已变化](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. 打开访问包
1. [添加或删除组、团队、应用程序或 SharePoint 站点](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>访问包管理者：更新项目的持续时间

1. [观看视频：日常管理：事情已变化](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. 打开访问包
1. [打开生命周期设置](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [更新过期设置](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>访问包管理者：更新审批项目访问权限的方式

1. [观看视频：日常管理：事情已变化](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [打开请求设置的现有策略](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [更新审批设置](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>访问包管理者：更新项目人员

1. [观看视频：日常管理：事情已变化](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [删除不再需要访问权限的用户](entitlement-management-access-package-assignments.md)
1. [打开请求设置的现有策略](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [添加需要访问权限的用户](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>访问包管理者：将特定用户直接分配到访问包

1. [如果用户需要不同的生命周期设置，请将新策略添加到访问包](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [将特定用户直接分配到访问包](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>分配和报表

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>管理员：查看给谁分配了访问包

1. 打开访问包
1. [查看分配](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [将报表和日志存档](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>管理员：查看分配给用户的资源

1. [查看用户的访问包](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [查看用户的资源分配](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>通过编程方式进行管理

你也可以使用 Microsoft Graph 来管理访问包、目录、策略、请求和分配。  相应角色中的用户通过具有委托的 `EntitlementManagement.Read.All` 或 `EntitlementManagement.ReadWrite.All` 权限的应用程序可以调用[权利管理 API](/graph/tutorial-access-package-api)。  具有这些应用程序权限的应用程序也可使用其中许多 API 函数，只是不能管理目录和访问包中的资源。

## <a name="next-steps"></a>后续步骤

- [委托和角色](entitlement-management-delegate.md)
- [请求过程和电子邮件通知](entitlement-management-process.md)