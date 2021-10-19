---
title: 如何调用请求服务 REST API
titleSuffix: Azure Active Directory Verifiable Credentials
description: 了解如何使用请求服务发布和验证 REST API
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 6dff1940219aa666453d150ed24d84f8eb0165da
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129809864"
---
# <a name="request-service-rest-api-preview"></a>请求服务 REST API（预览版）

Azure Active Directory 可验证凭据请求服务 REST API 允许使用 Azure AD 可验证凭据服务发布和验证可验证凭据。 本问将介绍如何开始使用请求服务 REST API。

> [!IMPORTANT]
> 请求服务 REST API 目前提供公共预览版 (beta)。
> 此预览版本的提供不含服务级别协议，我们会不时在该预览版本中针对 API 进行重大更改和弃用。 在预览版中，不建议将 API 用于生产工作负载。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="api-access-token"></a>API 访问令牌

若要让应用程序访问请求服务 REST API，需要引入具有所需权限的有效访问令牌。 Microsoft 标识平台发布的访问令牌包含请求服务 REST API 用于验证调用方的信息（范围）。 确保调用方具有执行所请求的操作的适当权限。

若要获取访问令牌，你的应用必须向 Microsoft 标识平台注册，并由管理员授权以访问请求服务 API。 若尚未注册 verifiable-credentials-app 应用程序，请依次按照[如何注册应用](verifiable-credentials-configure-tenant.md#step-3-register-an-application-in-azure-ad)和[生成应用程序机密](verifiable-credentials-configure-issuer.md#configure-the-verifiable-credentials-app)中的步骤进行操作。

### <a name="get-an-access-token"></a>获取访问令牌

使用 [OAuth 2.0 客户端凭据授予流](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)来获取使用 Microsoft 标识平台的访问令牌。 建议使用受信任的 oauth 库。 在此教程中，我们使用 Microsoft 身份验证库 [MSAL](../../active-directory/develop/msal-overview.md)。 MSAL 是 Microsoft 提供的库，它简化了向可调用安全 web API 的应用添加身份验证和授权的程序。

# <a name="http"></a>[HTTP](#tab/http)

```http
Pleaes refer to to the Microsoft Authentication Library (MSAL) documentation for more information on how to acquire tokens via HTTP.
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Initialize MSAL library by using the following code
ConfidentialClientApplicationBuilder.Create(AppSettings.ClientId)
    .WithClientSecret(AppSettings.ClientSecret)
    .WithAuthority(new Uri(AppSettings.Authority))
    .Build();

// Acquire an access token
result = await app.AcquireTokenForClient(AppSettings.Scopes)
                .ExecuteAsync();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
// Initialize MSAL library by using the following code
const msalConfig = {
  auth: {
      clientId: config.azClientId,
      authority: `https://login.microsoftonline.com/${config.azTenantId}`,
      clientSecret: config.azClientSecret,
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
const msalClientCredentialRequest = {
  scopes: ["bbb94529-53a3-4be5-a069-7eaf2712b826/.default"],
  skipCache: false, 
};
module.exports.msalCca = cca;
module.exports.msalClientCredentialRequest = msalClientCredentialRequest;

// Acquire an access token
const result = await mainApp.msalCca.acquireTokenByClientCredential(mainApp.msalClientCredentialRequest);
    if ( result ) {
      accessToken = result.accessToken;
    }
```

---

在上面的代码中提供以下参数。

| 参数 | 条件 | 说明 |
| --- | --- | --- |
| 颁发机构 | 必需 | 应用程序计划对其进行操作的目录租户。 例如 `https://login.microsoftonline.com/{your-tenant}`，将 `your-tenant` 替换为你的[租户 ID 或名称](../fundamentals/active-directory-how-to-find-tenant.md)。 |
| 客户端 ID | 必需 | 分配给应用的应用程序 ID。 可以在注册应用的 Azure 门户中找到此信息。 |
| 客户端机密 | 必需 | 为应用生成的客户端密码。|
| 作用域 | 必需 | 必须设置为 `bbb94529-53a3-4be5-a069-7eaf2712b826/.default`。 |


有关如何使用控制台应用的标识获取访问令牌的详细信息，请参阅以下文章之一：[C#](../develop/quickstart-v2-netcore-daemon.md)、[Python](../develop/quickstart-v2-python-daemon.md)、[Node.js](../develop/quickstart-v2-nodejs-console.md) 或 [Java](../develop/quickstart-v2-java-daemon.md)。

你还可以[使用证书访问令牌请求](../develop/v2-oauth2-client-creds-grant-flow.md)（而不是使用客户端密码）。

# <a name="http"></a>[HTTP](#tab/http)

```http
POST /{tenant}/oauth2/v2.0/token HTTP/1.1   //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=12345678-0000-0000-00000000000000000
&scope=bbb94529-53a3-4be5-a069-7eaf2712b826/.default
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Initialize MSAL library by using the following code
X509Certificate2 certificate = AppSettings.ReadCertificate(AppSettings.CertificateName);
    app = ConfidentialClientApplicationBuilder.Create(AppSettings.ClientId)
        .WithCertificate(certificate)
        .WithAuthority(new Uri(AppSettings.Authority))
        .Build();

// Acquire an access token
result = await app.AcquireTokenForClient(AppSettings.Scopes)
                .ExecuteAsync();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
// Initialize MSAL library by using the following code
const msalConfig = {
  auth: {
      clientId: config.azClientId,
      authority: `https://login.microsoftonline.com/${config.azTenantId}`,
      clientCertificate: {
            thumbprint: "CERT_THUMBPRINT", // a 40-digit hexadecimal string
            privateKey: "CERT_PRIVATE_KEY"
        }
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
const msalClientCredentialRequest = {
  scopes: ["bbb94529-53a3-4be5-a069-7eaf2712b826/.default"],
  skipCache: false, 
};
module.exports.msalCca = cca;
module.exports.msalClientCredentialRequest = msalClientCredentialRequest;

// Acquire an access token
const result = await mainApp.msalCca.acquireTokenByClientCredential(mainApp.msalClientCredentialRequest);
    if ( result ) {
      accessToken = result.accessToken;
    }
```

---

## <a name="call-the-api"></a>调用 API

若要发布或验证可验证凭据，请执行以下步骤：

1. 构建对请求服务 REST API 的 HTTP POST 请求。 将 `{tenantID}` 替换为你的[租户 ID](https://TBD) 或租户名称。

    ```http
    POST https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
    ```

1. 将访问令牌作为持有者令牌附加到 HTTP 请求中的授权标头。

    ```http
    Authorization: Bearer <token>
    ```

1. 将 `Content-Type` 标头设置为 `Application/json`。

1. 准备好将[发布](issuance-request-api.md#issuance-request-payload)或[出示](presentation-request-api.md#presentation-request-payload)请求的有效负载附加到请求正文。

1. 将请求提交到请求服务 REST API。

## <a name="issuance-request-example"></a>发布请求示例

下面的示例演示了可验证凭据的发布请求。 有关有效负载的信息，请参阅[请求服务 REST API 发布规范](issuance-request-api.md)

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

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
        "manifest": "https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert1",
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

有关完整代码，请查看以下代码示例之一：[C#](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/1-asp-net-core-api-idtokenhint/IssuerController.cs) 和 [Node.js](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/issuer.js)。

## <a name="presentation-request-example"></a>出示请求示例

下面的示例演示了可验证凭据的出示请求。 有关有效负载的信息，请参阅[请求服务 REST API 出示规范](presentation-request-api.md)

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

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

有关完整代码，请查看以下代码示例之一：

- [C#](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/1-asp-net-core-api-idtokenhint/VerifierController.cs) 
- [Node.js](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/verifier.js)。

## <a name="callback-events"></a>回调事件

请求负载包含[发布](issuance-request-api.md#callback-events)和[出示](presentation-request-api.md#callback-events)回调终结点。 终结点是 Web 应用的一部分，应公开提供。 Azure AD 可验证凭据服务将调用你的终结点，以便在发生特定事件时通知应用。 例如，当用户扫描 QR 码、在其验证器应用中使用深层链接或完成出示过程时，Azure AD 可验证凭据服务都将调用终结点。

下方示意图描述了应用对请求服务 REST API 进行的调用，以及对应用程序进行的回调。

![描述对 API 和回调事件调用过程的示意图。](media/get-started-request-api/callback-events.png)

配置终结点以侦听传入的 HTTP POST 请求。 下面的代码段演示如何处理发布回调 HTTP 请求，针对 UI 作出一致更新：

# <a name="http"></a>[HTTP](#tab/http)

不适用。 选择上面的一种编程语言。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[HttpPost]
public async Task<ActionResult> IssuanceCallback()
{
try
{
    string content = new System.IO.StreamReader(this.Request.Body).ReadToEndAsync().Result;
    _log.LogTrace("callback!: " + content);
    JObject issuanceResponse = JObject.Parse(content);
    
    // More code here
    if (issuanceResponse["code"].ToString() == "request_retrieved")
    {
        var cacheData = new
        {
            status = "request_retrieved",
            message = "QR Code is scanned. Waiting for issuance...",
        };
        _cache.Set(state, JsonConvert.SerializeObject(cacheData));

        // More code here
    }
}
```

有关完整代码，请参阅 GitHub 存储库上的[发布](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/1-asp-net-core-api-idtokenhint/IssuerController.cs)和[出示](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/1-asp-net-core-api-idtokenhint/VerifierController.cs)代码。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
mainApp.app.post('/api/issuer/issuance-request-callback', parser, async (req, res) => {
  var body = '';
  req.on('data', function (data) {
    body += data;
  });
  req.on('end', function () {
    requestTrace( req );
    console.log( body );
    var issuanceResponse = JSON.parse(body.toString());
    var message = null;
    
    if ( issuanceResponse.code == "request_retrieved" ) {
      message = "QR Code is scanned. Waiting for issuance to complete...";
    }
    if ( issuanceResponse.code == "issuance_successful" ) {
      message = "Credential successfully issued";
    }
    if ( issuanceResponse.code == "issuance_error" ) {
      message = issuanceResponse.error.message;
    }
    
    // More code here
    res.send()
  });  
  res.send()
})
```

有关完整代码，请参阅 GitHub 存储库上的[发布](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/issuer.js)和[出示](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/verifier.js)代码。

---

## <a name="next-steps"></a>后续步骤

请查看以下文章：

- [颁发 API 规范](issuance-request-api.md)
- [演示 API 规范](presentation-request-api.md)
