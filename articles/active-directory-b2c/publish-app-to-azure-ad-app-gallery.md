---
title: 将 Azure Active Directory B2C 应用发布到 Azure Active Directory 应用库
description: 了解如何将支持单一登录的 Azure AD B2C 应用列入 Azure Active Directory 应用库中。
titleSuffix: Azure AD B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4413832661ffe2eea6fd48f8337618e628f2094b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723151"
---
# <a name="publish-your-azure-ad-b2c-app-to-the-azure-ad-app-gallery"></a>将 Azure AD B2C 应用发布到 Azure AD 应用库

Azure Active Directory (Azure AD) 应用库是数千个应用的目录。 通过应用库，可以轻松部署和配置单一登录 (SSO) 并自动执行用户设置。 可以在库中找到常用云应用，例如 Workday、ServiceNow 和 Zoom。

本文介绍如何将 Azure Active Directory B2C (Azure AD B2C) 应用发布到 Azure AD 应用库。 应用发布后，它列在客户在向 Azure AD 租户添加应用时可以选择的选项中。

以下是将 Azure AD B2C 应用添加到应用库的一些好处：  

- 你的应用是经过验证的与 Microsoft 的集成。
- 在你的应用与 Azure AD 应用之间启用了 SSO 访问。
- 客户可以通过快速搜索在库中找到你的应用。
- 简化并最小化了应用配置。
- 客户可以获得分步配置教程。
- 客户可以将应用分配给其组织中的各个用户和组。
- 租户管理员可以向应用授予租户范围的管理员同意。

## <a name="sign-in-flow-overview"></a>登录流概述

登录流涉及以下步骤：

1. 用户转到[“我的应用”门户](https://myapps.microsoft.com/)，然后选择你的应用，这将打开应用登录 URL。
1. 应用登录 URL 启动授权请求，并将用户重定向到 Azure AD B2C 授权终结点。
1. 用户选择使用其 Azure AD“公司”帐户进行登录。 Azure AD B2C 将用户引到 Azure AD 授权终结点，在其中他们使用工作帐户进行登录。
1. 如果 Azure AD SSO 会话处于活动状态，Azure AD 会发出访问令牌，但不会提示用户再次登录。 如果 Azure AD 会话过期或无效，则系统会提示用户重新登录。

![登录 OpenID 连接流的关系图。](./media/publish-app-to-azure-ad-app-gallery/app-gallery-sign-in-flow.png)

根据用户的 SSO 会话和 Azure AD 标识设置，系统可能会提示他们执行以下操作：

- 提供其电子邮件地址或电话号码。
- 输入其密码或通过 [Microsoft Authenticator 应用](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)登录。
- 完成多重身份验证。
- 接受同意页。 客户的租户管理员可以[向应用授予租户范围的管理员同意](../active-directory/manage-apps/grant-admin-consent.md)。 授予同意后，不会向用户显示同意页。

成功登录后，Azure AD 将令牌返回到 Azure AD B2C。 Azure AD B2C 验证并读取令牌声明，然后将令牌返回到应用程序。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="step-1-register-your-application-in-azure-ad-b2c"></a>步骤 1：在 Azure AD B2C 中注册应用程序

要使能够通过 Azure AD B2C 登录到应用，请在 Azure AD B2C 目录中注册应用。 注册应用会在应用与 Azure AD B2C 之间建立信任关系。 

如果尚未创建，可以[注册 Web 应用](tutorial-register-applications.md)并[启用 ID 令牌隐式授权](tutorial-register-applications.md#enable-id-token-implicit-grant)。 稍后，你将向 Azure 应用库注册此应用。

## <a name="step-2-set-up-sign-in-for-multitenant-azure-ad"></a>步骤 2：为多租户 Azure AD 设置登录

若要使任何 Azure AD 租户的员工和客户可以使用 Azure AD B2C 进行登录，请遵循[为多租户 Azure AD 设置登录](identity-provider-azure-ad-multi-tenant.md?pivots=b2c-custom-policy)的指南。

## <a name="step-3-prepare-your-app"></a>步骤 3：准备应用

在应用中，复制登录终结点的 URL。 如果使用 [Web 应用程序示例](configure-authentication-sample-web-app.md)，则登录 URL 为 `https://localhost:5001/MicrosoftIdentity/Account/SignIn?`。 此 URL 是 Azure AD 应用库引导用户登录到你的应用的位置。

在生产环境中，应用注册重定向 URI 通常是运行应用的可公开访问的终结点，比如 `https://woodgrovedemo.com/Account/SignIn`。 回复 URL 必须以 `https` 开头。

## <a name="step-4-publish-your-azure-ad-b2c-app"></a>步骤 4：发布 Azure AD B2C 应用

最后，将多租户应用添加到 Azure AD 应用库。 遵循[将应用发布到 Azure AD 应用库](../active-directory/develop/v2-howto-app-gallery-listing.md)中的说明。 若要将应用添加到应用库，请执行以下步骤：

1. [创建并发布文档](../active-directory/develop/v2-howto-app-gallery-listing.md#step-5---create-and-publish-documentation)。
1. [提交应用](../active-directory/develop/v2-howto-app-gallery-listing.md#step-6---submit-your-app)并提供以下信息：

    |问题  |应提供的答案  |
    |---------|---------|
    |你希望提交哪种类型的请求?| 选择“在库中列出我的应用程序”。|
    |在库中列出应用程序时，希望启用哪些功能？ | 选择“联合 SSO (SAML、WS-Fed 和 OpenID Connect)”。 | 
    | 选择应用程序联合身份验证协议| 选择“OpenID Connect 与 OAuth 2.0”。 |
    | 应用程序（客户端）ID | 提供 [Azure AD B2C 应用程序](#step-1-register-your-application-in-azure-ad-b2c)的 ID。 |
    | 应用程序登录 URL|提供在[步骤 3. 准备应用](#step-3-prepare-your-app)中配置的应用登录 URL。|
    | 多租户| 请选择“是”。 |
    | | |

## <a name="next-steps"></a>后续步骤

- 了解如何[将应用发布到 Azure AD 应用库](../active-directory/develop/v2-howto-app-gallery-listing.md)。
