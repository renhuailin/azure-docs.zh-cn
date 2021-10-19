---
title: 应用中的身份验证和授权
description: 概述如何对 Azure Fluid Relay 服务使用身份验证和授权。
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/auth/
ms.openlocfilehash: fc3e55a91af1e7691d1d2677435c283914521dfe
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661566"
---
# <a name="authentication-and-authorization-in-your-app"></a>应用中的身份验证和授权

> [!NOTE]
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

安全性对于新式 Web 应用程序至关重要。 Fluid Framework 作为 Web 应用程序体系结构的一部分，是需要保护的重要基础结构。 Fluid Framework 是一种分层体系结构，它基于所连接的 Fluid 服务来实现与身份验证相关的概念。 这意味着，尽管所有 Fluid 服务具有共同的身份验证主题，但每个服务的具体内容会有所不同。

## <a name="azure-fluid-relay-service"></a>Azure Fluid Relay 服务

你创建的每个 Azure Fluid Relay 服务租户都分配有租户 ID 及其独有的租户密钥。

该密钥是一种共享机密。 你的应用/服务知道这一点，Azure Fluid Relay 服务也知道。 由于租户密钥与租户唯一关联，因此，使用它对请求进行签名可向 Azure Fluid Relay 服务保证请求来自租户的授权用户。

Azure Fluid Relay 服务通过密钥得知请求来自你的应用或服务。 这一点很重要，因为一旦 Azure Fluid Relay 服务可以相信是你的应用在发出请求，它就可以信任你发送的数据。 这也是安全处理密钥非常重要的原因。

> [!CAUTION]
> 在与 Azure Fluid Relay 服务通信时，任何有权访问该密钥的人都可以模拟你的应用程序。

## <a name="json-web-tokens-and-azure-fluid-relay-service"></a>JSON Web 令牌和 Azure Fluid Relay 服务

Azure Fluid Relay 使用 [JSON Web 令牌 (JWT)](https://jwt.io/) 对使用你的密钥签名的数据进行编码和验证。 JSON Web 令牌是 JSON 的签名位，可包含有关权限的附加信息。

> [!NOTE]
> JWT 的具体内容超出了本文的讨论范围。 有关 JWT 标准的更多详细信息，请参阅 <https://jwt.io/introduction>。

尽管各 Fluid 服务之间的身份验证细节不同，但必须始终存在几个值。

- **containerId**：Fluid 服务需要通过容器 ID 来识别对应于调用容器的服务。 注意：JWT 将此字段称为 documentId，但 Fluid 服务需要此字段中的容器 ID。
- **tenantId**：Azure Fluid Relay 服务使用租户 ID 检索用于对请求进行身份验证的共享机密。 
- **scopes**：范围定义调用容器的权限。 scopes 字段的内容灵活可变，允许你创建自己的自定义权限。

```json {linenos=inline,hl_lines=["5-6",13]}
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "documentId": "azureFluidDocumentId",
  "scopes": [ "doc:read", "doc:write", "summary:write" ],
  "user": {
    "name": "TestUser",
    "id": "Test-Id-123"
  },
  "iat": 1599098963,
  "exp": 1599098963,
  "tenantId": "AzureFluidTenantId",
  "ver": "1.0"
}.[Signature]
```

> [!NOTE]
> 请注意，令牌还包括用户信息（请参阅上面的第 7-9 行）。 你可以使用它来扩充使用[受众](../how-tos/connect-fluid-azure-service.md#getting-audience-details)功能自动提供给 Fluid 代码的用户信息。 有关详细信息，请参阅[向令牌添加自定义数据](../how-tos/connect-fluid-azure-service.md#adding-custom-data-to-tokens)。

## <a name="the-token-provider"></a>令牌提供程序

向 Azure Fluid Relay 发出的每个请求都必须使用有效的 JWT 进行签名。 Fluid 将创建和签署这些令牌的责任委托给令牌提供程序。

令牌提供程序负责创建和签署令牌，`@fluidframework/azure-client` 使用这些令牌向 Azure Fluid Relay 服务发出请求。 你需要提供自己的安全令牌提供程序实现。 但是，Fluid 会提供一个 `InsecureTokenProvider`，它接受​​你的租户密钥，然后在本地生成并返回一个签名令牌。 此令牌提供程序可用于测试，但不能用于生产场景。

### <a name="a-secure-serverless-token-provider"></a>安全的无服务器令牌提供程序

若要生成安全的令牌提供程序，可以选择创建无服务器 Azure 函数并将其公开为令牌提供程序。 这样，你就可以将租户密钥存储在安全的服务器上。 然后，你的应用程序将调用 Azure 函数来生成令牌，而不是像 `InsecureTokenProvider` 那样在本地对它们进行签名。 有关详细信息，请参阅[如何：使用 Azure 函数编写 TokenProvider](../how-tos/azure-function-token-provider.md)。

## <a name="connecting-user-auth-to-fluid-service-auth"></a>将用户身份验证连接到 Fluid 服务身份验证

Fluid 服务使用不绑定到特定用户的共享客户端密码对传入调用进行身份验证。 你可以根据 Fluid 服务的具体内容添加用户身份验证。 

用户身份验证的一个简单选项是简单地使用 [Azure 函数](../../azure-functions/index.yml)作为令牌提供程序，并将用户身份验证强行设置为获取令牌的条件。 如果应用程序尝试调用该函数，除非向身份验证系统进行身份验证，否则它将失败。 例如，如果使用 Azure Active Directory (Azure AD)，则可以为 Azure 函数创建一个 Azure AD 应用程序，并将它绑定到组织的身份验证系统。

在这种情况下，用户将使用 Azure AD 登录到你的应用程序，你将通过 Azure AD 获取用于调用 Azure 函数的令牌。 Azure 函数本身的行为没有发生变化，但现在只有同样向 Azure AD 进行了身份验证的用户才能访问它。

由于 Azure 函数现在是获取有效令牌的入口点，因此只有正确通过函数身份验证的用户才能从其客户端应用程序向 Azure Fluid Relay 服务提供该令牌。 这种两步式方法使你能够将自己的自定义身份验证过程与 Azure Fluid Relay 服务结合使用。

## <a name="see-also"></a>请参阅

- [JWT 简介](https://jwt.io/introduction)
- [如何创建 JSON Web 令牌](../how-tos/fluid-json-web-token.md)
- [Azure Fluid Relay 中的有效负载声明](../how-tos/fluid-json-web-token.md#payload-claims)
