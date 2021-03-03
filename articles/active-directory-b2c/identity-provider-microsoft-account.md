---
title: 设置通过 Microsoft 帐户注册与登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 Microsoft 帐户注册与登录的功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1f4aea472988555047a736e6a248fa5690e42a23
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645841"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Microsoft 帐户注册与登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-microsoft-account-application"></a>创建 Microsoft 帐户应用程序

若要为具有 Microsoft 帐户 Azure Active Directory B2C (Azure AD B2C) 的用户登录，需要在 [Azure 门户](https://portal.azure.com)中创建应用程序。 有关详细信息，请参阅 [将应用程序注册到 Microsoft 标识平台](../active-directory/develop/quickstart-register-app.md)。 如果还没有 Microsoft 帐户，可以在 [https://www.live.com/](https://www.live.com/) 获取。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用包含 Azure AD 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册” 。
1. 选择“新注册”。
1. 输入应用程序的 **名称**。 例如，MSAapp1。
1. 在“受支持的帐户类型”下，选择“任何组织目录（任何 Azure AD 目录 - 多租户）中的帐户和个人 Microsoft 帐户（例如，Skype、Xbox）”。

   有关不同帐户类型选择的详细信息，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](../active-directory/develop/quickstart-register-app.md)。
1. 在“重定向 URI（可选）”下，选择“Web”，然后在文本框中输入 `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp`。 将 `<tenant-name>` 替换为 Azure AD B2C 租户的名称。
1. 选择“注册”
1. 记录应用程序概述页上显示的“应用程序（客户端）ID”。 在下一部分中配置标识提供者时需要客户端 ID。
1. 选择“证书和密码”
1. 单击“新客户端密码”
1. 为密码输入“说明”，例如“应用程序密码 1”，然后单击“添加”。
1. 记录“值”列中显示的应用程序密码。 在下一部分中配置标识提供者时需要客户端密码。

::: zone pivot="b2c-user-flow"

## <a name="configure-microsoft-as-an-identity-provider"></a>将 Microsoft 配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择“标识提供者”，然后选择“Microsoft 帐户”。
1. 输入“名称”。 例如，MSA。
1. 对于“客户端 ID”，输入之前创建的 Azure AD 应用程序的应用程序（客户端）ID。
1. 对于“客户端密码”，输入已记录的客户端密码。
1. 选择“保存”。

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>将 Microsoft 标识提供者添加到用户流 

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击要添加 Microsoft 标识提供者的用户流。
1. 在 **社交标识提供者** 下，选择 " **Microsoft 帐户**"。
1. 选择“保存”。
1. 若要测试策略，请选择 " **运行用户流**"。
1. 对于 " **应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击 "**运行用户流**"

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configuring-optional-claims"></a>配置可选声明

如果要从 Azure AD 获取 `family_name` 和 `given_name` 声明，可以在 Azure 门户 UI 或应用程序清单中为应用程序配置可选声明。 有关详细信息，请参阅[如何向 Azure AD 应用提供可选声明](../active-directory/develop/active-directory-optional-claims.md)。

1. 登录 [Azure 门户](https://portal.azure.com)。 搜索并选择“Azure Active Directory”。
1. 从“管理”部分中选择“应用注册” 。
1. 在列表中选择要为其配置可选声明的应用程序。
1. 从“管理”部分中选择“令牌配置（预览）”。 
1. 选择“添加可选声明”。
1. 选择要配置的令牌类型。
1. 选择要添加的可选声明。
1. 单击“添加”。

## <a name="create-a-policy-key"></a>创建策略密钥

现在，已在 Azure AD 租户中创建了应用程序，需要将该应用程序的客户端密码存储在 Azure AD B2C 租户中。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“概述”页上选择“标识体验框架”。
1. 选择“策略密钥”，然后选择“添加”。
1. 对于“选项”，请选择 `Manual`。
1. 输入策略密钥的 **名称**。 例如，`MSASecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在“密码”中，输入在上一部分中记录的客户端密码。
1. 在“密钥用法”处选择 `Signature`。
1. 单击“创建”。

## <a name="configure-microsoft-as-an-identity-provider"></a>将 Microsoft 配置为标识提供者

若要使用户能够使用 Microsoft 帐户登录，需要将该帐户定义为 Azure AD B2C 可以通过终结点进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

要将 Azure AD 定义为声明提供程序，可在策略的扩展文件中添加 **ClaimsProvider** 元素。

1. 保存 TrustFrameworkExtensions.xml 策略文件。
1. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
1. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-MicrosoftAccount-OpenIdConnect">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. 将 client_id 的值替换为前面记录的 Azure AD 应用程序的“应用程序（客户端）ID”。
1. 保存文件。

现在，已配置了策略，因此 Azure AD B2C 知道如何与 Azure AD 中的 Microsoft 帐户应用程序进行通信。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-MicrosoftAccount-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end
