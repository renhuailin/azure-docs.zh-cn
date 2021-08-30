---
title: 通过自定义策略为多租户 Azure AD 设置登录
titleSuffix: Azure AD B2C
description: 使用自定义策略在 Azure Active Directory B2C 中添加多租户 Azure AD 标识提供者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 474bb5582011c9e701a188f227a54238a9f19b57
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112285564"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略为多租户 Azure Active Directory 设置登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

本文介绍如何支持用户使用 Azure Active Directory (Azure AD) 的多租户终结点登录。 允许来自多个 Azure AD 租户的用户使用 Azure AD B2C 进行登录，而不必为每个租户配置标识提供者。 但是，任何这些租户中的来宾成员都将无法登录。 为此，你需要[单独配置每个租户](identity-provider-azure-ad-single-tenant.md)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-azure-ad-app"></a>注册 Azure AD 应用

若要在 Azure Active Directory B2C (Azure AD B2C) 中为使用 Azure AD 帐户的用户启用登录，则需要在 [Azure 门户](https://portal.azure.com)中创建应用程序。 有关详细信息，请参阅[将应用程序注册到 Microsoft 标识平台](../active-directory/develop/quickstart-register-app.md)。


1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含组织 Azure AD 租户的目录（例如，contoso.com）。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册” 。
1. 选择“新注册”。
1. 输入应用程序的 **名称**。 例如，`Azure AD B2C App`。
1. 为此应用程序选择“任何组织目录(任何 Azure AD 目录 - 多租户)中的帐户”。
1. 对于“重定向 URI”，接受值 **Web**，并以全小写字母输入以下 URL，其中 `your-B2C-tenant-name` 将替换为 Azure AD B2C 租户的名称。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如，`https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`。

    如果使用[自定义域](custom-domain.md)，请输入 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-domain-name` 替换为你的自定义域，将 `your-tenant-name` 替换为租户的名称。

1. 选择“注册”。 记录“应用程序(客户端) ID”，以便在后续步骤中使用。
1. 依次选择“证书和机密”、“新建客户端机密”。 
1. 为机密输入说明，选择到期时间，然后选择“添加”。 记录机密的值，以便在后续步骤中使用。

### <a name="configuring-optional-claims"></a>配置可选声明

如果要从 Azure AD 获取 `family_name` 和 `given_name` 声明，可以在 Azure 门户 UI 或应用程序清单中为应用程序配置可选声明。 有关详细信息，请参阅[如何向 Azure AD 应用提供可选声明](../active-directory/develop/active-directory-optional-claims.md)。

1. 登录 [Azure 门户](https://portal.azure.com)。 搜索并选择“Azure Active Directory”。
1. 从“管理”部分中选择“应用注册” 。
1. 在列表中选择要为其配置可选声明的应用程序。
1. 在“管理”部分中，选择“令牌配置”。 
1. 选择“添加可选声明”。
1. 对于“令牌类型”，选择“ID”。
1. 选择要添加的可选声明：`family_name` 和 `given_name`。
1. 单击“添加”。

## <a name="optional-verify-your-app-authenticity"></a>[可选] 验证应用真实性

[发布者验证](../active-directory/develop/publisher-verification-overview.md)旨在帮助你的用户了解你[注册](#register-an-azure-ad-app)的应用的真实性。 经过验证的应用是指应用的发布者已使用其 MPN Microsoft 合作伙伴网络 (MPN) [验证](/partner-center/verification-responses)其标识。 了解如何[将应用标记为“发布者已验证”](../active-directory/develop/mark-app-as-publisher-verified.md)。 

## <a name="create-a-policy-key"></a>创建策略密钥

需将创建的应用程序密钥存储在 Azure AD B2C 租户中。

1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“策略”下，选择“Identity Experience Framework”。 
1. 选择“策略密钥”，然后选择“添加”。
1. 对于“选项”，请选择 `Manual`。
1. 输入策略密钥的 **名称**。 例如，`AADAppSecret`。  前缀 `B2C_1A_` 会在创建密钥时自动添加到密钥的名称，因此它在以下部分的 XML 中的引用是 B2C_1A_AADAppSecret。
1. 在“机密”中，输入你之前记录的客户端密码。
1. 在“密钥用法”处选择 `Signature`。
1. 选择“创建”。

## <a name="configure-azure-ad-as-an-identity-provider"></a>将 Azure AD 配置为标识提供者

若要用户使用 Azure AD 帐户进行登录，则需将 Azure AD 定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

要将 Azure AD 定义为声明提供程序，可在策略的扩展文件中将 Azure AD 添加到 ClaimsProvider 元素。

1. 打开 *TrustFrameworkExtensions.xml* 文件。
1. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
1. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADCommon-OpenIdConnect">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 在 ClaimsProvider 元素下，将 Domain 的值更新为可用于与其他标识提供者进行区分的唯一值。
1. 在 TechnicalProfile 元素下，更新 DisplayName 的值，例如 `Multi-Tenant AAD`。 此值会显示在登录页中的登录按钮上。
1. 将 client_id 设置为你之前注册的 Azure AD 多租户应用程序的应用程序 ID。
1. 在 CryptographicKeys 下，更新 StorageReferenceId 的值为之前创建的策略密钥的名称。 例如，`B2C_1A_AADAppSecret`。

### <a name="restrict-access"></a>限制访问

使用 `https://login.microsoftonline.com/` 作为 **ValidTokenIssuerPrefixes** 的值将允许所有 Azure AD 用户登录到你的应用程序。 更新有效令牌颁发者列表，并且仅允许可以登录的一组特定 Azure AD 租户用户进行访问。

若要获取值，请查看希望让用户从中登录的每个 Azure AD 租户的 OpenID Connect 发现元数据。 元数据 URL 的格式类似于 `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`，其中 `your-tenant` 为 Azure AD 租户名称。 例如：

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

对每个应用于登录的 Azure AD 租户执行以下步骤：

1. 为该租户打开浏览器并转到 OpenID Connect 元数据 URL。 查找 issuer 对象，然后记录其值。 它看起来应该类似于 `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`。
1. 复制并粘贴该值到 ValidTokenIssuerPrefixes 密钥。 多个证书颁发者以逗号隔开。 之前的 `ClaimsProvider` XML 示例中显示了一个出现两位证书颁发者的示例。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADCommonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADCommonExchange" TechnicalProfileReferenceId="AADCommon-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>测试自定义策略

1. 选择信赖方策略，例如 `B2C_1A_signup_signin`。
1. 对于“应用程序”，请选择[前面注册](tutorial-register-applications.md)的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“立即运行”按钮。
1. 在注册或登录页面上，选择“通用 AAD”以使用 Azure AD 帐户登录。

若要测试多租户登录功能，请使用存在另一个 Azure AD 租户的用户的凭据执行最后两个步骤。 复制“立即运行终结点”，然后在私密浏览器窗口中将其打开，例如，在 Google Chrome 中打开 Incognito Mode 或在 Microsoft Edge 中打开 InPrivate 窗口。 通过在私密浏览器窗口中打开，你可以不使用任何当前缓存的 Azure AD 凭据测试整个用户旅程。

如果登录过程是成功的，则你的浏览器会被重定向到 `https://jwt.ms`，其中显示 Azure AD B2C 返回的令牌内容。

## <a name="next-steps"></a>后续步骤

了解如何[将 Azure AD 令牌传递给应用程序](idp-pass-through-user-flow.md)。

::: zone-end
