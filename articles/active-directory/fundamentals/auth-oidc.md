---
title: 使用 Azure Active Directory 进行 OpenID Connect 身份验证
description: 有关如何实现使用 Azure Active Directory 进行 OpenID Connect 身份验证的体系结构指南。
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
ms.openlocfilehash: 0e5bf7e51de38d42e64f6737e687c5946a464160
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96168655"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>使用 Azure Active Directory 进行 OpenID Connect 身份验证

OpenID Connect (OIDC) 是基于 OAuth2 协议（用于授权）的身份验证协议。 OIDC 使用来自 OAuth2 的标准化消息流来提供标识服务。 

OIDC 的设计目标是“使简单的事情保持简单，使复杂的事情成为可能”。 OIDC 使开发人员可以跨网站和应用对其用户进行身份验证，而不必拥有和管理密码文件。 这为应用生成器提供了一种安全的方式来验证当前使用浏览器或本机应用连接到该应用程序的的人员的身份。

用户的身份验证必须在标识提供者中进行，用户的会话或凭据将在其中进行检查。 为此，需要一个受信任的代理。 本机应用通常会因此启动系统浏览器。 嵌入式视图被视为不受信任，因为没有任何设置可阻止应用对用户密码进行窥探。 

除了进行身份验证外，还可以要求用户提供同意。 同意是用户允许应用程序访问受保护资源的显式权限。 同意与身份验证不同，因为只需为资源提供一次同意。 同意将一直有效，直到用户或管理员手动撤销授权为止。 

## <a name="use-when"></a>何时使用

需要征得用户同意并进行 Web 登录。

![体系结构图](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>系统组件

* **用户**：从应用程序请求服务。

* **受信任的代理**：用户与之交互的组件。 这个受信任的代理通常是 Web 浏览器。

* **应用程序**：应用程序或资源服务器是资源或数据所在的位置。 它信任标识提供者以安全方式对受信任的代理进行身份验证和授权。 

* **Azure AD**：OIDC 提供程序（也称为标识提供者）会安全地管理与用户信息、用户访问权限及流中各方之间的信任关系有关的任何事情。 它会对用户的身份进行验证、授予和吊销对资源的访问权限，以及颁发令牌。 

## <a name="implement-oidc-with-azure-ad"></a>通过 Azure AD 实现 OIDC

* [将应用程序与 Azure AD 集成](../saas-apps/tutorial-list.md) 

* [Microsoft 标识平台上的 OAuth 2.0 和 OpenID Connect 协议](../develop/active-directory-v2-protocols.md) 

* [Microsoft 标识平台和 OpenID Connect 协议](../develop/v2-protocols-oidc.md) 

* [在 Azure Active Directory B2C 中使用 OpenID Connect 进行 Web 登录](../../active-directory-b2c/openid-connect.md) 

* [使用 OpenID Connect 和 Azure AD 保护应用程序](/learn/modules/secure-app-with-oidc-and-azure-ad/) 

