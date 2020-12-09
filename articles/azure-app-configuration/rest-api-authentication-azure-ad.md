---
title: Azure Active Directory REST API 身份验证
description: 使用 Azure Active Directory 通过使用来进行 Azure 应用配置的身份验证 REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cbf05245768a663e324e9bb6e1ad422eeee3ab1a
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930511"
---
# <a name="azure-active-directory-authentication"></a>Azure Active Directory 身份验证

你可以通过使用身份验证方案来验证 HTTP 请求 `Bearer` ，该身份验证方案使用从 Azure Active Directory (Azure AD) 获取的令牌。 必须通过传输层安全性 (TLS) 传输这些请求。

## <a name="prerequisites"></a>先决条件

必须将用于请求 Azure AD 令牌的主体分配给某个适用的 [Azure 应用配置角色](./rest-api-authorization-azure-ad.md)。

为每个请求提供身份验证所需的所有 HTTP 标头。 下面是最低要求：

|  请求标头 | 说明  |
| --------------- | ------------ |
| `Authorization` | 方案所需的身份验证信息 `Bearer` 。 |

**示例：**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Azure AD 令牌采集

在获取 Azure AD 令牌之前，你必须确定你要进行身份验证的用户、你请求令牌的用户以及要使用的 Azure AD 终结点 () 颁发者。

### <a name="audience"></a>目标受众

使用适当的受众请求 Azure AD 令牌。 对于 Azure 应用配置，请使用以下访问群体之一。 还可以将受众称为请求令牌的 *资源* 。

- {configurationStoreName}.azconfig.io
- *.azconfig.io

> [!IMPORTANT]
> 当请求的受众为时 `{configurationStoreName}.azconfig.io` ，它必须与 `Host` 请求标头完全匹配 (区分大小写) 用于发送请求。

### <a name="azure-ad-authority"></a>Azure AD 颁发机构

Azure AD 机构是用于获取 Azure AD 令牌的终结点。 其形式为 `https://login.microsoftonline.com/{tenantId}` 。 `{tenantId}`该段指的是尝试进行身份验证的用户或应用程序所属的 Azure AD 租户 ID。

### <a name="authentication-libraries"></a>身份验证库

Azure 提供一组称为 Azure Active Directory 身份验证库的库，用于简化获取 Azure AD 令牌的过程。 Azure 为多种语言构建这些库。 有关详细信息，请参阅[本文档](../active-directory/azuread-dev/active-directory-authentication-libraries.md)。

## <a name="errors"></a>错误

你可能会遇到以下错误。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**原因：** 尚未提供具有方案的授权请求标头 `Bearer` 。

**解决方案：** 提供有效的 `Authorization` HTTP 请求标头。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**原因：** Azure AD 标记无效。

**解决方案：** 从 Azure AD 机构获取 Azure AD 令牌，并确保已使用适当的受众。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**原因：** Azure AD 标记无效。

**解决方案：** 从 Azure AD 机构获取 Azure AD 令牌。 确保 Azure AD 租户是与配置存储所属的订阅关联的租户。 如果主体属于多个 Azure AD 租户，则会出现此错误。