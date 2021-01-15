---
title: 添加标识提供者-Azure Active Directory B2C |Microsoft Docs
description: 了解如何将标识提供程序添加到 Active Directory B2C 租户。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 01/14/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 1665f8f595e2bb9ba2a5f2c8528f85854630ab4f
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216574"
---
# <a name="add-an-identity-provider-to-your-azure-active-directory-b2c-tenant"></a>将标识提供者添加到 Azure Active Directory B2C 租户

可以配置 Azure AD B2C，以允许用户使用外部社交或企业标识提供者 (IdP) 提供的凭据登录到你的应用程序。 Azure AD B2C 支持 Facebook、Microsoft 帐户、Google、Twitter 等外部标识提供程序，以及支持 OAuth 1.0、OAuth 2.0、OpenID Connect 和 SAML 协议的任何标识提供者。

使用外部标识提供者联合，可让使用者通过其现有的社交帐户或企业帐户登录，而不必仅仅出于访问你的应用程序的目的创建一个新帐户。

在注册或登录页上，Azure AD B2C 会提供外部标识提供者的列表，供用户选择用来登录。 用户选择一个外部标识提供者后，将会转到（重定向到）所选提供者的网站，以完成登录过程。 用户成功登录后，将返回到 Azure AD B2C，以便对应用程序中的帐户进行身份验证。

![使用社交帐户 (Facebook) 进行移动登录的示例](media/add-identity-provider/external-idp.png)

可使用 Azure 门户向[用户流](user-flow-overview.md)添加 Azure Active Directory B2C (Azure AD B2C) 支持的标识提供程序。 你还可以将标识提供程序添加到你的 [自定义策略](custom-policy-get-started.md)。

## <a name="select-an-identity-provider"></a>添加标识提供程序

通常在应用程序中只使用一个标识提供程序，但可以视需要添加更多标识提供程序。 以下操作指南文章介绍了如何创建标识提供程序应用程序、如何将标识提供程序添加到租户，以及如何将标识提供者添加到用户流或自定义策略中。

* [AD FS](identity-provider-adfs.md)
* [Amazon](identity-provider-amazon.md)
* [Azure AD（单租户）](identity-provider-azure-ad-single-tenant.md)
* [Azure AD（多租户）](identity-provider-azure-ad-multi-tenant.md)
* [Azure AD B2C](identity-provider-azure-ad-b2c.md)
* [Facebook](identity-provider-facebook.md)
* [一般标识提供者](identity-provider-generic-openid-connect.md)
* [GitHub](identity-provider-github.md)
* [ID.me](identity-provider-id-me.md)
* [Google](identity-provider-google.md)
* [LinkedIn](identity-provider-linkedin.md)
* [Microsoft 帐户](identity-provider-microsoft-account.md)
* [QQ](identity-provider-qq.md)
* [Salesforce](identity-provider-salesforce.md)
* [Salesforce (SAML 协议) ](identity-provider-salesforce-saml.md)
* [Twitter](identity-provider-twitter.md)
* [微信](identity-provider-wechat.md)
* [微博](identity-provider-weibo.md)
