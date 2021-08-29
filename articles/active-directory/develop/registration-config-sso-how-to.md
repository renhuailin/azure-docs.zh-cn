---
title: 配置应用程序单一登录
description: 如何为正在使用 Azure AD 开发和注册的自定义应用程序配置单一登录。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, has-adal-ref
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 5fa84215c6393fe1bddee3d72dd93951dee06f31
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428991"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>如何为应用程序配置单一登录

通过适用于 OpenID Connect 的 Azure AD、SAML 2.0 或 WS-Fed 联合时，会自动启用应用中的联合单一登录 (SSO)。 如果与 Azure AD 已有一个现有会话，但是最终用户仍然需要登录，这有可能是应用配置错误。

* 如果使用 ADAL/MSAL，请确保将 **PromptBehavior** 设置为“自动”而不是“始终”。

* 如果生成移动应用，可能需要进行额外配置以启用中转或非中转 SSO。

对于 Android 系统，请参阅[在 Android 中启用跨应用 SSO](../azuread-dev/howto-v1-enable-sso-android.md)。<br>

对于 iOS 系统，请参阅[在 iOS 中启用跨应用 SSO](../azuread-dev/howto-v1-enable-sso-ios.md)。

## <a name="next-steps"></a>后续步骤

[Azure AD SSO](../manage-apps/what-is-single-sign-on.md)<br>

[在 Android 中启用跨应用 SSO](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[在 iOS 中启用跨应用 SSO](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[将应用与 AzureAD 集成](./quickstart-register-app.md)<br>

[Microsoft 标识平台中的权限和许可](./v2-permissions-and-consent.md)<br>

[AzureAD Microsoft Q&A](/answers/topics/azure-active-directory.html)