---
title: 提高你开发的客户端应用程序中身份验证和授权的复原能力
titleSuffix: Microsoft identity platform
description: 有关使用 Microsoft 标识平台提高客户端应用程序中身份验证和授权复原能力的指南
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: bc3b041e44fad66a4edc6ff34c0e534dc423de86
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226584"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>提高你开发的客户端应用程序中身份验证和授权的复原能力

本部分提供了有关在使用 Microsoft 标识平台和 Azure Active Directory 将用户登录并代表这些用户执行操作的客户端应用程序中构建复原能力的指南。

## <a name="use-the-microsoft-authentication-library-msal"></a>使用 Microsoft 身份验证库 (MSAL)

[Microsoft 身份验证库 (MSAL)](../develop/msal-overview.md) 是 [Microsoft 标识平台](../develop/index.yml)的关键部分。 它简化并管理令牌的获取、管理、缓存和刷新，并使用最佳做法来实现复原能力。 MSAL 设计用来提供安全的解决方案，使开发人员无需担心实现细节。

MSAL 缓存令牌并使用无提示令牌获取模式。 它还会自动序列化本身提供安全存储的平台（例如 Windows UWP、iOS 和 Android）上的令牌缓存。 当使用 [Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization)、[MSAL.NET](../develop/msal-net-token-cache-serialization.md)、[MSAL for Java](../develop/msal-java-token-cache-serialization.md) 和 [MSAL for Python](../develop/msal-python-token-cache-serialization.md) 时，开发人员可以自定义序列化行为。

![使用 MSAL 来调用 Microsoft 标识的设备和应用程序的图像](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

在使用 MSAL 时，会自动支持令牌缓存、刷新和无提示获取。 可以使用简单模式来获取新式身份验证所需的令牌。 我们支持很多语言，你可以在[示例](../develop/sample-v2-code.md)页上找到与你的语言和方案相匹配的示例。

## <a name="c"></a>[C#](#tab/csharp)

```csharp
try
{
    result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
    result = await app.AcquireToken(scopes).WithClaims(ex.Claims).ExecuteAsync()
}
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
return myMSALObj.acquireTokenSilent(request).catch(error => {
    console.warn("silent token acquisition fails. acquiring token using redirect");
    if (error instanceof msal.InteractionRequiredAuthError) {
        // fallback to interaction when silent call fails
        return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
            console.log(tokenResponse);

            return tokenResponse;
        }).catch(error => {
            console.error(error);
        });
    } else {
        console.warn(error);
    }
});
```

---

在某些情况下，MSAL 可以主动刷新令牌。 当 Microsoft 标识颁发生存期较长的令牌时，它可以向客户端发送信息来指明刷新令牌的最佳时间（“refresh\_in”）。 MSAL 会根据此信息主动刷新令牌。 当旧令牌有效但会有更大的时间范围来进行另一次成功的令牌获取时，应用将继续运行。

### <a name="stay-up-to-date"></a>掌握最新动态

开发人员应当实施更新到最新 MSAL 版本的流程。 身份验证是应用安全的一部分，你的应用需要与新的 MSAL 版本中包含的安全增强功能保持同步。 对于正在持续开发的库来说，这通常是一个很好的做法，这样做可以确保你拥有与应用复原能力相关的最新代码。 随着 Microsoft 标识不断创新各种方法来使应用具有更强的复原能力，使用最新 MSAL 的应用将是经过最充分的准备来基于这些创新进行构建的应用。

[查看最新的 MSAL.js 版本和发行说明](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[查看最新的 MSAL .NET 版本和发行说明](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[查看最新的 MSAL Python 版本和发行说明](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[查看最新的 MSAL Java 版本和发行说明](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[查看最新的 MSAL iOS 和 macOS 版本和发行说明](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[查看最新的 MSAL Android 版本和发行说明](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[查看最新的 MSAL Angular 版本和发行说明](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[查看最新的 Microsoft.Identity.Web 版本和发行说明](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="use-resilient-patterns-for-token-handling"></a>使用可复原模式进行令牌处理

如果未使用 MSAL，则可以使用这些可复原模式进行令牌处理。 这些最佳做法由 MSAL 库自动实施。 

通常，使用新式身份验证的应用程序会调用一个终结点来检索对用户进行身份验证或授权应用程序调用受保护 API 的令牌。 MSAL 用于处理身份验证的细节并实现多种模式来提高此过程的复原能力。 如果选择使用 MSAL 以外的库，请使用本部分的指南来实施最佳做法。 如果你使用 MSAL，则可以免费获得所有这些最佳做法，因为 MSAL 会自动实现它们。

### <a name="cache-tokens"></a>缓存令牌

应用应当正确缓存从 Microsoft 标识收到的令牌。 当你的应用接收令牌时，包含令牌的 HTTP 响应还会包含一个“expires_in”属性，该属性告知应用程序缓存和重复使用令牌的时间长度。 应用程序必须使用 "expires_in" 属性来确定令牌的使用期限，这一点非常重要。 应用程序绝对不得尝试对 API 访问令牌进行解码。

![应用程序调用 Microsoft 标识，但该调用会通过运行此应用程序的设备上的令牌缓存来进行](media/resilience-client-app/token-cache.png)

使用缓存的令牌可防止你的应用与 Microsoft 标识之间发生不必要的流量，并通过减少令牌获取调用次数使应用不易受到令牌获取失败的影响。 缓存的令牌还可以提高你的应用程序的性能，因为应用因获取令牌而需要进行阻塞的频率会更低。 你的用户可以在该令牌的生存期内保持登录到你的应用程序。

### <a name="serialize-and-persist-tokens"></a>将令牌序列化和持久化

应用会安全地序列化其令牌缓存，以便在应用实例之间持久保存令牌。 令牌在其有效生存期内可以重复使用。 [刷新令牌](../develop/v2-oauth2-auth-code-flow.md#refresh-the-access-token)，[访问令牌](../develop/access-tokens.md)在颁发后就可以使用越来越长的时间。 在此有效时间内还允许用户多次启动你的应用程序。 当应用启动时，它会检查是否存在可以使用的有效访问令牌或刷新令牌。 这会增加应用的复原能力和性能，因为这可以避免任何不必要的 Microsoft 标识调用。

![应用程序调用 Microsoft 标识，但该调用会通过运行此应用程序的设备上的令牌缓存和令牌存储来进行](media/resilience-client-app/token-store.png)

持久性令牌存储应进行访问控制，并进行加密以仅供所有者用户或进程标识访问。 在诸如移动平台、Windows 平台和 Mac 平台之类的平台上，开发人员应当利用内置功能来存储凭据。

### <a name="acquire-tokens-silently"></a>以无提示方式获取令牌

对用户进行身份验证或检索授权以调用 API 的过程中，可能需要在 Microsoft 标识中执行多个步骤。 例如，当用户首次登录时，可能需要输入凭据并通过短信执行多重身份验证。 每个步骤都会增加对提供该服务的资源的依赖性。 用户的最佳体验以及依赖性最少的体验是尝试在请求用户交互之前以无提示方式获取令牌，以避免这些额外的步骤。

![此图显示了 Microsoft 标识中可能需要运行以完成对用户的身份验证或授权过程的各种服务](media/resilience-client-app/external-dependencies.png)

以静默方式获取令牌首先会使用来自应用的令牌缓存的有效令牌。 如果没有可用的有效令牌，则应用会尝试使用刷新令牌（如果有）和令牌终结点来获取令牌。 如果这些选项都不可用，则应用会使用“prompt = none”参数来获取令牌。 这将使用授权终结点，但不会向用户显示任何 UI。 如果 Microsoft 标识可以在不与用户交互的情况下提供应用的令牌，则它会这样做。 如果这些方法都不能生成令牌，则用户需要以交互方式重新进行身份验证。

> [!NOTE]
> 通常情况下，应用应避免使用“登录”和“同意”之类的提示，因为它们会强制实施用户交互，即使不需要交互也是如此。

## <a name="handle-service-responses-properly"></a>正确处理服务响应

当应用程序应当处理所有错误响应时，某些响应可能会影响复原能力。 如果你的应用程序收到 HTTP 429 响应代码（请求过多），则 Microsoft 标识会限制你的请求。 如果你的应用继续发出太多请求，则会继续限制你的应用，阻止它接收令牌。 你的应用程序不应再次尝试获取令牌，直至经过 Retry-After 响应字段中的时间（以秒为单位）。 收到 429 响应通常表示应用程序未正确缓存并重复使用令牌。 开发人员应当查看令牌在应用程序中是如何缓存并重复使用的。

应用程序在收到 HTTP 5xx 响应代码时不得进入快速重试循环。 如果收到 HTTP 5xx 响应代码，应用程序应遵循与 429 响应相同的 Retry-After 处理。 如果响应未提供 Retry-After 标头，我们建议实施指数退避重试，第一次重试至少应比响应晚 5 秒。

当请求超时时，应用程序不应立即重试。 实施指数退避重试，第一次重试至少应比响应晚 5 秒。

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>评估用于检索授权相关信息的选项

许多应用程序和 API 都需要关于用户的特定信息来做出授权决策。 应用程序可以使用几种方法来获取此信息。 每一种方法都有其优缺点。 开发人员应权衡这些方法，以确定哪种方法最适合其应用中的复原能力。

### <a name="tokens"></a>令牌

标识 (ID) 令牌和访问令牌包含标准声明，这些声明提供关于主题的信息。 [Microsoft 标识平台 ID 令牌](../develop/id-tokens.md)和 [Microsoft 标识平台访问令牌](../develop/access-tokens.md)中介绍了这些令牌。 如果你的应用需要的信息已在令牌中，则检索该数据的最有效方法是使用令牌声明，因为这样不需要单独检索该信息，从而节省额外网络调用的开销。 网络调用越少意味着应用程序的总体复原能力越高。

> [!NOTE]
> 某些应用程序调用 UserInfo 终结点来检索关于已进行身份验证的用户的声明。 应用可以接收的 ID 令牌中提供的信息是它可以从 UserInfo 终结点获取的信息的超集。 你的应用应使用 ID 令牌来获取关于用户的信息，而不是调用 UserInfo 终结点。

应用开发人员可以使用[可选声明](../develop/active-directory-optional-claims.md)来补充标准令牌声明。 一个常见的可选声明是 [groups](../develop/active-directory-optional-claims.md#configuring-groups-optional-claims)。 可以通过若干种方式来添加组声明。 “ApplicationGroup”选项仅包括分配给应用程序的组。 “All”或“SecurityGroup”选项包括同一租户的所有应用中的组，这些选项可用来向令牌中添加许多组。 请务必在你的案例中评估影响，因为它可能导致令牌膨胀甚至需要额外的调用来获取组的完整列表，从而可能抵消通过在令牌中请求组而提高的效率。

你可以不在令牌中使用组，而是改为使用并包括应用角色。 开发人员可以为其应用和 API 定义[应用角色](../develop/howto-add-app-roles-in-azure-ad-apps.md)，客户可以使用门户或 API 从其目录管理这些角色。 然后，IT 专业人员可以将角色分配给不同的用户和组，以便控制特定用户对特定内容和功能的访问。 当为应用程序或 API 颁发令牌时，分配给用户的角色将出现在令牌的角色声明中。 直接在令牌中获取此信息可避免额外的 API 调用。

最后，IT 管理员还可以基于租户中的特定信息添加声明。 例如，可以让企业的一个扩展使用特定于企业的用户 ID。

在所有情况下，将目录中的信息直接添加到令牌都可以提高效率，并且可以通过减少应用具有的依赖项的数目来提高应用复原能力。 另一方面，它不能解决因无法获取令牌而导致的任何复原能力问题。 你只应为应用程序的主要场景添加可选声明。 如果应用只需要管理员功能的信息，则最好只让应用程序根据需要获取该信息。

### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph 提供了一个统一的 API 终结点，用于访问描述组织中的工作效率模式、标识模式和安全性模式的 Microsoft 365 数据。 使用 Microsoft Graph 的应用程序可能会使用 Microsoft 365 中的任何信息来做出授权决策。

应用只需要一个令牌即可访问所有 Microsoft 365。 这比使用特定于 Microsoft 365 组件（例如 Microsoft Exchange 或 Microsoft SharePoint）的旧 API（需要多个令牌）时的复原能力更强。

使用 Microsoft Graph API 时，建议使用 [Microsoft Graph SDK](/graph/sdks/sdks-overview)。 Microsoft Graph SDK 设计用来简化可访问 Microsoft Graph 的优质、高效且可复原的应用程序的构建。

对于授权决策，开发人员应考虑何时使用令牌中提供的声明作为某些 Microsoft Graph 调用的替代方法。 如上所述，开发人员可以在其令牌中请求组、应用角色和可选声明。 就复原能力而言，使用 Microsoft Graph 进行授权需要额外的网络调用，这些调用依赖于 Microsoft 标识（用于获取访问 Microsoft Graph 所需的令牌）以及 Microsoft Graph 本身。 但是，如果你的应用程序已依赖 Microsoft Graph 作为其数据层，则依赖 Graph 进行授权不会带来额外的风险。

## <a name="use-broker-authentication-on-mobile-devices"></a>在移动设备上使用代理身份验证

在移动设备上，使用身份验证代理（如 Microsoft Authenticator）将会改进复原能力。 该代理在随其他选项（如系统浏览器或嵌入式 Web 视图）提供的功能基础上增加了优点。 身份验证代理可以利用包含有关用户和设备的声明的[主刷新令牌](../devices/concept-primary-refresh-token.md) (PRT)，并且可用于获取身份验证令牌，以便从该设备访问其他应用程序。 在使用 PRT 来请求访问某个应用程序时，Azure AD 会信任该令牌的设备和 MFA 声明。 这样就会避免再次对该设备进行身份验证，从而提升复原能力。 用户在同一设备上不会收到多个 MFA 提示，这样就会因降低对外部服务的依赖而提升复原能力，并且会改善用户体验。

![某个应用程序调用 Microsoft 标识，但该调用通过运行该应用程序的设备上的令牌缓存以及令牌存储和身份验证代理来进行](media/resilience-client-app/authentication-broker.png)

MSAL 自动支持代理身份验证。 可以在以下页上找到有关使用中转身份验证的详细信息：

- [在 macOS 和 iOS 上配置 SSO](../develop/single-sign-on-macos-ios.md#sso-through-authentication-broker-on-ios)
- [如何使用 MSAL 在 Android 上启用跨应用 SSO](../develop/msal-android-single-sign-on.md)

## <a name="adopt-continuous-access-evaluation"></a>采用连续访问评估

[连续访问评估 (CAE)](../conditional-access/concept-continuous-access-evaluation.md) 是最新开发的一项功能，它可以使用长期生存的令牌提高应用程序的安全性和复原能力。 CAE 是在 OpenID Foundation 的共享信号和事件工作组中开发的新兴行业标准。 使用 CAE，可以根据[严重事件](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation)和[策略评估](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview)而不是依赖令牌生存期短的特性来撤销访问令牌。 对于某些资源 API，由于风险和策略是实时评估的，因此 CAE 实际上可以将令牌生存期提高到最多 28 小时。 当资源 API 和应用程序采用 CAE 时，Microsoft 标识将能够颁发可撤销并在长时间内有效的访问令牌。 MSAL 会主动刷新这些生存期长的令牌。

尽管 CAE 处于早期阶段，但目前可以[开发将从 CAE 受益的客户端应用程序](../develop/app-resilience-continuous-access-evaluation.md)，前提是应用程序使用的资源 (API) 采用 CAE。 随着越来越多的资源采用 CAE，应用程序也将能够为这些资源获取启用了 CAE 的令牌。 Microsoft Graph API 和 [Microsoft Graph SDK](/graph/sdks/sdks-overview) 将在 2021 年初提供 CAE 功能预览版。 如果你想参与包含 CAE 的 Microsoft Graph 的公共预览版，可以在此处告知我们你感兴趣：[https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview)。

如果你开发资源 API，我们鼓励你参与[共享信号和事件 WG](https://openid.net/wg/sse/)。 我们正在与此工作组合作，目的是在 Microsoft 标识与资源提供程序之间实现安全事件共享。

## <a name="next-steps"></a>后续步骤

- [如何在应用程序中使用启用了连续访问评估的 API](../develop/app-resilience-continuous-access-evaluation.md)
- [为守护程序应用程序构建复原能力](resilience-daemon-app.md)
- [在标识和访问管理基础结构中构建复原能力](resilience-in-infrastructure.md)
- [在 CIAM 系统中构建复原能力](resilience-b2c.md)
