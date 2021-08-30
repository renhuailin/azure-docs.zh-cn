---
title: 使用自定义策略将 AD FS 添加为 OpenID Connect 标识提供者
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中使用 OpenID Connect 协议和自定义策略设置 AD FS 2016
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 69803dc374e92b8fcc856237cb8b791dac5cb40c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723191"
---
# <a name="add-ad-fs-as-an-openid-connect-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略将 AD FS 添加为 OpenID Connect 标识提供者

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]


## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-an-ad-fs-application"></a>创建 AD FS 应用程序

若要在 Azure Active Directory B2C (Azure AD B2C) 中为使用 AD FS 帐户的用户启用登录，请在 AD FS 中创建应用程序组。 有关详细信息，请参阅[结合使用 OpenID Connect 和 AD FS 2016 及更高版本生成 Web 应用程序](/windows-server/identity/ad-fs/development/enabling-openid-connect-with-ad-fs)

若要创建应用程序组，请执行以下步骤：

1. 在“服务器管理器”中，选择“工具”，然后选择“AD FS 管理”  。
1. 在”AD FS管理”中，右键单击“应用程序组”，然后选择“添加应用程序组” 。
1. 在“应用程序组向导”的“欢迎”屏幕上：
    1. 输入应用程序的名称。 例如“Azure AD B2C 应用程序”。
    1. 在“客户端-服务器应用程序”下，选择“访问 Web 应用程序的 Web 浏览器”模板 。
    1. 选择“下一步”。
1. 在“应用程序组向导”的“本机应用程序”屏幕上：
    1. 复制“客户端标识符”值。 客户端标识符是 AD FS“应用程序 ID”。 本文稍后需要应用程序 ID。
    1. 在“重定向 URI”中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 如果使用[自定义域](custom-domain.md)，请输入 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称，将 `your-domain-name` 替换为你的自定义域。
    1. 选择“下一步”，然后选择“下一步”以完成应用注册向导 。 
    1. 选择“关闭”。


### <a name="configure-the-app-claims"></a>配置应用声明

在此步骤中，配置 AD FS 应用程序返回到 Azure AD B2C 的声明。  

1. 在“应用程序组”中，选择你创建的应用程序。
1. 在“应用程序属性”窗口的“应用程序”下，选择“Web 应用程序” 。 然后选择“编辑”。
    :::image type="content" source="./media/identity-provider-adfs/ad-fs-edit-app.png" alt-text="该屏幕截图显示如何编辑 Web 应用程序。":::
1. 选择“颁发转换规则”选项卡，然后选择“添加规则” 。
1. 在“声明规则模板”中，选择“以声明方式发送 LDAP 特性”。
1. 提供“声明规则名称”。 有关“属性存储”，选择“选择 Active Directory”并添加以下声明 。

    | LDAP 属性 | 传出声明类型 |
    | -------------- | ------------------- |
    | User-Principal-Name | UPN |
    | Surname | family_name |
    | Given-Name | given_name |
    | Display-Name | name |

    请注意，某些名称将不会显示在传出声明类型下拉列表中。 需要手动键入它们。 （下拉列表是可编辑的）。

1. 选择“完成”，然后选择“关闭” 。


::: zone pivot="b2c-user-flow"

## <a name="configure-ad-fs-as-an-identity-provider"></a>将 AD FS 配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“标识提供程序”，然后选择“新建 OpenID Connect 提供程序” 。
1. 输入“名称”。 例如，Contoso。
1. 对于“元数据 URL”，请输入 [AD FS OpenID Connect 配置文档](/windows-server/identity/ad-fs/development/ad-fs-openid-connect-oauth-concepts#ad-fs-endpoints)的 URL。 例如：

    ```http
    https://adfs.contoso.com/adfs/.well-known/openid-configuration 
    ```

1. 对于“客户端 ID”，输入之前记录的应用程序 ID。
1. 对于“范围”，请输入 `openid`。
1. 对于“响应类型”，请选择“id_token” 。
1. （可选）对于“域提示”，请输入 `contoso.com`。 有关详细信息，请参阅[使用 Azure Active Directory B2C 设置直接登录](direct-signin.md#redirect-sign-in-to-a-social-provider)。
1. 在“标识提供者声明映射”下，选择以下声明：

    - 用户 ID：sub
    - 显示名称：unique_name
    - 给定名称：given_name
    - 姓氏：family_name

1. 选择“保存”。

## <a name="add-ad-fs-identity-provider-to-a-user-flow"></a>将 AD FS 标识提供者添加到用户流 

此时，AD FS (Contoso) 标识提供者已设置，但还不能在任何登录页中使用。 将 AD FS 标识提供者添加到用户流的步骤：

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 选择要将 AD FS 标识提供者 (Contoso) 添加到的用户流。
1. 在“社交标识提供者”下，选择“Contoso” 。
1. 选择“保存”。
1. 若要测试策略，请选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为“testapp1”的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“运行用户流”按钮。
1. 在注册或登录页面上，选择“Contoso”以使用 Contoso 帐户登录。

如果登录过程成功，则浏览器将重定向到 `https://jwt.ms`，其中显示了 Azure AD B2C 返回的令牌内容。

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="configure-ad-fs-as-an-identity-provider"></a>将 AD FS 配置为标识提供者

要使用户能够使用 AD FS 帐户登录，需将 AD FS 定义为 Azure AD B2C 可通过终结点与之通信的声明提供程序。 

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-OpenIdConnect">
          <DisplayName>Contoso</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://your-adfs-domain/adfs/.well-known/openid-configuration</Item>
            <Item Key="response_types">id_token</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your AD FS application ID</Item>
          </Metadata>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="upn" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="unique_name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. 对于“元数据 URL”，请输入 [AD FS OpenID Connect 配置文档](/windows-server/identity/ad-fs/development/ad-fs-openid-connect-oauth-concepts#ad-fs-endpoints)的 URL。 例如：

    ```
    https://adfs.contoso.com/adfs/.well-known/openid-configuration 
    ```
5. 将 **client_id** 设置为应用程序注册中的应用程序 ID。
6. 保存文件。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>测试自定义策略

1. 选择信赖方策略，例如 `B2C_1A_signup_signin`。
1. 对于“应用程序”，请选择[前面注册](tutorial-register-applications.md)的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“立即运行”按钮。
1. 在注册或登录页面上，选择“Contoso”以使用 Contoso 帐户登录。

如果登录过程是成功的，则你的浏览器会被重定向到 `https://jwt.ms`，其中显示 Azure AD B2C 返回的令牌内容。


::: zone-end

## <a name="next-steps"></a>后续步骤

了解如何[将 AD-FS 令牌传递给应用程序](idp-pass-through-user-flow.md)。