---
title: 使用自定义策略将 Azure Active Directory B2C 用户界面嵌入应用
titleSuffix: Azure AD B2C
description: 了解如何使用自定义策略将 Azure Active Directory B2C 用户界面嵌入应用
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bed0b1e887fd5735077ef66725b267f42be9bbc1
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114445361"
---
# <a name="embedded-sign-in-experience"></a>嵌入的登录体验

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

为了获得更简单的登录体验，可以避免将用户重定向到单独的登录页或生成弹出窗口。 通过使用内联框架元素 `<iframe>`，可以将 Azure AD B2C 登录用户界面直接嵌入 Web 应用程序。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="web-application-embedded-sign-in"></a>Web 应用程序的嵌入式登录

使用内联框架元素 `<iframe>` 将文档嵌入到 HTML5 网页中。 可以使用 iframe 元素直接将 Azure AD B2C 登录用户界面嵌入到 Web 应用程序中，如以下示例中所示：

![使用悬停 DIV 体验登录](media/embedded-login/login-hovering.png)

使用 iframe 时，请考虑以下内容：

- 嵌入的登录仅支持本地帐户。 大多数社交标识提供者（例如 Google 和 Facebook）阻止其登录页在内联框架中呈现。
- 由于 iframe 中的 Azure AD B2C 会话 cookie 被视为第三方 cookie，因此某些浏览器（例如 incognito 模式下的 Safari 或 Chrome）会阻止或清除这些 cookie，从而导致不期望的用户体验。 若要避免此问题，请确保应用程序域名和 Azure AD B2C 域具有相同的源。 若要使用同一源，请为 Azure AD B2C 租户[启用自定义域](custom-domain.md)，然后使用相同的源配置 Web 应用。 例如，在“https://app.contoso.com”上托管的应用程序具有的源与在“https://login.contoso.com”上运行的 Azure AD B2C 的源相同。

## <a name="prerequisites"></a>先决条件

* 完成 [Active Directory B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)中的步骤。
* 为策略[启用自定义域](custom-domain.md)。

## <a name="configure-your-policy"></a>配置策略

若要允许 Azure AD B2C 用户界面嵌入到 iframe 中，必须在 Azure AD B2C HTTP 响应标头中包含内容安全策略 `Content-Security-Policy` 和框架选项 `X-Frame-Options`。 这些标头允许 Azure AD B2C 用户界面在应用程序域名下运行。

在 [RelyingParty](relyingparty.md) 元素内添加 JourneyFraming 元素。  UserJourneyBehaviors 元素必须跟在 DefaultUserJourney 之后 。 UserJourneyBehaviors 元素应当如以下示例所示：

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

源属性包含 Web 应用程序的 URI。 在 URI 之间添加一个空格。 每个 URI 必须满足以下要求：

- URI 必须受信任并由你的应用程序所有。
- URI 必须使用 https 方案。  
- 必须指定 Web 应用的完整 URI。 不支持通配符。

此外，我们还建议你在应用程序页上分别设置“Content-Security-Policy”和“X-Frame-Options”标头，阻止你自己的域名嵌入到 iframe 中。 这可以减轻与 iframe 的嵌套嵌入相关的更早浏览器的安全问题。

## <a name="adjust-policy-user-interface"></a>调整策略用户界面

通过 Azure AD B2C [用户界面自定义](customize-ui.md)，你可以完全控制呈现给用户的 HTML 和 CSS 内容。 按照使用内容定义自定义 HTML 页面的步骤进行操作。 若要使 Azure AD B2C 用户界面适合 iframe 大小，请提供干净的 HTML 页面，不包含背景和额外空间。  

下面的 CSS 代码隐藏 Azure AD B2C HTML 元素，并调整面板大小以填充 iframe。

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

在某些情况下，可能需要通知应用程序当前正在呈现哪个 Azure AD B2C 页面。 例如，当用户选择注册选项时，你可能希望应用程序通过隐藏用于使用社交帐户登录或调整 iframe 大小的链接来做出响应。

若要向应用程序通知当前呈现的 Azure AD B2C 页面，请[为 JavaScript 启用策略](./javascript-and-page-layout.md)，然后使用 HTML5 发布消息。 以下 JavaScript 代码将向应用发送一条发布消息，其中包含 `signUp`：

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>配置 Web 应用程序

当用户选择“登录”按钮时，[Web 应用](integrate-with-app-code-samples.md#web-apps-and-apis)将生成一个授权请求，该请求会将用户引导至 Azure AD B2C 登录体验。 登录完成后，Azure AD B2C 会将 ID 令牌或授权代码返回到应用程序中配置的重定向 URI。

为了支持嵌入登录，iframe src 属性指向登录控制器（如 `/account/SignUpSignIn`），它将生成授权请求，并将用户重定向到 Azure AD B2C 策略。

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

在应用程序收到并验证 ID 令牌后，授权流已完成，并且应用程序将识别并信任用户。 由于授权流发生在 iframe 内，因此需要重新加载主页。 重新加载页面后，“登录”按钮将更改为“注销”，用户名将显示在 UI 中。  

下面是一个示例，演示登录重定向 URI 如何刷新主页：

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>将带有社交帐户的登录添加到 Web 应用

社交标识提供程序阻止其登录页在内联框架中呈现。 可以为社交帐户使用单独的策略，也可以将单个策略用于使用本地帐户和社交帐户进行登录和注册。 然后，可以使用 `domain_hint` 查询字符串参数。 域提示参数将用户直接转到社交标识提供者的登录页。

在应用程序中，添加“通过社交帐户登录”按钮。 当用户单击某个社交帐户按钮时，该控件需要更改策略名称或设置域提示参数。

<!-- TBD: add a diagram -->

重定向 URI 可以是 iframe 使用的同一重定向 URI。 可以跳过页面重载。

## <a name="configure-a-single-page-application"></a>配置单页应用程序

对于单页应用程序，还需要另一个加载到 iframe 中的“登录”HTML 页。 此登录页承载用于生成授权代码并返回令牌的身份验证库代码。

当单页应用程序需要访问令牌时，使用 JavaScript 代码从包含它的 iframe 和对象获取访问令牌。

> [!NOTE]
> 当前不支持在 iframe 中运行 MSAL 2.0。

下面的代码是一个在主页上运行并调用 iframe 的 JavaScript 代码的示例：

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>后续步骤

请参阅以下相关文章：

- [使用界面自定义](customize-ui.md)
- [RelyingParty](relyingparty.md) 元素引用
- [启用 JavaScript 策略](./javascript-and-page-layout.md)
- [示例代码](integrate-with-app-code-samples.md)

::: zone-end
