---
title: 使用 Azure Active Directory 实现 Kerberos 约束委派
description: 使用 Azure Active Directory 实现 Kerberos 约束委派时的体系结构指南。
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
ms.openlocfilehash: 2c12ab8421b578a0c1bf60f5d901d5d9223fc685
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231266"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Windows 身份验证 - 使用 Azure Active Directory 实现 Kerberos 约束委派

Kerberos 约束委派 (KCD) 提供资源之间的约束委派，并且是基于服务主体名称。 它要求域管理员创建委派，并且限制为单一域。 基于资源的 KCD 通常用作一种为 Web 应用程序提供 Kerberos 身份验证的方法，这种 Web 应用程序在 Active Directory 林中的多个域中具有用户。

Azure Active Directory 应用程序代理可以提供单一登录 (SSO)，还能远程访问基于 KCD 的应用程序（这些应用程序需要使用 Kerberos 票证进行访问）和 Kerberos 约束委派 (KCD)。

通过为应用程序代理连接器授予模拟 Active Directory 中的用户的权限，可以为使用集成 Windows 身份验证 (IWA) 的本地 KCD 应用程序启用 SSO。 应用程序代理连接器使用这一全新代表用户发送和接收令牌。

## <a name="use-when"></a>何时使用

需要提供远程访问，需要使用预身份验证进行保护，以及需要本地 IWA 应用程序的 SSO 功能时。

![体系结构示意图](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>系统组件

* **用户**：访问应用程序代理提供的旧版应用程序。

* **Web 浏览器**：用户与之交互以访问应用程序外部 URL 的组件。

* Azure AD：对用户进行身份验证。 

* **应用程序代理服务**：充当反向代理，用于将请求从用户发送到本地应用程序。 位于 Azure AD 中。 应用程序代理还可以强制实施任何条件访问策略。

* **应用程序代理连接器**：在 Windows server 上安装在本地，以提供与应用程序的连接。 向 Azure AD 返回响应。 与 Active Directory 执行 KCD 协商，模拟用户以在应用程序中获取 Kerberos 令牌。

* Active Directory：将应用程序的 Kerberos 令牌发送到应用程序代理连接器。

* 旧版应用程序：从应用程序代理接收用户请求的应用程序。 旧应用程序将响应返回到应用程序代理连接器。

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>使用 Azure AD 实现 Windows 身份验证 (KCD)

* [使用应用程序代理通过 Kerberos 约束委派单一登录到应用程序](../app-proxy/application-proxy-configure-single-sign-on-with-kcd.md) 

* [在 Azure Active Directory 中添加一个本地应用程序以通过应用程序代理进行远程访问](../app-proxy/application-proxy-add-on-premises-application.md)