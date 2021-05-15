---
title: 使用 Azure Active Directory 进行基于密码的身份验证
description: 有关如何使用 Azure Active Directory 实现基于密码的身份验证的体系结构指南。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd6a5c8af117bf6cb39969a5f1b1f17ff08681c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172816"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>使用 Azure Active Directory 进行基于密码的身份验证

基于密码的单一登录 (SSO) 使用应用程序的现有身份验证过程。 启用基于密码的 SSO 时，Azure Active Directory (Azure AD) 将收集用户凭据，将用户凭据进行加密并安全地存储在目录中。 当用户尝试登录时，Azure AD 将为应用程序提供用户名和密码。

如果应用程序使用用户名和密码而非访问令牌和标头进行身份验证，请选择基于密码的 SSO。 提供了基于 HTML 的登录页面的任何基于云的应用程序都支持基于密码的 SSO。 

## <a name="use-when"></a>何时使用

需要使用预身份验证进行保护，并通过密码保管库将 SSO 提供给 Web 应用。

![体系结构图](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>系统组件

* 用户：从“我的应用”访问基于表单的应用程序，或者直接访问站点。 

* **Web 浏览器**：用户与之交互以访问应用程序外部 URL 的组件。 用户通过 MyApps 扩展访问基于表单的应用程序。 

* MyApps 扩展：标识已配置的基于密码的 SSO 应用程序，并向登录表单提供凭据。 在 Web 浏览器上安装 MyApps 扩展。 

* Azure AD：对用户进行身份验证。

## <a name="implement-password-based-sso-with-azure-ad"></a>通过 Azure AD 实现基于密码的 SSO

* [什么是基于密码的 SSO](../manage-apps/what-is-single-sign-on.md) 

* [为云应用程序配置基于密码的 SSO](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

* [用应用程序代理为本地应用程序配置基于密码的 SSO](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)

