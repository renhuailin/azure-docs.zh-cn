---
title: Azure Active Directory 用于 FHIR 的 Azure API 的标识配置
description: 了解 Azure FHIR 服务器的标识、身份验证和授权原则。
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 277838e3ce870b528f986292f88ad2b2b20f42b1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017896"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Azure Active Directory 用于 FHIR 的 Azure API 的标识配置

使用医疗保健数据的一个重要部分是确保数据安全，不受未经授权的用户或应用程序的访问。 FHIR 服务器使用 [OAuth 2.0](https://oauth.net/2/) 确保此数据安全性。 使用[Azure Active Directory](../../active-directory/index.yml)保护[AZURE API for FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/) ，这是 OAuth 2.0 标识提供程序的一个示例。 本文概述了 FHIR 服务器授权以及获得令牌以访问 FHIR 服务器所需的步骤。 尽管这些步骤将适用于任何 FHIR 服务器和任何标识提供者，但我们将通过 Azure API for FHIR 作为 FHIR 服务器，并 Azure AD 作为本文中的标识提供者。

## <a name="access-control-overview"></a>访问控制概述

为了使客户端应用程序能够访问 Azure API for FHIR，它必须提供一个访问令牌。 访问令牌是一组经过签名的 [Base64](https://en.wikipedia.org/wiki/Base64) 编码的属性 (声明) ，用于传达有关客户端的标识和角色的信息以及授予客户端的权限。

有多种方法可以获取令牌，但 Azure API for FHIR 并不关心令牌的获取方式，只要该令牌是具有正确声明的正确签名令牌即可。 

使用 [授权代码流](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) 作为示例，访问 FHIR 服务器将执行以下四个步骤：

![FHIR 授权](media/azure-ad-hcapi/fhir-authorization.png)

1. 客户端向 `/authorize` Azure AD 的终结点发送请求。 Azure AD 会将客户端重定向到登录页面，用户将使用相应的凭据进行身份验证 (例如，用户名和密码或双因素身份验证) 。 请参阅 [获取授权代码](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)的详细信息。 身份验证成功后，会将 *授权代码* 返回到客户端。 Azure AD 将仅允许将此授权代码返回到客户端应用程序注册中配置的已注册答复 URL (参见下面) 。
1. 客户端应用程序在 Azure AD 的终结点上交换 *访问令牌* 的授权代码 `/token` 。 请求令牌时，客户端应用程序可能需要提供客户端机密 (应用程序密码) 。 有关 [获取访问令牌](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)的详细信息，请参阅。
1. 客户端向 FHIR 的 Azure API 发出请求，例如 `GET /Patient` 搜索所有患者。 发出请求时，它将访问令牌包含在 HTTP 请求标头中，例如 `Authorization: Bearer eyJ0e...` ，其中 `eyJ0e...` 表示 Base64 编码的访问令牌。
1. 适用于 FHIR 的 Azure API 验证令牌是否包含标记) 中 (属性的适当声明。 如果所有内容都已签出，则它将完成请求，并将结果返回到客户端。

务必要注意的是，用于 FHIR 的 Azure API 不涉及验证用户凭据，并且它不会颁发令牌。 身份验证和令牌创建是通过 Azure AD 来完成的。 适用于 FHIR 的 Azure API 只是验证令牌是否已正确签名 (它是否可信) 并且其是否具有适当的声明。

## <a name="structure-of-an-access-token"></a>访问令牌的结构

开发 FHIR 应用程序通常涉及调试访问问题。 如果客户端拒绝访问 Azure API for FHIR，则了解访问令牌的结构以及如何对其进行解码以检查令牌的声明)  (内容很有用。 

FHIR 服务器通常需要 (JWT 的 [JSON Web 令牌](https://en.wikipedia.org/wiki/JSON_Web_Token) ，有时会 ) 。 它由三个部分组成：

1. 标头，如下所示：
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
1. 有效负载 (声明) 例如：
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
1. 签名，通过将标头的 Base64 编码内容与负载进行连接，并根据 `alg` 标头中指定 () 的算法计算这些内容的加密哈希。 服务器将能够从标识提供程序获取公钥，并验证此令牌是否由特定的标识提供程序颁发，并且它未被篡改。

完整标记包含 Base64 编码 (实际上是 Base64 url 编码) 这三个段的版本。 这三个段连接起来并使用 `.` (点) 进行分隔。

下面显示了一个示例令牌：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

可以通过工具（如）对令牌进行解码和检查 [https://jwt.ms](https://jwt.ms) 。 令牌解码的结果是：

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

如上所述，可以通过多种方式从 Azure AD 获取令牌。 [Azure AD 开发人员文档](../../active-directory/develop/index.yml)中对它们进行了详细介绍。

Azure AD 具有两个不同版本的 OAuth 2.0 终结点，这些终结点称为 `v1.0` 和 `v2.0` 。 这两个版本都是 OAuth 2.0 终结点， `v1.0` 并且和 `v2.0` 称号引用 Azure AD 如何实现该标准的不同之处。 

使用 FHIR 服务器时，可以使用 `v1.0` 或 `v2.0` 终结点。 选择可能取决于你在客户端应用程序中使用的身份验证库。

Azure AD 文档的相关章节如下：

* `v1.0` 终结点：
    * [授权代码流](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)。
    * [客户端凭据流](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)。
* `v2.0` 终结点：
    * [授权代码流](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。
    * [客户端凭据流](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)。

例如， (代表用于获取令牌的流) 的其他变体。 有关详细信息，请查看 Azure AD 文档。 使用用于 FHIR 的 Azure API 时，还有一些快捷方式用于获取访问令牌 (用于) [使用 Azure CLI](get-healthcare-apis-access-token-cli.md)进行调试。

## <a name="next-steps"></a>后续步骤

在本文档中，你已学习了一些涉及到使用 Azure AD 保护对 Azure API for FHIR 的访问的基本概念。 若要了解如何部署适用于 FHIR 的 Azure API 实例，请继续执行部署快速入门。

>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)