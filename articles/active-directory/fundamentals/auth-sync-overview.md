---
title: Azure Active Directory 身份验证和同步协议概述
description: 有关将 Azure AD 与旧式身份验证协议和同步模式相集成的体系结构指南
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
ms.openlocfilehash: ab63bc5bd2819a239741da525eebb2404a47bbf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92441192"
---
# <a name="azure-active-directory-integrations-with-authentication-and-synchronization-protocols"></a>Azure Active Directory 与身份验证和同步协议的集成

Microsoft Azure Active Directory (Azure AD) 支持与许多身份验证和同步协议集成。 通过身份验证集成，只需对使用旧式身份验证方法的应用程序进行少量更改（或无需更改），即可使用 Azure AD 及其安全和管理功能。 利用同步集成，可以将用户和组数据同步到 Azure AD，然后使用用户 Azure AD 管理功能。 某些同步模式还支持自动预配。

## <a name="legacy-authentication-protocols"></a>旧式身份验证协议

下表展示了 Azure AD 身份验证与旧式身份验证协议及其功能的集成。 选择要查看的身份验证协议的名称

* 详细说明

* 何时使用

* 体系结构图

* 系统组件的说明

* 有关如何实现集成的链接

 

| 身份验证协议| 身份验证| 授权| 多重身份验证| 条件性访问 |
| - |- | - | - | - |
| [基于标头的身份验证](auth-header-based.md)|![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [LDAP 身份验证](auth-ldap.md)| ![选中标记](./media/authentication-patterns/check.png)| | |  |
| [OAuth 2.0 身份验证](auth-oauth2.md)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [OIDC 身份验证](auth-oidc.md)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [基于密码的 SSO 身份验证](auth-password-based-sso.md )| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [RADIUS 身份验证]( auth-radius.md)| ![选中标记](./media/authentication-patterns/check.png)| | ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [远程桌面网关服务](auth-remote-desktop-gateway.md)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [安全外壳 (SSH)](auth-ssh.md) |  ![选中标记](./media/authentication-patterns/check.png)| | ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [SAML 身份验证](auth-saml.md)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |
| [Windows 身份验证 - Kerberos 约束委派](auth-kcd.md)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>同步模式

下表展示了 Azure AD 与同步模式及其功能的集成。 选择要查看的模式的名称

* 详细说明

* 何时使用

* 体系结构图

* 系统组件的说明

* 有关如何实现集成的链接



| 同步模式| 目录同步| 用户预配 |
| - | - | - |
| [目录同步](sync-directory.md)| ![选中标记](./media/authentication-patterns/check.png)|  |
| [LDAP 同步](sync-ldap.md)| ![选中标记](./media/authentication-patterns/check.png)|  |
| [SCIM 同步](sync-scim.md)| ![选中标记](./media/authentication-patterns/check.png)| ![选中标记](./media/authentication-patterns/check.png) |

