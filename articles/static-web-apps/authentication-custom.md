---
title: Azure Static Web Apps 中的自定义身份验证
description: 了解如何配置 Azure Static Web Apps 的自定义身份验证
services: static-web-apps
author: aaronpowell
ms.author: aapowell
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 10/08/2021
ms.openlocfilehash: 49921eba1a7f4c6c898eaadf1d8743d8d210057a
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729798"
---
# <a name="custom-authentication-in-azure-static-web-apps"></a>Azure Static Web Apps 中的自定义身份验证

Azure Static Web Apps 提供[托管身份验证](authentication-authorization.md)，此身份验证方法使用 Azure 管理的提供程序注册。 为了实现比该注册更高的灵活性，可使用自定义注册替代默认设置。

- 自定义身份验证还允许配置支持 [OpenID Connect](https://openid.net/connect/) 的[自定义提供程序](./authentication-custom.md?tabs=openid-connect#configure-a-custom-identity-provider)。 此配置允许注册多个外部提供程序。

- 使用任何自定义注册会禁用所有预配置的提供程序。

> [!NOTE]
> 只能在 Azure Static Web Apps 标准计划中使用自定义身份验证。

## <a name="configure-a-custom-identity-provider"></a>配置自定义标识提供程序

在`auth`配置文件[的 ](configuration.md) 部分中配置自定义标识提供程序。

为了避免在源代码管理中添加机密，配置将在配置文件的[应用程序设置](application-settings.md)中查找匹配的名称。 你还可以选择将机密存储在 [Azure 密钥保管库](./key-vault-secrets.md)中。

# <a name="azure-active-directory"></a>Azure Active Directory

要创建注册，请首先创建以下[应用程序设置](application-settings.md)：

| 设置名称 | “值” |
| --- | --- |
| `AAD_CLIENT_ID` | 用于 Azure AD 应用注册的应用程序（客户端）ID。 |
| `AAD_CLIENT_SECRET` | 用于 Azure AD 应用注册的客户端密码。 |

接下来，使用以下示例在[配置文件](configuration.md)中配置提供程序。

Azure Active Directory 提供程序有两个不同的版本。 版本 1 显式定义了 `userDetailsClaim`，它允许有效负载返回用户信息。 相比之下，版本 2 默认返回用户信息，并由 `openIdIssuer` URL 中的 `v2.0` 指定。

### <a name="azure-active-directory-version-1"></a>Azure Active Directory 版本 1

```json
{
  "auth": {
    "identityProviders": {
      "azureActiveDirectory": {
        "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
        "registration": {
          "openIdIssuer": "https://login.microsoftonline.com/<TENANT_ID>",
          "clientIdSettingName": "AAD_CLIENT_ID",
          "clientSecretSettingName": "AAD_CLIENT_SECRET"
        }
      }
    }
  }
}
```

确保将 `<TENANT_ID>` 替换为你的 Azure Active Directory 租户 ID。

### <a name="azure-active-directory-version-2"></a>Azure Active Directory 版本 2

```json
{
  "auth": {
    "identityProviders": {
      "azureActiveDirectory": {
        "registration": {
          "openIdIssuer": "https://login.microsoftonline.com/<TENANT_ID>/v2.0",
          "clientIdSettingName": "AAD_CLIENT_ID",
          "clientSecretSettingName": "AAD_CLIENT_SECRET"
        }
      }
    }
  }
}
```

确保将 `<TENANT_ID>` 替换为你的 Azure Active Directory 租户 ID。

有关如何配置 Azure Active Directory 的详细信息，请参阅有关使用现有注册的[应用服务身份验证/授权文档](../app-service/configure-authentication-provider-aad.md#-option-2-use-an-existing-registration-created-separately)。

> [!NOTE]
> 虽然 Azure Active Directory 的配置部分是 `azureActiveDirectory`，但平台在用于登录、注销和清除用户信息的 URL 中将它别名为 `aad`。 有关详细信息，请参阅[身份验证和授权](authentication-authorization.md)部分。

# <a name="apple"></a>[Apple](#tab/apple)

要创建注册，请首先创建以下[应用程序设置](application-settings.md)：

| 设置名称 | “值” |
| --- | --- |
| `APPLE_CLIENT_ID` | Apple 客户端 ID。 |
| `APPLE_CLIENT_SECRET` | Apple 客户端密码。 |

接下来，使用以下示例在[配置文件](configuration.md)中配置提供程序。

```json
{
  "auth": {
    "identityProviders": {
      "apple": {
        "registration": {
          "clientIdSettingName": "APPLE_CLIENT_ID",
          "clientSecretSettingName": "APPLE_CLIENT_SECRET"
        }
      }
    }
  }
}
```

有关如何将 Apple 配置为身份验证提供程序的详细信息，请参阅[应用服务身份验证/授权文档](../app-service/configure-authentication-provider-apple.md)。

# <a name="facebook"></a>[Facebook](#tab/facebook)

要创建注册，请首先创建以下[应用程序设置](application-settings.md)：

| 设置名称 | “值” |
| --- | --- |
| `FACEBOOK_APP_ID` | Facebook 应用程序 ID。 |
| `FACEBOOK_APP_SECRET` | Facebook 应用程序机密。 |

接下来，使用以下示例在[配置文件](configuration.md)中配置提供程序。

```json
{
  "auth": {
    "identityProviders": {
      "facebook": {
        "registration": {
          "appIdSettingName": "FACEBOOK_APP_ID",
          "appSecretSettingName": "FACEBOOK_APP_SECRET"
        }
      }
    }
  }
}
```

有关如何将 Facebook 配置为身份验证提供程序的详细信息，请参阅[应用服务身份验证/授权文档](../app-service/configure-authentication-provider-facebook.md)。

# <a name="github"></a>[GitHub](#tab/github)


要创建注册，请首先创建以下[应用程序设置](application-settings.md)：

| 设置名称 | “值” |
| --- | --- |
| `GITHUB_CLIENT_ID` | GitHub 客户端 ID。 |
| `GITHUB_CLIENT_SECRET` | GitHub 客户端密码。 |

接下来，使用以下示例在[配置文件](configuration.md)中配置提供程序。

```json
{
  "auth": {
    "identityProviders": {
      "github": {
        "registration": {
          "clientIdSettingName": "GITHUB_CLIENT_ID",
          "clientSecretSettingName": "GITHUB_CLIENT_SECRET"
        }
      }
    }
  }
}
```

# <a name="google"></a>[Google](#tab/google)


要创建注册，请首先创建以下[应用程序设置](application-settings.md)：

| 设置名称 | “值” |
| --- | --- |
| `GOOGLE_CLIENT_ID` | Google 客户端 ID。 |
| `GOOGLE_CLIENT_SECRET` | Google 客户端密码。 |

接下来，使用以下示例在[配置文件](configuration.md)中配置提供程序。

```json
{
  "auth": {
    "identityProviders": {
      "google": {
        "registration": {
          "clientIdSettingName": "GOOGLE_CLIENT_ID",
          "clientSecretSettingName": "GOOGLE_CLIENT_SECRET"
        }
      }
    }
  }
}
```

有关如何将 Google 配置为身份验证提供程序的详细信息，请参阅[应用服务身份验证/授权文档](../app-service/configure-authentication-provider-google.md)。

# <a name="twitter"></a>[Twitter](#tab/twitter)

要创建注册，请首先创建以下[应用程序设置](application-settings.md)：

| 设置名称 | “值” |
| --- | --- |
| `TWITTER_CONSUMER_KEY` | Twitter 使用者密钥。 |
| `TWITTER_CONSUMER_SECRET` | Twitter 使用者机密。 |

接下来，使用以下示例在[配置文件](configuration.md)中配置提供程序。

```json
{
  "auth": {
    "identityProviders": {
      "twitter": {
        "registration": {
          "consumerKeySettingName": "TWITTER_CONSUMER_KEY",
          "consumerSecretSettingName": "TWITTER_CONSUMER_SECRET"
        }
      }
    }
  }
}
```

有关如何将 Twitter 配置为身份验证提供程序的详细信息，请参阅[应用服务身份验证/授权文档](../app-service/configure-authentication-provider-twitter.md)。

# <a name="openid-connect"></a>[OpenID Connect](#tab/openid-connect)

可将 Azure Static Web Apps 配置为使用遵守 [OpenID Connect (OIDC) 规范](https://openid.net/connect/)的自定义身份验证提供程序。 需要执行以下步骤才能使用自定义 OIDC 提供程序。

- 允许一个或多个 OIDC 提供程序。
- 每个提供程序在配置中必须具有唯一的名称。
- 只有一个提供程序可以充当默认的重定向目标。

### <a name="register-your-application-with-the-identity-provider"></a>向以下标识提供者注册你的应用程序

需要将应用程序的详细信息注册到标识提供者。 有关需要执行哪些步骤来生成应用程序的客户端 ID 和客户端机密，请咨询提供者 。

向标识提供者注册应用程序后，在静态 Web 应用的[应用程序设置](application-settings.md)中创建以下应用程序机密：

| 设置名称 | “值” |
| --- | --- |
| `MY_PROVIDER_CLIENT_ID` | 身份验证提供程序为静态 Web 应用生成的客户端 ID。 |
| `MY_PROVIDER_CLIENT_SECRET` | 身份验证提供程序为静态 Web 应用自定义注册生成的客户端密码。 |

如果注册其他提供程序，那么每个提供程序都需要在应用程序设置中关联的客户端 ID 和客户端密码存储。

> [!IMPORTANT]
> 应用程序机密是敏感的安全凭据。 请不要向任何人透露此机密、在客户端应用程序中分发此机密，或者将其签入到源代码管理中。

获得注册凭据后，使用以下步骤创建自定义注册。

1. 需要提供程序的 OpenID Connect 元数据。 此信息通常是通过一个[配置元数据文档](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)（提供者的颁发者 URL，以 `/.well-known/openid-configuration` 为后缀）公开的。 收集此配置 URL。

1. 在[配置文件](configuration.md)中添加 `auth` 节，并在其中包含 OIDC 提供程序的配置块，以及你的提供程序定义。

   ```json
   {
     "auth": {
       "identityProviders": {
         "customOpenIdConnectProviders": {
           "myProvider": {
             "registration": {
               "clientIdSettingName": "MY_PROVIDER_CLIENT_ID",
               "clientCredential": {
                 "clientSecretSettingName": "MY_PROVIDER_CLIENT_SECRET"
               },
               "openIdConnectConfiguration": {
                 "wellKnownOpenIdConfiguration": "https://<PROVIDER_ISSUER_URL>/.well-known/openid-configuration"
               }
             },
             "login": {
               "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
               "scopes": [],
               "loginParameterNames": []
             }
           }
         }
       }
     }
   }
   ```

  - 此示例中的提供程序名称 `myProvider` 是 Azure Static Web Apps 使用的唯一标识符。
  - 确保将 `<PROVIDER_ISSUER_URL>` 替换为提供程序的颁发者 URL 的路径。
  - `login` 对象允许提供自定义范围、登录参数或自定义声明的值。

---

## <a name="authentication-callbacks"></a>身份验证回调

标识提供程序需使用重定向 URL 来完成登录或注销请求。 大多数提供程序都要求将回调 URL 添加到允许列表。 以下终结点可用作重定向目标。

| 类型   | URL 模式                                                 |
| ------ | ----------------------------------------------------------- |
| 登录  | `https://<YOUR_SITE>/.auth/login/<PROVIDER_NAME_IN_CONFIG>/callback`  |
| Logout | `https://<YOUR_SITE>/.auth/logout/<PROVIDER_NAME_IN_CONFIG>/callback` |

如果你使用的是 Azure Active Directory，请使用 `aad` 作为 `<PROVIDER_NAME_IN_CONFIG>` 占位符的值。

> [!Note]
> 这些 URL 由 Azure Static Web Apps 提供，以便用来接收身份验证提供程序的响应，你无需在这些路由中创建页面。

## <a name="login-logout-and-purging-user-details"></a>登录、注销和清除用户详细信息

要使用自定义标识提供程序，请使用以下 URL 模式。

| 操作             | 模式                                  |
| ------------------ | ---------------------------------------- |
| 登录              | `/.auth/login/<PROVIDER_NAME_IN_CONFIG>` |
| Logout             | `/.auth/logout`                          |
| 清除用户详细信息 | `/.auth/purge/<PROVIDER_NAME_IN_CONFIG>` |

如果你使用的是 Azure Active Directory，请使用 `aad` 作为 `<PROVIDER_NAME_IN_CONFIG>` 占位符的值。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [保护 Azure 密钥保管库中的身份验证机密](./key-vault-secrets.md)
