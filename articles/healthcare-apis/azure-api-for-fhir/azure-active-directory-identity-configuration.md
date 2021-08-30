---
title: 适用于 Azure API for FHIR 的 Azure Active Directory 标识配置
description: 了解 Azure FHIR 服务器的标识、身份验证和授权原则。
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: cavoeg
ms.openlocfilehash: bd5bc1c447a019a5d76995f386407136d1d50332
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778262"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>适用于 Azure API for FHIR 的 Azure Active Directory 标识配置

使用医疗保健数据时，确保数据安全并且不能由未经授权的用户或应用程序访问，这一点非常重要。 FHIR 服务器使用 [OAuth 2.0](https://oauth.net/2/) 确保此数据的安全性。 使用 [Azure Active Directory](../../active-directory/index.yml) 保护 [Azure API for FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/)，这是 OAuth 2.0 标识提供者的一个示例。 本文概述了 FHIR 服务器授权以及获取令牌以访问 FHIR 服务器所需的步骤。 尽管这些步骤适用于任何 FHIR 服务器和任何标识提供者，但本文会演练将 Azure API for FHIR 作为 FHIR 服务器，将 Azure Active Directory (Azure AD) 作为标识提供者。

## <a name="access-control-overview"></a>访问控制概述

为了让客户端应用程序能够访问 Azure API for FHIR，它必须提供一个访问令牌。 访问令牌是经过签名的 [Base64](https://en.wikipedia.org/wiki/Base64) 编码的属性（声明）集合，可在其中了解客户端标识和角色以及授予客户端的权限。

有多种方法可以获取令牌，但 Azure API for FHIR 不考虑令牌的获取方式，只要该令牌是具有正确声明的正确签名令牌即可。 

以[授权代码流](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)为例，访问 FHIR 服务器需执行以下四个步骤：

![FHIR 授权](media/azure-ad-hcapi/fhir-authorization.png)

1. 客户端向 Azure AD 的 `/authorize` 终结点发送一个请求。 Azure AD 将客户端重定向到登录页面，用户将在这里使用相应的凭据（例如，用户名加密码或双因素身份验证）进行身份验证。 有关详细信息，请参阅[获取授权代码](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)。 成功完成身份验证后，授权代码将返回到客户端。 Azure AD 将只允许将此授权代码返回到在客户端应用程序注册中配置的注册回复 URL（见下文）。
1. 在 Azure AD 的 `/token` 终结点上，客户端应用程序会使用授权代码交换访问令牌。 请求令牌时，客户端应用程序可能需要提供客户端密码（应用程序密码）。 有关详细信息，请参阅[获取访问令牌](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)。
1. 客户端向 Azure API for FHIR 发出请求，例如 `GET /Patient` 表示搜索所有患者。 发出请求时，它将访问令牌包含在 HTTP 请求头中，例如 `Authorization: Bearer eyJ0e...`，其中 `eyJ0e...` 表示 Base64 编码的访问令牌。
1. Azure API for FHIR 验证令牌是否包含相应声明（令牌中的属性）。 如果所有内容都已签出，则它将完成请求，并将包含结果的 FHIR 捆绑返回到客户端。

需要特别注意的是，Azure API for FHIR 不涉及验证用户凭据，也不会颁发令牌。 身份验证和令牌创建是通过 Azure AD 来完成的。 Azure API for FHIR 只是验证令牌是否已正确签名（它是否可信）和是否具有相应声明。

## <a name="structure-of-an-access-token"></a>访问令牌的结构

开发 FHIR 应用程序通常涉及调试访问问题。 如果客户端被拒绝访问 Azure API for FHIR，则了解访问令牌的结构以及如何对其进行解码以检查令牌内容（声明）很有用。 

FHIR 服务器通常需要 [JSON Web 令牌](https://en.wikipedia.org/wiki/JSON_Web_Token)（JWT，有时发音为“jot”）。 它由三个部分组成：

**第 1 部分**：标头，例如：
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```

**第 2 部分**：有效负载（声明），例如：
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```

**第 3 部分**：签名，它是通过串联标头和有效负载的 Base64 编码内容并根据标头中指定的算法 (`alg`) 计算这些内容的加密哈希计算得出的。 服务器将能够从标识提供者获取公钥，并验证此令牌是否由特定的标识提供者颁发且未被篡改。

完整的令牌由这三段的 Base64 编码（实际上是 Base64 URL 编码）的版本组成。 这三段串联起来并以“`.`”（点）进行分隔。

令牌的示例如下所示：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

可通过工具（如 [https://jwt.ms](https://jwt.ms)）对令牌进行解码和检查。 令牌解码的结果是：

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>获取访问令牌

如上所述，可通过多种方式从 Azure AD 获取令牌。 [Azure AD 开发人员文档](../../active-directory/develop/index.yml)中对这些方式进行了详细介绍。

Azure AD 有两个不同版本的 OAuth 2.0 终结点，分别称为 `v1.0` 和 `v2.0`。 这两个版本都是 OAuth 2.0 终结点，`v1.0` 和 `v2.0` 名称指的是 Azure AD 实现该标准的方式的差异。 

使用 FHIR 服务器时，可以使用 `v1.0` 或 `v2.0` 终结点。 你可根据在客户端应用程序中使用的身份验证库来选择。

Azure AD 文档的相关部分如下：

* `v1.0` 终结点：
    * [授权代码流](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)。
    * [客户端凭据流](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)。
* `v2.0` 终结点：
    * [授权代码流](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。
    * [客户端凭据流](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)。

获取令牌还有其他变体（例如代表流）。 有关详细信息，请查看 Azure AD 文档。 使用 Azure API for FHIR 时，还有一些[使用 Azure CLI](get-healthcare-apis-access-token-cli.md) 获取访问令牌（用于调试目的）的快捷方式。

## <a name="next-steps"></a>后续步骤

在本文档中，你学习了一些涉及到使用 Azure AD 保护对 Azure API for FHIR 的访问的基本概念。 若要了解如何部署 Azure API for FHIR 服务，请参阅以下文章。

>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)