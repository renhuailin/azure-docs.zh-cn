---
title: 使用 LinkedIn 帐户设置注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 LinkedIn 帐户注册与登录的功能。
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
ms.openlocfilehash: 1ade28af1094694d2cb6dffaecec4e4131630535
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584854"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 LinkedIn 帐户注册与登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

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

## <a name="create-a-linkedin-application"></a>创建 LinkedIn 应用程序

要将 LinkedIn 帐户用作 Azure Active Directory B2C (Azure AD B2C) 中的[标识提供者](authorization-code-flow.md)，需要在表示它的租户中创建一个应用程序。 如果还没有 LinkedIn 帐户，可以在注册 [https://www.linkedin.com/](https://www.linkedin.com/) 。

1. 使用 LinkedIn 帐户凭据登录到 [LinkedIn 开发人员网站](https://www.developer.linkedin.com/)。
1. 选择 **"我的应用**"，然后单击 " **创建应用**"。
1. 输入 **应用名称**、 **LinkedIn 页**、 **隐私策略 URL** 和 **应用徽标**。
1. 同意 LinkedIn **API 使用条款** ，并单击 " **创建应用**"。
1. 选择 " **身份验证** " 选项卡。在 " **身份验证密钥**" 下，复制 " **客户端 ID** " 和 " **客户端密码**" 值。 你将需要这两个将 LinkedIn 配置为租户中的标识提供者。 “客户端密钥”是一个很重要的安全凭据。
1. 选择 **应用的授权重定向 url** 旁边的 "编辑铅笔"，然后选择 " **添加重定向 URL**"。 输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` ，将替换 `your-tenant-name` 为你的租户的名称。 输入租户名称时，必须全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。 选择“更新”。
2. 默认情况下，LinkedIn 应用未批准与登录相关的作用域。 若要请求复查，请选择 " **产品** " 选项卡，然后选择 " **使用 LinkedIn 登录**"。 评审完成后，所需范围将添加到你的应用程序。
   > [!NOTE]
   > 可以在 " **OAuth 2.0 作用域**" 部分的 "**身份验证**" 选项卡上查看应用当前允许的范围。

::: zone pivot="b2c-user-flow"

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>将 LinkedIn 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择“标识提供者”，然后选择“LinkedIn”。
1. 输入“名称”。 例如 *LinkedIn*。
1. 对于“客户端 ID”，请输入前面创建的 LinkedIn 应用程序的客户端 ID。
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
5. 选择 " **策略密钥** "，然后选择 " **添加**"。
6. 对于“选项”，请选择 `Manual`。
7. 输入策略密钥的 **名称**。 例如，`LinkedInSecret`。 前缀 *B2C_1A_* 会自动添加到密钥名称。
8. 在 " **密钥**" 中，输入你之前记录的客户端密码。
9. 在“密钥用法”处选择 `Signature`。
10. 单击“创建”。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 LinkedIn 帐户登录，需将该帐户定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

通过将 LinkedIn 帐户添加到策略扩展文件中的 **ClaimsProviders** 元素，将其定义为声明提供程序。

1. 在编辑器中打开 * SocialAndLocalAccounts/**TrustFrameworkExtensions.xml** _ 文件。 此文件位于 [自定义策略初学者包][starter-pack] 中，你可以在其中一个必备组件中下载。
1. 找到 _ *ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
1. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
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

1. 将 **client_id** 的值替换为前面记录的 LinkedIn 应用程序的客户端 id。
1. 保存文件。

### <a name="add-the-claims-transformations"></a>添加声明转换

LinkedIn 技术配置文件需要将 **ExtractGivenNameFromLinkedInResponse** 和 **ExtractSurNameFromLinkedInResponse** 声明转换添加到 ClaimsTransformations 的列表中。 如果未在文件中定义 **ClaimsTransformations** 元素，请按如下所示添加父 XML 元素。 声明转换还需要一个名为 **nullStringClaim** 的新声明类型。

将 **BuildingBlocks** 元素添加到 *TrustFrameworkExtensions.xml* 文件顶部附近。 有关示例，请参阅 *TrustFrameworkBase.xml* 。

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

你现在已配置了一个策略，以便 Azure AD B2C 知道如何与 LinkedIn 帐户通信。 尝试上传策略的扩展文件，以确认它目前不会有任何问题。

1. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略” 。
1. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件。
1. 单击“上载” 。

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，标识提供者已设置，但在任何注册或登录屏幕上都不可用。 若要使其可用，需要创建现有模板用户旅程的副本，并对其进行修改，使其具有 LinkedIn 标识提供者。

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
1. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
1. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
1. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
1. 重命名用户旅程的 ID。 例如，`SignUpSignInLinkedIn`。

### <a name="display-the-button"></a>显示按钮

**ClaimsProviderSelection** 元素类似于注册或登录屏幕上的标识提供者按钮。 如果为 LinkedIn 帐户添加 **ClaimsProviderSelection** 元素，则当用户进入页面时，会显示一个新按钮。

1. 在创建的用户旅程中找到包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelections** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `LinkedInExchange`：

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 LinkedIn 帐户通信以接收令牌。

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
1. 添加以下 **ClaimsExchange** 元素，确保在 ID 和 **TargetClaimsExchangeId** 处使用相同的值：

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    将 **TechnicalProfileReferenceId** 的值更新为之前创建的技术配置文件的 ID。 例如，`LinkedIn-OAUTH`。

1. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

::: zone-end

：： zone pivot = "b2c-用户-流"

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>将 LinkedIn 标识提供者添加到用户流 

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击要访问 LinkedIn 标识提供者的用户流。
1. 在 " **社交标识提供者**" 下，选择 " **LinkedIn**"。
1. 选择“保存”。
1. 若要测试策略，请选择 " **运行用户流**"。
1. 对于 " **应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击 "**运行用户流**"

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 *SignUpSignInLinkedIn.xml*。
1. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如，`SignUpSignInLinkedIn`。
1. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如 `http://contoso.com/B2C_1A_signup_signin_linkedin`
1. 更新 **DefaultUserJourney** 中的 **ReferenceId** 属性的值，以匹配所创建的新用户旅程的 ID (SignUpSignLinkedIn)。
1. 保存更改并上传文件，然后选择列表中的新策略。
1. 确保在“选择应用程序”字段选择你创建的 Azure AD B2C 应用程序，然后单击“立即运行”对其进行测试 。


## <a name="migration-from-v10-to-v20"></a>从 v1.0 迁移到 v2.0

LinkedIn 最近[已将其 API 从 v1.0 更新到 v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)。 若要将现有配置迁移到新配置，请使用以下部分中的信息更新技术配置文件中的元素。

### <a name="replace-items-in-the-metadata"></a>替换元数据中的项

在 **技术配置文件** 的现有 **元数据** 元素中，更新以下项中的下列 **项** 元素：

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

更改为：

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>向元数据添加项

在 **技术配置文件** 的 **元数据** 中，添加以下 **Item** 元素：

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>更新 OutputClaims

在 **技术配置文件** 的现有 **OutputClaims** 中，更新以下 **OutputClaim** 元素：

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

更改为：

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>添加新的 OutputClaimsTransformation 元素

在 **技术配置文件** 的 **OutputClaimsTransformations** 中，添加以下 **OutputClaimsTransformation** 元素：

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>定义新的声明转换和声明类型

在最后一个步骤中，您添加了需要定义的新声明转换。 若要定义声明转换，请将它们添加到 **ClaimsTransformations** 列表中。 如果未在文件中定义 **ClaimsTransformations** 元素，请按如下所示添加父 XML 元素。 声明转换还需要一个名为 **nullStringClaim** 的新声明类型。

应将 **BuildingBlocks** 元素添加到文件的顶部附近。 例如，请参阅 *TrustframeworkBase.xml* 。

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>获取电子邮件地址

作为 LinkedIn 从1.0 版迁移到 v2.0 的一部分，需要额外调用另一个 API 才能获得电子邮件地址。 如果需要在注册过程中获取电子邮件地址，请执行以下操作：

1. 完成上述步骤以允许 Azure AD B2C 与 LinkedIn 联合以允许用户登录。 在联合身份验证过程中，Azure AD B2C 接收 LinkedIn 的访问令牌。
1. 将 LinkedIn 访问令牌保存到声明。 [请参阅此处的说明](idp-pass-through-user-flow.md)。
1. 添加以下向 LinkedIn 的 API 发出请求的声明提供程序 `/emailAddress` 。 若要授权此请求，需要 LinkedIn 访问令牌。

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 将以下业务流程步骤添加到用户旅程，以便在用户使用 LinkedIn 登录时触发 API 声明提供程序。 请确保 `Order` 相应地更新该编号。 在触发 LinkedIn 技术配置文件的业务流程步骤之后立即添加此步骤。

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

在注册期间从 LinkedIn 获取电子邮件地址是可选的。 如果你选择不从 LinkedIn 获取电子邮件，但在注册过程中需要一个，则用户需要手动输入电子邮件地址并对其进行验证。

有关使用 LinkedIn 标识提供者的策略的完整示例，请参阅 [自定义策略初学者包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider)。

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="migration-from-v10-to-v20"></a>从 v1.0 迁移到 v2.0

LinkedIn 最近[已将其 API 从 v1.0 更新到 v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)。 在迁移过程中，Azure AD B2C 只能获取 LinkedIn 用户在注册期间提供的全名。 如果电子邮件地址是注册期间收集的属性之一，则用户必须手动输入该电子邮件地址并对其进行验证。

::: zone-end