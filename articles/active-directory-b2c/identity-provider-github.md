---
title: 使用 GitHub 帐户设置注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 GitHub 帐户注册与登录的功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a890a72773392ec179c3565954c3136d47db1dc4
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584912"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 GitHub 帐户注册与登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>先决条件

::: zone pivot="b2c-user-flow"

* [创建用户流](tutorial-create-user-flows.md)，以便用户能够注册并登录应用程序。
* 请[向 Azure Active Directory B2C 租户添加 Web API 应用程序](add-web-api-application.md)（如果尚未这样做）。

::: zone-end

::: zone pivot="b2c-custom-policy"

* 完成 [Active Directory B2C 中的自定义策略入门](custom-policy-get-started.md)中的步骤。
* 请[向 Azure Active Directory B2C 租户添加 Web API 应用程序](add-web-api-application.md)（如果尚未这样做）。

::: zone-end

## <a name="create-a-github-oauth-application"></a>创建 GitHub OAuth 应用程序

要将 GitHub 帐户用作 Azure Active Directory B2C (Azure AD B2C) 中的[标识提供者](authorization-code-flow.md)，需要在表示它的租户中创建一个应用程序。 如果还没有 GitHub 帐户，可以在注册 [https://www.github.com/](https://www.github.com/) 。

1. 使用 GitHub 凭据登录 [GitHub 开发人员](https://github.com/settings/developers)网站。
1. 选择“OAuth 应用”，然后选择“新建 OAuth 应用”。
1. 输入 **应用程序名称** 和 **主页 URL**。
1. 在“授权回调 URL”中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为 Azure AD B2C 租户的名称。 输入租户名称时，全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。
1. 单击“注册应用程序”。
1. 复制 " **客户端 ID** " 和 " **客户端密钥**" 的值。 将标识提供者添加到租户时需要这两个值。

::: zone pivot="b2c-user-flow"

## <a name="configure-a-github-account-as-an-identity-provider"></a>将 GitHub 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择“标识提供者”，然后选择“GitHub (预览)”。
1. 输入“名称”。 例如，GitHub。
1. 对于 **客户端 ID**，输入你之前创建的 GitHub 应用程序的客户端 ID。
1. 对于 **客户端密码**，请输入你记录的客户端密码。
1. 选择“保存”。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的客户端机密。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“概述”页上选择“标识体验框架”。
1. 选择“策略密钥”，然后选择“添加”。
1. 对于“选项”，请选择 `Manual`。
1. 输入策略密钥的 **名称**。 例如，`GitHubSecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在“机密”中，输入前面记录的应用程序机密。
1. 在“密钥用法”处选择 `Signature`。
1. 单击“创建”。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果你希望用户使用 GitHub 帐户登录，则需要将该帐户定义为声明提供程序，Azure AD B2C 可通过终结点进行通信。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过将 GitHub 帐户添加到策略扩展文件中的 **ClaimsProviders** 元素来将其定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml*。
1. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
1. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>github.com</Domain>
      <DisplayName>GitHub</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="GitHub-OAUTH2">
          <DisplayName>GitHub</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">github.com</Item>
            <Item Key="authorization_endpoint">https://github.com/login/oauth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://github.com/login/oauth/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.github.com/user</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="scope">read:user user:email</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="UserAgentForClaimsExchange">CPIM-Basic/{tenant}/{policy}</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your GitHub application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GitHubSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="numericUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="github.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateIssuerUserId" />
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

1. 将 **client_id** 设置为应用程序注册中的应用程序 ID。
1. 保存文件。

### <a name="add-the-claims-transformations"></a>添加声明转换

GitHub 技术配置文件要求将 **CreateIssuerUserId** 声明转换添加到 ClaimsTransformations 列表。 如果未在文件中定义 **ClaimsTransformations** 元素，请按如下所示添加父 XML 元素。 声明转换还需要一个名为 **numericUserId** 的新声明类型。

1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 [ClaimsSchema](claimsschema.md) 元素。 如果该元素不存在，请添加该元素。
1. 将 numericUserId 声明添加到 **ClaimsSchema** 元素中。
1. 找到 " [ClaimsTransformations](claimstransformations.md) " 元素。 如果该元素不存在，请添加该元素。
1. 将 CreateIssuerUserId 声明转换添加到 **ClaimsTransformations** 元素。

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="numericUserId">
      <DisplayName>Numeric user Identifier</DisplayName>
      <DataType>long</DataType>
    </ClaimType>
  </ClaimsSchema>
  <ClaimsTransformations>
    <ClaimsTransformation Id="CreateIssuerUserId" TransformationMethod="ConvertNumberToStringClaim">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

至此，已配置策略，以便 Azure AD B2C 知道如何与 GitHub 帐户通信。 请尝试上传该策略的扩展文件，这只是为了确认它到目前为止不会出现任何问题。

1. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略” 。
2. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件。
3. 单击“上载” 。

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，标识提供者已设置，但不会出现在任何注册/登录屏幕中。 若要使其可用，请创建现有模板用户旅程的副本，然后对其进行修改，使其也具有 GitHub 标识提供者。

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
2. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
3. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
4. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
5. 重命名用户旅程的 ID。 例如，`SignUpSignInGitHub`。

### <a name="display-the-button"></a>显示按钮

**ClaimsProviderSelection** 元素类似于注册/登录屏幕上的标识提供者按钮。 如果为 GitHub 帐户添加 **claimsexchange** 元素，则当用户进入页面时，会显示一个新按钮。

1. 在创建的用户旅程中找到包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelects** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `GitHubExchange`：

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="GitHubExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在此示例中，操作用于 Azure AD B2C 与 GitHub 帐户通信以接收令牌。

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
2. 添加以下 **ClaimsExchange** 元素，以确保为用于 **TargetClaimsExchangeId** 的 ID 使用相同的值：

    ```xml
    <ClaimsExchange Id="GitHubExchange" TechnicalProfileReferenceId="GitHub-OAuth" />
    ```

    将 **TechnicalProfileReferenceId** 的值更新为之前创建的技术配置文件的 ID。 例如，`GitHub-OAuth`。

3. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-github-identity-provider-to-a-user-flow"></a>将 GitHub 标识提供者添加到用户流 

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击你想要用于 GitHub 标识提供者的用户流。
1. 在 **社交标识提供者** 下，选择 " **GitHub**"。
1. 选择“保存”。
1. 若要测试策略，请选择 " **运行用户流**"。
1. 对于 " **应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击 "**运行用户流**"

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 *SignUpSignInGitHub.xml*。
1. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如，`SignUpSignInGitHub`。
1. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如 `http://contoso.com/B2C_1A_signup_signin_github`
1. 更新 **DefaultUserJourney** 中的 **ReferenceId** 属性的值，使其与你 (SignUpSignGitHub) 创建的新用户旅程的 ID 匹配。
1. 保存更改，并上传文件。
1. 在“自定义策略”下，选择“B2C_1A_signup_signin” 。
1. 对于 " **选择应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择 " **立即运行** "，然后选择 "github"，用 github 登录并测试自定义策略。

::: zone-end
