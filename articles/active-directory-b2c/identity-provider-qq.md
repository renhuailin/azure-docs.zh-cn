---
title: 使用 Azure Active Directory B2C 设置通过 QQ 帐户注册与登录
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 QQ 帐户注册与登录的功能。
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
ms.openlocfilehash: b497176deff896e785387f4b64a8e66ff4d6d58e
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654313"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 QQ 帐户注册与登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-qq-application"></a>创建 QQ 应用程序

要将 QQ 帐户用作 Azure Active Directory B2C (Azure AD B2C) 中的标识提供者，需要在表示它的租户中创建一个应用程序。 如果还没有 QQ 帐户，可以在注册 [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) 。

### <a name="register-for-the-qq-developer-program"></a>注册 QQ 开发人员计划

1. 使用 QQ 帐户凭据登录 [QQ 开发人员门户](http://open.qq.com)。
1. 登录后，请继续进行 [https://open.qq.com/reg](https://open.qq.com/reg) 注册，作为开发人员。
1. 选择“个人”(individual developer)。
1. 输入所需的信息，并选择“下一步” (next step)。
1. 完成电子邮件验证过程。 注册为开发人员后，需要等待几天，以获得批准。

### <a name="register-a-qq-application"></a>注册 QQ 应用程序

1. 转到 [https://connect.qq.com/index.html](https://connect.qq.com/index.html)。
1. 单击“应用管理”(app management)。
1. 选择“创建应用” (create app)，然后输入所需的信息。
1. 在“授权回调域”(callback URL) 中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp`。 例如，如果 `tenant_name` 是 contoso，请将 URL 设置为 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`。
1. 选择“创建应用”(create app)。
1. 在确认页上选择“应用管理”(app management) 返回应用管理页。
1. 选择刚刚创建的应用旁边的“查看”(view)。
1. 选择“修改”(edit)。
1. 复制“应用 ID”和“应用密钥”。 将标识提供者添加到租户时需要这两个值。

::: zone pivot="b2c-user-flow"

## <a name="configure-qq-as-an-identity-provider"></a>将 QQ 配置为标识提供者

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择“标识提供者”，然后选择“QQ (预览)”。
1. 输入“名称”。 例如，QQ。
1. 对于 **客户端 ID**，输入你之前创建的 QQ 应用程序的应用 ID。
1. 对于 **客户端密码**，输入你记录的应用密钥。
1. 选择“保存”。 

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的客户端机密。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
4. 在“概述”页上选择“标识体验框架”。
5. 选择“策略密钥”，然后选择“添加”。
6. 对于“选项”，请选择 `Manual`。
7. 输入策略密钥的 **名称**。 例如，`QQSecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
8. 在“机密”中，输入前面记录的应用程序机密。
9. 在“密钥用法”处选择 `Signature`。
10. 单击“创建”。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 QQ 帐户登录，则需要将该帐户定义为声明提供程序，Azure AD B2C 可通过终结点进行通信。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过将 QQ 帐户添加到策略扩展文件中的 **ClaimsProviders** 元素来将其定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>qq.com</Domain>
      <DisplayName>QQ (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="QQ-OAUTH">
          <DisplayName>QQ</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">qq</Item>
            <Item Key="authorization_endpoint">https://graph.qq.com/oauth2.0/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://graph.qq.com/oauth2.0/token</Item>
            <Item Key="ClaimsEndpoint">https://graph.qq.com/oauth2.0/me</Item>
            <Item Key="scope">get_user_info</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="ClaimsResponseFormat">JsonP</Item>
            <Item Key="ResponseErrorCodeParamName">error</Item>
            <Item Key="external_user_identity_claim_id">openid</Item>
            <Item Key="client_id">Your QQ application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_QQSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="UserId" PartnerClaimType="openid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="qq.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

4. 将 **client_id** 设置为应用程序注册中的应用程序 ID。
5. 保存文件。

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

至此，已配置策略，以便 Azure AD B2C 知道如何与 QQ 帐户通信。 请尝试上传该策略的扩展文件，这只是为了确认它到目前为止不会出现任何问题。

1. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略” 。
2. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件。
3. 单击“上载” 。

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，标识提供者已设置，但不会出现在任何注册/登录屏幕中。 若要使其可用，请创建现有模板用户旅程的副本，然后对其进行修改，使其也包含 QQ 标识提供者。

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
2. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
3. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
4. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
5. 重命名用户旅程的 ID。 例如，`SignUpSignInQQ`。

### <a name="display-the-button"></a>显示按钮

**ClaimsProviderSelection** 元素类似于注册/登录屏幕上的标识提供者按钮。 如果为 QQ 帐户添加 **claimsexchange** 元素，则当用户进入页面时，会显示一个新按钮。

1. 在创建的用户旅程中找到包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelects** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `QQExchange`：

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="QQExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在这种情况下，操作是为了使 Azure AD B2C 与 QQ 帐户通信以接收令牌。

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
2. 添加以下 **ClaimsExchange** 元素，以确保为用于 **TargetClaimsExchangeId** 的 ID 使用相同的值：

    ```xml
    <ClaimsExchange Id="QQExchange" TechnicalProfileReferenceId="QQ-OAuth" />
    ```

    将 **TechnicalProfileReferenceId** 的值更新为之前创建的技术配置文件的 ID。 例如，`QQ-OAuth`。

3. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-qq-identity-provider-to-a-user-flow"></a>将 QQ 标识提供者添加到用户流 

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击要用于 QQ 标识提供者的用户流。
1. 在 **社交标识提供者** 下，选择 " **QQ**"。
1. 选择“保存”。 
1. 若要测试策略，请选择 " **运行用户流**"。
1. 对于 " **应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击 "**运行用户流**"

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 *SignUpSignInQQ.xml*。
1. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如，`SignUpSignInQQ`。
1. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如 `http://contoso.com/B2C_1A_signup_signin_QQ`
1. 更新 **DefaultUserJourney** 中的 **ReferenceId** 属性的值，使其与你 (SignUpSignQQ) 创建的新用户旅程的 ID 匹配。
1. 保存更改，并上传文件。
1. 在“自定义策略”下，选择“B2C_1A_signup_signin” 。
1. 对于 " **选择应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择 " **立即运行** "，并选择 "qq" 以便用 QQ 登录并测试自定义策略。

::: zone-end
