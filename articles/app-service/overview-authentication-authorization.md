---
title: 身份验证和授权
description: 了解 Azure 应用服务和 Azure Functions 中内置的身份验证和授权支持，并了解它如何保护应用，防止未经授权的访问。
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 07/21/2021
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 304dc18933cc89a19dcc949fd6ff6b33a9be4479
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397348"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Azure 应用服务和 Azure Functions 中的身份验证和授权

Azure 应用服务提供内置的身份验证和授权功能（有时称为“简易身份验证”）。只需在 Web 应用、RESTful API、移动后端和 [Azure Functions](../azure-functions/functions-overview.md) 中编写少量的代码或根本无需编写代码，就能让用户登录和访问数据。 本文介绍应用服务如何帮助简化应用的身份验证和授权。

## <a name="why-use-the-built-in-authentication"></a>为何使用内置身份验证？

你并非必须使用此功能进行身份验证和授权。 可以在所选的 Web 框架中使用捆绑的安全功能，也可以编写自己的实用程序。 但你需要通过最新的安全、协议和浏览器更新来确保解决方案始终是最新状态。

实现安全的身份验证（登录用户）和授权（提供对安全数据的访问）解决方案需要耗费大量精力。 必须确保遵循行业最佳做法和标准，并使实现保持最新。 适用于应用服务和 Azure Functions 的内置身份验证功能通过联合标识提供者提供现成的身份验证，可为你节省时间和精力，使你能够专注于应用程序的其余部分。

- 借助 Azure 应用服务，可以将各种身份验证功能集成到 Web 应用或 API 中，而无需你自行实现。
- 它直接内置于平台中，无需使用任何特定的语言、SDK、安全专业知识甚至任何代码。
- 可与多个登录提供商集成。 例如 Azure AD、Facebook、Google、Twitter。

## <a name="identity-providers"></a>标识提供者

应用服务使用[联合标识](https://en.wikipedia.org/wiki/Federated_identity)，在其中，第三方标识提供者会自动管理用户标识和身份验证流。 默认提供以下五个标识提供者：

| 提供程序 | 登录终结点 | 操作说明指南 |
| - | - | - |
| [Microsoft 标识平台](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` | [应用服务 Microsoft 标识平台登录](configure-authentication-provider-aad.md) |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` | [应用服务 Facebook 登录](configure-authentication-provider-facebook.md) |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` | [应用服务 Google 登录](configure-authentication-provider-google.md) |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` | [应用服务 Twitter 登录](configure-authentication-provider-twitter.md) |
| 任何 [OpenID Connect](https://openid.net/connect/) 提供程序（预览版） | `/.auth/login/<providerName>` | [应用服务 OpenID Connect 登录](configure-authentication-provider-openid-connect.md) |

对其中一个提供程序启用身份验证和授权时，其登录终结点可用于用户身份验证，以及验证来自提供程序的身份验证令牌。 你可以轻松为用户提供任意数量的登录选项。

## <a name="considerations-for-using-built-in-authentication"></a>使用内置身份验证的注意事项

启用此功能会导致向应用程序发出的所有请求自动重定向到 HTTPS，而不管用于实施 HTTPS 的应用服务配置设置如何。 可以通过 V2 配置中的  `requireHttps` 设置来禁用此功能。 但建议一直使用 HTTPS，且应确保不会通过不安全的 HTTP 连接传输安全令牌。

应用服务可用于在限制或不限制对站点内容和 API 的访问的情况下进行身份验证。 若要仅限经过身份验证的用户访问应用，请将“请求未经过身份验证时需执行的操作”设置为使用某个配置的标识提供程序登录。 若要进行身份验证但不限制访问权限，请将“请求未经过身份验证时需执行的操作”设置为“允许匿名请求(无操作)”。

> [!NOTE]
> 应为每个应用注册指定它自己的权限和同意。 避免通过对不同的部署槽使用不同的应用注册，在环境之间共享权限。 测试新代码时，这种做法有助于防止问题影响到生产应用。

## <a name="how-it-works"></a>工作原理

[功能体系结构](#feature-architecture)

[身份验证流](#authentication-flow)

[授权行为](#authorization-behavior)

[令牌存储](#token-store)

[日志记录和跟踪](#logging-and-tracing)

### <a name="feature-architecture"></a>功能体系结构

身份验证和授权中间件组件是平台的一项功能，与应用程序在同一 VM 上运行。 启用后，每个传入的 HTTP 请求将通过此模块，然后由应用程序处理。

:::image type="content" source="media/app-service-authentication-overview/architecture.png" alt-text="一个体系结构图，显示请求被站点沙盒中的进程拦截，该进程与标识提供者进行交互，然后再允许流量发往已部署的站点" lightbox="media/app-service-authentication-overview/architecture.png":::

平台中间件处理应用的几个操作：

- 使用指定的标识提供者对用户和客户端进行身份验证
- 验证、存储和刷新由配置的标识提供者颁发的 OAuth 令牌
- 管理经过身份验证的会话
- 将标识信息插入 HTTP 请求标头

该模块独立于应用程序代码运行，并且可以使用 Azure 资源管理器设置或使用[配置文件](configure-authentication-file-based.md)进行配置。 不需要任何 SDK、特定编程语言，或者对应用程序代码进行更改。 

#### <a name="feature-architecture-on-windows-non-container-deployment"></a>Windows 上的功能体系结构（非容器部署）

身份验证和授权模块在应用程序所在的同一沙盒中作为本机 [IIS 模块](/iis/get-started/introduction-to-iis/iis-modules-overview)运行。 启用后，每个传入的 HTTP 请求将通过此模块，然后由应用程序处理。

#### <a name="feature-architecture-on-linux-and-containers"></a>Linux 和容器上的功能体系结构

身份验证和授权模块在独立于应用程序代码的单独容器中运行。 使用所谓的[代表模式](/azure/architecture/patterns/ambassador)，它与传入的流量交互，以执行与在 Windows 上所执行的类似功能。 由于它不在进程内运行，因此不能与特定的语言框架直接集成；但是，应用所需的相关信息通过使用请求标头进行传递，如下所述。

### <a name="authentication-flow"></a>身份验证流

身份验证流对于所有提供程序是相同的，但根据是否要使用提供程序的 SDK 登录而有所差别：

- 不使用提供程序 SDK：应用程序向应用服务委托联合登录。 浏览器应用通常采用此方案，这可以防止向用户显示提供程序的登录页。 服务器代码管理登录过程，因此，此流也称为“服务器导向流”或“服务器流”。  此方案适用于浏览器应用。 它也适用于使用移动应用客户端 SDK 登录用户的本机应用，因为 SDK 会打开 Web 视图，使用应用服务身份验证将用户登录。
- 使用提供程序 SDK：应用程序手动将用户登录到提供程序，然后将身份验证令牌提交给应用服务进行验证。 无浏览器应用通常采用此方案，这可以防止向用户显示提供程序的登录页。 应用程序代码管理登录过程，因此，此流也称为“客户端导向流”或“客户端流”。  此方案适用于 REST API、[Azure Functions](../azure-functions/functions-overview.md) 和 JavaScript 浏览器客户端，以及在登录过程中需要更高灵活性的浏览器应用。 它还适用于使用提供程序 SDK 登录用户的本机移动应用。

对于应用服务中受信任浏览器应用对应用服务或 [Azure Functions](../azure-functions/functions-overview.md) 中另一 REST API 的调用，可以使用服务器导向流对其进行身份验证。 有关详细信息，请参阅[自定义登录和注销](configure-authentication-customize-sign-in-out.md)。

下表说明了身份验证流的步骤。

| 步骤 | 不使用提供程序 SDK | 使用提供程序 SDK |
| - | - | - |
| 1.将用户登录 | 将客户端重定向到 `/.auth/login/<provider>`。 | 客户端代码直接使用提供程序的 SDK 将用户登录，并接收身份验证令牌。 有关详细信息，请参阅提供程序文档。 |
| 2.身份验证后 | 提供程序将客户端重定向到 `/.auth/login/<provider>/callback`。 | 客户端代码[将来自提供程序的令牌发布到](configure-authentication-customize-sign-in-out.md#client-directed-sign-in)`/.auth/login/<provider>` 进行验证。 |
| 3.建立经过身份验证的会话 | 应用服务将经过身份验证的 Cookie 添加到响应中。 | 应用服务将自身的身份验证令牌返回给客户端代码。 |
| 4.提供经过身份验证的内容 | 客户端在后续请求中包含身份验证 Cookie（由浏览器自动处理）。 | 客户端代码在 `X-ZUMO-AUTH` 标头中提供身份验证令牌（由移动应用客户端 SDK 自动处理）。 |

对于客户端浏览器，应用服务可自动将所有未经身份验证的用户定向到 `/.auth/login/<provider>`。 还可以向用户提供一个或多个 `/.auth/login/<provider>` 链接，让他们使用所选的提供程序登录到你的应用。

<a name="authorization"></a>

### <a name="authorization-behavior"></a>授权行为

在 [Azure 门户](https://portal.azure.com)中，当传入请求未经过身份验证时，可以使用多种行为配置应用服务。 以下标题介绍了选项。

**允许未经身份验证的请求**

此选项将对未经身份验证的流量的授权交给应用程序代码处理。 对于经过身份验证的请求，应用服务还会在 HTTP 标头中一起传递身份验证信息。

使用此选项可以更灵活地处理匿名请求。 例如，可以向用户[提供多个登录提供程序](configure-authentication-customize-sign-in-out.md#use-multiple-sign-in-providers)。 但是，必须编写代码。

**需要身份验证**

此选项将拒绝任何未经身份验证流量流入应用程序。 这个拒绝可以是指向已配置的标识提供者的重定向操作。 在这些情况下，浏览器客户端将重定向到所选提供商的 `/.auth/login/<provider>`。 如果匿名请求来自本机移动应用，则返回的响应为 `HTTP 401 Unauthorized`。 你还可针对所有请求将拒绝配置为 `HTTP 401 Unauthorized` 或 `HTTP 403 Forbidden`。

使用此选项不需要在应用中编写任何身份验证代码。 可以通过检查用户的声明来处理精细授权，例如角色特定的授权（请参阅[访问用户声明](configure-authentication-user-identities.md)）。

> [!CAUTION]
> 以这种方式限制访问适用于对应用的所有调用，对于想要主页公开可用的应用程序来说，这可能是不可取的，就像在许多单页应用程序中一样。

> [!NOTE]
> 默认情况下，Azure AD 租户中的任何用户都可以从 Azure AD 请求应用程序的令牌。 若要仅允许一组定义的用户访问应用，可以[在 Azure AD 中配置应用程序](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)。

### <a name="token-store"></a>令牌存储

应用服务提供内置的令牌存储，这是与 Web 应用、API 或本机移动应用的用户相关联的令牌存储库。 对任何提供程序启用身份验证时，此令牌存储可立即供应用使用。 如果应用程序代码需要代表用户访问这些提供程序中的数据，例如：

- 发布到经过身份验证的用户的 Facebook 时间线
- 使用 Microsoft Graph API 读取用户的公司数据

通常，必须编写代码才能在应用程序中收集、存储和刷新这些令牌。 使用令牌存储，只需在需要令牌时才[检索令牌](configure-authentication-oauth-tokens.md#retrieve-tokens-in-app-code)；当令牌失效时，可以[告知应用服务刷新令牌](configure-authentication-oauth-tokens.md#refresh-auth-tokens)。 

将为经身份验证的会话缓存 ID 令牌、访问令牌和刷新令牌，它们只能由关联的用户访问。  

如果不需要在应用中使用令牌，可以在应用的“身份验证/授权”页中禁用令牌存储。

### <a name="logging-and-tracing"></a>日志记录和跟踪

如果[启用应用程序日志记录](troubleshoot-diagnostic-logs.md)，将在日志文件中直接看到身份验证和授权跟踪。 如果出现意外的身份验证错误，查看现有的应用程序日志即可方便找到所有详细信息。 如果启用[失败请求跟踪](troubleshoot-diagnostic-logs.md)，可以确切地查看身份验证和授权模块在失败请求中发挥的作用。 在跟踪日志中，找到对名为 `EasyAuthModule_32/64` 的模块的引用。

## <a name="more-resources"></a>更多资源

- [操作说明：将应用服务或 Azure Functions 应用配置为使用 Azure AD 登录](configure-authentication-provider-aad.md)
- [自定义登录和注销](configure-authentication-customize-sign-in-out.md)
<!-- - [Authenticate native client apps](configure-authentication-client-apps.md) -->
- [使用 OAuth 令牌和会话](configure-authentication-oauth-tokens.md)
- [访问用户和应用程序声明](configure-authentication-user-identities.md)
- [基于文件的配置](configure-authentication-file-based.md)

示例：
- [教程：向 Azure 应用服务上运行的 Web 应用添加身份验证](scenario-secure-app-authentication-app-service.md)
- [教程：在 Azure 应用服务（Windows 或 Linux）中对用户进行端到端身份验证和授权](tutorial-auth-aad.md)
- [.NET Core 与 Azure 应用服务 EasyAuth 的集成（第三方）](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
- [使用 .NET Core 进行 Azure 应用服务身份验证（第三方）](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)