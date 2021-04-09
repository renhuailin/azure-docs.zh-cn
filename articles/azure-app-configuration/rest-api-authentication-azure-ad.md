---
title: Azure Active Directory REST API - 身份验证
description: 通过 REST API 使用 Azure Active Directory 向“Azure 应用程序配置”进行身份验证
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cbf05245768a663e324e9bb6e1ad422eeee3ab1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930511"
---
# <a name="azure-active-directory-authentication"></a>Azure Active Directory 身份验证

可以将 `Bearer`身份验证方案与从 Azure Active Directory (Azure AD) 获取的令牌配合使用，来对 HTTP 请求进行身份验证。 必须通过传输层安全性 (TLS) 传输这些请求。

## <a name="prerequisites"></a>先决条件

必须将用于请求 Azure AD 令牌的主体分配到适用的 [Azure 应用程序配置角色](./rest-api-authorization-azure-ad.md)之一。

为每个请求提供身份验证所需的所有 HTTP 标头。 以下是最低要求：

|  请求标头 | 说明  |
| --------------- | ------------ |
| `Authorization` | `Bearer` 方案所需的身份验证信息。 |

**示例：**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Azure AD 令牌获取

在获取 Azure AD 令牌之前，必须先确定要以哪一用户的身份进行身份验证、要为哪一受众请求令牌，以及要使用哪一 Azure AD 终结点（颁发机构）。

### <a name="audience"></a>目标受众

为适当的受众请求 Azure AD 令牌。 对于 Azure 应用程序配置，使用以下受众之一。 受众还可以称作要为其请求令牌的“资源”。

- {configurationStoreName}.azconfig.io
- *.azconfig.io

> [!IMPORTANT]
> 当请求的受众为 `{configurationStoreName}.azconfig.io` 时，它必须与用于发送请求的 `Host` 请求标头（区分大小写）完全匹配。

### <a name="azure-ad-authority"></a>Azure AD 颁发机构

Azure AD 颁发机构是用于获取 Azure AD 令牌的终结点。 其形式为 `https://login.microsoftonline.com/{tenantId}`。 `{tenantId}` 段指的是尝试进行身份验证的用户或应用程序所属的 Azure AD 租户 ID。

### <a name="authentication-libraries"></a>身份验证库

Azure 提供一组称为 Azure Active Directory 身份验证库的库来简化获取 Azure AD 令牌的过程。 Azure 针对多种语言生成了这些库。 有关详细信息，请参阅[本文档](../active-directory/azuread-dev/active-directory-authentication-libraries.md)。

## <a name="errors"></a>错误

你可能会遇到以下错误。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**原因：** 你未提供使用 `Bearer` 方案的 Authorization 请求头。

**解决方案：** 提供有效的 `Authorization` HTTP 请求头。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**原因：** Azure AD 令牌无效。

**解决方案：** 从 Azure AD 颁发机构获取 Azure AD 令牌，并确保已使用适当的受众。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**原因：** Azure AD 令牌无效。

**解决方案：** 从 Azure AD 颁发机构获取 Azure AD 令牌。 确保 Azure AD 租户与配置存储所属的订阅关联。 如果主体属于多个 Azure AD 租户，则可能出现此错误。