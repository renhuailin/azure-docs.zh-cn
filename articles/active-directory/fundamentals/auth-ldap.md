---
title: 使用 Azure Active Directory 进行 LDAP 身份验证
description: 有关通过 Azure Active Directory 实现 LDAP 身份验证的体系结构指南。
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
ms.openlocfilehash: f209d394e1a0c2c4ddde9cbf8df2704647e2822a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168706"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>使用 Azure Active Directory 进行 LDAP 身份验证

轻型目录访问协议 (LDAP) 是一种用于处理各种目录服务的应用程序协议。 Active Directory 等目录服务可[存储用户和帐户信息](https://www.dnsstuff.com/active-directory-service-accounts)，以及密码等安全信息。 然后，该服务会允许将该信息共享给网络上的其他设备。 企业应用程序（例如电子邮件、客户关系管理器 (CRM) 和人力资源 (HR) 软件）可使用 LDAP 来验证、访问和查找信息。 

Azure Active Directory (Azure AD) 通过 Azure AD 域服务 (AD DS) 支持此模式。 这使采用云优先策略的组织可通过将本地 LDAP 资源转移到云来实现环境的现代化。 直接优势如下： 

* 与 Azure AD 集成。 用户和组的添加或其对象的属性更改都将自动从 Azure AD 租户同步到 AD DS。 本地 Active Directory 中对象的更改将同步到 Azure AD，然后同步到 AD DS。

* 简化了操作。 减少了手动维护和修补本地基础结构的需要。 

* 可靠。 获得了高度可用的托管服务 

## <a name="use-when"></a>何时使用

应用程序或服务需要使用 LDAP 身份验证时。

![体系结构示意图](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>系统组件

* **用户**：通过浏览器访问依赖于 LDAP 的应用程序。

* **Web 浏览器**：用户与之交互以访问应用程序外部 URL 的接口。

* **虚拟网络**：Azure 中的专用网络，旧版应用程序可通过该网络使用 LDAP 服务。 

* **旧版应用程序**：需要在 Azure 的虚拟网络中部署 LDAP 的应用程序或服务器工作负载，或可通过网络路由查看 AD DS 实例 IP 的应用程序或服务器工作负载。 

* **Azure AD**：通过 Azure AD Connect 同步组织本地目录中的标识信息。

* **Azure AD 域服务 (AD DS)** ：从 Azure AD 执行单向同步，以提供对一组集中用户、组和凭据的访问。 AD DS 实例将分配给一个虚拟网络。 Azure 中连接到分配给 AD DS 的虚拟网络的应用程序、服务和 VM 可使用常见 AD DS 功能，如 LDAP、域加入、组策略、Kerberos 和 NTLM 身份验证。
   > [!NOTE]
   >  在组织无法同步密码哈希或用户使用智能卡登录的环境中，我们建议你在 AD DS 中使用资源林。 

* **Azure AD Connect**：用于将本地标识信息同步到 Microsoft Azure AD 的工具。 部署向导和引导式体验有助于配置连接所需的先决条件和组件，包括从 Active Directory 到 Azure AD 的同步和登录。 

* **Active Directory**：一项用于存储 [本地标识信息（如用户和帐户信息）](https://www.dnsstuff.com/active-directory-service-accounts)安全信息（如密码）的目录服务。

## <a name="implement-ldap-authentication-with-azure-ad"></a>使用 Azure AD 实现 LDAP 身份验证

* [创建和配置 Azure AD DS 实例](../../active-directory-domain-services/tutorial-create-instance.md) 

* [为 Azure AD DS 实例配置虚拟网络](../../active-directory-domain-services/tutorial-configure-networking.md) 

* [为 Azure AD DS 托管域配置安全 LDAP](../../active-directory-domain-services/tutorial-configure-ldaps.md) 

* [在 Azure AD DS 中创建到本地域的出站林信任](../../active-directory-domain-services/tutorial-create-forest-trust.md)

