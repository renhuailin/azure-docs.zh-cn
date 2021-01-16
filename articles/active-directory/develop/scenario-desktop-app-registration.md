---
title: 注册用于调用 Web API 的桌面应用 - Microsoft 标识平台 | Azure
description: 了解如何构建调用 Web API 的桌面应用（应用注册）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c769fd86f432d135542c1f09b83ea5a01c6ab0a6
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250598"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>调用 Web API 的桌面应用：应用注册

本文涵盖了桌面应用程序的应用注册详细信息。

## <a name="supported-account-types"></a>支持的帐户类型

在桌面应用程序中支持的帐户类型取决于你想要启用的体验。 由于此关系，支持的帐户类型取决于要使用的流。

### <a name="audience-for-interactive-token-acquisition"></a>交互式令牌获取的受众

如果桌面应用程序使用交互式身份验证，则可通过任何[帐户类型](quickstart-register-app.md)将用户登录。

### <a name="audience-for-desktop-app-silent-flows"></a>桌面应用无提示流的受众

- 若要使用集成 Windows 身份验证或用户名和密码，应用程序需要在你自己的租户中将用户登录，例如，当你是业务线应用 (LOB) 开发人员时。 或者，在 Azure Active Directory 组织中，如果你的应用程序是 ISV 方案，也需要在你自己的租户中将用户登录。 Microsoft 个人帐户不支持这些身份验证流。
- 如果你使用传递商家对客户 (B2C) 颁发机构和策略的社交标识来将用户登录，则只能使用交互式和用户-密码身份验证。

## <a name="redirect-uris"></a>重定向 URI

可以在桌面应用程序中使用的重定向 URI 取决于要使用的流。

- 如果使用交互式身份验证或设备代码流，请使用 `https://login.microsoftonline.com/common/oauth2/nativeclient`。 若要实现此配置，请在应用程序的“身份验证”部分中选择相应的 URL。 

  > [!IMPORTANT]
  > `https://login.microsoftonline.com/common/oauth2/nativeclient`建议使用作为重定向 URI 作为最佳安全方案。  如果未指定重定向 URI， `urn:ietf:wg:oauth:2.0:oob` 则默认情况下，MSAL.NET 将不会建议。  此默认值会在下一个主要版本中更新为重大更改。

- 如果针对 macOS 构建本机 Objective-C 或 Swift 应用，请基于应用程序的捆绑包标识符采用以下格式注册重定向 URI：`msauth.<your.app.bundle.id>://auth`。 将 `<your.app.bundle.id>` 替换为应用程序的捆绑包标识符。
- 如果你的应用仅使用集成 Windows 身份验证或用户名和密码，则不需要为应用程序注册重定向 URI。 这些流前往 Microsoft 标识平台 v2.0 终结点并返回。 不会在任何特定 URI 上调用你的应用程序。
- 若要使用[守护程序应用程序](scenario-daemon-overview.md)中使用的客户端凭据流将[设备代码流](scenario-desktop-acquire-token.md#device-code-flow)、[集成 Windows 身份验证](scenario-desktop-acquire-token.md#integrated-windows-authentication)以及[用户名和密码](scenario-desktop-acquire-token.md#username-and-password)与机密的客户端应用程序（它们都不要求重定向 URI）区分开来，需要将应用程序配置为公共客户端应用程序。 为了实现该配置：

    1. 在 " <a href="https://portal.azure.com/" target="_blank">Azure 门户 <span class="docon docon-navigate-external x-hidden-focus"></span></a>中，选择"**应用注册** 中的应用，然后选择 "**身份验证**"。
    1. 在 "**高级设置**  >  " "**允许公用客户端流**  >  **"** 中，选择 **"是"**。

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="在 Azure 门户中的“身份验证”窗格上启用公共客户端设置":::

## <a name="api-permissions"></a>API 权限

桌面应用程序为已登录用户调用 API。 它们需要请求委托的权限。 它们无法请求应用程序权限，权限仅在[守护程序应用程序](scenario-daemon-overview.md)中处理。

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[应用代码配置](scenario-desktop-app-configuration.md)。
