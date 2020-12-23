---
title: '配置 Apple (预览版的登录) '
description: 了解如何使用 Apple 作为应用服务或 Azure Functions 应用的标识提供者配置登录。
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: b77e0613f502d003b5e4651e34be4cadbd4209a9
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603126"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>将应用服务或 Azure Functions 应用配置为使用 Apple 提供程序登录 (预览版) 

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介绍如何将 Azure App Service 或 Azure Functions 配置为使用 Apple 作为身份验证提供程序进行登录。 

若要完成本文中的过程，你必须已在 Apple 开发人员计划中注册。 若要注册 Apple 开发人员计划，请参阅 [developer.apple.com/programs/enroll](https://developer.apple.com/programs/enroll/)。

> [!CAUTION]
> 使用 Apple 启用登录将禁止通过某些客户端（例如 Azure 门户、Azure CLI 和 Azure PowerShell）来管理应用程序的应用服务身份验证/授权功能。 此功能依赖于一个新的 API 图面，该图面目前为预览版，并未应用于所有管理体验中。

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>在 Apple 开发人员门户中创建应用程序
需要在 Apple 开发人员门户中创建应用 ID 和服务 ID。

1. 在 Apple 开发人员门户上，中转到 " **证书、标识符、& 配置文件**"。
2. 在 " **标识符** " 选项卡上，选择 " (" 和 " **)** " 按钮。
3. 在 " **注册新的标识符** " 页面上，选择 " **应用 id** " 并选择 " **继续**"。  (应用 Id 包含一个或多个服务 Id。 ) ![ 在 Apple 开发人员门户中注册新应用标识符](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. 在 " **注册应用程序 ID** " 页上，提供描述和捆绑 id，并从 "功能" 列表中选择 " **使用 Apple 登录** "。 然后选择“继续”。  记下 **应用 Id 前缀 ("团队 id")** 此步骤中，稍后需要用到它。
![在 Apple 开发人员门户中配置新的应用标识符](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. 查看应用注册信息，然后选择 " **注册**"。
6. 同样，在 " **标识符** " 选项卡上，选择 " (" 和 " **)** " 按钮。
![在 Apple 开发人员门户中创建新的服务标识符](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. 在 " **注册新的标识符** " 页面上，选择 " **服务 id** " 并选择 " **继续**"。
![在 Apple 开发人员门户中注册新服务标识符](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. 在 " **注册服务 ID** " 页上，提供描述和标识符。 说明将显示在 "同意" 屏幕上的用户。 此标识符将是用于在应用服务中配置 Apple 提供程序时使用的客户端 ID。 然后选择“配置”。
![提供说明和标识符](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. 在弹出窗口中，将主应用 Id 设置为之前创建的应用 Id。 在 "域" 部分中指定应用程序的域。 对于返回 URL，请使用 URL `<app-url>/.auth/login/apple/callback` 。 例如，`https://contoso.azurewebsites.net/.auth/login/apple/callback`。 然后选择 " **添加** 并 **保存**"。
![指定域并返回注册 URL](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. 查看服务注册信息，然后选择 " **保存**"。

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>生成客户端机密
Apple 要求应用开发人员创建 JWT 令牌并对其进行签名，作为客户端机密值。 若要生成此机密，请首先从 Apple 开发人员门户生成并下载椭圆曲线私钥。 然后，使用该密钥对具有[特定有效负载](#structure-the-client-secret-jwt)的[JWT 进行签名](#sign-the-client-secret-jwt)。

### <a name="create-and-download-the-private-key"></a>创建和下载私钥
1. 在 Apple 开发人员门户的 " **密钥** " 选项卡上，选择 " **创建密钥** " 或选择 " **(" +)** "按钮。
2. 在 " **注册新密钥** " 页上，为密钥指定一个名称，选中 " **使用 Apple 登录** " 旁边的框，然后选择 " **配置**"。
3. 在 " **配置密钥** " 页上，将密钥链接到你之前创建的主应用 ID，然后选择 " **保存**"。
4. 完成密钥创建，方法是确认信息并选择 " **继续** "，然后查看信息并选择 " **注册**"。
5. 在 " **下载密钥** " 页上，下载密钥。 它将作为 `.p8` (PKCS # 8) 文件下载，你将使用文件内容对你的客户端密钥 JWT 进行签名。

### <a name="structure-the-client-secret-jwt"></a>构建客户端机密 JWT 的结构
Apple 要求客户端密码是 JWT 令牌的 base64 编码。 已解码的 JWT 令牌应具有结构化的有效负载，如以下示例所示：
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **sub**： Apple 客户端 ID (也是服务 ID) 
- **iss**：你的 Apple 开发人员团队 ID
- **aud**： Apple 正在接收令牌，因此它们是观众
- **exp**： **nbf** 后无超过6个月

上述有效负载的 base64 编码版本如下所示： ```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_注意：在创建 (或 nbf) 日期后，Apple 不接受过期日期超过6个月的客户端密码 Jwt。这意味着你将需要每六个月至少轮换你的客户端密钥。_

有关生成和验证令牌的详细信息，请参阅 [Apple 开发人员文档](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)。 

### <a name="sign-the-client-secret-jwt"></a>为客户端密码 JWT 签名
你将使用 `.p8` 之前下载的文件对客户端密钥 JWT 进行签名。 此文件是一个 [PCKS # 8 文件](https://en.wikipedia.org/wiki/PKCS_8) ，其中包含 PEM 格式的专用签名密钥。 有许多库可以为你创建和签名 JWT。 

可以联机使用不同种类的开源库来创建 JWT 令牌并对其进行签名。 有关生成 JWT 令牌的详细信息，请参阅 jwt.io。 例如，生成客户端机密的一种方法是导入 [System.identitymodel NuGet 包](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) ，并运行如下所示的一小部分 c # 代码。

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **teamId**：你的 Apple 开发人员团队 ID
- **clientId**： Apple 客户端 ID (也是服务 ID) 
- **p8key**： PEM 格式键-可以通过 `.p8` 在文本编辑器中打开文件来获取密钥，还可以在 `-----BEGIN PRIVATE KEY-----` `-----END PRIVATE KEY-----` 不换行的情况下复制所有内容。
- **keyId**：已下载密钥的 ID

返回的此令牌是用于配置 Apple 提供程序的客户端机密值。

> [!IMPORTANT]
> 客户端密钥是一个非常重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。
>

使用你选择的设置名称将客户端机密添加为应用的[应用程序设置](./configure-common.md#configure-app-settings)。 记下此名称备用。

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>向应用程序添加提供者信息

> [!NOTE]
> 所需配置采用新的 API 格式，目前只有[基于文件的配置（预览版）](.\app-service-authentication-how-to.md#config-file)支持此格式。 你需要使用这样的文件执行以下步骤。

本部分将指导你更新配置，使之包括新的 IDP。 后面提供了配置示例。

1. 在 `identityProviders` 对象中， `apple` 如果对象不存在，则添加一个对象。
2. 向该密钥分配一个对象，在其中包含 `registration` 对象，还可以包含 `login` 对象：
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client id>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. 在 `registration` 对象中，将设置 `clientId` 为你收集的客户端 ID。
    
    b. 在 `registration` 对象中，将设置 `clientSecretSettingName` 为存储客户端机密的应用程序设置的名称。
    
    c. 在 `login` 对象中，你可以选择将数组设置 `scopes` 为包括在使用 Apple 进行身份验证时所使用的作用域的列表，例如 "名称" 和 "电子邮件"。 如果已配置作用域，则在用户首次登录时，会在许可屏幕上显式请求它们。

设置此配置后，即可在应用中使用 Apple 提供程序进行身份验证。

完整配置可能与以下示例类似 (其中，APPLE_GENERATED_CLIENT_SECRET 设置指向包含生成的 JWT) 的应用程序设置：

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
