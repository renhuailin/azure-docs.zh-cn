---
title: 指定请求服务 REST API 验证请求
titleSuffix: Azure Active Directory Verifiable Credentials
description: 了解如何启动可验证凭据出示请求
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 264fbdf3f0e7640e1a247288cec56b38e57485fc
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730528"
---
# <a name="request-service-rest-api-presentation-specification-preview"></a>请求服务 REST API 出示规范（预览版）

使用 Azure Active Directory (Azure AD) 的可验证凭据请求服务 REST API，可以颁发和验证可验证的凭据。 本文详细说明了用于出示请求的请求服务 REST API。 出示请求要求用户出示可验证凭据，然后验证该凭据。


## <a name="http-request"></a>HTTP 请求

请求服务 REST API 出示请求支持以下 HTTP 方法：

| 方法 |说明  |
|---------|---------|
|POST | 使用本文中指定的 JSON 有效负载。 |

请求服务 REST API 出示请求需要以下 HTTP 头：

| 方法 |值  |
|---------|---------|
|`Authorization`| 将访问令牌作为持有者令牌附加到 HTTP 请求中的授权头。 例如 `Authorization: Bearer <token>`。|
|`Content-Type`| `Application/json`|

构建对请求服务 REST API 的 HTTP POST 请求。 将 `{tenantID}` 替换为你的[租户 ID](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application) 或租户名称。

```http
https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
```

以下 HTTP 请求演示了对请求服务 REST API 的出示请求：

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{  
    "includeQRCode": true,  
    "callback": {  
    "url": "https://www.contoso.com/api/verifier/presentationCallbac",  
    "state": "11111111-2222-2222-2222-333333333333",  
      "headers": {  
        "api-key": "an-api-key-can-go-here"  
      }  
    },  
    ...
} 
```  

需要拥有以下权限才能调用请求服务 REST API。 有关详细信息，请参阅[授予权限以获取访问令牌](verifiable-credentials-configure-tenant.md#31-grant-permissions-to-get-access-tokens)。

| 权限类型 | 权限  |
|---------|---------|
| 应用程序 | bbb94529-53a3-4be5-a069-7eaf2712b826/.default|

## <a name="presentation-request-payload"></a>出示请求有效负载

出示请求有效负载包含有关可验证凭据出示请求的信息。 以下示例演示了来自特定颁发者的出示请求。 此请求的结果返回一个 QR 码，其中包含用于启动出示过程的链接。

```json
{
  "includeQRCode": true,
  "callback": {
    "url": "https://www.contoso.com/api/verifier/presentationCallback",
    "state": "92d076dd-450a-4247-aa5b-d2e75a1a5d58",
    "headers": {
      "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
    }
  },
  "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiOiJiRWo5MDY...",
  "registration": {
    "clientName": "Veritable Credential Expert Verifier"
  },
  "presentation": {
    "includeReceipt": true,
    "requestedCredentials": [
      {
        "type": "VerifiedCredentialExpert",
        "purpose": "So we can see that you a veritable credentials expert",
        "acceptedIssuers": [
          "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiO..."
        ]
      }
    ]
  }
}
```

有效负载包含以下属性。  

|参数 |类型  | 说明 |
|---------|---------|---------|
| `includeQRCode` |  boolean |   确定此请求的响应中是否包含 QR 码。 显示 QR 码并要求用户扫描它。 扫描该 QR 码会通过此出示请求启动验证器应用。 可能的值为 `true`（默认值）或 `false`。 如果设置为 `false`，则使用 `url` 返回属性来呈现深层链接。  |
| `authority` | string|  验证者 Azure AD 租户的分散式标识符。 有关详细信息，请参阅[收集租户详细信息以设置示例应用程序](verifiable-credentials-configure-verifier.md#gather-tenant-details-to-set-up-your-sample-application)。|
| `registration` | [RequestRegistration](#requestregistration-type)|  提供有关验证者的信息。 |
| `presentation` | [RequestPresentation](#requestpresentation-type)| 提供有关可验证凭据出示请求的信息。  |
|`callback`|  [Callback](#callback-type)| 允许开发人员在可验证凭据出示期间更新 UI。 当用户完成该过程时，一旦结果返回到应用程序，就继续执行该过程。|

### <a name="requestregistration-type"></a>RequestRegistration 类型

RequestRegistration 类型为颁发者提供信息注册。 RequestRegistration 类型包含以下属性：

|属性 |类型 |说明 |
|---------|---------|---------|
| `clientName` | string|  可验证凭据的颁发者的显示名称。 此名称将向验证器应用中的用户显示。 |

以下屏幕截图显示了 `clientName` 属性，以及出示请求中 `authority`（验证者）的显示名称。

![显示如何批准出示请求的屏幕截图。](media/presentation-request-api/approve-presentation-request.jpg)

### <a name="requestpresentation-type"></a>RequestPresentation 类型

RequestPresentation 提供出示可验证凭据所需的信息。 RequestPresentation 包含以下属性：

|属性 |类型 |说明 |
|---------|---------|---------|
| `includeReceipt` |  boolean | 确定是否应在此请求的响应中包含回执。 可能的值为 `true` 或 `false`（默认值）。 回执包含从验证器发送到可验证凭据服务的原始有效负载。  回执用于故障排除，默认情况下不应设置回执。 在 OpenId Connect SIOP 请求中，回执包含原始请求中的 ID 令牌。 |
| `requestedCredentials` | collection| [RequestCredential](#requestcredential-type) 对象的集合。|

### <a name="requestcredential-type"></a>RequestCredential 类型

RequestCredential 提供有关用户需要提供的所请求的凭据的信息。 RequestCredential 包含以下属性：

|属性 |类型 |说明 |
|---------|---------|---------|
| `type`| string| 可验证凭据类型。 `type` 必须匹配颁发者可验证凭据清单中定义的类型。 例如 `VerifiedCredentialExpert`。 若要获取颁发者清单，请按照指南[收集凭据和环境详细信息以设置示例应用程序](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application)进行操作。 复制“颁发凭据 URL”并在 Web 浏览器中将其打开，然后检查 id 属性 。 |
| `purpose`| string | 提供有关请求此可验证凭据的目的的信息。 |
| `acceptedIssuers`| 字符串集合 | 颁发者 DID 的集合，这些颁发者 DID 可以颁发使用者可出示的可验证凭据类型。 若要获取你的颁发者 DID，请按照指南[收集凭据和环境详细信息以设置示例应用程序](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application)进行操作，并复制“分散式标识符(DID)”的值。 |


### <a name="callback-type"></a>回调类型

请求服务 REST API 会生成发往回调终结点的多个事件。 这些事件使你可以更新 UI，并在结果返回到应用程序后继续执行过程。 Callback 类型包含以下属性：

|属性 |类型 |说明 |
|---------|---------|---------|
| `url` | string| 应用程序的回调终结点的 URI。 |
| `state` | string| 与初始有效负载中传递的状态相关联。 |
| `headers` | string| [可选] 可以包含 POST 消息接收端所需的 HTTP 头的集合。 头中只应包含 api-key 或用于授权的任何头。|

## <a name="successful-response"></a>成功的响应

如果成功，则此方法将在响应正文中返回 HTTP 201“已创建”响应代码和事件对象集合。 以下 JSON 演示了成功响应：

```json
{  
    "requestId": "e4ef27ca-eb8c-4b63-823b-3b95140eac11",
    "url": "openid://vc/?request_uri=https://beta.did.msidentity.com/v1.0/87654321-0000-0000-0000-000000000000/verifiablecredentials/request/e4ef27ca-eb8c-4b63-823b-3b95140eac11",
    "expiry": 1633017751,
    "qrCode": "data:image/png;base64,iVBORw0KGgoA<SNIP>"
} 
```

该响应包含以下属性：

|属性 |类型 |说明 |
|---------|---------|---------|
| `requestId`| string | 自动生成的相关 ID。 [回调](#callback-events)使用同一请求。 使你可以跟踪出示请求及其回调。 |
| `url`|  string| 用于启动验证器应用并启动出示过程的 URL。 如果用户无法扫描 QR 码，你可以向他们显示此 URL。 |
| `expiry`| integer| 指示响应过期时间。 |
| `qrCode`| string | 可供用户扫描以启动出示流的 QR 码。 |

当应用收到响应时，应用需要向用户显示 QR 码。 用户扫描 QR 码，这会打开验证器应用并启动出示过程。

## <a name="error-response"></a>错误响应

还可以返回错误响应，使应用能够对其进行适当处理。 以下 JSON 显示了未授权错误消息。


```json
{
    "requestId": "fb888ac646c96083de83b099b2678de0",
    "date": "Wed, 29 Sep 2021 21:49:00 GMT",
    "error": {
        "code": "unauthorized",
        "message": "Failed to authenticate the request."
    }
}
```

该响应包含以下属性：

|属性 |类型 |说明 |
|---------|---------|---------|
| `requestId`| string | 自动生成的请求 ID。|
| `date`| date| 出错时间。 |
| `error.code` | string| 返回错误代码。 |
| `error.message`| 字符串| 错误消息。 |

## <a name="callback-events"></a>回调事件

当用户扫描 QR 码、在其验证器应用中使用深层链接或完成出示过程时，将调用回调终结点。 

|属性 |类型 |说明 |
|---------|---------|---------|
| `requestId`| string | 在将有效负载发布到可验证凭据服务后映射到原始请求。|
| `code` |string |在验证器应用检索到请求时返回的代码。 可能的值： <ul><li>`request_retrieved` 用户扫描了 QR 码，或单击了用于启动出示流的链接。</li><li>`presentation_verified` 可验证凭据验证已成功完成。</li></ul>    |
| `state` |string| state 返回在原始有效负载中传递的状态值。   |
| `subject`|string | 可验证凭据用户 DID。|
| `issuers`| array |返回所请求的可验证凭据的数组。 对于它提供的每个可验证凭据： </li><li>可验证凭据类型。</li><li>检索到的声明。</li><li>可验证凭据颁发者的域。 </li><li>可验证凭据颁发者的域验证状态。 </li></ul> |
| `receipt`| string | [可选] 回执包含从验证器发送到可验证凭据服务的原始有效负载。  |

以下示例演示了当验证器应用启动出示请求时的回调有效负载。

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

以下示例演示了成功完成可验证凭据出示后的回调有效负载。

```json
{
  "requestId": "87e1cb24-9096-409f-81cb-9e645f23a4ba",
  "code": "presentation_verified",
  "state": "f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",
  "subject": "did:ion:EiAlrenrtD3Lsw0GlbzS1O2YFdy3Xtu8yo35W<SNIP>…",
  "issuers": [
    {
      "type": [
        "VerifiableCredential",
        "VerifiedCredentialExpert"
      ],
      "claims": {
        "firstName": "John",
        "lastName": "Doe"
      },
      "domain": "https://contoso.com/",
      "verified": "DNS",
      "issuer": "did:ion:….."
    }
  ],
  "receipt": {
    "id_token": "eyJraWQiOiJkaWQ6aW<SNIP>"
  }
} 
```

## <a name="next-steps"></a>后续步骤

了解[如何调用请求服务 REST API](get-started-request-api.md)
