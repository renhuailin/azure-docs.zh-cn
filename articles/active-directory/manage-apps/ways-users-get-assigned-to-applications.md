---
title: 了解如何在 Azure Active Directory 中将用户分配到应用
description: 了解如何将用户分配到使用 Azure Active Directory 进行标识管理的应用。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: aedcecf528f07d9d7f89c1d5659d8fcd2cb36df2
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016722"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>了解如何在 Azure Active Directory 中将用户分配到应用
本文介绍如何将用户分配给租户中的应用程序。

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>如何将用户分配给 Azure AD 中的应用程序？
用户要访问应用程序，必须先以某种方式将其分配给该应用程序。 可使用管理员、业务委托，或有时使用用户本身的身份执行分配。 下文介绍了可以将用户分配给应用程序的方式：

*  管理员直接[将用户分配给](./assign-user-or-group-access-portal.md)应用程序
*  管理员[将用户是其成员的组分配](./assign-user-or-group-access-portal.md)给应用程序，包括：
    * 已从本地同步的组
    * 在云中创建的静态安全组
    * 在云中创建的[动态安全组](../enterprise-users/groups-dynamic-membership.md)
    * 在云中创建的 Microsoft 365 组
    * [所有用户](../fundamentals/active-directory-groups-create-azure-portal.md)组
*  管理员启用 [自助服务应用程序访问](./manage-self-service-access.md)以允许用户使用 ["我的应用"](../user-help/my-apps-portal-end-user-access.md) **添加** 应用程序，**无需企业批准** 即可添加应用功能
*  管理员启用 [自助服务应用程序访问](./manage-self-service-access.md) 以允许用户使用 ["我的应用" "](../user-help/my-apps-portal-end-user-access.md) **添加应用** " 功能添加应用程序，但只有在 **选择的一组业务审批者之前进行审批**
*  管理员启用 [自助服务组管理](../enterprise-users/groups-self-service-management.md)以允许用户 **无需业务批准**，即可加入已对其分配应用程序的组
*  管理员启用 [自助服务组管理](../enterprise-users/groups-self-service-management.md)以允许用户在经过 **选定业务批准者的事先批准** 的情况下，加入已对其分配应用程序的组
*  对于第一方应用程序（如 [Microsoft 365](https://products.office.com/)），管理员直接为用户分配许可证
*  对于第一方应用程序（如 [Microsoft 365](https://products.office.com/)），管理员直接为此用户所在的组分配许可证
*  [管理员同意](../develop/howto-convert-app-to-be-multi-tenant.md)所有用户均可使用某个应用程序，然后用户登录该应用程序
* 通过登录应用程序，用户自己[同意使用应用程序](../develop/howto-convert-app-to-be-multi-tenant.md)

## <a name="next-steps"></a>后续步骤
* [应用程序管理的快速入门系列](view-applications-portal.md)
* [什么是应用程序管理？](what-is-application-management.md)
* [什么是单一登录？](what-is-single-sign-on.md)