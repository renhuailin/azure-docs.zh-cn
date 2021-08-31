---
title: Azure API for FHIR 访问令牌验证
description: 演练令牌验证，并提供有关如何排查访问问题的提示
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: cavoeg
ms.openlocfilehash: c823e74fa76b1b0d8d2b9974305e7f7df44c57a9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778724"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>Azure API for FHIR 访问令牌验证

Azure API for FHIR 验证访问令牌的方式取决于实现和配置。 本文将逐步说明验证步骤，这些步骤可能有助于排查访问问题。

## <a name="validate-token-has-no-issues-with-identity-provider"></a>验证令牌是否不存在标识提供者相关的问题

令牌验证的第一步是验证该令牌是否由正确的标识提供者颁发并且尚未修改。 FHIR 服务器将配置为使用称作颁发机构 `Authority` 的特定标识提供者。 FHIR 服务器将从 `/.well-known/openid-configuration` 终结点检索有关标识提供者的信息。 使用 Azure AD 时，完整的 URL 是：

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

其中，`<TENANT-ID>` 是特定的 Azure AD 租户（租户 ID 或域名）。

Azure AD 将向 FHIR 服务器返回如下所示的文档。

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
FHIR 服务器的重要属性为 `jwks_uri`（告知服务器要从何处提取用于验证令牌签名的加密密钥）和 `issuer`（告知服务器要在此服务器颁发的令牌的颁发者声明 (`iss`) 中包含哪些内容）。 FHIR 服务器可以使用此文档来验证它收到的令牌是否真实可信。

## <a name="validate-claims-of-the-token"></a>验证令牌的声明

服务器验证令牌的真实性后，FHIR 服务器将继续验证客户端是否具有访问令牌所需的声明。

使用 Azure API for FHIR 时，服务器将验证：

1. 令牌是否包含适当的 `Audience`（`aud` 声明）。
1. 允许该令牌的目标用户或主体访问 FHIR 服务器数据平面。 令牌的 `oid` 声明包含用于唯一标识该用户或主体的标识对象 ID。

建议将 FHIR 服务[配置为使用 Azure RBAC ](configure-azure-rbac.md)来管理数据平面角色分配。 但是，如果 FHIR 服务使用外部或辅助 Azure Active Directory 租户，则你也可以[配置本地 RBAC](configure-local-rbac.md)。 

使用适用于 Azure 的 OSS Microsoft FHIR 服务器时，服务器将验证：

1. 令牌是否包含适当的 `Audience`（`aud` 声明）。
1. 令牌的 `roles` 声明中包含一个有权访问 FHIR 服务器的角色。

请查阅有关如何[在 FHIR 服务器上定义角色](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md)的详细信息。

FHIR 服务器还可以验证访问令牌是否包含用于访问客户端正在尝试访问的 FHIR API 部分的范围（在令牌声明 `scp`中）。 目前，Azure API for FHIR 和适用于 Azure 的 FHIR 服务器不会验证令牌范围。

## <a name="next-steps"></a>后续步骤
了解如何完成令牌验证后，可以完成创建 JavaScript 应用程序和读取 FHIR 数据的教程。

>[!div class="nextstepaction"]
>[Web 应用程序教程](tutorial-web-app-fhir-server.md)