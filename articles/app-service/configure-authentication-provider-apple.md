---
title: 配置 Apple 登录（预览版）
description: 了解如何将 Apple 登录配置为应用服务或 Azure Functions 应用的标识提供者。
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: 01216b7527fd6479f43c3bf0b9d4abeb60d06435
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093042"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>将应用服务或 Azure Functions 应用配置为使用 Apple 登录提供程序（预览版）进行登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介绍如何配置 Azure 应用服务或 Azure Functions，以便将 Apple 登录用作身份验证提供程序。 

若要完成本文中的过程，你必须先在 Apple 开发人员计划中注册。 若要注册 Apple 开发人员计划，请转到 [developer.apple.com/programs/enroll](https://developer.apple.com/programs/enroll/)。

> [!CAUTION]
> 启用 Apple 登录会禁止通过某些客户端（例如 Azure 门户、Azure CLI 和 Azure PowerShell）管理你的应用程序的应用服务身份验证/授权功能。 此功能依赖于一个新的 API 图面，该图面目前为预览版，并未应用于所有管理体验中。

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>在 Apple 开发人员门户中创建应用程序
需要在 Apple 开发人员门户中创建应用 ID 和服务 ID。

1. 在 Apple 开发人员门户上，依次转到“证书”、“标识符”和“配置文件”。
2. 在“标识符”选项卡上，选择 (+) 按钮 。
3. 在“注册新的标识符”页上选择“应用 ID”，然后选择“继续”  。 （应用 ID 包含一个或多个服务 ID。）![在 Apple 开发人员门户中注册新的应用标识符](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. 在“注册应用 ID”页上，提供说明和捆绑 ID，然后从功能列表中选择“Apple 登录” 。 然后，选择“继续”。 记下此步骤中的应用 ID 前缀（团队 ID），稍后需要用到它。
![在 Apple 开发人员门户中配置新的应用标识符](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. 查看应用注册信息，并选择“注册”。
6. 同样，在“标识符”选项卡上，选择 (+) 按钮 。
![在 Apple 开发人员门户中创建新的服务标识符](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. 在“注册新的标识符”页上选择“服务 ID”，然后选择“继续”  。
![在 Apple 开发人员门户中注册新的服务标识符](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. 在“注册服务 ID”页上，提供说明和标识符。 说明内容将在同意屏幕上显示给用户。 此标识符将会是你的客户端 ID，用于对应用服务配置 Apple 提供程序。 然后选择“配置”。
![提供说明和标识符](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. 在弹出窗口中，将主应用 ID 设置为之前创建的应用 ID。 在域部分中指定应用程序的域。 对于返回 URL，使用 URL `<app-url>/.auth/login/apple/callback`。 例如，`https://contoso.azurewebsites.net/.auth/login/apple/callback`。 然后选择“添加”和“保存” 。
![指定注册的域和返回 URL](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. 查看服务注册信息，然后选择“保存”。

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>生成客户端密码
Apple 要求应用开发人员创建 JWT 令牌并将其签名为客户端密码值。 若要生成此密码，首先从 Apple 开发人员门户生成椭圆曲线私钥并将其下载。 然后使用该密钥对具有[[特定有效负载](#structure-the-client-secret-jwt)的 JWT 进行签名](#sign-the-client-secret-jwt)。

### <a name="create-and-download-the-private-key"></a>创建和下载私钥
1. 在 Apple 开发人员门户的“密钥”选项卡上，选择“创建密钥”或选择 (+) 按钮  。
2. 在“注册新密钥”页上，为密钥指定一个名称，选中“Apple 登录”旁边的框，然后选择“配置”  。
3. 在“配置密钥”页上，将密钥链接到之前创建的主应用 ID，然后选择“保存” 。
4. 通过确认信息并选择“继续”，然后查看信息并选择“注册”来完成密钥的创建 。
5. 在“下载密钥”页上，下载密钥。 它将下载为 `.p8` (PKCS#8) 文件，你将使用文件内容对你的客户端密码 JWT 进行签名。

### <a name="structure-the-client-secret-jwt"></a>构建客户端密码 JWT
Apple 要求客户端密码是 base64 编码的 JWT 令牌。 解码后的 JWT 令牌具有的有效负载的结构应如以下示例所示：
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
- **sub**：Apple 客户端 ID（也是服务 ID）
- **iss**：你的 Apple 开发人员团队 ID
- **aud**：Apple 正在接收令牌，因此他们是受众
- **exp**：nbf 后不超过 6 个月

上述有效负载的 base64 编码版本如下所示：```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_注意：Apple 不接受创建（或 nbf）日期后到期日期超过 6 个月的客户端密码 JWT。这意味着你将需要至少每六个月轮换一次你的客户端密码。_

有关生成和验证令牌的详细信息，请参阅 [Apple 的开发人员文档](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)。 

### <a name="sign-the-client-secret-jwt"></a>对客户端密码 JWT 进行签名
你将使用之前下载的 `.p8` 文件对客户端密码 JWT 进行签名。 此文件是一个 [PCKS#8 文件](https://en.wikipedia.org/wiki/PKCS_8)，其中包含 PEM 格式的专用签名密钥。 可使用许多库来创建 JWT 并对其进行签名。 

可以联机使用不同种类的开源库来创建 JWT 令牌并对其进行签名。 有关生成 JWT 令牌的详细信息，请参阅 jwt.io。 例如，生成客户端密码的一种方法是导入 [Microsoft.IdentityModel.Tokens NuGet 包](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) 并运行如下所示的少量 C# 代码。

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
- **clientId**：Apple 客户端 ID（也是服务 ID）
- **p8key**：PEM 格式密钥 - 获取密钥的方式如下：在文本编辑器中打开 `.p8` 文件，复制没有换行符的 `-----BEGIN PRIVATE KEY-----` 和 `-----END PRIVATE KEY-----` 之间的所有内容
- **keyId**：下载的密钥的 ID

返回的此令牌是用于配置 Apple 提供程序的客户端密码值。

> [!IMPORTANT]
> 客户端密钥是一个非常重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。
>

使用你选择的设置名称将客户端机密添加为应用的[应用程序设置](./configure-common.md#configure-app-settings)。 记下此名称备用。

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>向应用程序添加提供者信息

> [!NOTE]
> 所需配置采用新的 API 格式，目前只有[基于文件的配置（预览版）](configure-authentication-file-based.md)支持此格式。 你需要使用这样的文件执行以下步骤。

本部分将指导你更新配置，使之包括新的 IDP。 后面提供了配置示例。

1. 在 `identityProviders` 对象内，添加一个 `apple` 对象（如果它尚不存在）。
2. 向该密钥分配一个对象，在其中包含 `registration` 对象，还可以包含 `login` 对象：
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client ID>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. 在 `registration` 对象内，将 `clientId` 设置为你收集的客户端 ID。
    
    b. 在 `registration` 对象内，将 `clientSecretSettingName` 设置为存储客户端密码的应用程序设置的名称。
    
    c. 在 `login` 对象内，可选择将 `scopes` 数组设置为包含使用 Apple 进行身份验证时所使用的范围列表，例如“名称”和“电子邮件”。 如果范围已配置，则在用户首次登录时，它们会在同意屏幕上被显式请求。

设置此配置后，即可在应用中使用 Apple 提供程序进行身份验证。

完整配置可能类似于以下示例（其中的 APPLE_GENERATED_CLIENT_SECRET 设置指向包含一个生成的 JWT 的应用程序设置）：

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
