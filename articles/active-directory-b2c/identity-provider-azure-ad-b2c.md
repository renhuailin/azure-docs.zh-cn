---
title: 使用其他 Azure AD B2C 租户中的 Azure AD B2C 帐户设置注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C 为使用应用程序中其他租户的 Azure AD B2C 帐户的客户提供注册和登录。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ea4def3cfaa19e27dc05e955bf97b41976ec2190
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98953914"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>使用其他 Azure AD B2C 租户中的 Azure AD B2C 帐户设置注册和登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>概述

本文介绍如何设置与另一个 Azure AD B2C 租户的联合。 当你的应用程序受 Azure AD B2C 保护时，这将允许其他 Azure AD B2c 的用户通过其现有帐户进行登录。 在下图中，用户可以使用由 *Fabrikam* 的 Azure AD B2C 租户管理的帐户登录到受 *Contoso* Azure AD B2C 保护的应用程序 

![与另一个 Azure AD B2C 租户 Azure AD B2C 联合](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>必备条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>创建 Azure AD B2C 应用程序

若要使用来自其他 Azure AD B2C (租户的帐户（例如 Fabrikam) ）为用户启用登录，请在 Azure AD B2C (例如 Contoso) ：

1. 创建 [用户流](tutorial-create-user-flows.md)或 [自定义策略](custom-policy-get-started.md)。
1. 然后，在 Azure AD B2C 中创建应用程序，如本节中所述。 

创建应用程序。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保你使用的目录包含其他 Azure AD B2C 租户 (例如，Fabrikam.com) 。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如， *ContosoApp*。
1. 在“支持的帐户类型”下，选择“任何标识提供者或组织目录中的帐户(用于通过用户流对用户进行身份验证)” 。
1. 在 " **重定向 URI**" 下，选择 " **Web**"，然后以所有小写字母输入以下 URL，其中 `your-B2C-tenant-name` 将替换为 Azure AD B2C 租户的名称 (例如 Contoso) 。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如，`https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`。

1. 在 "权限" 下，选中 "向 **管理员同意 openid 并 offline_access 权限** " 复选框。
1. 选择“注册”  。
1. 在 **Azure AD B2C 应用注册** "页中，选择你创建的应用程序，例如 *ContosoApp*。
1. 记录应用程序概述页上显示的“应用程序（客户端）ID”。 在下一部分中配置标识提供者时，需要用到此项。
1. 在左侧菜单中“管理”下，选择“证书和机密”。 
1. 选择“新建客户端机密”。
1. 在“说明”框中输入客户端机密的说明。 例如，*clientsecret1*。
1. 在“过期时间”下，选择机密持续生效的时间，然后选择“添加”。
1. 记下机密的“值”。 在下一部分中配置标识提供者时，需要用到此项。


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>将 Azure AD B2C 配置为标识提供程序

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保你使用的目录包含你要配置联合 (的 Azure AD B2C 租户，例如 Contoso) 。 在顶部菜单中选择 " **目录 + 订阅** " 筛选器，然后选择包含 Azure AD B2C 租户的目录 (例如 Contoso) 。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“标识提供程序”，然后选择“新建 OpenID Connect 提供程序” 。
1. 输入“名称”。 例如，输入 *Fabrikam*。
1. 对于 " **元数据 url**"，请输入以下 url，并将替换 `{tenant}` 为 Azure AD B2C 租户的域名 (例如，Fabrikam) 。 将替换为 `{policy}` 你在另一个租户中配置的策略名称：

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    例如，`https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`。

1. 对于“客户端 ID”，输入之前记录的应用程序 ID。
1. 对于“客户端机密”，请输入之前记录的客户端机密。
1. 对于“范围”，请输入 `openid`。
1. 对于“响应类型”和“响应模式” ，请保留默认值。
1.  (**域提示** 的可选) 中，输入要用于 [直接登录](direct-signin.md#redirect-sign-in-to-a-social-provider)的域名。 例如， *fabrikam.com*。
1. 在“标识提供者声明映射”下，选择以下声明：

    - **用户 ID**： *sub*
    - 显示名称：name
    - 给定名称：given_name
    - 姓氏：family_name
    - **电子邮件**： *电子邮件*

1. 选择“保存”。

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>将 Azure AD B2C 标识提供者添加到用户流 

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击要添加 Azure AD B2C 标识提供者的用户流。
1. 在 " **社交标识提供者**" 下，选择 " **Fabrikam**"。
1. 选择“保存”。
1. 若要测试策略，请选择 " **运行用户流**"。
1. 对于 " **应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击 "**运行用户流**"
1. 在注册或登录页上，选择 " *Fabrikam* " 以便用其他 Azure AD B2C 租户登录。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>创建策略密钥

需要存储之前在 Azure AD B2C 租户中创建的应用程序密钥。

1. 请确保你使用的目录包含你要配置联合 (的 Azure AD B2C 租户，例如 Contoso) 。 在顶部菜单中选择 " **目录 + 订阅** " 筛选器，然后选择包含 Azure AD B2C 租户的目录 (例如 Contoso) 。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“策略”下，选择“Identity Experience Framework”。 
1. 选择 " **策略密钥** "，然后选择 " **添加**"。
1. 对于“选项”，请选择 `Manual`。
1. 输入策略密钥的 **名称**。 例如，`FabrikamAppSecret`。  创建前缀 `B2C_1A_` 后，会自动将其添加到密钥的名称，因此，在下一部分的 XML 中，其引用为 *B2C_1A_FabrikamAppSecret*。
1. 在 " **密钥**" 中，输入你之前记录的客户端密码。
1. 在“密钥用法”处选择 `Signature`。
1. 选择“创建”。

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>将 Azure AD B2C 配置为标识提供程序

若要允许用户使用其他 Azure AD B2C 租户 (Fabrikam) 中的帐户进行登录，需要将其他 Azure AD B2C 定义为 Azure AD B2C 可通过终结点进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过将 Azure AD B2C 添加到策略扩展文件中的 **ClaimsProvider** 元素，将 Azure AD B2C 定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml* 文件。
1. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
1. 如下所示添加新的 **ClaimsProvider**：
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AzureADB2CFabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 更新以下具有相关值的 XML 元素：

    |XML 元素  |值  |
    |---------|---------|
    |ClaimsProvider\Domain  | 用于 [直接登录](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider)的域名。 输入要在直接登录中使用的域名。 例如， *fabrikam.com*。 |
    |TechnicalProfile\DisplayName|此值会显示在登录屏幕中的登录按钮上。 例如， *Fabrikam*。 |
    |Metadata \ client_id|标识提供者的应用程序标识符。 用之前在另一个 Azure AD B2C 租户中创建的应用程序 ID 更新客户端 ID。|
    |Metadata\METADATA|指向 OpenID Connect 标识提供者配置文档的 URL，也称为 OpenID 众所周知的配置终结点。 输入以下 URL `{tenant}` ，并将替换为其他 Azure AD B2C 租户 (Fabrikam) 的域名。 将替换为 `{tenant}` 你在另一个租户中配置的策略名称，并将替换为 `{policy]` 策略名称： `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration` 。 例如，`https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`。|
    |CryptographicKeys| 将 **StorageReferenceId** 的值更新为之前创建的策略密钥的名称。 例如，`B2C_1A_FabrikamAppSecret`。| 
    

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADB2CFabrikamExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADB2CFabrikamExchange" TechnicalProfileReferenceId="AzureADB2CFabrikam-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end

## <a name="next-steps"></a>后续步骤

了解如何将 [其他 Azure AD B2C 令牌传递给应用程序](idp-pass-through-user-flow.md)。
