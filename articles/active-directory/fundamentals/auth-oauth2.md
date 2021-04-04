---
title: 通过 Azure Active Directory 进行 OAUTH 2.0 身份验证
description: 有关如何实现使用 Azure Active Directory 进行 OAUTH 2.0 身份验证的体系结构指南。
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
ms.openlocfilehash: c1604d79ce5eb9949028cd677b340bf3d4b09f6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172833"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>通过 Azure Active Directory 进行 OAuth 2.0 身份验证

OAuth 2.0 是用于授权的行业协议。 它允许用户授予对其受保护资源的有限访问权限。 OAuth 专为与超文本传输协议 (HTTP) 配合使用而设计，可将客户端的角色与资源所有者分开。 客户端会请求对资源所有者控制且由资源服务器托管的的资源的访问权限。 资源服务器在获得资源所有者的批准后颁发访问令牌。 客户端使用访问令牌访问资源服务器托管的受保护资源。 

OAuth 2.0 直接与 OpenID Connect (OIDC) 相关。 由于 OIDC 是建立在 OAuth 2.0 之上的身份验证和授权层，因此它不与 OAuth 1.0 后向兼容。 Azure Active Directory (Azure AD) 支持所有 OAuth 2.0 流。 

## <a name="use-when"></a>何时使用：

适用于富客户端、新式应用方案和 RESTful Web API 访问权限。

![体系结构示意图](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>系统组件

* **用户**：从 Web 应用程序（应用）请求服务。 用户通常是拥有数据且有权允许客户端访问数据或资源的资源所有者。 

* **Web 浏览器**：用户与之交互的 Web 浏览器是 OAuth 客户端。 

* **Web 应用**：Web 应用或资源服务器是资源或数据所在的位置。 它信任授权服务器，以安全地对 OAuth 客户端进行验证和授权。 

* **Azure AD**：Azure AD 是授权服务器，也称为标识提供者 (IdP)。 它安全地处理与用户信息、用户访问权限和信任关系相关的任何内容。 它负责颁发可授予和撤销对资源的访问权限的令牌。

## <a name="implement-oauth-20-with-azure-ad"></a>通过 Azure AD 实现 OAuth 2.0

* [将应用程序与 Azure AD 集成](../saas-apps/tutorial-list.md) 

* [Microsoft 标识平台上的 OAuth 2.0 和 OpenID Connect 协议](../develop/active-directory-v2-protocols.md) 

* [应用程序类型和 OAuth2](../develop/v2-app-types.md) 

