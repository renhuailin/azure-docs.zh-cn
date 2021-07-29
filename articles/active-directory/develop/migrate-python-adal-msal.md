---
title: Python ADAL 到 MSAL 的迁移指南 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何将 Azure Active Directory 身份验证库 (ADAL) Python 应用迁移到适用于 Python 的 Microsoft 身份验证库 (MSAL)。
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: marsma, rayluo, nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 4be4e9868d7c2bebf73e77b0b04140ce710214d1
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110454012"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>适用于 Python 的 ADAL 到 MSAL 迁移指南

本文重点介绍需要做出哪些更改，才能迁移使用 Azure Active Directory 身份验证库 (ADAL) 的应用，使之使用 Microsoft 身份验证库 (MSAL)。

你可以了解有关 MSAL 的更多信息，并从 [Microsoft 身份验证库概述](msal-overview.md)开始。

## <a name="difference-highlights"></a>差异重点说明

ADAL 适用于 Azure Active Directory (Azure AD) v1.0 终结点。 Microsoft 身份验证库 (MSAL) 适用于 Microsoft 标识平台（前称为 Azure Active Directory v2.0 终结点）。 Microsoft 标识平台与 Azure AD v1.0 的不同之处在于：

支持：
  - 工作和学校帐户（Azure AD 预配帐户）
  - 个人帐户（例如 Outlook.com 或 Hotmail.com）
  - 通过 Azure AD B2C 产品/服务使用自己的电子邮件或社交身份（如 LinkedIn、Facebook、Google）的客户

- 与以下协议的标准兼容：
  - OAuth v2.0
  - OpenID Connect (OIDC)

有关详细信息，请参阅 [Microsoft 标识平台有何不同？](../azuread-dev/azure-ad-endpoint-comparison.md)。

### <a name="scopes-not-resources"></a>范围不是资源

ADAL Python 获取资源的令牌，而 MSAL Python 则是获取范围的令牌。 MSAL Python 中的 API 面不再包含资源参数。 可能需要以字符串列表的形式提供范围，这些字符串声明所需的权限和请求的资源。 若要查看一些示例范围，请参阅 [Microsoft Graph 的范围](/graph/permissions-reference)。

可以将 `/.default` 作用域后缀添加到资源中，帮助将应用从 v1.0 终结点 (ADAL) 迁移到 Microsoft 标识平台 (MSAL)。 例如，对于 `https://graph.microsoft.com` 的资源值，等效的作用域值为 `https://graph.microsoft.com/.default`。  如果资源未采用 URL 形式，但资源 ID 采用 `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` 形式，则仍可以使用作用域值 `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default`。

有关不同类型作用域的更多详细信息，请参阅 [Microsoft 标识平台中的权限和许可](./v2-permissions-and-consent.md)以及[接受 v1.0 令牌的 Web API 的作用域](./msal-v1-app-scopes.md)两篇文章。

### <a name="error-handling"></a>错误处理。

适用于 Python 的 Azure Active Directory 身份验证库 (ADAL) 使用异常 `AdalError` 来指示问题。 而适用于 Python 的 MSAL 通常使用错误代码。 有关详细信息，请参阅[适用于 Python 的 MSAL 错误处理](msal-error-handling-python.md)。

### <a name="api-changes"></a>API 更改

下表列出了适用于 Python 的 ADAL 中的 API，以及该 API 在适用于 Python 的 MSAL 中的对应项：

| 适用于 Python 的 ADAL 的 API  | 适用于 Python 的 MSAL 的 API |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.__init__) 或 [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.__init__)  |
| 不适用  | [PublicClientApplication.acquire_token_interactive()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_interactive)  |
| 不适用  | [ConfidentialClientApplication.initiate_auth_code_flow()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.initiate_auth_code_flow)  |
| [acquire_token_with_authorization_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [ConfidentialClientApplication.acquire_token_by_auth_code_flow()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_by_auth_code_flow) |
| [acquire_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [PublicClientApplication.acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_silent) 或 [ConfidentialClientApplication.acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | 这两个帮助程序仅在[迁移](#migrate-existing-refresh-tokens-for-msal-python)期间使用：[PublicClientApplication.acquire_token_by_refresh_token()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_refresh_token) 或 [ConfidentialClientApplication.acquire_token_by_refresh_token()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_by_refresh_token) |
| [acquire_user_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) 和 [cancel_request_to_get_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) 和 [acquire_token_with_client_certificate()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| 空值 | [acquire_token_on_behalf_of()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| 空值 | 具有持久性的缓存，可从 [MSAL 扩展](https://github.com/marstr/original-microsoft-authentication-extensions-for-python)获取 |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>迁移 MSAL Python 的现有刷新令牌

Microsoft 身份验证库 (MSAL) 已将刷新令牌的概念抽象化。 MSAL Python 默认提供内存中令牌缓存，因此你无需存储、查找或更新刷新令牌。 用户看到的登录提示更少，因为刷新令牌通常无需用户的干预即可更新。 有关令牌缓存的详细信息，请参阅[适用于 Python 的 MSAL 中的自定义令牌缓存序列化](msal-python-token-cache-serialization.md)。

以下代码可帮助你迁移由另一个 OAuth2 库（包括但不限于 ADAL Python）管理的刷新令牌，使之由适用于 Python 的 MSAL 管理。 迁移这些刷新令牌的一个原因是，在将应用迁移到适用于 Python 的 MSAL 时，避免现有用户再次登录。

迁移刷新令牌的方法是使用适用于 Python 的 MSAL 通过前一个刷新令牌获取新的访问令牌。 返回新的刷新令牌后，适用于 Python 的 MSAL 会将其存储在缓存中。
为此，从 MSAL Python 1.3.0 开始，我们在 MSAL 内提供 API。
请参阅下面的代码片段，引用自[通过 MSAL Python 迁移刷新令牌的完整示例](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.3.0/sample/migrate_rt.py#L28-L67)

```python
import msal
def get_preexisting_rt_and_their_scopes_from_elsewhere():
    # Maybe you have an ADAL-powered app like this
    #   https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/1.2.3/sample/device_code_sample.py#L72
    # which uses a resource rather than a scope,
    # you need to convert your v1 resource into v2 scopes
    # See https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources
    # You may be able to append "/.default" to your v1 resource to form a scope
    # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    # Or maybe you have an app already talking to the Microsoft identity platform,
    # powered by some 3rd-party auth library, and persist its tokens somehow.

    # Either way, you need to extract RTs from there, and return them like this.
    return [
        ("old_rt_1", ["scope1", "scope2"]),
        ("old_rt_2", ["scope3", "scope4"]),
        ]


# We will migrate all the old RTs into a new app powered by MSAL
app = msal.PublicClientApplication(
    "client_id", authority="...",
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache
    )

# We choose a migration strategy of migrating all RTs in one loop
for old_rt, scopes in get_preexisting_rt_and_their_scopes_from_elsewhere():
    result = app.acquire_token_by_refresh_token(old_rt, scopes)
    if "error" in result:
        print("Discarding unsuccessful RT. Error: ", json.dumps(result, indent=2))

print("Migration completed")
```


## <a name="next-steps"></a>后续步骤

有关详细信息，请阅读 [v1.0 与 v2.0 的比较](../azuread-dev/azure-ad-endpoint-comparison.md)。
