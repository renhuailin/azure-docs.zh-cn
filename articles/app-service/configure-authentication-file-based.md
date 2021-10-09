---
title: 基于文件的 AuthN/AuthZ 配置
description: 若要启用某些预览功能，请使用配置文件在应用服务中配置身份验证和授权。
ms.topic: article
ms.date: 07/15/2021
ms.openlocfilehash: 2875c529e644b46d08251ae57ee9f424492a2433
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657208"
---
# <a name="file-based-configuration-in-azure-app-service-authentication"></a>Azure 应用服务身份验证中的基于文件的配置

借助[应用服务身份验证](overview-authentication-authorization.md)，可以使用文件配置身份验证设置。 在通过 [Azure 资源管理器](../azure-resource-manager/management/overview.md) API 公开应用服务身份验证/授权的某些预览功能之前，可能需要使用基于文件的配置才能使用这些功能。

> [!IMPORTANT]
> 请记住，应用的有效负载（并由此该文件）可能随[槽](./deploy-staging-slots.md)在环境之间移动。 可能需要将不同的应用注册固定到每个槽，在这些情况下，应继续使用标准配置方法，而非配置文件。

## <a name="enabling-file-based-configuration"></a>启用基于文件的配置

1. 在项目根目录（部署到 Web/函数应用中的 D:\home\site\wwwroot）为配置创建新的 JSON 文件。 根据[基于文件的配置引用](#configuration-file-reference)填写所需的配置。 如果修改现有 Azure 资源管理器配置，确保将 `authsettings` 集合中捕获的属性转换为配置文件。

2. 修改现有配置，它在 `Microsoft.Web/sites/<siteName>/config/authsettingsV2` 下的 [Azure 资源管理器](../azure-resource-manager/management/overview.md) API 中捕获。 若要进行修改，可以使用 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md) 或 [Azure 资源浏览器](https://resources.azure.com/)之类的工具。 在 authsettingsV2 集合中，需要设置两个属性（并且可能会删除其他属性）：

    1. 将 `platform.enabled` 设为 true
    2. 将 `platform.configFilePath` 设为文件的名称（例如 auth.json）

> [!NOTE]
> `platform.configFilePath` 的格式在平台之间有所不同。 在 Windows 上，支持相对路径和绝对路径。 建议使用相对路径。 对于 Linux，当前仅支持绝对路径，因此设置的值应为“/home/site/wwwroot/auth.json”或类似。

完成此配置更新后，该文件的内容将用于定义该站点的应用服务身份验证/授权行为。 如果希望返回到 Azure 资源管理器配置，可以通过将 `platform.configFilePath` 设置更改为 null 来实现。

## <a name="configuration-file-reference"></a>配置文件引用

从配置文件引用的任何机密都必须存储为[应用程序设置](./configure-common.md#configure-app-settings)。 可以将设置命名为任何所需名称。 只需确保配置文件中的引用使用相同的键。

以下详尽无遗地介绍文件中的可能配置选项：

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="more-resources"></a>更多资源

- [教程：对用户进行端到端身份验证和授权](tutorial-auth-aad.md)
- [用于身份验证的环境变量和应用设置](reference-app-settings.md#authentication--authorization)
