---
title: 注册 SaaS 应用程序 - Azure 市场
description: 了解如何使用 Azure 门户来注册 SaaS 应用程序并接收 Azure Active Directory 安全令牌。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
author: saasguide
ms.author: souchak
ms.openlocfilehash: 3e2c92d5fed23480f102f1c96631c3a8c9a2e2c8
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456492"
---
# <a name="register-a-saas-application"></a>注册 SaaS 应用程序

本文介绍如何使用 Microsoft [Azure 门户](https://portal.azure.com/)来注册 SaaS 应用程序，以及如何获取发布者的访问令牌（Azure Active Directory 访问令牌）。 发布者将使用此令牌通过调用 SaaS 履行 API 对 SaaS 应用程序进行身份验证。  履行 API 使用 OAuth 2.0 客户端凭据来授权 Azure Active Directory (v1.0) 终结点上的流发出服务到服务访问令牌请求。

Azure 市场不会对你的 SaaS 服务用于最终用户的身份验证方法施加任何限制。 只是在对 Azure 市场中的 SaaS 服务进行身份验证时才会需要下面的流。

有关 Azure AD (Active Directory) 的详细信息，请参阅[什么是身份验证](../../active-directory/develop/authentication-vs-authorization.md)？

## <a name="register-an-azure-ad-secured-app"></a>注册受 Azure AD 保护的应用

任何想要使用 Azure AD 功能的应用程序都必须先在 Azure AD 租户中注册。 此注册过程涉及向 Azure AD 提供有关你的应用程序的一些详细信息。 若要使用 Azure 门户注册新应用程序，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 如果你的帐户有权访问多个 Azure AD 租户，请在右上角单击该帐户，并将门户会话设置为所需租户。
3. 在左侧导航窗格中，依次单击“Azure Active Directory”服务、“应用注册”、“新建应用程序注册”。

    ![SaaS AD 应用注册](./media/saas-offer-app-registration-v1.png)

4. 在“创建”页上，输入应用程序的注册信息：
    -   **名称**：输入有意义的应用程序名称
    -   **应用程序类型**：  
        
        为安装在安全服务器上的[客户端应用程序](../../active-directory/develop/developer-glossary.md#client-application)和[资源/API 应用程序](../../active-directory/develop/developer-glossary.md#resource-server)选择“Web 应用/API”。 此设置用于 OAuth 机密性 [Web 客户端](../../active-directory/develop/developer-glossary.md#web-client)和公共的[基于用户代理的客户端](../../active-directory/develop/developer-glossary.md#user-agent-based-client)。
        相同的应用程序还可以公开客户端和资源/API。

        有关 Web 应用程序的特定示例，请查看 [Azure AD 开发人员指南](../../active-directory/develop/index.yml)的[入门](../../active-directory/develop/quickstart-create-new-tenant.md)部分提供的快速入门引导式设置。

5. 完成后，单击“注册”。  Azure AD 会将一个独一无二的应用程序 ID 分配到你的新应用程序。 建议注册一个只访问该 API 的应用（作为单一租户注册）。

6. 若要创建客户端密码，请导航到“证书和机密”页，然后单击“+ 新建客户端密码” 。  请确保复制该机密值，以便在代码中使用。

Azure AD 应用 ID 与你的发布者 ID 相关联，因此，请确保在你所有的产品/服务中都使用同一应用 ID。

>[!Note]
>如果发布者在合作伙伴中心有两个或更多不同的帐户，则 Azure AD 应用程序注册详细信息只能在一个帐户中使用。 不支持在不同的发布者帐户下使用同一租户 ID、产品/服务的应用程序 ID 对。

## <a name="how-to-get-the-publishers-authorization-token"></a>如何获取发布者的授权令牌

在注册了应用程序后，可以通过编程方式来请求发布者的授权令牌（Azure AD 访问令牌，使用 Azure AD V1 终结点）。 发布者在调用各种 SaaS 履行 API 时必须使用此令牌。 此令牌仅在 1 小时内有效。 

有关这些令牌的详细信息，请参阅 [Azure Active Directory 访问令牌](../../active-directory/develop/access-tokens.md)。  请注意，下面的流中使用了 V1 终结点令牌。

### <a name="get-the-token-with-an-http-post"></a>使用 HTTP POST 获取令牌

#### <a name="http-method"></a>HTTP 方法

邮递<br>

##### <a name="request-url"></a>*请求 URL* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*URI 参数*

|  参数名称    |  必需         |  说明 |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  True      |  已注册的 AAD 应用程序的租户 ID。 |

##### <a name="request-header"></a>请求标头

|  标头名称       |  必需         |  说明 |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  True      |  与请求关联的内容类型。 默认值是 `application/x-www-form-urlencoded`。 |

##### <a name="request-body"></a>*请求正文*

|  属性名称     |  必需         |  说明 |
|  ---------------   |  ---------------  | ------------ |
|  `grant_type`      |  True      |  授权类型。 使用 `"client_credentials"`。 |
|  `client_id`       |  True      |  与 Azure AD 应用关联的客户端/应用标识符。 |
|  `client_secret`   |  True      |  与 Azure AD 应用关联的机密。 |
|  `resource`        |  True      |  为其请求令牌的目标资源。 使用 `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`，因为在本例中市场 SaaS API 始终是目标资源。 |

##### <a name="response"></a>*响应*

|  名称     |  类型         |  说明 |
|  ------   |  ---------------  | ------------ |
|  200 正常   |  TokenResponse    |  请求成功。 |

##### <a name="tokenresponse"></a>*TokenResponse*

示例响应：

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

| 元素 | 说明 |
| ------- | ----------- |
| `access_token` | 此元素是 `<access_token>`，在调用所有 SaaS 履行和市场计量 API 时，该值将作为授权参数传递。 调用受保护 REST API 时，该令牌将作为“持有者”令牌嵌入在 `Authorization` 请求标头字段中，使 API 能够对调用方进行身份验证。 | 
| `expires_in` | 访问令牌在过期之前保持有效的秒数，从颁发时间开始算起。 可在令牌的 `iat` 声明中找到颁发时间。 |
| `expires_on` | 访问令牌过期的时间范围。 该日期表示为自“1970-01-01T0:0:0Z UTC”开始的秒数（对应于令牌的 `exp` 声明）。 |
| `not_before` | 访问令牌生效且可被接受的时间范围。 该日期表示为自“1970-01-01T0:0:0Z UTC”开始的秒数（对应于令牌的 `nbf` 声明）。 |
| `resource` | 请求访问令牌时所针对的资源，与请求的 `resource` 查询字符串参数匹配。 |
| `token_type` | 令牌的类型，这是一个“持有者”访问令牌，意味着资源可向此令牌的持有者授予访问权限。 |

## <a name="next-steps"></a>后续步骤

受 Azure AD 保护的应用现在可以使用 [SaaS 履行 API 版本 2](./pc-saas-fulfillment-api-v2.md)。
