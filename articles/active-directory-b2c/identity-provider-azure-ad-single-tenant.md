---
title: 为 Azure AD 组织设置登录
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中设置登录特定 Azure Active Directory 组织。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 05c4d36f266fb526a1d0232cc32f0408e4322c80
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654381"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中设置登录特定 Azure Active Directory 组织

本文介绍如何使用 Azure AD B2C 中的用户流为特定 Azure AD 组织中的用户启用登录。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-azure-ad-app"></a>注册 Azure AD 应用

若要让用户从特定的 Azure AD 组织登录，需要在组织 Azure AD 租户中注册一个应用程序。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含组织 Azure AD 租户的目录（例如，contoso.com）。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册” 。
1. 选择“新注册”。
1. 输入应用程序的 **名称**。 例如，`Azure AD B2C App`。
1. 对于此应用程序，接受默认选择“仅此组织目录中的帐户”。
1. 对于“重定向 URI”，接受值 **Web**，并以全小写字母输入以下 URL，其中 `your-B2C-tenant-name` 将替换为 Azure AD B2C 租户的名称。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如，`https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`。

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
1. 单击“添加” 。

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>将 Azure AD 配置为标识提供者

1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“标识提供程序”，然后选择“新建 OpenID Connect 提供程序” 。
1. 输入“名称”。 例如，输入“Contoso Azure AD”。
1. 对于“元数据 URL”，输入以下 URL，并将 `{tenant}` 替换为 Azure AD 租户的域名：

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    例如，`https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`。
    例如，`https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`。

1. 对于“客户端 ID”，输入之前记录的应用程序 ID。
1. 对于“客户端机密”，请输入之前记录的客户端机密。
1. 对于“范围”，请输入 `openid profile`。
1. 对于“响应类型”和“响应模式” ，请保留默认值。
1. （可选）对于“域提示”，请输入 `contoso.com`。 有关详细信息，请参阅[使用 Azure Active Directory B2C 设置直接登录](direct-signin.md#redirect-sign-in-to-a-social-provider)。
1. 在“标识提供者声明映射”下，选择以下声明：

    - **用户 ID**：*oid*
    - 显示名称：name
    - 给定名称：given_name
    - 姓氏：family_name
    - **电子邮件**： *preferred_username*

1. 选择“保存”。 

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>创建策略密钥

需将创建的应用程序密钥存储在 Azure AD B2C 租户中。

1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在顶部菜单中选择 " **目录 + 订阅" 筛选器** ，然后选择包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“策略”下，选择“Identity Experience Framework”。 
1. 选择 " **策略密钥** "，然后选择 " **添加**"。
1. 对于“选项”，请选择 `Manual`。
1. 输入策略密钥的 **名称**。 例如，`ContosoAppSecret`。  创建前缀 `B2C_1A_` 后，会自动将其添加到密钥的名称，因此，在下一部分的 XML 中，其引用为 *B2C_1A_ContosoAppSecret*。
1. 在 " **密钥**" 中，输入你之前记录的客户端密码。
1. 在“密钥用法”处选择 `Signature`。
1. 选择“创建”。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 Azure AD 登录，则需将 Azure AD 定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

要将 Azure AD 定义为声明提供程序，可在策略的扩展文件中将 Azure AD 添加到 ClaimsProvider 元素。

1. 打开 *TrustFrameworkExtensions.xml* 文件。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
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

4. 在 ClaimsProvider 元素下，将 Domain 的值更新为可用于与其他标识提供者进行区分的唯一值。 例如，`Contoso`。 你没有在此域设置的末尾添加 `.com`。
5. 在 ClaimsProvider 元素下，将 DisplayName 的值更新为声明提供程序的友好名称。 目前不会使用此值。

### <a name="update-the-technical-profile"></a>更新技术配置文件

若要从 Azure AD 终结点获取令牌，需要定义 Azure AD B2C 与 Azure AD 通信时应使用的协议。 此操作在 ClaimsProvider 的 TechnicalProfile 元素内完成。

1. 更新 TechnicalProfile 元素的 ID。 此 ID 用于从策略的其他部分引用此技术配置文件，例如 `OIDC-Contoso` 。
1. 更新 DisplayName 的值。 此值会显示在登录屏幕中的登录按钮上。
1. 更新 Description 的值。
1. Azure AD 使用 OpenID Connect 协议，因此请确保 Protocol 的值是 `OpenIdConnect`。
1. 将 METADATA 的值设置为 `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`，其中 `tenant-name` 为你的 Azure AD 租户名称。 例如： `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. 将 **client_id** 设置为应用程序注册中的应用程序 ID。
1. 在 **CryptographicKeys** 下，将 **StorageReferenceId** 的值更新为之前创建的策略密钥的名称。 例如，`B2C_1A_ContosoAppSecret`。

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

现已配置策略，因此 Azure AD B2C 知道如何与 Azure AD 目录通信。 请尝试上传该策略的扩展文件，这只是为了确认它到目前为止不会出现任何问题。

1. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略” 。
1. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件。
1. 单击“上载” 。

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，标识提供者已设置，但在任何注册/登录页面中都不提供该服务。 若要使其可用，请创建现有模板用户旅程的副本，然后对其进行修改，使其也具有 Azure AD 标识提供者：

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
1. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
1. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
1. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
1. 重命名用户旅程的 ID。 例如，`SignUpSignInContoso`。

### <a name="display-the-button"></a>显示按钮

**Claimsexchange** 元素类似于注册/登录页上的标识提供者按钮。 如果为 Azure AD 添加 **ClaimsProviderSelection** 元素，则当用户进入页面时，会显示一个新按钮。

1. 在 `Order="1"` *TrustFrameworkExtensions.xml* 中创建的用户旅程中找到包含的 OrchestrationStep 元素。
1. 在 **ClaimsProviderSelections** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `ContosoExchange`：

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 Azure AD 通信以接收令牌。 可通过链接 Azure AD 声明提供程序的技术配置文件来将按钮链接到操作：

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
1. 添加以下 **ClaimsExchange** 元素，以确保为用于 **TargetClaimsExchangeId** 的 **Id** 使用相同的值：

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    将 **TechnicalProfileReferenceId** 的值更新为之前创建的技术配置文件的 **Id** 。 例如，`OIDC-Contoso`。

1. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>将 Azure AD 标识提供者添加到用户流 

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击要 Azure AD 标识提供者的用户流。
1. 在 " **社交标识提供者**" 下，选择 " **Contoso Azure AD**"。
1. 选择“保存”。 
1. 若要测试策略，请选择 " **运行用户流**"。
1. 对于 " **应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击 "**运行用户流**"

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 SignUpSignInContoso.xml。
1. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如，`SignUpSignInContoso`。
1. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如，`http://contoso.com/B2C_1A_signup_signin_contoso`。
1. 更新 **DefaultUserJourney** 中的 **ReferenceId** 属性的值，使其与之前创建的用户旅程的 ID 匹配。 例如， *SignUpSignInContoso*。
1. 保存更改并上传文件。
1. 在 " **自定义策略**" 下，选择列表中的新策略。
1. 在 " **选择应用程序** " 下拉菜单中，选择之前创建的 Azure AD B2C 应用程序。 例如，testapp1。
1. 复制 " **立即运行" 终结点** 并在专用浏览器窗口中打开它，例如，在 Google Chrome 中的 Incognito 模式或 Microsoft Edge 中的 InPrivate 窗口。 通过在专用浏览器窗口中打开，可以通过不使用任何当前缓存 Azure AD 凭据来测试整个用户旅程。
1. 选择 Azure AD 登录 "按钮（例如 *Contoso Employee*），然后输入 Azure AD 组织租户中某个用户的凭据。 系统会要求你对应用程序进行授权，然后为你的配置文件输入信息。

如果登录过程成功，浏览器将重定向到 `https://jwt.ms` ，后者显示 Azure AD B2C 返回的令牌的内容。

## <a name="next-steps"></a>后续步骤

使用自定义策略时，有时可能需要在部署过程中对策略进行故障排除时提供其他信息。

若要帮助诊断问题，可以暂时将策略置于 "开发人员模式" 中，并 Azure 应用程序 Insights 收集日志。 了解 Azure Active Directory B2C 中的 [操作方法：收集日志](troubleshoot-with-application-insights.md)。

::: zone-end