---
title: 支持在开发的应用中通过 FIDO2 密钥进行无密码身份验证 |Microsoft
titleSuffix: Microsoft identity platform
description: 本部署指南介绍了如何在开发的应用程序中通过 FIDO2 安全密钥支持无密码 authentication
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 1/29/2021
ms.author: nichola
ms.custom: aaddev
ms.openlocfilehash: 5abece0e272d4b72ba6f787ad44b091df5d45226
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417063"
---
# <a name="support-passwordless-authentication-with-fido2-keys-in-apps-you-develop"></a>支持在开发的应用中通过 FIDO2 密钥进行无密码身份验证

这些配置和最佳实践可帮助你避免阻止 [FIDO2 无密码 authentication](../../active-directory/authentication/concept-authentication-passwordless.md) 向应用程序的用户提供的常见方案。

## <a name="general-best-practices"></a>一般最佳实践

### <a name="domain-hints"></a>域提示

不要使用域提示来绕过 [主领域发现](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)。 此功能旨在使登录更简单，但联合标识提供程序可能不支持无密码 authentication。

### <a name="requiring-specific-credentials"></a>需要特定凭据

如果使用的是 SAML，请不要 [使用 RequestedAuthnContext 元素](single-sign-on-saml-protocol.md#requestauthncontext)指定需要密码。

RequestedAuthnContext 元素是可选的，因此，若要解决此问题，可以将其从 SAML 身份验证请求中删除。 这是一个一般的最佳实践，因为使用此元素还可以阻止其他身份验证选项（如多重身份验证）正常工作。

### <a name="using-the-most-recently-used-authentication-method"></a>使用最近使用过的身份验证方法

用户最近使用的登录方法将首先显示给他们。 当用户认为必须使用第一个选项时，这可能会导致混淆。 不过，他们可以选择其他选项，方法是选择 "其他登录方式"，如下所示。

:::image type="content" source="./media/support-fido2-authentication/most-recently-used-method.png" alt-text="用户身份验证体验的图像突出显示了按钮，用户可以通过该按钮更改身份验证方法。":::

## <a name="platform-specific-best-practices"></a>平台特定的最佳实践

### <a name="desktop"></a>桌面型

用于实现身份验证的建议选项如下：

- 使用 Microsoft 身份验证库 (MSAL) 的 .NET 桌面应用程序应使用 Windows 身份验证管理器 (WAM) 。 [GitHub 上记录](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/wam)了此集成及其优点。
- 在嵌入式浏览器中使用 [WebView2](https://docs.microsoft.com/microsoft-edge/webview2/) 支持 FIDO2。
- 使用系统浏览器。 默认情况下，桌面平台的 MSAL 库使用此方法。 你可以在 FIDO2 浏览器兼容性上查阅我们的页面，以确保你使用的浏览器支持 FIDO2 authentication。

### <a name="mobile"></a>移动型

从2020年2月开始，本机 iOS 或 Android 应用当前不支持 FIDO2，但正在开发中。

若要为应用程序准备可用性，并作为常规最佳做法，iOS 和 Android 应用程序应将 MSAL 与默认配置结合使用系统 web 浏览器。

如果未使用 MSAL，则仍应使用系统 web 浏览器进行身份验证。 诸如单一登录和条件性访问等功能依赖于系统 web 浏览器提供的共享 web 图面。 这意味着，使用 [Chrome 自定义选项卡](https://developer.chrome.com/docs/multidevice/android/customtabs/) (Android) 或 [通过 Web 服务对用户进行身份验证 | (iOS) 的 Apple 开发人员文档](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service) 。

### <a name="web-and-single-page-apps"></a>网页和单页应用

对于在 web 浏览器中运行的应用程序，FIDO2 无密码 authentication 的可用性将取决于浏览器和平台的组合。 可以参考我们的 [FIDO2 兼容性矩阵](../authentication/fido2-compatibility.md) 来检查你的用户将遇到的组合是否受支持。

## <a name="next-steps"></a>后续步骤

[Azure Active Directory 的无密码 authentication 选项](../../active-directory/authentication/concept-authentication-passwordless.md)
