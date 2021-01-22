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
ms.date: 01/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a378a95a28b1299502ef15cadca60f3807f99429
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98674393"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 LinkedIn 帐户注册与登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-linkedin-application"></a>创建 LinkedIn 应用程序

若要在 Azure Active Directory B2C (Azure AD B2C) 中为具有 LinkedIn 帐户的用户登录，需要在 [LinkedIn 开发人员网站](https://www.developer.linkedin.com/)中创建应用程序。 有关详细信息，请参阅 [授权代码流](/linkedin/shared/authentication/authorization-code-flow)。 如果还没有 LinkedIn 帐户，可以在注册 [https://www.linkedin.com/](https://www.linkedin.com/) 。

1. 使用 LinkedIn 帐户凭据登录到 [LinkedIn 开发人员网站](https://www.developer.linkedin.com/)。
1. 选择 **"我的应用**"，然后单击 " **创建应用**"。
1. 输入 **应用名称**、 **LinkedIn 页**、 **隐私策略 URL** 和 **应用徽标**。
1. 同意 LinkedIn **API 使用条款** ，并单击 " **创建应用**"。
1. 选择 " **身份验证** " 选项卡。在 " **身份验证密钥**" 下，复制 " **客户端 ID** " 和 " **客户端密码**" 值。 你将需要这两个将 LinkedIn 配置为租户中的标识提供者。 “客户端密钥”是一个很重要的安全凭据。
1. 选择 **应用的授权重定向 url** 旁边的 "编辑铅笔"，然后选择 " **添加重定向 URL**"。 输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` ，将替换 `your-tenant-name` 为你的租户的名称。 输入租户名称时，必须全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。 选择“更新”  。
2. 默认情况下，LinkedIn 应用未批准与登录相关的作用域。 若要请求复查，请选择 " **产品** " 选项卡，然后选择 " **使用 LinkedIn 登录**"。 评审完成后，所需范围将添加到你的应用程序。
   > [!NOTE]
   > 可以在 " **OAuth 2.0 作用域**" 部分的 "**身份验证**" 选项卡上查看应用当前允许的范围。

::: zone pivot="b2c-user-flow"

## <a name="configure-linkedin-as-an-identity-provider"></a>将 LinkedIn 配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择“标识提供者”，然后选择“LinkedIn”。
1. 输入“名称”。 例如 *LinkedIn*。
1. 对于“客户端 ID”，请输入前面创建的 LinkedIn 应用程序的客户端 ID。
1. 对于 **客户端密码**，请输入你记录的客户端密码。
1. 选择“保存”。

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>将 LinkedIn 标识提供者添加到用户流 

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击要添加 LinkedIn 标识提供者的用户流。
1. 在 " **社交标识提供者**" 下，选择 " **LinkedIn**"。
1. 选择“保存”。
1. 若要测试策略，请选择 " **运行用户流**"。
1. 对于 " **应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击 "**运行用户流**"

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的客户端机密。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
4. 在“概述”页上选择“标识体验框架”。
5. 选择 " **策略密钥** "，然后选择 " **添加**"。
6. 对于“选项”，请选择 `Manual`。
7. 输入策略密钥的 **名称**。 例如，`LinkedInSecret`。 前缀 *B2C_1A_* 会自动添加到密钥名称。
8. 在 " **密钥**" 中，输入你之前记录的客户端密码。
9. 在“密钥用法”处选择 `Signature`。
10. 单击“创建”。

## <a name="configure-linkedin-as-an-identity-provider"></a>将 LinkedIn 配置为标识提供者

要使用户能够使用 LinkedIn 帐户登录，你需要将该帐户定义为声明提供程序，该提供程序 Azure AD B2C 可以通过终结点进行通信。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

通过将 LinkedIn 帐户添加到策略扩展文件中的 **ClaimsProviders** 元素，将其定义为声明提供程序。

1. 在编辑器中打开 * SocialAndLocalAccounts/**TrustFrameworkExtensions.xml** _ 文件。 此文件位于 [自定义策略初学者包][starter-pack] 中，你可以在其中一个必备组件中下载。
1. 找到 _ *ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
1. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAuth2">
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

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

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