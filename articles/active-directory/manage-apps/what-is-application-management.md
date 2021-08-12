---
title: 什么是 Azure Active Directory 中的应用程序管理
description: 将 Azure Active Directory (AD) 用作云和本地应用程序的标识和访问管理 (IAM) 系统的概述。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: davidmu
ms.reviewer: ''
ms.openlocfilehash: e483efc2beae5ddf76c135aaa785b32732aa2f62
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113566401"
---
# <a name="what-is-application-management"></a>什么是应用程序管理？

Azure AD 是一个标识和访问管理 (IAM) 系统。 它提供了一个用于存储数字标识相关信息的单一位置。 你可以将软件应用程序配置为使用 Azure AD 来作为存储用户信息的位置。

Azure AD 必须配置为与应用程序相集成。 换句话说，它需要知道哪些应用正在使用它进行标识。 使 Azure AD 意识到这些应用的存在以及应如何处理它们的过程称为“应用程序管理”。

可在 Azure Active Directory 门户的“管理”部分中的“企业应用程序”页面上管理应用程序。

![Azure AD 门户的“管理”部分下的“企业应用程序”选项。](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>什么是标识和访问管理 (IAM) 系统？

应用程序是一种用于某种用途的软件。 大多数应用都要求用户登录。

集中式标识系统提供单一的位置来存储可供所有应用程序使用的用户信息。 这些系统渐渐被称作了标识和访问管理 (IAM) 系统。 Azure Active Directory 是 Microsoft 云的 IAM 系统。

>[!TIP]
>IAM 系统提供了一个用于跟踪用户标识的单一位置。 Azure AD 是 Microsoft 云的 IAM 系统。

## <a name="why-manage-applications-with-a-cloud-solution"></a>为何使用云解决方案管理应用程序？

组织通常拥有数以百计的应用程序，用户依赖这些应用程序来完成工作。 用户从许多设备和位置访问这些应用程序。 会不断添加和开发新的应用程序，也会不断停用应用程序。 有如此多的应用和访问点，因此使用一个适合所有这些内容的标识解决方案非常重要。

>[!TIP]
>Azure AD 应用库包含了许多为将 Azure AD 用作标识提供程序而已预先配置的常用应用程序。

## <a name="how-does-azure-ad-work-with-apps"></a>Azure AD 如何与应用一起工作？

Azure AD 位于中间，它为云和本地应用提供标识管理。

![显示通过 Azure AD 进行联合的应用的关系图](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>通过[自动执行用户预配](../app-provisioning/user-provisioning.md)来降低管理成本，使得在将用户添加到公司 HR 系统时，他们会自动添加到 Azure AD 中。

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>可将哪些类型的应用程序与 Azure AD 集成？

可将 Azure AD 用作几乎任何应用的标识系统。 许多应用已经过预先配置，稍微操作一下即可进行设置好。 这些预配置的应用在 [Azure AD 应用库](/azure/active-directory/saas-apps/)中发布。

如果应用尚未在库中，你也可手动配置大多数应用的单一登录。 Azure AD 提供了多种 SSO 选项。 其中最常用的一些是基于 SAML 的 SSO 和基于 OIDC 的 SSO。 若要详细了解如何集成应用来启用 SSO，请参阅[单一登录选项](sso-options.md)。

你的组织使用的是本地应用吗？ 你可使用应用代理来集成它们。 若要了解详细信息，请参阅[通过 Azure AD 应用程序代理远程访问本地应用程序](../app-proxy/application-proxy.md)。

>[!TIP]
>生成自己的业务线应用程序时，可将其与 Azure AD 集成来支持单一登录。 若要详细了解如何针对 Azure AD 开发应用，请参阅 [Microsoft 标识平台](..//develop/v2-overview.md)。

## <a name="manage-risk-with-conditional-access-policies"></a>利用条件访问策略管理风险

将 Azure AD 单一登录 (SSO) 与[条件访问](../conditional-access/concept-conditional-access-cloud-apps.md)相结合可以提供访问应用程序时的高级安全性。 条件访问策略基于你设置的条件提供对应用的精细控制。

## <a name="improve-productivity-with-single-sign-on"></a>使用单一登录提高工作效率

单一登录 (SSO) 在 Microsoft 365 和你使用的其他所有应用之间提供统一的用户体验。 你无需再反复输入用户名和密码！

若要详细了解单一登录，请参阅[什么是单一登录](what-is-single-sign-on.md)。

## <a name="address-governance-and-compliance"></a>解决管理和符合性问题

通过使用安全事件和事件监视 (SIEM) 工具的报告监视应用。 可从门户或 API 访问报告。 以编程的方式审核有权访问应用程序的用户，并通过访问评审删除不活跃用户的访问权限。

## <a name="manage-costs"></a>管理成本

通过迁移到 Azure AD，可节约成本并消除管理本地基础结构的难题。 Azure AD 还提供对应用程序的自助式访问，同时为管理员和用户节约时间。 单一登录不需特定于应用程序的密码。 这种单一登录功能可以节约应用程序密码重置的相关费用，并避免在找回密码时失去工作效率。

对于着重人力资源的应用程序或其他具有大量用户的应用程序，你可使用应用预配来简化操作。 应用预配可自动执行添加和删除用户的过程。 若要了解详细信息，请参阅[什么是应用程序预配？](../app-provisioning/user-provisioning.md)

## <a name="next-steps"></a>后续步骤

- [应用程序管理的快速入门系列](view-applications-portal.md)
- [应用程序集成入门](plan-an-application-integration.md)
- [了解如何执行自动预配](../app-provisioning/user-provisioning.md)
