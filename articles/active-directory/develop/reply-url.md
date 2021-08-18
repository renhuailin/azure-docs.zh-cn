---
title: 重定向 URI（回复 URL）限制 | Azure AD
titleSuffix: Microsoft identity platform
description: 说明 Microsoft 标识平台对重定向 URI（回复 URL）格式强制实施的限制和局限。
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/23/2021
ms.topic: conceptual
ms.subservice: develop
ms.custom: contperf-fy21q4-portal, aaddev
ms.service: active-directory
ms.reviewer: marsma, lenalepa, manrath
ms.openlocfilehash: b9484973e724246db76ccc927437fccf2c4c7be1
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112966457"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>重定向 URI（回复 URL）限制和局限

重定向 URI（或回复 URL）是在为应用成功授权并为其授予授权代码或访问令牌后，授权服务器将用户发送到的位置。 授权服务器将代码或令牌发送到重定向 URI，因此在应用注册过程中注册正确的位置非常重要。

 重定向 URI 存在以下限制：

* 重定向 URI 必须以方案 `https` 开头。 有一些 [localhost 重定向 URI 例外](#localhost-exceptions)。

* 重定向 URI 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 例如，如果应用程序在其路径中包括 `.../abc/response-oidc`，请不要在重定向 URI 中指定 `.../ABC/response-oidc`。 由于 Web 浏览器将路径视为区分大小写，因此在重定向到大小写不匹配的 `.../ABC/response-oidc` URL 时，可能会排除与 `.../abc/response-oidc` 关联的 cookie。

* 不带路径段的重定向 URI 会给响应中的 URI 追加一个尾随斜杠。 例如， https://contoso.com 和 http://localhost:7071 这样的 URI 将会分别以 https://contoso.com/ 和 http://localhost:7071/ 的形式返回。 这只适用于响应模式为查询或片段的情况。

* 包含路径段的重定向 URI 不会追加尾随斜杠。 （例如， https://contoso.com/abc 、 https://contoso.com/abc/response-oidc 将会在响应中按原样使用）

## <a name="maximum-number-of-redirect-uris"></a>重定向 URI 的最大数量

此表显示了可以在 Microsoft 标识平台中添加到应用注册的重定向 URI 的最大数目。

| 正在登录的帐户 | 最大重定向 URI 数 | 说明 |
|--------------------------|---------------------------------|-------------|
| 任何组织的 Azure Active Directory (Azure AD) 租户中的 Microsoft 工作或学校帐户 | 256 | 应用程序清单中的 `signInAudience` 字段设置为 AzureADMyOrg 或 AzureADMultipleOrgs |
| 个人 Microsoft 帐户以及工作和学校帐户 | 100 | 应用程序清单中的 `signInAudience` 字段设置为 AzureADandPersonalMicrosoftAccount |

## <a name="maximum-uri-length"></a>最大 URI 长度

对于要添加到应用注册中的每个重定向 URI，最多可以使用 256 个字符。

## <a name="supported-schemes"></a>支持的方案

Azure Active Directory (Azure AD) 应用程序模型目前同时支持 HTTP 和 HTTPS 方案，这两种方案所针对的应用可以在任何组织的 Azure AD 租户中登录工作或学校帐户。 这些帐户类型由应用程序清单的 `signInAudience` 字段中的 `AzureADMyOrg` 和 `AzureADMultipleOrgs` 值指定。 对于登录个人 Microsoft 帐户 (MSA) 以及工作和学校帐户的应用（即 `signInAudience` 设置为 `AzureADandPersonalMicrosoftAccount`），只允许使用 HTTPS 方案。

若要将具有 HTTP 方案的重定向 URI 添加到用于登录工作帐户或学校帐户的应用注册，请使用 Azure 门户的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)中的应用程序清单编辑器。 虽然可以使用清单编辑器来设置基于 HTTP 的重定向 URI，但我们强烈建议你为重定向 URI 使用 HTTPS 方案。

## <a name="localhost-exceptions"></a>Localhost 例外

[RFC 8252 8.3 节](https://tools.ietf.org/html/rfc8252#section-8.3)和 [7.3](https://tools.ietf.org/html/rfc8252#section-7.3) 节指出，“环回”或“localhost”重定向 URI 有两个特殊的注意事项：

1. `http` URI 方案是可接受的，因为重定向绝不会离开设备。 因此，下面这两个 URI 都是可接受的：
    - `http://localhost/myApp`
    - `https://localhost/myApp`
1. 由于原生应用程序经常需要临时端口范围，因此，在匹配重定向 URI 时会忽略端口组件（例如 `:5001` 或 `:443`）。 因此，下面所有这些 URI 都被视为等效：
    - `http://localhost/MyApp`
    - `http://localhost:1234/MyApp`
    - `http://localhost:5000/MyApp`
    - `http://localhost:8080/MyApp`

从开发的角度来看，这意味着：

* 不要注册多个只有端口不同的重定向 URI。 登录服务器会任意选取一个，并使用与该重定向 URI 关联的行为（例如，不管它是 `web` 类型的、`native` 类型的还是 `spa` 类型的重定向，都会这样做）。

    当你想在同一应用程序注册中使用不同的身份验证流（例如，授权代码授予和隐式流）时，这尤其重要。 若要将正确的响应行为与每个重定向 URI 相关联，登录服务器必须能够区分重定向 URI，在只有端口不同的情况下不能这样做。
* 若要在 localhost 上注册多个重定向 URI，以在开发过程中测试不同的流，请使用 URI 的 path 组件来区分它们。 例如，`http://localhost/MyWebApp` 与 `http://localhost/MyNativeApp` 不匹配。
* 当前不支持 IPv6 环回地址 (`[::1]`)。

#### <a name="prefer-127001-over-localhost"></a>首选 127.0.0.1，而不是 localhost

若要防止应用被错误配置的防火墙或重命名的网络接口中断，请使用重定向 URI 中的 IP 文本环回地址 `127.0.0.1`，而不是使用 `localhost`。 例如，`https://127.0.0.1`。

但不能使用 Azure 门户中“重定向 URI”文本框添加基于环回且使用 `http` 方案的重定向 URI：

:::image type="content" source="media/reply-url/portal-01-no-http-loopback-redirect-uri.png" alt-text="Azure 门户中的“错误”对话框显示不允许基于 http 的环回重定向 URI":::

若要添加在 `127.0.0.1` 环回地址中使用 `http` 方案的重定向 URI，当前必须修改应用程序清单中的 [replyUrlsWithType](reference-app-manifest.md#replyurlswithtype-attribute) 属性。

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>重定向 URI 中对通配符的限制

类似 `https://*.contoso.com` 的通配符 URI 可能看起来很方便，但由于安全方面的影响，应避免使用它们。 根据 OAuth 2.0 规范（[RFC 6749 第 3.1.2 部分](https://tools.ietf.org/html/rfc6749#section-3.1.2)），重定向终结点 URI 必须是绝对 URI。

对于配置为登录个人 Microsoft 帐户以及工作或学校帐户的应用注册，目前不支持通配符 URI。 但是，对于组织的 Azure AD 租户中配置为仅将工作帐户或学校帐户登录的应用，允许使用通配符 URI。

若要将具有通配符的重定向 URI 添加到用于登录工作帐户或学校帐户的应用注册，请使用 Azure 门户的 **应用注册** 中的应用程序清单编辑器。 虽然可以通过使用清单编辑器来设置带通配符的重定向 URI，但我们还是强烈建议你遵循 RFC 6749 的 3.1.2 节的要求， 只使用绝对 URI。

如果方案所需的重定向 URI 数目超过允许的最大限制，请考虑以下状态参数方法，而不要添加通配符重定向 URI。

#### <a name="use-a-state-parameter"></a>使用状态参数

如果你有多个子域，并且你的方案要求在身份验证成功时将用户重定向到开始操作时所在的页面，则使用状态参数可能有帮助。

在此方法中：

1. 为每个应用程序创建一个“共享的”重定向 URI，用于处理从授权终结点收到的安全令牌。
1. 应用程序可以在状态参数中发送应用程序特定的参数（例如用户的来源子域 URL，或品牌信息等）。 使用状态参数时，请根据 [RFC 6749 第 10.12 部分](https://tools.ietf.org/html/rfc6749#section-10.12)中的规定提供 CSRF 保护措施。
1. 应用程序特定的参数包含应用程序为用户呈现正确体验（即，构造相应的应用程序状态）所需的所有信息。 Azure AD 授权终结点会去除状态参数中的 HTML，因此请确保不要在此参数中传递 HTML 内容。
1. 当 Azure AD 向“共享的”重定向 URI 发送响应时，会将状态参数发回到应用程序。
1. 然后，应用程序可以使用状态参数中的值来确定要进一步将用户发送到哪个 URL。 确保验证 CSRF 保护措施。

> [!WARNING]
> 此方法允许遭到攻击的客户端修改状态参数中发送的其他参数，从而将用户重定向到其他 URL，这就是 RFC 6819 中所述的[开放重定向程序威胁](https://tools.ietf.org/html/rfc6819#section-4.2.4)。 因此，客户端必须对状态加密或通过其他一些方式进行验证（如根据令牌验证重定向 URI 中的域名），从而保护这些参数。

## <a name="next-steps"></a>后续步骤

了解应用注册[应用程序清单](reference-app-manifest.md)。
