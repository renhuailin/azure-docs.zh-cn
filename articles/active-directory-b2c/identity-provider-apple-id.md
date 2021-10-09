---
title: 设置使用 Apple ID 注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，为应用程序客户提供通过 Apple ID 注册与登录的功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 13120c083eda20b76581448c842db94d657cf42b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128571889"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c-preview"></a>使用 Azure Active Directory B2C 设置通过 Apple ID 注册与登录（预览）

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>创建 Apple ID 应用程序

若要在 Azure Active Directory B2C (Azure AD B2C) 中为用户启用使用 Apple ID 登录的功能，需要在 [https://developer.apple.com](https://developer.apple.com) 中创建应用程序。 有关详细信息，请参阅[使用 Apple 登录](https://developer.apple.com/sign-in-with-apple/get-started/)。 如果你还没有 Apple 开发者帐户，可在 [Apple 开发者计划](https://developer.apple.com/programs/enroll/)上注册一个。

1. 使用帐户凭据登录 [Apple 开发者门户](https://developer.apple.com/account)。
1. 从菜单中，选择“证书、ID 和配置文件”，然后选择“(+)”。
1. 对于“注册新标识符”，选择“应用 ID”，然后选择“继续”。
1. 对于“选择类型”，选择“应用”，然后选择“继续”。
1. 对于“注册应用 ID”：
    1. 输入“说明” 
    1. 输入“捆绑包 ID”，如 `com.contoso.azure-ad-b2c`。 
    1. 对于“功能”，从功能列表中选择“通过 Apple 登录”。 
    1. 记下这一步当中的团队 ID（应用 ID 前缀）。 稍后需要用到此信息。
    1. 选择“继续”  ，然后选择“注册”  。
1. 从菜单中，选择“证书、ID 和配置文件”，然后选择“(+)”。
1. 对于“注册新标识符”，选择“服务 ID”，然后选择“继续”。
1. 对于“注册服务 ID”：
    1. 输入“说明”。 说明内容将在同意屏幕上显示给用户。
    1. 输入“标识符”，如 `com.consoto.azure-ad-b2c-service`。 记下服务 ID 标识符。 标识符是 OpenID Connect 流的客户端 ID。
    1. 选择“继续”，然后选择“注册”。
1. 从“标识符”中选择你创建的标识符。
1. 选择“通过 Apple 登录”，然后选择“配置”。
    1. 选择配置“通过 Apple 登录”所用的“主应用 ID”。
    1. 在“域和子域”中，输入 `your-tenant-name.b2clogin.com`。 将 your-tenant-name 替换为租户的名称。 如果使用[自定义域](custom-domain.md)，请输入 `https://your-domain-name`。
    1. 在“返回 URL”中，输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 如果使用[自定义域](custom-domain.md)，请输入 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称，将 `your-domain-name` 替换为你的自定义域。 回调 URL 需采用全小写形式。
    1. 选择“下一步”，然后选择“完成”。
    1. 当弹出窗口关闭时，选择“继续”，然后选择“保存”。

## <a name="creating-an-apple-client-secret"></a>创建 Apple 客户端密码

1. 在 Apple 开发者门户菜单中，选择“密钥”，然后选择“(+)”。
1. 对于“注册新密钥”：
    1. 键入“密钥名称”。
    1. 选择“通过 Apple 登录”，然后选择“配置”。
    1. 对于“主应用 ID”，选择先前创建的应用，然后选择“保存”。
    1. 选择“配置”，然后选择“注册”以完成密钥注册过程。 记下密钥 ID。 配置用户流时需要此密钥。
1. 对于“下载密钥”，选择“下载”以下载包含密钥的 .p8 文件。


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>将 Apple 配置为标识提供程序

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在“Azure 服务”下，选择“Azure AD B2C”。 或者，使用搜索框查找并选择“Azure AD B2C”。
1. 依次选择“标识提供程序”和“Apple (预览)”。
1. 对于名称，输入“使用 Apple 帐户登录”。 
1. 输入“Apple 开发者 ID (团队 ID)”。
1. 输入“Apple 服务 ID（客户端 ID）”。
1. 输入[创建 Apple 客户端密码](#creating-an-apple-client-secret)这一步当中的 Apple 密钥 ID。
1. 选择并上传“Apple 证书数据”。
1. 选择“保存”。


> [!IMPORTANT] 
> - 通过 Apple 登录要求管理员每 6 个月更新一次其客户端密码。 
> - 在此功能的公共预览阶段，如果 Apple 客户端密码过期，需要手动更新密码。 Apple 标识提供程序配置社交 IDP 页面会提前出现提醒消息，但建议你自行设置提醒。 
> - 如果需要更新密码，请在 Azure 门户中打开 Azure AD B2C，转到“标识提供程序” > “Apple”，并选择“更新密码”。
> - 遵循指南，了解如何[提供“使用 Apple ID 登录”按钮](#customize-your-user-interface)。

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>将 Apple 标识提供程序添加到用户流

若要使用户能够使用 Apple ID 登录，需要将 Apple 标识提供程序添加到用户流。 使用 Apple 登录只能为建议版本的用户流配置。 将 Apple 标识提供程序添加到用户流的步骤：

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 选择要为其添加 Apple 标识提供程序的用户流。 
1. 在“社交标识提供程序”下，选择“Apple (预览)”。
1. 选择“保存”。
1. 若要测试策略，请选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为“testapp1”的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“运行用户流”按钮。
1. 在注册或登录页上，选择“Apple”以使用 Apple ID 登录。

如果登录过程成功，浏览器将重定向到 `https://jwt.ms`，后者显示 Azure AD B2C 返回的令牌内容。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>签署客户端密码

使用以前下载的 .p8 文件将客户端密码签署到 JWT 令牌。 可使用许多库来创建 JWT 并对其进行签名。 使用 [Azure 函数](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function)为你创建令牌。 

1. 创建 [Azure 函数](../azure-functions/functions-create-function-app-portal.md)。
1. 在“开发人员”下，选择“代码和测试”。 
1. 复制 [run.csx](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx) 文件的内容，并将其粘贴到编辑器中。
1. 选择“保存”。
1. 发出 HTTP `POST` 请求，并提供以下信息：

    - appleTeamId：你的 Apple 开发者团队 ID
    - appleServiceId：Apple 服务 ID（客户端 ID）
    - appleKeyId：存储在 JWT 标头中的 10 位密钥 ID（Apple 要求）
    - p8key：PEM 格式密钥。 获取此密钥的方式如下：在文本编辑器中打开 .p8 文件，复制 `-----BEGIN PRIVATE KEY-----` 和 `-----END PRIVATE KEY-----` 之间的所有内容（无换行符）。
 
以下 JSON 是调用 Azure 函数的一个示例：

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleServiceId": "com.yourcompany.app1",
    "appleKeyId": "URKEYID001",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

Azure 函数在响应中使用格式正确且已签名的客户端密码 JWT 做出响应，例如：

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的客户端机密。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在“Azure 服务”下，选择“Azure AD B2C”。 或者，使用搜索框查找并选择“Azure AD B2C”。
1. 在“概述”页上，选择“Identity Experience Framework”。
1. 选择“策略密钥”，然后选择“添加” 。
1. 对于“选项”，选择“手动”。
1. 输入策略密钥的 **名称**。 例如，“AppleSecret”。 前缀“B2C_1A_”会自动添加到密钥名称中。
1. 在“密钥”中，输入 Azure 函数返回的令牌值（JWT 令牌）。
1. 对于“密钥用法”，请选择“签名” 。
1. 选择“创建”。

> [!IMPORTANT] 
> - 通过 Apple 登录要求管理员每 6 个月更新一次其客户端密码。
> - 如果 Apple 客户端密码过期，需要手动更新密码，并将新值存储在策略密钥中。
> - 建议在 6 个月内自行设置提醒以生成新的客户端密码。 
> - 遵循指南，了解如何[提供“使用 Apple ID 登录”按钮](#customize-your-user-interface)。

## <a name="configure-apple-as-an-identity-provider"></a>将 Apple 配置为标识提供程序

若要允许用户使用 Apple ID 登录，需将该帐户定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过在策略的扩展文件中将 Apple ID 添加到 ClaimsProvider 元素，将该帐户定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Sign in with Apple</DisplayName>
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
            <OutputClaim ClaimTypeReferenceId="email" />
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

4. 将 client_id 设置为服务标识符。 例如，`com.consoto.azure-ad-b2c-service`。
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

## <a name="test-your-custom-policy"></a>测试自定义策略

1. 选择信赖方策略，例如 `B2C_1A_signup_signin`。
1. 对于“应用程序”，请选择[前面注册](tutorial-register-applications.md)的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“立即运行”按钮。
1. 在注册或登录页上，选择“Apple”以使用 Apple ID 登录。

如果登录过程成功，浏览器将重定向到 `https://jwt.ms`，后者显示 Azure AD B2C 返回的令牌内容。

::: zone-end

## <a name="customize-your-user-interface"></a>自定义用户界面

遵循指南，了解如何[提供“使用 Apple ID 登录”](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/introduction/)。 Apple 提供几个“使用 Apple ID 登录”按钮，你可以使用这些按钮让用户设置帐户并登录。 如有必要，可以创建自定义按钮来提供“使用 Apple ID 登录”选项。 了解如何[显示“使用 Apple ID 登录”按钮](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/buttons/)。

与 Apple 用户界面准则一致：

- [利用 HTML 模板自定义用户界面](customize-ui-with-html.md)
- [本地化](language-customization.md)标识提供者的名称。

