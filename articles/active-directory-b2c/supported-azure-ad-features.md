---
title: 支持的 Azure AD 功能
description: 了解 Azure AD B2C 中仍然支持的 Azure AD 功能。
services: active-directory-b2c
author: kengaderdus
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/08/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: f43f6afb5e102dc33bc8b5b1e1a146a58f394f60
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621223"
---
# <a name="supported-azure-ad-features"></a>支持的 Azure AD 功能

Azure AD B2C 租户与你可能已拥有的 Azure Active Directory 租户不同，但却依赖于它。 以下 Azure AD 功能可在 Azure AD B2C 租户中使用。

|功能  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [组](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | 组可用于管理管理帐户和用户帐户。| 组可用于管理管理帐户。 [使用者帐户](user-overview.md#consumer-user)不能是任何组的成员。 |
| [邀请外部标识来宾](../active-directory//external-identities/add-users-administrator.md)| 你可以邀请来宾用户并配置外部标识功能（如联合身份验证），以及使用 Facebook 和 Google 帐户登录。 | 你只能邀请 Microsoft 帐户或 Azure AD 用户作为来宾访问你的 Azure AD 租户，以访问应用程序或管理租户。 对于[使用者帐户](user-overview.md#consumer-user)，你可以使用 Azure AD B2C 用户流和自定义策略来管理用户，并使用外部标识提供程序（如 Google 或 Facebook）注册或登录。 |
| [角色和管理员](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| 完全支持管理帐户和用户帐户。 | [使用者帐户](user-overview.md#consumer-user)不支持角色。 使用者帐户无权访问任何 Azure 资源。|
| [自定义域名](../active-directory/fundamentals/add-custom-domain.md) |  只能对管理帐户使用 Azure AD 自定义域。 | [使用者帐户](user-overview.md#consumer-user)可以使用用户名、电话号码或任何电子邮件地址登录。 可以在重定向 URL 中使用[自定义域](custom-domain.md)。|
| [条件访问](../active-directory/conditional-access/overview.md) | 完全支持管理帐户和用户帐户。 | [使用者帐户](user-overview.md#consumer-user)支持一部分 Azure AD 条件访问功能。了解如何配置 Azure AD B2C [条件访问](conditional-access-user-flow.md)。|
| [高级 P1](https://azure.microsoft.com/pricing/details/active-directory) | 充分支持 Azure AD 高级 P1 功能。 例如，[密码保护](../active-directory/authentication/concept-password-ban-bad.md)、[混合标识](../active-directory/hybrid/whatis-hybrid-identity.md)、[条件访问](../active-directory/roles/permissions-reference.md#)、[动态组](../active-directory/enterprise-users/groups-create-rule.md)等。 | [使用者帐户](user-overview.md#consumer-user)支持一部分 Azure AD 条件访问功能。 了解如何配置 Azure AD B2C [条件访问](conditional-access-user-flow.md)。|
| [高级 P2](https://azure.microsoft.com/pricing/details/active-directory/) | 充分支持 Azure AD 高级 P2 功能。 例如，[标识保护](../active-directory/identity-protection/overview-identity-protection.md)和[标识治理](../active-directory/governance/identity-governance-overview.md)。  | [使用者帐户](user-overview.md#consumer-user)支持一部分 Azure AD 标识保护功能。 了解如何[使用标识保护调查风险](identity-protection-investigate-risk.md)，并配置 Azure AD B2C [条件访问](conditional-access-user-flow.md)。 |

> [!NOTE]
> **租户中的其他 Azure 资源**： <br>在 Azure AD B2C 租户中，无法预配其他 Azure 资源，如虚拟机、Azure Web 应用或 Azure 功能。 必须在 Azure AD 租户中创建这些资源。