---
title: 什么是单一登录？
description: 了解 Azure Active Directory 中企业应用程序的单一登录。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.custom: contperf-fy21q1
ms.openlocfilehash: bb4a38d46a241c902d0718104d9a4030bf84607f
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858746"
---
# <a name="what-is-single-sign-on-in-azure-active-directory"></a>Azure Active Directory 中的单一登录是什么？

本文提供了有关可供使用的单一登录 (SSO) 的信息，以及在使用 Azure Active Directory (Azure AD) 时计划单一登录部署的简介。 单一登录是一种身份验证方法，允许用户使用一组凭据登录到多个独立的软件系统。 使用 SSO 意味着用户无需登录使用的每个应用程序。 使用 SSO，用户可以访问全部所需的应用程序，而无需使用不同的凭据进行身份验证。 

Azure AD 中已有许多应用程序可以使用 SSO。 取决于应用程序的需求及其实现方式，使用 SSO 时有多种选项。 在 Azure AD 中创建应用程序之前，请花一点时间来规划 SSO 部署。 使用“我的应用”门户可以更轻松地管理应用程序。

## <a name="single-sign-on-options"></a>单一登录选项

选择哪种 SSO 方法取决于为应用程序配置的身份验证方式。 云应用程序可以使用基于联合的选项，例如 OpenID Connect、OAuth 和 SAML。 应用程序还可以使用基于密码的 SSO、基于链接的 SSO，也可以禁用 SSO。

- 联合 - 将 SSO 设置为在多个标识提供者之间工作时，这称为“联合身份验证”。 基于联合身份验证协议的 SSO 实现可以提升安全性、可靠性和最终用户体验，实现起来也更容易。 

    使用联合单一登录时，Azure AD 通过使用用户的 Azure AD 帐户让用户向应用程序进行身份验证。 SAML 2.0、WS 联合身份验证或 OpenID Connect应用程序支持此方法。 联合 SSO 是 SSO 最丰富的模式。 当应用程序支持联合 SSO 时，使用 联合 SSO，而不是基于密码的 SSO 和 Active Directory 联合身份验证服务 (AD FS)。

    在某些情况下，不存在适用于企业应用程序的 SSO 选项。 如果使用“应用注册”注册应用程序，则默认情况下，单一登录功能将配置为使用 OpenID Connect 和 OAuth。 在这种情况下，企业应用程序下的导航中不会显示单一登录选项。

    当应用程序托管在另一个租户中时，单一登录不可用。 帐户不具有所需权限（全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者）时，单一登录也不可用。 权限还可能导致出现可打开单一登录，但无法进行保存的情况。

    > [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]

- 密码 - 本地应用程序可以使用基于密码的集成式 Windows 身份验证、基于标头、基于链接的方法进行 SSO。 当应用程序配置为应用程序代理时，本地选项适用。

    使用基于密码的 SSO 时，用户在首次访问应用程序时使用用户名和密码登录。 首次登录后，Azure AD 会为应用程序提供用户名和密码。 基于密码的 SSO 允许使用 Web 浏览器扩展插件或移动应用安全存储和重放应用程序的密码。 此选项使用应用程序提供的现有登录过程，允许管理员管理密码，不需要用户知道密码。

- 链接 - 在一段时间内迁移应用程序时，链接登录可提供一致的用户体验。 如果要将应用程序迁移到 Azure AD，可以使用基于链接的 SSO 快速地发布指向要迁移的所有应用程序的链接。 用户可以在“我的应用”或 Microsoft 365 门户中查找所有链接。

    用户向链接的应用程序进行身份验证后，需要先创建帐户，然后才能为用户提供单一登录访问权限。 预配此帐户可以自动完成，也可以由管理员手动完成。 不能对链接的应用程序应用条件访问策略或多重身份验证，因为链接的应用程序不通过 Azure AD 提供单一登录功能。 配置链接应用程序时，只是添加了一个用于启动应用程序的链接。

- 禁用 - 禁用 SSO 后，应用程序将不再可使用它。 已禁用单一登录时，用户可能需要进行两次身份验证。 首先，用户向 Azure AD 进行身份验证，然后他们再登录到应用程序。

    在以下情况下禁用 SSO：

    - 尚未准备好将此应用程序与 Azure AD 单一登录集成
    - 要测试应用程序的其他方面
    - 本地应用程序不要求用户进行身份验证，但你希望用户进行身份验证。 禁用 SSO 后，用户需要进行身份验证。

    如果已将应用程序配置为 SP 发起的基于 SAML 的单一登录，并将 SSO 模式更改为禁用，则不会阻止用户在 MyApps 门户外对应用程序进行登录。 若要实现此目的，需要[禁用用户登录功能](disable-user-sign-in-portal.md)。

## <a name="plan-sso-deployment"></a>规划 SSO 部署

Web 应用程序由各种公司托管，并以服务的形式提供。 Web 应用程序的一些常见示例包括 Microsoft 365、GitHub 和 Salesforce 以及成千上万个其他应用程序。 用户使用计算机上的 Web 浏览器访问 Web 应用程序。 通过单一登录，用户可以在不同的 Web 应用程序之间导航，而无需多次登录。

如何实现 SSO 取决于托管应用程序的位置。 由于路由网络流量来访问应用程序的方式不同，托管位置非常重要。 用户无需使用 Internet 访问托管在本地网络上的本地应用程序。 如果应用程序托管在云中，则用户需要 Internet 才能使用该应用程序。 云托管应用程序也称为软件即服务 (SaaS) 应用程序。

对于云应用程序，请使用联合身份验证协议。 还可以对本地应用程序使用单一登录。 你可以使用应用程序代理为本地应用程序配置访问权限。 有关详细信息，请参阅通过 Azure AD 应用程序代理远程访问本地应用程序。

## <a name="my-apps"></a>我的应用

如果你是应用程序用户，则你可能并不关心 SSO 的详细信息。 你只想使用能提高工作效率而无需频繁输入密码的应用程序。 可以在“我的应用门户”中找到并管理你的应用程序。 

## <a name="next-steps"></a>后续步骤

- [启用单一登录](add-application-portal-setup-sso.md)
