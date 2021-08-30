---
title: 外部标识的标识提供者 - Azure AD
description: 了解如何将 Azure AD 用作默认标识提供者，以便与外部用户共享。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f867e0091d792ef88b79f1c1bb7f4272a35e928
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721376"
---
# <a name="identity-providers-for-external-identities"></a>外部标识的标识提供者

标识提供程序创建、维护和管理标识信息，同时向应用程序提供身份验证服务。 与外部用户共享应用和资源时，Azure AD 是用于共享的默认标识提供者。 这意味着，当你邀请已有 Azure AD 或 Microsoft 帐户的外部用户时，这些用户可以自动登录，而无需你进行进一步的配置。

除了 Azure AD 帐户外，外部标识还提供各种标识提供者。

- **Microsoft 帐户**：来宾用户可使用自己的个人 Microsoft 帐户 (MSA) 兑换 B2B 协作邀请。 设置自助式注册用户流时，可将 [Microsoft 帐户](microsoft-account.md)添加为允许的标识提供者之一。 无需其他配置，此标识提供者即可供用户流使用。

- **电子邮件一次性密码**：兑换邀请或访问共享资源时，来宾用户可请求临时代码，该代码将发送到其电子邮件地址。 他们输入此代码后，可以继续登录。 在无法通过其他方式对 B2B 来宾用户进行身份验证时，可使用电子邮件一次性密码功能对其进行身份验证。 设置自助式注册用户流时，可将电子邮件一次性密码添加为允许的标识提供者之一。 需进行一些设置；请参阅[电子邮件一次性密码身份验证](one-time-passcode.md)。

- **Google**：通过 Google 联合身份验证，外部用户可以使用他们自己的 Gmail 帐户登录你的应用，来兑换你发出的邀请。 Google 联合身份验证还可以用于自助注册用户流。 了解如何将 [Google 添加为标识提供者](google-federation.md)。
   > [!IMPORTANT]
   >
   > - 从 2021 年 7 月 12 日开始，如果 Azure AD B2B 客户设置了新的 Google 集成，将其用于自定义应用程序或业务线应用程序的自助注册，则在身份验证转移到系统 Web 视图之前，无法使用 Google 标识进行身份验证。 [了解详细信息](google-federation.md#deprecation-of-web-view-sign-in-support)。
   > - 从 2021 年 9 月 30 日开始，Google 将[弃用嵌入式 Web 视图登录支持](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 如果你的应用使用嵌入式 Web 视图对用户进行身份验证，而你将 Google 联合身份验证与 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 或 Azure AD B2B 配合使用来进行[外部用户邀请](google-federation.md)或自助注册，则 Google Gmail 用户将无法进行身份验证。 [了解详细信息](google-federation.md#deprecation-of-web-view-sign-in-support)。


- **Facebook**：生成应用时，可以配置自助注册并启用 Facebook 联合身份验证，这样用户即能够使用他们自己的 Facebook 帐户注册你的应用。 Facebook 只能用于自助注册用户流，当用户兑换你发出的邀请时不能将它用作登录选项。 了解如何将 [Facebook 添加为标识提供者](facebook-federation.md)。

- SAML/WS-Fed 标识提供者联合身份验证：你还可以设置与任何支持 SAML 或 WS-Fed 协议的外部 IdP 的联合身份验证。 通过 SAML/WS-Fed IdP 联合身份验证，外部用户可以使用他们现有的社交或公司帐户登录你的应用，来兑换你发出的邀请。 参阅如何[设置 SAML/WS-Fed IdP 联合身份验证](direct-federation.md)。
   > [!NOTE]
   > 联合 SAML/WS-Fed IDP 不能用于自助注册用户流。

## <a name="adding-social-identity-providers"></a>添加社交标识提供者

默认情况下为自助注册启用 Azure AD，这样用户始终可以选择使用 Azure AD 帐户进行注册。 但是，可启用其他标识提供者，包括社交标识提供者（如 Google 或 Facebook）。 若要在 Azure AD 租户中设置社交标识提供者，需针对标识提供者创建一个应用程序并配置凭据。 你将获得一个客户端或应用 ID 和一个客户端或应用机密，然后你可以将其添加到 Azure AD 租户。

将标识提供者添加到 Azure AD 租户后：

- 当你邀请外部用户访问组织中的应用或资源时，外部用户可以使用自己的帐户通过该标识提供者登录。
- 当你为应用启用[自助注册](self-service-sign-up-overview.md)时，外部用户可以使用自己的帐户通过你添加的标识提供者注册你的应用。 他们能够从你在注册页上提供的社交标识提供者选项中进行选择：

   ![屏幕截图，显示带有 Google 和 Facebook 选项的登录屏幕](media/identity-providers/sign-in-with-social-identity.png)

为了获得最佳的登录体验，请尽可能与标识提供者联合，以便你的受邀来宾在访问你的应用时能够获得无缝的登录体验。  

## <a name="next-steps"></a>后续步骤

若要了解如何向应用程序中添加用于登录的标识提供者，请参阅以下文章：

- [添加电子邮件一次性密码身份验证](one-time-passcode.md)
- [添加 Google](google-federation.md) 作为允许的社交标识提供者
- [添加 Facebook](facebook-federation.md) 作为允许的社交标识提供者
- 与其标识提供者支持 SAML 2.0 或 WS-Fed 联合身份验证协议的任何组织[建立 SAML/WS-Fed IdP 联合身份验证](direct-federation.md)。 请注意，自助注册用户流无法选择 SAML/WS-Fed IdP 联合身份验证。
