---
title: 支持在自己开发的应用中通过 FIDO2 密钥进行无密码身份验证 | Azure
titleSuffix: Microsoft identity platform
description: 本部署指南介绍了如何支持在自己开发的应用中通过 FIDO2 安全密钥进行无密码身份验证
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 1/29/2021
ms.author: nichola
ms.custom: aaddev
ms.openlocfilehash: f63d7aed75b14f5f008a639d667d8806b233b9fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174592"
---
# <a name="support-passwordless-authentication-with-fido2-keys-in-apps-you-develop"></a>支持在自己开发的应用中通过 FIDO2 密钥进行无密码身份验证

这些配置和最佳做法将帮助你避免导致 [FIDO2 无密码身份验证](../../active-directory/authentication/concept-authentication-passwordless.md)对应用程序用户不可用的常见情况。

## <a name="general-best-practices"></a>一般最佳实践

### <a name="domain-hints"></a>域提示

请勿使用域提示来绕过[主领域发现](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)。 此功能旨在简化登录过程，但联合标识提供程序可能不支持无密码身份验证。

### <a name="requiring-specific-credentials"></a>需要特定凭据

如果使用的是 SAML，请不要[使用 RequestedAuthnContext 元素](single-sign-on-saml-protocol.md#requestauthncontext)指定需要密码。

RequestedAuthnContext 元素是可选的，因此，若要解决此问题，可以将其从 SAML 身份验证请求中删除。 这是一般的最佳做法，因为使用此元素还可能阻止其他身份验证选项（如多重身份验证）正常工作。

### <a name="using-the-most-recently-used-authentication-method"></a>使用最近用过的身份验证方法

用户首先会看到他们最近用过的登录方法。 当用户认为必须使用呈现的第一个选项时，这可能会导致混淆。 不过，他们可以选择其他选项，方法是选择“其他登录方式”，如下所示。

:::image type="content" source="./media/support-fido2-authentication/most-recently-used-method.png" alt-text="突出显示了允许用户更改身份验证方法的按钮的用户身份验证体验的图像。":::

## <a name="platform-specific-best-practices"></a>平台特定的最佳做法

### <a name="desktop"></a>桌面

用于实现身份验证的建议选项如下：

- 使用 Microsoft 身份验证库 (MSAL) 的 .NET 桌面应用程序应使用 Windows 身份验证管理器 (WAM)。 [GitHub 上记录](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/wam)了此集成及其优点。
- 在嵌入式浏览器中使用 [WebView2](/microsoft-edge/webview2/) 支持 FIDO2。
- 使用系统浏览器。 默认情况下，桌面平台的 MSAL 库使用此方法。 你可以查阅我们的页面了解 FIDO2 浏览器兼容性，以确保你使用的浏览器支持 FIDO2 身份验证。

### <a name="mobile"></a>移动

从 2021 年 2 月开始，本机 iOS 或 Android 应用当前不支持 FIDO2，但正在开发中。

为了准备应用程序的可用性，并作为一般的最佳做法，iOS 和 Android 应用程序应使用 MSAL，并且默认使用系统 Web 浏览器。

如果未使用 MSAL，则仍应使用系统 Web 浏览器进行身份验证。 诸如单一登录和条件访问等功能依赖于系统 Web 浏览器提供的共享 Web 图面。 这意味着，使用 [Chrome 自定义选项卡](https://developer.chrome.com/docs/multidevice/android/customtabs/) (Android) 或 [通过 Web 服务对用户进行身份验证 | Apple 开发人员文档](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service) (iOS)。

### <a name="web-and-single-page-apps"></a>Web 和单页应用

对于在 Web 浏览器中运行的应用程序，FIDO2 无密码身份验证的可用性将取决于浏览器和平台的组合。 可以参考我们的 [FIDO2 兼容性矩阵](../authentication/fido2-compatibility.md)来检查你的用户将遇到的组合是否受支持。

## <a name="next-steps"></a>后续步骤

[Azure Active Directory 的无密码身份验证选项](../../active-directory/authentication/concept-authentication-passwordless.md)