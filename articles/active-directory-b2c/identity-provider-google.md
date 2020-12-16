---
title: 使用 Google 帐户设置注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C 提供在应用程序中通过 Google 帐户注册与登录到客户的设置。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5b04613a74755f8515d967b8dd28397b36816fec
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516224"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Google 帐户注册与登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-google-application"></a>创建 Google 应用程序

若要使用 Google 帐户作为 Azure Active Directory B2C (Azure AD B2C) 中的 [标识提供者](authorization-code-flow.md) ，则需要在 Google 开发人员控制台中创建应用程序。 如果还没有 Google 帐户，可以在上注册 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) 。

1. 使用 Google 帐户凭据登录 [Google 开发人员控制台](https://console.developers.google.com/)。
1. 在页面的左上角，选择 "项目" 列表，然后选择 " **新建项目**"。
1. 输入 **项目名称**，选择 " **创建**"。
1. 通过选择屏幕左上角的 "项目" 下拉框，确保使用的是新项目，按 "名称" 选择项目，然后选择 " **打开**"。
1. 在左侧菜单中选择 " **OAuth 许可屏幕** "，选择 " **外部**"，然后选择 " **创建**"。
输入应用程序的 **名称**。 在 "授权的 **域**" 部分中，输入 *b2clogin.com* ，然后选择 "**保存**"。
1. 在左侧菜单中选择“凭据”，然后选择“创建凭据” > “Oauth 客户端 ID”。
1. 在“应用程序类型”下，选择“Web 应用程序”。
1. 输入应用程序的名称，在已授权 JavaScript 来源中输入 `https://your-tenant-name.b2clogin.com`，并在已授权重定向 URI 中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称。 输入租户名称时，必须全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。
1. 单击“创建”。
1. 复制 " **客户端 ID** " 和 " **客户端密钥**" 的值。 将 Google 配置为租户中的标识提供者时需要这两项。 **客户端密钥** 是重要的安全凭据。

::: zone pivot="b2c-user-flow"

## <a name="configure-a-google-account-as-an-identity-provider"></a>将 Google 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择 " **标识提供者**"，然后选择 " **Google**"。
1. 输入“名称”。 例如， *Google*。
1. 对于 " **客户端 id**"，请输入之前创建的 Google 应用程序的客户端 id。
1. 对于 **客户端密码**，请输入你记录的客户端密码。
1. 选择“保存”。 

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的客户端机密。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
4. 在“概述”页上选择“标识体验框架”。
5. 选择“策略密钥”，然后选择“添加”。
6. 对于“选项”，请选择 `Manual`。
7. 输入策略密钥的 **名称**。 例如，`GoogleSecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
8. 在“机密”中，输入前面记录的应用程序机密。
9. 在“密钥用法”处选择 `Signature`。
10. 单击“创建”。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 Google 帐户登录，需将该帐户定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过在策略的扩展文件中将 Google 帐户添加到 **ClaimsProviders** 元素，将该帐户定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAUTH">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. 将 **client_id** 设置为应用程序注册中的应用程序 ID。
5. 保存文件。

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

至此，已配置策略，以便 Azure AD B2C 知道如何与 Google 帐户通信。 请尝试上传该策略的扩展文件，这只是为了确认它到目前为止不会出现任何问题。

1. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略” 。
2. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件。
3. 单击“上载” 。

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，标识提供者已设置，但不会出现在任何注册/登录屏幕中。 若要使其可用，请创建现有模板用户旅程的副本，然后对其进行修改，使其也具有 Google 标识提供者。

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
2. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
3. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
4. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
5. 重命名用户旅程的 ID。 例如，`SignUpSignInGoogle`。

### <a name="display-the-button"></a>显示按钮

**ClaimsProviderSelection** 元素类似于注册/登录屏幕上的标识提供者按钮。 如果为 Google 帐户添加 **ClaimsProviderSelection** 元素，则当用户进入页面时，会显示一个新按钮。

1. 在创建的用户旅程中找到包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelects** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `GoogleExchange`：

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 Google 帐户通信以接收令牌。

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
2. 添加以下 **ClaimsExchange** 元素，以确保为用于 **TargetClaimsExchangeId** 的 ID 使用相同的值：

    ```xml
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```

    将 **TechnicalProfileReferenceId** 的值更新为之前创建的技术配置文件的 ID。 例如，`Google-OAuth`。

3. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-google-identity-provider-to-a-user-flow"></a>将 Google 标识提供者添加到用户流 

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击要传递给 Google 标识提供者的用户流。
1. 在 " **社交标识提供者**" 下，选择 " **Google**"。
1. 选择“保存”。 
1. 若要测试策略，请选择 " **运行用户流**"。
1. 对于 " **应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击 "**运行用户流**"

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 *SignUpSignInGoogle.xml*。
1. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如，`SignUpSignInGoogle`。
1. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如 `http://contoso.com/B2C_1A_signup_signin_google`
1. 更新 **DefaultUserJourney** 中的 **ReferenceId** 属性的值，以匹配所创建的新用户旅程的 ID (SignUpSignGoogle)。
1. 保存更改，并上传文件。
1. 在“自定义策略”下，选择“B2C_1A_signup_signin” 。
1. 对于 " **选择应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择 " **立即运行** "，然后选择 "google" 以通过 google 登录并测试自定义策略。

::: zone-end

## <a name="next-steps"></a>后续步骤

了解如何将 [Google 令牌传递给你的应用程序](idp-pass-through-user-flow.md)。