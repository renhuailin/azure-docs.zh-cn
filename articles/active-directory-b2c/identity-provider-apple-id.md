---
title: 使用 Apple ID 设置注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C 向应用程序中的 Apple ID 提供注册和登录。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: dc6801de858c72a703317805d00f8e50cae69bbe
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054496"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c-preview"></a>使用 Azure Active Directory B2C (预览版设置 Apple ID 的注册和登录) 

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>创建 Apple ID 应用程序

若要使用 Azure Active Directory B2C (Azure AD B2C) 中的 Apple ID 登录，则需要在中创建应用程序 [https://developer.apple.com](https://developer.apple.com) 。 有关详细信息，请参阅 [通过 Apple 登录](https://developer.apple.com/sign-in-with-apple/get-started/)。 如果还没有 Apple 开发人员帐户，则可以在 [Apple 开发人员计划](https://developer.apple.com/programs/enroll/)注册。

1. 用你的帐户凭据登录 [Apple 开发人员门户](https://developer.apple.com/account) 。
1. 从菜单中，选择 " **证书"、"id"、"& 配置文件**"，然后选择 " **(+)**"。
1. 对于 " **注册新标识符**"，请选择 " **应用 id**"，然后选择 " **继续**"。
1. 对于 " **选择类型**"，选择 " **应用**"，然后选择 " **继续**"。
1. 对于 " **注册应用 ID**"：
    1. 输入 **描述** 
    1. 输入 " **捆绑 ID**"，例如 `com.contoso.azure-ad-b2c` 。 
    1. 对于 **功能**，请从 "功能" 列表中选择 " **通过 Apple 登录** "。 
    1. 记下应用 ID 前缀 (此步骤) 的团队 ID。 稍后需要用到此信息。
    1. 选择“继续”  ，然后选择“注册”  。
1. 从菜单中，选择 " **证书"、"id"、"& 配置文件**"，然后选择 " **(+)**"。
1. 对于 " **注册新标识符**"，请选择 " **服务 id**"，然后选择 " **继续**"。
1. 对于 " **注册服务 ID**"：
    1. 输入 **描述**。 在许可屏幕上向用户显示说明。
    1. 输入 **标识符**，如 `com.consoto.azure-ad-b2c-service` 。 标识符是 OpenID Connect 流的客户端 ID。
    1. 选择 " **继续**"，然后选择 " **注册**"。
1. 从 " **标识符**" 中选择创建的标识符。
1. 选择 " **通过 Apple 登录**"，然后选择 " **配置**"。
    1. 选择要配置与 Apple 一起登录的 **主应用 ID** 。
    1. 在 " **域和子域**" 中，输入 `your-tenant-name.b2clogin.com` 。 将 your-tenant-name 替换为租户的名称。
    1. 在 " **返回 url**" 中，输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` 。 将 your-tenant-name 替换为租户的名称。
    1. 选择 " **下一步**"，然后选择 " **完成**"。
    1. 当弹出窗口关闭时，选择 " **继续**"，然后选择 " **保存**"。

## <a name="creating-an-apple-client-secret"></a>创建 Apple 客户端机密

1. 在 Apple 开发人员门户菜单中，选择 " **密钥**"，然后选择 " **(" +)**"。
1. 对于 " **注册新密钥**"：
    1. 键入 **密钥名称**。
    1. 选择 " **通过 Apple 登录**"，然后选择 " **配置**"。
    1. 对于 " **主要应用 ID**"，请选择之前创建的应用，然后选择 " **保存**"。
    1. 选择 " **配置**"，然后选择 " **注册** " 以完成密钥注册过程。
1. 对于 " **下载密钥**"，请选择 " **下载** " 以下载包含密钥的 p8 文件。


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>将 Apple 配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录到 [Azure 门户](https://portal.azure.com/) 。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 在 " **Azure 服务**" 下，选择 **Azure AD B2C**。 或使用 "搜索" 框查找并选择 **Azure AD B2C**。
1. 选择 " **标识提供者**"，然后选择 " **Apple (预览")**。
1. 输入“名称”。 例如， *Apple*。
1. 输入 **(团队 id) 的 Apple 开发人员 id**。
1. 输入 " **客户端 id")  ("Apple 服务 id**"。
1. 输入 **Apple 密钥 ID**。
1. 选择并上传 **Apple 证书数据**。
1. 选择“保存”。


> [!IMPORTANT] 
> - 登录 Apple 要求管理员每6个月续订其客户端密码。 
> - 在此功能的公共预览版期间，如果 Apple 客户端密码过期，则需要手动续订该密钥。 Apple 标识提供者配置社交 IDP 页面之前会提前出现一个警告，但建议你设置自己的提醒。 
> - 如果需要续订机密，请在 Azure 门户中打开 Azure AD B2C，然后访问 "**标识提供者**  >  **"**，并选择 "**续订机密**"。

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>将 Apple 标识提供者添加到用户流

若要使用户能够使用 Apple ID 登录，需要将 Apple 标识提供者添加到用户流。 使用 Apple 登录只能为 **建议** 版本的用户流进行配置。 将 Apple 标识提供者添加到用户流：

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 选择要为其添加 Apple 标识提供者的用户流。 
1. 在 " **社交标识提供者**" 下，选择 " **Apple (预览")**。
1. 选择“保存”。
1. 若要测试策略，请选择 " **运行用户流**"。
1. 对于 " **应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“运行用户流”。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>为客户端密码签名

使用以前下载的 p8 文件将客户端密码签名为 JWT 令牌。 可使用许多库来创建 JWT 并对其进行签名。 使用 [Azure 函数创建令牌](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function) 。 

1. 创建 [Azure 函数](../azure-functions/functions-create-function-app-portal.md)。
1. 在 " **开发人员**" 下，选择 " **代码 + 测试**"。 
1. 复制 [run.csx](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx) 文件的内容，并将其粘贴到编辑器中。
1. 选择“保存”。
1. 发出 HTTP `POST` 请求，并提供以下信息：

    - **appleTeamId**：你的 Apple 开发人员团队 ID
    - **appleServiceId**： APPLE 服务 ID (也是客户端 ID) 
    - **p8key**： PEM 格式键。 您可以通过在文本编辑器中打开 p8 文件并在和之间复制所有内容来获取此信息 `-----BEGIN PRIVATE KEY-----` `-----END PRIVATE KEY-----` 。
 
以下 json 是调用 Azure 函数的一个示例：

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleKeyId": "URKEYID001",
    "appleServiceId": "com.yourcompany.app1",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

Azure function 在响应中使用正确的格式和签名的客户端机密 JWT 来做出响应，例如：

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的客户端机密。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录。
2. 在 " **Azure 服务**" 下，选择 **Azure AD B2C**。 或使用 "搜索" 框查找并选择 **Azure AD B2C**。
1. 在 " **概述** " 页上，选择 " **标识体验框架**"。
1. 选择“策略密钥”，然后选择“添加” 。
1. 对于 **选项**，请选择 " **手动**"。
1. 输入策略密钥的 **名称**。 例如 "AppleSecret"。 前缀 "B2C_1A_" 会自动添加到密钥名称。
1. 在 " **密钥**" 中，输入 Azure 函数返回的令牌值 (JWT 令牌) 。
1. 对于“密钥用法”，请选择“签名” 。
1. 选择“创建”。

> [!IMPORTANT] 
> - 登录 Apple 要求管理员每6个月续订其客户端密码。
> - 如果 Apple 客户端密码过期，则需要手动续订该密钥，并将新值存储在策略密钥中。
> - 建议你在6个月内设置自己的提醒以生成新的客户端密钥。 

## <a name="configure-apple-as-an-identity-provider"></a>将 Apple 配置为标识提供者

若要使用户能够使用 Apple ID 登录，需要将该帐户定义为声明提供程序，Azure AD B2C 可通过终结点进行通信。 终结点提供一组声明，Azure AD B2C 使用这些声明来验证特定用户是否已经过身份验证。

可以通过将 Apple ID 添加到策略扩展文件中的 **ClaimsProviders** 元素来将其定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.name.firstName user.name.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.name.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.name.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="user.email"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. 将 **client_id** 设置为服务标识符。 例如 `com.consoto.azure-ad-b2c-service`。
5. 保存文件。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end
