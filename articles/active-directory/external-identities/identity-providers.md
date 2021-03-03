---
title: 外部标识的标识提供者 - Azure AD
description: 了解如何使用 Azure AD 作为默认标识提供者与外部用户共享。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42ed42a1fc9a2750cc928b5fd03eb4b32f770276
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644079"
---
# <a name="identity-providers-for-external-identities"></a>外部标识的标识提供者

标识提供程序创建、维护和管理标识信息，同时向应用程序提供身份验证服务。 与外部用户共享应用和资源时，Azure AD 是用于共享的默认标识提供者。 这意味着，当你邀请已有 Azure AD 或 Microsoft 帐户的外部用户时，这些用户可以自动登录，而无需你进行进一步的配置。

但是，你可以允许用户使用各种标识提供者登录。

- **Google**：通过 Google 联合身份验证，外部用户可以使用他们自己的 Gmail 帐户登录你的应用，来兑换你发出的邀请。 Google 联合身份验证还可以用于自助注册用户流。
   > [!IMPORTANT]
   > 从 2021 年 1 月 4 日开始，Google 将[弃用 WebView 登录支持](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)。 如果要通过 Gmail 使用 Google 联合身份验证或自助服务注册，则应[测试业务线本机应用程序的兼容性](google-federation.md#deprecation-of-webview-sign-in-support)。

- **Facebook**：生成应用时，可以配置自助注册并启用 Facebook 联合身份验证，这样用户即能够使用他们自己的 Facebook 帐户注册你的应用。 Facebook 只能用于自助注册用户流，当用户兑换你发出的邀请时不能将它用作登录选项。

- **直接联合身份验证**：你还可以设置与任何支持 SAML 或 WS 联合身份验证协议的外部标识提供者的直接联合身份验证。 通过直接联合身份验证，外部用户可以使用他们现有的社交或公司帐户登录你的应用，来兑换你发出的邀请。 
   > [!NOTE]
   > 直接联合身份验证标识提供者不能用于自助注册用户流。


## <a name="how-it-works"></a>工作原理

通过 Azure AD 外部标识自助注册功能，用户可以使用他们的 Azure AD、Google 或 Facebook 帐户注册。 若要在 Azure AD 租户中设置社交标识提供者，需针对每个标识提供者创建一个应用程序并配置凭据。 你将获得一个客户端或应用 ID 和一个客户端或应用机密，然后你可以将其添加到 Azure AD 租户。

将标识提供者添加到 Azure AD 租户后：

- 当你邀请外部用户访问组织中的应用或资源时，外部用户可以使用自己的帐户通过该标识提供者登录。
- 当你为应用启用[自助注册](self-service-sign-up-overview.md)时，外部用户可以使用自己的帐户通过你添加的标识提供者注册你的应用。

> [!NOTE]
> 默认情况下为自助注册启用 Azure AD，这样用户始终可以选择使用 Azure AD 帐户进行注册。

外部用户在兑换你的邀请或注册你的应用时，可以选择通过社交标识提供者进行登录和身份验证：

![屏幕截图，显示带有 Google 和 Facebook 选项的登录屏幕](media/identity-providers/sign-in-with-social-identity.png)

为了获得最佳的登录体验，请尽可能与标识提供者联合，以便你的受邀来宾在访问你的应用时能够获得无缝的登录体验。  

## <a name="next-steps"></a>后续步骤

若要了解如何向应用程序中添加用于登录的标识提供者，请参阅以下文章：

- 向社交标识提供者列表[添加 Google](google-federation.md)
- 向社交标识提供者列表[添加 Facebook](facebook-federation.md)
- 与其标识提供者支持 SAML 2.0 或 WS 联合身份验证协议的任何组织[建立直接联合](direct-federation.md)。 请注意，直接联合不是自助注册用户流的选项。
