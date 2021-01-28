---
title: 使用自定义策略为多租户 Azure AD 设置登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C 中的自定义策略添加多租户 Azure AD 标识提供者。
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
ms.openlocfilehash: 8c2b97d1848450ecda2e83d5ba12469d7c61d8f9
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98952732"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略为多租户 Azure Active Directory 设置登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

本文说明如何使用多租户终结点为用户登录，以便 Azure Active Directory (Azure AD) 。 允许多个 Azure AD 租户中的用户使用 Azure AD B2C 登录，无需为每个租户配置标识提供者。 但是，任何这些租户中的来宾成员都将无法登录。 为此，你需要[单独配置每个租户](identity-provider-azure-ad-single-tenant.md)。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-application"></a>注册应用程序

若要在 Azure Active Directory B2C (Azure AD B2C) 中为具有 Azure AD 帐户的用户登录，则需要在 [Azure 门户](https://portal.azure.com)中创建应用程序。 有关详细信息，请参阅 [将应用程序注册到 Microsoft 标识平台](../active-directory/develop/quickstart-register-app.md)。


1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含组织 Azure AD 租户的目录（例如，contoso.com）。 在顶部菜单中选择 " **目录 + 订阅" 筛选器** ，然后选择包含你的租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册” 。
1. 选择“新注册”。
1. 输入应用程序的 **名称**。 例如，`Azure AD B2C App`。
1. 选择 **任何组织目录中的帐户 (此应用程序的任何 Azure AD 目录–多租户)** 。
1. 对于“重定向 URI”，接受值 **Web**，并以全小写字母输入以下 URL，其中 `your-B2C-tenant-name` 将替换为 Azure AD B2C 租户的名称。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如，`https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`。

1. 选择“注册”。 记录“应用程序(客户端) ID”，以便在后续步骤中使用。
1. 依次选择“证书和机密”、“新建客户端机密”。 
1. 为机密输入说明，选择到期时间，然后选择“添加”。 记录机密的值，以便在后续步骤中使用。

## <a name="configuring-optional-claims"></a>配置可选声明

如果要 `family_name` 从 Azure AD 获取和声明， `given_name` 则可以在 Azure 门户 UI 或应用程序清单中为应用程序配置可选声明。 有关详细信息，请参阅[如何向 Azure AD 应用提供可选声明](../active-directory/develop/active-directory-optional-claims.md)。

1. 登录 [Azure 门户](https://portal.azure.com)。 搜索并选择“Azure Active Directory”。
1. 从“管理”部分中选择“应用注册” 。
1. 在列表中选择要为其配置可选声明的应用程序。
1. 在“管理”部分中，选择“令牌配置”。 
1. 选择“添加可选声明”。
1. 对于“令牌类型”，选择“ID”。
1. 选择要添加、和的可选声明 `family_name` `given_name` 。
1. 单击“添加”。

## <a name="create-a-policy-key"></a>创建策略密钥

需将创建的应用程序密钥存储在 Azure AD B2C 租户中。

1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在顶部菜单中选择 " **目录 + 订阅" 筛选器** ，然后选择包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“策略”下，选择“Identity Experience Framework”。 
1. 选择 " **策略密钥** "，然后选择 " **添加**"。
1. 对于“选项”，请选择 `Manual`。
1. 输入策略密钥的 **名称**。 例如，`AADAppSecret`。  创建前缀 `B2C_1A_` 后，会自动将其添加到密钥的名称，因此，在下一部分的 XML 中，其引用为 *B2C_1A_AADAppSecret*。
1. 在 " **密钥**" 中，输入你之前记录的客户端密码。
1. 在“密钥用法”处选择 `Signature`。
1. 选择“创建”。

## <a name="configure-azure-ad-as-an-identity-provider"></a>将 Azure AD 配置为标识提供者

若要使用户能够使用 Azure AD 帐户登录，需要将 Azure AD 定义为 Azure AD B2C 可通过终结点进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

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
1. 在 **技术配置文件** 元素下，更新 **DisplayName** 的值，例如 `Contoso Employee` 。 此值会显示在登录页中的登录按钮上。
1. 将 **client_id** 设置为之前注册的 Azure AD 多租户应用程序的应用程序 id。
1. 在 **CryptographicKeys** 下，将 **StorageReferenceId** 的值更新为之前创建的策略密钥的名称。 例如，`B2C_1A_AADAppSecret`。

### <a name="restrict-access"></a>限制访问

使用 `https://login.microsoftonline.com/` 作为 **ValidTokenIssuerPrefixes** 的值将允许所有 Azure AD 用户登录到你的应用程序。 更新有效令牌颁发者的列表，并将访问权限限制为可登录的 Azure AD 租户用户的特定列表。

若要获取这些值，请查看要让用户登录的每个 Azure AD 租户的 OpenID Connect 发现元数据。 元数据 URL 的格式类似于 `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration` ，其中 `your-tenant` 是 Azure AD 租户名称。 例如：

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

针对应用于登录的每个 Azure AD 租户执行以下步骤：

1. 打开浏览器并中转到租户的 OpenID Connect 元数据 URL。 查找 **颁发者** 对象并记录其值。 其外观应类似于 `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/` 。
1. 将值复制并粘贴到 **ValidTokenIssuerPrefixes** 项。 使用逗号分隔多个颁发者。 上面的 XML 示例中会显示一个包含两个颁发者的示例 `ClaimsProvider` 。

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

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

若要测试多租户登录功能，请使用另一个 Azure AD 租户的用户的凭据执行最后两个步骤。 复制 " **立即运行" 终结点** 并在专用浏览器窗口中打开它，例如，在 Google Chrome 中的 Incognito 模式或 Microsoft Edge 中的 InPrivate 窗口。 通过在专用浏览器窗口中打开，可以通过不使用任何当前缓存 Azure AD 凭据来测试整个用户旅程。

## <a name="next-steps"></a>后续步骤

使用自定义策略时，有时可能需要在部署过程中对策略进行故障排除时提供其他信息。

若要帮助诊断问题，可以暂时将策略置于 "开发人员模式" 中，并 Azure 应用程序 Insights 收集日志。 了解 Azure Active Directory B2C 中的 [操作方法：收集日志](troubleshoot-with-application-insights.md)。

::: zone-end
