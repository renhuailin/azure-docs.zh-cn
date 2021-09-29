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
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2e024c9cbe5733b5cf5cfb81946957518b7ea28d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128574455"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Microsoft 帐户注册与登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="verify-the-applications-publisher-domain"></a>验证应用程序的发布者域
从 2020 年 11 月开始，新的应用程序注册在用户同意提示中会显示为“未验证”，除非[应用程序的发布者域已验证](../active-directory/develop/howto-configure-publisher-domain.md)，并且公司标识已使用 Microsoft 合作伙伴网络进行验证且与应用程序相关联。 （[详细了解](../active-directory/develop/publisher-verification-overview.md)此更改。）请注意，对于 Azure AD B2C 用户流，发布者域仅在使用 Microsoft 帐户或其他 [Azure AD](../active-directory-b2c/identity-provider-azure-ad-single-tenant.md) 租户作为标识提供者时才会显示。 若要满足这些新要求，请执行以下操作：

1. [使用 Microsoft 合作伙伴网络 (MPN) 帐户验证公司标识](/partner-center/verification-responses)。 此过程会验证有关贵公司和贵公司主要联系人的信息。
1. 使用以下选项之一完成发布者验证过程，以便将 MPN 帐户与应用注册相关联：
   - 如果 Microsoft 帐户标识提供者的应用注册在 Azure AD 租户中，请[在应用注册门户中验证应用](../active-directory/develop/mark-app-as-publisher-verified.md)。
   - 如果 Microsoft 帐户标识提供者的应用注册在 Azure AD B2C 租户中，请[使用 Microsoft Graph API（例如，使用 Graph 浏览器）将应用标记为已验证的发布者](../active-directory/develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)。 用于设置应用的已验证发布者的 UI 当前已对 Azure AD B2C 租户禁用。

## <a name="create-a-microsoft-account-application"></a>创建 Microsoft 帐户应用程序

若要在 Azure Active Directory B2C (Azure AD B2C) 中为使用 Microsoft 帐户的用户启用登录，你需要在 [Azure 门户](https://portal.azure.com)中创建应用程序。 有关详细信息，请参阅[将应用程序注册到 Microsoft 标识平台](../active-directory/develop/quickstart-register-app.md)。 如果还没有 Microsoft 帐户，可以在 [https://www.live.com/](https://www.live.com/) 获取。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录 + 订阅”页上，在“目录名称”列表中找到 Azure AD 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册” 。
1. 选择“新注册”。
1. 输入应用程序的 **名称**。 例如，MSAapp1。
1. 在“受支持的帐户类型”下，选择“任何组织目录（任何 Azure AD 目录 - 多租户）中的帐户和个人 Microsoft 帐户（例如，Skype、Xbox）”。

   有关不同帐户类型选择的详细信息，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](../active-directory/develop/quickstart-register-app.md)。
1. 对于“重定向 URI (可选)”，选择“Web”并输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 如果使用[自定义域](custom-domain.md)，请输入 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称，将 `your-domain-name` 替换为你的自定义域。
1. 选择“注册”
1. 记录应用程序概述页上显示的“应用程序（客户端）ID”。 在下一部分配置标识提供者时，需要此客户端 ID。
1. 选择“证书和密码”
1. 单击“新客户端密码”
1. 为密码输入“说明”，例如“应用程序密码 1”，然后单击“添加”。
1. 记录“值”列中显示的应用程序密码。 在下一部分配置标识提供者时，需要此客户端密码。

::: zone pivot="b2c-user-flow"

## <a name="configure-microsoft-as-an-identity-provider"></a>将 Microsoft 配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择“标识提供者”，然后选择“Microsoft 帐户”。
1. 输入“名称”。 例如，MSA。
1. 对于“客户端 ID”，输入之前创建的 Azure AD 应用程序的应用程序（客户端）ID。
1. 对于“客户端密码”，输入已记录的客户端密码。
1. 选择“保存”。

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>将 Microsoft 标识提供者添加到用户流 

此时，Microsoft 标识提供者已设置，但还不能在任何登录页中使用。 若要将 Microsoft 标识提供者添加到用户流，请执行以下操作：

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击要添加 Microsoft 标识提供者的用户流。
1. 在“社交标识提供者”下，选择“Microsoft 帐户”。
1. 选择“保存”。
1. 若要测试策略，请选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为“testapp1”的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“运行用户流”按钮。
1. 在注册或登录页上，选择“Microsoft”以使用 Microsoft 帐户登录。

如果登录过程成功，则浏览器会重定向到 `https://jwt.ms`，后者显示 Azure AD B2C 返回的令牌内容。

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
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“概述”页上选择“标识体验框架”。
1. 选择“策略密钥”，然后选择“添加”。
1. 对于“选项”，请选择 `Manual`。
1. 输入策略密钥的 **名称**。 例如，`MSASecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在“密码”中，输入在上一部分中记录的客户端密码。
1. 在“密钥用法”处选择 `Signature`。
1. 单击“创建”。

## <a name="configure-microsoft-as-an-identity-provider"></a>将 Microsoft 配置为标识提供者

要让用户使用 Microsoft 帐户登录，需将该帐户定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

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

## <a name="test-your-custom-policy"></a>测试自定义策略

1. 选择信赖方策略，例如 `B2C_1A_signup_signin`。
1. 对于“应用程序”，请选择[前面注册](tutorial-register-applications.md)的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“立即运行”按钮。
1. 在注册或登录页上，选择“Microsoft”以使用 Microsoft 帐户登录。

如果登录过程成功，则浏览器会重定向到 `https://jwt.ms`，后者显示 Azure AD B2C 返回的令牌内容。

::: zone-end