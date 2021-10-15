---
title: 注册调用 Web API 的桌面应用 | Azure
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: d196fa6eabd0a6f9fc08b8d1b1d60e649d02ee1b
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234958"
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

通过在 Azure 门户中“应用注册”中为应用[配置平台设置](quickstart-register-app.md#add-a-redirect-uri)，来指定应用的重定向 URI。

- 对于使用交互式身份验证的应用：

  - 使用嵌入式浏览器的应用：`https://login.microsoftonline.com/common/oauth2/nativeclient`（注意：如果应用会弹出一个通常不包含地址栏的窗口，则它使用的是“嵌入式浏览器”。）
  - 使用系统浏览器的应用：`http://localhost`（注意：如果应用会使用系统的默认浏览器（例如，Edge、Chrome、Firefox 等）来访问 Microsoft 登录门户，则它使用的是“系统浏览器”。）
  
  > [!IMPORTANT]
  > 作为安全最佳做法，我们建议显式设置 `https://login.microsoftonline.com/common/oauth2/nativeclient` 或 `http://localhost` 作为重定向 URI。 如果未指定任何其他重定向 URI（不建议这样做），某些身份验证库（如 MSAL.NET）将使用默认值 `urn:ietf:wg:oauth:2.0:oob`。 在下一个主要版本中，此默认值将作为中断性变更进行更新。

- 如果针对 macOS 构建本机 Objective-C 或 Swift 应用，请基于应用程序的捆绑包标识符采用以下格式注册重定向 URI：`msauth.<your.app.bundle.id>://auth`。 将 `<your.app.bundle.id>` 替换为应用程序的捆绑包标识符。
- 如果生成 Node.js Electron 应用，请使用自定义文件协议，而不是常规 web (https://) 重定向 URI，以便处理授权流的重定向步骤，例如 `msal://redirect`。 自定义文件协议名称不能太明显而容易被猜到，应遵循[适用于本机应用的 Oauth 2.0 规范](https://tools.ietf.org/html/rfc8252#section-7.1)中的建议。
- 如果你的应用仅使用集成 Windows 身份验证或用户名和密码，则不需要为应用程序注册重定向 URI。 这些流前往 Microsoft 标识平台 v2.0 终结点并返回。 不会在任何特定 URI 上调用你的应用程序。
- 若要使用[守护程序应用程序](scenario-daemon-overview.md)中使用的客户端凭据流将[设备代码流](scenario-desktop-acquire-token-device-code-flow.md)、[集成 Windows 身份验证](scenario-desktop-acquire-token-integrated-windows-authentication.md)以及[用户名和密码](scenario-desktop-acquire-token-username-password.md)与机密的客户端应用程序（它们都不要求重定向 URI）区分开来，请将应用程序配置为公共客户端应用程序。 为了实现该配置：

    1. 在 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>中，选择“应用注册”中的应用，然后选择“身份验证” 。
    1. 在“高级设置” > “允许公共客户端流” > “启用以下移动和桌面流:”中，选择“是”。

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="在 Azure 门户中的“身份验证”窗格上启用公共客户端设置":::

## <a name="api-permissions"></a>API 权限

桌面应用程序为已登录用户调用 API。 它们需要请求委托的权限。 它们无法请求应用程序权限，权限仅在[守护程序应用程序](scenario-daemon-overview.md)中处理。

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[应用代码配置](scenario-desktop-app-configuration.md)。
