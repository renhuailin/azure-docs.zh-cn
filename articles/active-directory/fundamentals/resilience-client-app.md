---
title: 提高身份验证和授权在你开发的客户端应用程序中的复原能力
titleSuffix: Microsoft identity platform
description: 有关使用 Microsoft 标识平台提高客户端应用程序中的身份验证和授权复原能力的指南
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: 69967035f98d7ec2fcedff173dcf481455014ac2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919362"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>提高身份验证和授权在你开发的客户端应用程序中的复原能力

本部分提供有关在使用 Microsoft 标识平台的客户端应用程序中构建复原能力，并 Azure Active Directory 登录用户并代表这些用户执行操作的指南。

## <a name="use-the-microsoft-authentication-library-msal"></a>使用 Microsoft 身份验证库 (MSAL) 

[ (MSAL) 的 Microsoft 身份验证库](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)是[microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop)的重要组成部分。 它简化和管理获取、管理、缓存和刷新令牌的操作，并使用最佳做法来实现复原。 MSAL 旨在实现安全的解决方案，使开发人员无需担心实现细节。

MSAL 缓存令牌并使用静默令牌采集模式。 它还会自动序列化本机提供安全存储（如 Windows UWP、iOS 和 Android）的平台上的令牌缓存。 当使用[MSAL.NET](https://docs.microsoft.com/azure/active-directory/develop/msal-net-token-cache-serialization)、 [MSAL For Java](https://docs.microsoft.com/azure/active-directory/develop/msal-java-token-cache-serialization)和[Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) [MSAL for Python](https://docs.microsoft.com/azure/active-directory/develop/msal-python-token-cache-serialization)时，开发人员可以自定义序列化行为。

![使用 MSAL 的设备和应用程序的映像来调用 Microsoft 标识](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

当使用 MSAL 时，可以使用以下模式获取令牌缓存、刷新和无提示令牌采集。

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

在某些情况下，MSAL 可以主动刷新令牌。 当 Microsoft 标识发出长时间的令牌时，它可以将信息发送到客户端，以获取刷新令牌 ( "refresh \_ in" ) 的最佳时间。 MSAL 将根据此信息主动刷新令牌。 该应用程序将继续运行，而旧的令牌有效，但会有更长的时间范围，以便进行另一个成功的令牌采集。

### <a name="stay-up-to-date"></a>掌握最新动态

开发人员应具有更新到最新 MSAL 版本的流程。 身份验证是你的应用程序安全的一部分，你的应用程序需要保持最新的安全增强功能，其中包含在新的 MSAL 版本中。 对于持续开发下的库而言，这通常是一种很好的做法，这样做可确保你拥有与应用复原能力有关的最新代码。 随着 Microsoft 标识不断地创新应用程序的复原能力，使用最新的 MSAL 的应用程序将是在这些创新上进行构建的最好方法。

[查看最新的 MSAL.js 版本和发行说明](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[查看最新的 MSAL .NET 版本和发行说明](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[查看最新的 MSAL Python 版本和发行说明](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[查看最新的 MSAL Java 版本和发行说明](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[查看最新的 MSAL iOS 和 macOS 版本和发行说明](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[查看最新的 MSAL Android 版本和发行说明](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[查看最新的 MSAL 角版本和发行说明](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[查看最新的 Microsoft System.web 版本和发行说明](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="if-not-using-msal-use-these-resilient-patterns-for-token-handling"></a>如果不使用 MSAL，请将这些复原模式用于标记处理

通常，使用新式身份验证的应用程序将调用一个终结点来检索对用户进行身份验证或授权应用程序调用受保护的 Api 的令牌。 MSAL 旨在处理身份验证的详细信息并实现多种模式，从而提高此过程的复原能力。 如果选择使用 MSAL 以外的库，请使用本部分中的指南来实施最佳实践。 如果你使用 MSAL，则会免费获取所有这些最佳实践，因为 MSAL 会自动实现它们。

### <a name="cache-tokens"></a>缓存令牌

应用应正确缓存从 Microsoft 标识收到的令牌。 当你的应用程序接收令牌时，包含令牌的 HTTP 响应还会包含一个 "expires_in" 属性，该属性告知应用程序缓存和重新使用令牌的时间长度。 很重要的一点是，应用程序使用 "expires_in" 属性来确定令牌的使用期限。 应用程序永远不能尝试对 API 访问令牌进行解码。

![调用 Microsoft 标识的应用程序，但调用会在运行该应用程序的设备上经历令牌缓存](media/resilience-client-app/token-cache.png)

使用缓存的令牌可防止应用与 Microsoft 标识之间发生不必要的流量，并通过减少令牌获取调用的数量，使应用不会受到令牌采集故障的影响。 缓存令牌还可以提高应用程序的性能，因为应用程序需要在获取令牌时阻止。 你的用户可以在该令牌的生存期内保持登录到你的应用程序。

### <a name="serialize-and-persist-tokens"></a>序列化和保留标记

应用程序应安全地序列化其令牌缓存，以便在应用程序实例之间持久保存令牌。 标记可以在其有效生存期内重复使用。 [刷新令牌](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)（以及、越来越多的 [访问令牌](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)）会发出很多小时。 这一有效时间可跨用户多次启动应用程序。 当你的应用程序启动时，它应检查是否存在可以使用的有效访问令牌或刷新令牌。 这会增加应用的恢复能力和性能，因为它可以避免任何不必要的 Microsoft 标识调用。

![调用 Microsoft 标识的应用程序，但调用会通过令牌缓存以及运行应用程序的设备上的令牌存储](media/resilience-client-app/token-store.png)

永久性令牌存储应进行访问控制，并加密为拥有的用户或进程标识。 在移动、Windows 和 Mac 等平台上，开发人员应利用内置功能来存储凭据。

### <a name="acquire-tokens-silently"></a>以静默方式获取令牌

对用户进行身份验证或检索授权以调用 API 的过程中，可能需要在 Microsoft 标识中执行多个步骤。 例如，当用户首次登录时，可能需要输入凭据并通过短信执行多重身份验证。 每个步骤都在提供该服务的资源上添加了依赖项。 用户的最佳体验，以及依赖项最低的体验是尝试在请求用户交互之前以无提示方式获取令牌，以避免这些额外的步骤。

![显示 Microsoft 标识中的各种服务可能需要运行才能完成身份验证或授权用户的关系图](media/resilience-client-app/external-dependencies.png)

以静默方式获取令牌将从应用的令牌缓存中使用有效的令牌开始。 如果没有可用的有效令牌，应用应尝试使用刷新令牌（如果可用）和令牌终结点获取令牌。 如果这些选项都不可用，则应用应使用 "prompt = none" 参数获取令牌。 这将使用授权终结点，但不会向用户显示任何用户界面。 如果 Microsoft 标识可以提供应用程序的令牌，而不与用户交互，则会出现这种情况。 如果这些方法都不能生成令牌，则用户需要以交互方式重新进行身份验证。

> [!NOTE]
> 通常情况下，应用程序应避免使用 "登录" 和 "同意" 等提示，因为它们将强制用户交互，即使不需要交互也是如此。

## <a name="handle-service-responses-properly"></a>正确处理服务响应

当应用程序应处理所有错误响应时，某些响应会影响复原能力。 如果你的应用程序收到 HTTP 429 响应代码，请求过多，Microsoft 标识会阻止你的请求。 如果应用继续发出太多请求，则会继续中止，阻止应用接收令牌。 应用程序不应再次尝试获取令牌，直到在 Retry-After 响应字段中经过一段时间（以秒为单位）。 接收429响应通常表示应用程序未缓存并正确地使用令牌。 开发人员应查看如何缓存令牌并在应用程序中重复使用。

当应用程序收到 HTTP 5xx 响应代码时，应用程序不得进入快速重试循环。 当存在时，应用程序应遵循与429响应相同的 Retry-After 处理方式。 如果响应未提供 Retry-After 标头，我们建议在响应后，使用第一次重试至少5秒来实现指数回退重试。

当请求超时时，应用程序不应立即重试。 在响应后，使用第一次重试至少5秒来实现指数回退重试。

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>评估用于检索与授权相关的信息的选项

许多应用程序和 Api 都需要有关用户的特定信息来做出授权决策。 应用程序可以使用几种方法来获取此信息。 每种方法都有其优点和缺点。 开发人员应权衡这些策略，以确定哪种策略最适合其应用中的复原能力。

### <a name="tokens"></a>令牌

标识 (ID) 令牌和访问令牌包含提供有关主题的信息的标准声明。 [Microsoft 标识平台 ID 令牌](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)和[microsoft 标识平台访问令牌中对](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)这些信息进行了介绍。 如果你的应用程序所需的信息已在令牌中，则检索该数据的最有效方法是使用令牌声明，因为这样可以节省额外网络调用的开销，以单独检索信息。 更少的网络调用意味着应用程序的总体恢复性更高。

> [!NOTE]
> 某些应用程序调用用户信息终结点来检索有关已进行身份验证的用户的声明。 应用可以接收的 ID 令牌中提供的信息是它可以从 UserInfo 终结点获取的信息的超集。 应用应使用 ID 令牌来获取有关用户的信息，而不是调用用户信息终结点。

应用开发人员可以使用 [可选声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)来补充标准令牌声明。 一个常见的可选声明是 [组](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-groups-optional-claims)。 有多种方法可添加组声明。 "应用程序组" 选项仅包括分配给应用程序的组。 "所有" 或 "安全组" 选项包括同一租户中的所有应用的组，可以将多个组添加到令牌中。 务必在您的情况下评估效果，因为它可能会使令牌膨胀，甚至需要额外的调用来获取组的完整列表，从而可能会使令牌中的用户获得更高的效率。

可以改为使用并包括应用角色，而不是使用令牌中的组。 开发人员可以使用门户或 Api 为其应用程序和 Api 定义 [应用程序角色](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) ，客户可以从其目录中管理应用程序和 api。 然后，IT 专业人员可以将角色分配给不同的用户和组，以控制谁有权访问哪些内容和功能。 当对应用程序或 API 发出令牌时，分配给用户的角色将在令牌中的角色声明中可用。 直接在令牌中获取此信息可节省额外的 Api 调用。

最后，IT 管理员还可以基于租户中的特定信息添加声明。 例如，企业可以拥有一个具有企业特定用户 ID 的扩展。

在所有情况下，将目录中的信息直接添加到令牌可以提高应用程序的依赖关系的数量，从而提高应用程序的复原能力。 另一方面，它不会解决无法获取令牌的任何复原问题。 只应为应用程序的主要方案添加可选声明。 如果应用只需要管理员功能的信息，则应用程序最好只在需要时获取该信息。

### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph 提供了一个统一的 API 终结点，用于访问描述组织中的工作效率、标识和安全性模式的 Microsoft 365 数据。 使用 Microsoft Graph 的应用程序可能会跨 Microsoft 365 使用任何信息来做出授权决策。

应用只需要一个令牌即可访问所有 Microsoft 365。 这比使用特定于 Microsoft 365 组件（如 Microsoft Exchange 或 Microsoft SharePoint）（其中需要多个标记）的旧 Api 更具弹性。

使用 Microsoft Graph Api 时，我们建议你使用 [Microsoft Graph 的 SDK](https://docs.microsoft.com/graph/sdks/sdks-overview)。 Microsoft Graph Sdk 旨在简化访问 Microsoft Graph 的高质量、高效且可复原的应用程序。

对于授权决策，开发人员应考虑何时使用令牌中提供的声明作为某些 Microsoft Graph 调用的替代方法。 如上所述，开发人员可以在其令牌中请求组、应用角色和可选声明。 在恢复能力方面，使用授权 Microsoft Graph 需要额外的网络调用，该调用依赖于 Microsoft 标识 (获取用于访问 Microsoft Graph) 和 Microsoft Graph 本身的令牌。 但是，如果你的应用程序已依赖 Microsoft Graph 作为其数据层，则依赖于授权的关系图不会带来额外的风险。

## <a name="use-broker-authentication-on-mobile-devices"></a>在移动设备上使用 broker 身份验证

在移动设备上，使用诸如 Microsoft Authenticator 的身份验证代理可以提高复原能力。 Broker 增加了与其他选项（如系统浏览器或嵌入式 Web 视图）提供的优势以上的好处。 身份验证代理可以利用 [主刷新令牌](https://docs.microsoft.com/azure/active-directory/devices/concept-primary-refresh-token) (PRT) ，其中包含有关用户和设备的声明，并可用于获取身份验证令牌，以便从设备访问其他应用程序。 当使用 PRT 请求访问应用程序时，它的设备和 MFA 声明受 Azure AD 信任。 这样可以避免再次对设备进行身份验证，从而提高了复原能力。 对于同一设备上的多个 MFA 提示，用户不会遇到质询，因此降低了对外部服务的依赖关系并改善了用户体验，从而提高了复原能力。

![调用 Microsoft 标识的应用程序，但调用会在运行应用程序的设备上通过令牌缓存以及令牌存储和身份验证代理](media/resilience-client-app/authentication-broker.png)

MSAL 自动支持 Broker 身份验证。 可以在以下页上找到有关使用中转身份验证的详细信息：

- [在 macOS 和 iOS 上配置 SSO](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-macos-ios#sso-through-authentication-broker-on-ios)
- [如何使用 MSAL 在 Android 上启用跨应用 SSO](https://docs.microsoft.com/azure/active-directory/develop/msal-android-single-sign-on)

## <a name="adopt-continuous-access-evaluation"></a>采用持续访问评估

[持续访问评估 (CAE) ](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) 是一项最新开发，它可以提高应用程序的安全性，并使用长期的令牌提高恢复能力。 CAE 是在 OpenID Foundation 的共享信号和事件工作组中开发的新兴行业标准。 使用 CAE，可以根据 [关键事件](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation#critical-event-evaluation) 和 [策略评估](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation#conditional-access-policy-evaluation-preview)撤销访问令牌，而不是依赖短令牌生存期。 对于某些资源 Api，由于风险和策略是实时评估的，CAE 可以将令牌生存期大幅提高到28小时。 当资源 Api 和应用程序采用 CAE 时，Microsoft 标识将能够颁发可吊销并在长时间内有效的访问令牌。 MSAL 将主动刷新这些长时间生存期的令牌。

尽管 CAE 处于早期阶段，但当应用程序使用采纳 CAE) 资源 (Api 时，可以 [立即开发客户端应用程序](../develop/app-resilience-continuous-access-evaluation.md) 。 随着更多的资源采用 CAE，你的应用程序也可以获取这些资源的启用了 CAE 的令牌。 Microsoft Graph API 和 [Microsoft Graph sdk](https://docs.microsoft.com/graph/sdks/sdks-overview)将在2021年初预览 CAE 功能。 如果你想要参加 Microsoft Graph 与 CAE 的公共预览版，可以在此处告诉我们你的兴趣： [https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview) 。

如果你开发资源 Api，我们鼓励你参与 [共享信号和事件 WG](https://openid.net/wg/sse/)。 我们正在使用此组来启用 Microsoft 标识和资源提供程序之间的安全事件共享。

## <a name="next-steps"></a>后续步骤

- [如何在应用程序中使用启用了持续存取评估的 Api](../develop/app-resilience-continuous-access-evaluation.md)
- [为守护程序应用程序构建复原能力](resilience-daemon-app.md)
- [在标识和访问管理基础结构中构建复原能力](resilience-in-infrastructure.md)
- [使用 Azure Active Directory B2C 在客户标识和访问管理中构建复原能力](resilience-b2c.md)