---
title: 通过 Azure Active Directory 进行 SAML 身份验证
description: 有关如何使用 Azure Active Directory 实现 SAML 身份验证的体系结构指南
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
ms.openlocfilehash: 7cd2aa5e9ff8cbaeead69f11d2e3de7f760b53ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168638"
---
# <a name="saml-authentication-with-azure-active-directory"></a>通过 Azure Active Directory 进行 SAML 身份验证

安全断言标记语言 (SAML) 是在标识提供者和服务提供商之间交换身份验证和授权数据的开放标准。 SAML 是一种基于 XML 的标记语言，用于安全断言，也就是一些生命，服务提供程序使用这些声明来做出访问控制决策。 

SAML 规范定义了三个角色：

* 主体，通常是用户
* 身份提供者 (IdP)
* 服务提供者 (SP)


## <a name="use-when"></a>何时使用

需要为企业 SAML 应用程序 (SSO) 体验提供单一登录时。

尽管 SAML 解决的最重要用例之一是 SSO（特别是跨安全域扩展 SSO），但也有其他用例（也称为配置文件）。 

![SAML 的体系结构关系图](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>系统组件

* **用户**：从应用程序请求服务。

* **Web 浏览器**：用户与之交互的组件。

* **Web 应用**：企业应用程序，支持 SAML 并使用 Azure AD 作为 IdP。

* **令牌**：SAML 断言（也称为 SAML 令牌），用于携带 IdP 有关主体（用户）做出的声明的集合。 令牌包含身份验证信息、属性和授权决策声明。

* **Azure AD**：企业云 IdP，为 SAML 应用提供 SSO 和多重身份验证。 它在向依赖应用程序提供身份验证服务的同时，还同步、维护和管理用户的标识信息。 

## <a name="implement-saml-authentication-with-azure-ad"></a>使用 Azure AD 实现 SAML 身份验证

* [有关集成使用 Azure Active Directory 的 SaaS 应用程序的教程](../saas-apps/tutorial-list.md) 

* [为非库应用程序配置基于 SAML 的单点登录](../manage-apps/add-application-portal.md) 

* [Azure AD 使用 SAML 协议的方式](../develop/active-directory-saml-protocol-reference.md)