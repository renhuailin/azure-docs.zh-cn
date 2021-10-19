---
title: 指定请求服务 REST API 发布请求
titleSuffix: Azure Active Directory Verifiable Credentials
description: 了解如何发布已发布的可验证凭据
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 6b1a33ee563123d5fb724f0bc29c3e7c753a86ea
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129811059"
---
# <a name="request-service-rest-api-issuance-specification-preview"></a>请求服务 REST API 发布规范（预览版）

可以使用 Azure Active Directory (Azure AD) 可验证凭据请求服务 REST API，发布及验证真实凭据。 本文将指定请求服务 REST API 发布请求。


## <a name="http-request"></a>HTTP 请求

请求服务 REST API 发布请求支持以下 HTTP 方法：

| 方法 |说明  |
|---------|---------|
|POST | 使用本文中指定的 JSON 有效负载。 |

请求服务 REST API 发布请求需要用到以下 HTTP 标头：

| 方法 |值  |
|---------|---------|
|`Authorization`| 将访问令牌作为持有者令牌附加到 HTTP 请求中的授权标头。 例如 `Authorization: Bearer <token>`。|
|`Content-Type`| `Application/json`|

构建对请求服务 REST API 的 HTTP POST 请求。 将 `{tenantID}` 替换为你的[租户 ID](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application) 或租户名称。

```http
https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
```

以下 HTTP 请求演示了对请求服务 REST API 的 HTTP 请求：

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
    "includeQRCode": true, 
        "callback": {  
            "url": "https://wwww.contoso.com/vc/callback",  
            "state": "Aaaabbbb11112222", 
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

## <a name="issuance-request-payload"></a>发布请求有效负载

发布请求有效负载包含有关可验证凭据发布请求的信息。 下方示例演示了使用 PIN 代码流和用户声明（如名字和姓氏）的发布请求。 此请求的结果返回一个 QR 码，其中包含用于启动发布过程的链接。

```json
{
    "includeQRCode": true,
    "callback": {
        "url": "https://www.contoso.com/api/issuer/issuanceCallback",
        "state": "de19cb6b-36c1-45fe-9409-909a51292a9c",
        "headers": {
            "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
        }
    },
    "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDek...",
    "registration": {
        "clientName": "Verifiable Credential Expert Sample"
    },
    "issuance": {
        "type": "VerifiedCredentialExpert",
        "pin": {
            "value": "3539",
            "length": 4
        },
        "claims": {
            "given_name": "Megan",
            "family_name": "Bowen"
        }
    }
}
```

有效负载包含以下属性。  


|参数 |类型  | 说明 |
|---------|---------|---------|
| `includeQRCode` |  boolean |   确定此请求的响应中是否包含 QR 码。 显示 QR 码并要求用户扫描它。 扫描该 QR 码后，系统便会通过发布请求启动验证器应用。 可能的值：`true`（默认值）或 `false`。 如果设置为 `false`，则使用返回 `url` 属性来呈现深层链接。  |
| `authority` | string|  发布者的分散标识符。 有关详细信息，请参阅[收集凭据和环境详细信息来设置示例应用程序](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application)。|
| `registration` | [RequestRegistration](#requestregistration-type)|  提供可以在验证器应用程序中显示的发布者的相关信息。 |
| `issuance` | [RequestIssuance](#requestissuance-type)| 提供有关发布请求的信息。  |
|`callback`|  [Callback](#callback-type)| 允许开发人员在可验证凭据发布过程中异步获取有关流的信息。 例如，在用户扫描 QR 码后发生的调用。|

### <a name="requestregistration-type"></a>RequestRegistration 类型

RequestRegistration 类型可为发布者提供信息注册。 RequestRegistration 类型包含以下属性：

|属性 |类型 |说明 |
|---------|---------|---------|
| `clientName` | string|  可验证凭据发布者的显示名称。  |
| `logoUrl` |  string |  [可选] 发布者徽标的 URL。  |
| `termsOfServiceUrl` |  string | [可选] 你发布的可验证凭据的使用条款的 URL。  |

> [!NOTE]
> 此时，RequestRegistration 信息不会在发布期间显示于 Microsoft Authenticator 应用中，但可以在有效负载中使用。

### <a name="requestissuance-type"></a>RequestIssuance 类型

RequestIssuance 提供发布可验证凭据所需的信息。 目前有三种可在 RequestIssuance 中发送的输入类型。 可验证凭据发布服务使用这些类型将声明插入到可验证凭据中，并利用发布者的 DID 来证明该信息。 上述三种类型如下所示：

- ID 令牌
- 通过可验证的呈交提供的可验证凭据。
- 自证声明

可在[自定义可验证凭据](credential-design.md)一文中找到有关输入类型的详细信息。 

RequestIssuance 包含以下属性：

|属性 |类型 |说明 |
|---------|---------|---------|
| type |  字符串 |  可验证凭据类型。 应当匹配可验证凭据清单中定义的类型。 例如：`VerifiedCredentialExpert`。 有关详细信息，请参阅[在 Azure 中创建可验证凭据专家卡](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application)。 |
| manifest | string| 可验证凭据清单文档的 URL。 有关详细信息，请参阅[收集凭据和环境详细信息来设置示例应用程序](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application)。|
| 声明 | string| [可选] 包括使用者在可验证凭据中进行的断言的集合。 对于 PIN 代码流，必须提供用户的名字和姓氏，这一点非常重要。 有关详细信息，请参阅[可验证凭据名称](verifiable-credentials-configure-issuer.md#verifiable-credential-names)。 |
| 固定 | [PIN](#pin-type)| [可选] 用于在发布期间增强安全性的 PIN 号。 对于 PIN 代码流，此属性为必需。 你可以生成 PIN 代码，并将其提供给应用中的用户。 用户必须提供你生成的 PIN 代码。 |

### <a name="pin-type"></a>PIN 类型

PIN 类型定义可在发布 PIN 期间显示的 PIN 代码，其中 PIN 是可选的，并且在使用时应当一律采用带外发送的方式。 使用哈希 PIN 代码时，必须定义 salt、alg 和迭代属性。 PIN 包含以下属性：

|属性 |类型 |说明 |
|---------|---------|---------|
| `value` | string| 包含纯文本形式的 PIN 值。 使用哈希 PIN 时，value 属性包含 base64 编码的加盐哈希。|
| `type` | string|  Pin 代码的类型。 可能的值：`numeric`（默认值）。 |
| `length` | integer|  PIN 代码的长度。  默认长度为 6。 最小长度：4 最大长度：16。|
| `salt` | string|  哈希 PIN 代码的盐。 盐在哈希计算期间附加。 编码格式：UTF-8。 |
| `alg` | string|  用于哈希 PIN 的哈希算法。 支持的算法：`sha256`。 |
| `iterations` | integer| 哈希迭代数。 可能的值：`1`。|


### <a name="callback-type"></a>回调类型

请求服务 REST API 会生成发往回调终结点的多个事件。 这些事件使你可以更新 UI，并在将结果返回到应用程序后继续执行进程。 回调类型包含以下属性：

|属性 |类型 |说明 |
|---------|---------|---------|
| `url` | string| 应用程序回调终结点的 URI。 |
| `state` | string| 与初始有效负载中传递的状态相关联。 |
| `headers` | string| [可选] 可以包含 POST 消息接收端所需的 HTTP 标头的集合。 标头中只应包含 api-key 或用于授权的任何标头。|

## <a name="successful-response"></a>成功的响应

如果成功，则此方法将在响应正文中返回 HTTP 201 Created 响应代码以及事件对象的集合。 以下 JSON 演示的是成功响应情形：

```json
{  
    "requestId": :"799f23ea-5241-45af-99ad-cf8e5018814e",  
    "url": "openid://vc?request_uri=https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiablecredentials/request/178319f7-20be-4945-80fb-7d52d47ae82e",  
    "expiry": 1622227690,  
    "qrCode": "data:image/png;base64,iVBORw0KggoA<SNIP>"  
} 
```

该响应包含以下属性：

|属性 |类型 |说明 |
|---------|---------|---------|
| `requestId`| string | 自动生成的相关 ID。 [回调](#callback-events)使用同一请求。 使你可以跟踪发布请求及其回调。 |
| `url`|  string| 用于启动验证器应用以及启动发布过程的 URL。 如果用户无法扫描 QR 码，可以向他们提供此 URL。 |
| `expiry`| integer| 指示响应的过期时间。 |
| `qrCode`| string | 用户可以通过扫描它来启动发布流的 QR 码。 |

应用收到响应时，需要向用户提供 QR 码。 如果用户扫描 QR 码，验证器应用即会打开，发布进程亦会启动。

## <a name="error-response"></a>错误响应

还可以返回错误响应，以便应用作出适当处理。 以下 JSON 显示了未授权错误消息。


```json
{
    "requestId": "d60b068e7fbd975896e179b99347866a",
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

当用户扫描 QR 码、在其验证器应用中使用深层链接或完成发布进程时，系统将调用回调终结点。 


|属性 |类型 |说明 |
|---------|---------|---------|
| `requestId`| string | 在将有效负载发布到可验证凭据服务后，会映射到原始请求。|
| `code` |string |在验证器应用检索到请求时返回的代码。 可能的值： <ul><li>`request_retrieved` 用户扫描了 QR 码，或单击了用于启动发布流的链接。</li><li>`issuance_successful` 已成功发布可验证凭据。</li><li>`Issuance_error` 发布时出错。 详情请查看 `error` 属性。</li></ul>    |
| `state` |string| state 返回在原始有效负载中传递的状态值。   |
| `error`| error | 如果 `code` 为 `Issuance_error`，则此属性包含有关错误的信息。| 
| `error.code` | string| 返回错误代码。 |
| `error.message`| 字符串| 错误消息。 |

以下示例演示了当验证器应用启动发布请求时的回调有效负载。

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

下方示例演示了用户成功完成发布进程后的回调有效负载。

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",
    "code":"issuance_successful",
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",
} 
```

### <a name="callback-errors"></a>回调错误  

可以通过错误消息调用回调终结点。

下表列出了错误代码。 这些特定于错误的详细信息一般会存储可能在发布过程中出现的大多数错误。

|Message  |定义    |
|---------|---------|
| `fetch_contract_error*`| 无法提取可验证凭据协定。 当 API 无法提取请求有效负载 [RequestIssuance 对象](#requestissuance-type)中指定的清单时，通常会发生此错误|
| `issuance_service_error*` | 可验证凭据服务无法验证要求，或者可验证凭据服务端出现问题。|
| `unspecified_error`| 发生未放入此存储桶的问题。 不应该经常收到此错误，但始终值得展开调查。 |

以下示例演示发生错误时的回调有效负载。

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",  
    "code": "issuance_error",  
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",  
    "error": { 
      "code":"IssuanceFlowFailed", 
      "message":"issuance_service_error”, 
    } 
} 
``` 

## <a name="next-steps"></a>后续步骤

了解[如何调用请求服务 REST API](get-started-request-api.md)
