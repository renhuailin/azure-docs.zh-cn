---
title: Azure Active Directory 的基于标头的身份验证
description: 有关通过 Azure Active Directory 实现基于标头的身份验证的体系结构指南。
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
ms.openlocfilehash: 69e87fc919893a544f5d0b1b615a110f25486e57
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168740"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Azure Active Directory 的基于标头的身份验证

旧应用程序通常使用基于标头的身份验证。 在此方案中，用户 (或消息发起方) 对中间标识解决方案进行身份验证。 中介解决方案对用户进行身份验证，并将所需的超文本传输协议 (HTTP) 标头传播到目标 web 服务。 Azure Active Directory (AD) 通过其应用程序代理服务支持此模式，并与其他网络控制器解决方案集成。 

在我们的解决方案中，应用程序代理提供对应用程序的远程访问，对用户进行身份验证，并传递应用程序所需的标头。 

## <a name="use-when"></a>何时使用

远程用户需要 (SSO) 安全地登录到需要基于标头的身份验证的本地应用程序。

![基于体系结构映像头的身份验证](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>系统组件

* **用户**：访问应用程序代理提供的旧版应用程序。

* **Web 浏览器**：用户与之交互以访问应用程序外部 URL 的组件。

* **Azure AD**：对用户进行身份验证。 

* **应用程序代理服务**：充当反向代理，用于将请求从用户发送到本地应用程序。 它驻留在 Azure AD 中，还可以强制实施任何条件性访问策略。

* **应用程序代理连接器**：在 Windows server 上安装在本地，以提供与应用程序的连接。 它仅使用出站连接。 返回 Azure AD 的响应。

* **旧版应用** 程序：从应用程序代理接收用户请求的应用程序。 旧应用程序接收所需的 HTTP 标头，以设置会话并返回响应。 

## <a name="implement-header-based-authentication-with-azure-ad"></a>实现基于标头的身份验证和 Azure AD

* [在 Azure AD 中通过应用程序代理添加用于远程访问的本地应用程序](../manage-apps/application-proxy-add-on-premises-application.md)  

* [使用应用程序代理和 PingAccess 通过基于标头的身份验证进行单一登录](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md) 

* [通过应用交付控制器和网络保护旧版应用](../manage-apps/secure-hybrid-access.md)