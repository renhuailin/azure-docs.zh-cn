---
title: 使用 Azure Active Directory 进行基于标头的身份验证
description: 有关如何使用 Azure Active Directory 实现基于标头的身份验证的体系结构指南。
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
ms.openlocfilehash: 72651ac47ebf3f67a8ee3aae9f71a533ded53a83
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111954976"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>使用 Azure Active Directory 进行基于标头的身份验证

旧应用程序通常使用基于标头的身份验证。 在此方案中，用户（或消息发起方）对中间标识解决方案进行身份验证。 中间解决方案对用户进行身份验证，并将所需的超文本传输协议 (HTTP) 标头传播到目标 Web 服务。 Azure Active Directory (AD) 通过其应用程序代理服务支持此模式，并与其他网络控制器解决方案集成。 

在我们的解决方案中，应用程序代理提供对应用程序的远程访问，对用户进行身份验证，并传递应用程序所需的标头。 

## <a name="use-when"></a>何时使用

远程用户需要安全地单一登录 (SSO) 到需要基于标头的身份验证的本地应用程序。

![体系结构映像基于标头的身份验证](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>系统组件

* 用户：访问应用程序代理提供的旧版应用程序。

* **Web 浏览器**：用户与之交互以访问应用程序外部 URL 的组件。

* Azure AD：对用户进行身份验证。 

* 应用程序代理服务：充当反向代理，用于将请求从用户发送到本地应用程序。 它驻留在 Azure AD 中，还可以强制实施任何条件性访问策略。

* 应用程序代理连接器：本地安装在 Windows server 上，以提供与应用程序的连接。 它仅使用出站连接。 向 Azure AD 返回响应。

* 旧版应用程序：从应用程序代理接收用户请求的应用程序。 旧应用程序接收所需的 HTTP 标头，以设置会话并返回响应。 

## <a name="implement-header-based-authentication-with-azure-ad"></a>使用 Azure AD 执行基于标头的身份验证

* [添加一个本地应用程序以通过 Azure AD 中的应用程序代理进行远程访问](../app-proxy/application-proxy-add-on-premises-application.md)  

* [使用应用程序代理和 PingAccess 通过基于标头的身份验证进行单一登录](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md) 

* [通过应用交付控制器和网络保护旧版应用](../manage-apps/secure-hybrid-access.md)