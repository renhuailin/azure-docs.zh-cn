---
title: 了解如何将用户分配给应用
description: 了解如何将用户分配到使用 Azure Active Directory 进行标识管理的应用。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 5a7955a183893540e6c8de3981ad561200e31f70
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613560"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>了解如何在 Azure Active Directory 中将用户分配到应用

本文介绍如何将用户分配给租户中的应用程序。

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>如何将用户分配给 Azure AD 中的应用程序？

用户要访问应用程序，必须先以某种方式将其分配给该应用程序。 可使用管理员、业务委托，或有时使用用户本身的身份执行分配。 下文介绍了可以将用户分配给应用程序的方式：

* 管理员直接[将用户分配给](./assign-user-or-group-access-portal.md)应用程序
* 管理员[将用户是其成员的组分配](./assign-user-or-group-access-portal.md)给应用程序，包括：

  * 已从本地同步的组
  * 在云中创建的静态安全组
  * 在云中创建的[动态安全组](../enterprise-users/groups-dynamic-membership.md)
  * 在云中创建的 Microsoft 365 组
  * [所有用户](../fundamentals/active-directory-groups-create-azure-portal.md)组
* 管理员启用[自助服务应用程序访问](./manage-self-service-access.md)以允许用户无需业务批准，即可使用[我的应用](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)的“添加应用”功能添加应用程序
* 管理员启用 [自助服务应用程序访问](./manage-self-service-access.md)以允许用户仅 **在经过选定业务批准者的事先批准的情况下**，使用 [我的应用](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)的“添加应用”功能添加应用程序
* 管理员启用 [自助服务组管理](../enterprise-users/groups-self-service-management.md)以允许用户 **无需业务批准**，即可加入已对其分配应用程序的组
* 管理员启用 [自助服务组管理](../enterprise-users/groups-self-service-management.md)以允许用户在经过 **选定业务批准者的事先批准** 的情况下，加入已对其分配应用程序的组
* 对于第一方应用程序（如 [Microsoft 365](https://products.office.com/)），管理员直接为用户分配许可证
* 对于第一方应用程序（如 [Microsoft 365](https://products.office.com/)），管理员直接为此用户所在的组分配许可证
* [管理员同意](../develop/howto-convert-app-to-be-multi-tenant.md)所有用户均可使用某个应用程序，然后用户登录该应用程序
* 通过登录应用程序，用户自己[同意使用应用程序](../develop/howto-convert-app-to-be-multi-tenant.md)

## <a name="next-steps"></a>后续步骤

* [应用程序管理的快速入门系列](view-applications-portal.md)
* [什么是应用程序管理？](what-is-application-management.md)
* [什么是单一登录？](what-is-single-sign-on.md)