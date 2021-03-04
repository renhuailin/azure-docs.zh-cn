---
title: 在 Azure AD B2C 中设置密码重置流
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中设置密码重置流。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8e4926fb228837c3ec0573af8ee5b15da5fd7431
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033873"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中设置密码重置流

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-reset-flow"></a>密码重置流

[注册和登录旅程](add-sign-up-and-sign-in-policy.md)允许用户使用 "**忘记了密码？"** 链接重置他们自己的密码。 密码重置流涉及以下步骤：

1. 在注册和登录页上，用户单击 " **忘记了密码？** " 链接。 Azure AD B2C 启动密码重置流。 
2. 用户通过定时一次性密码提供并验证其电子邮件地址。
3. 然后，用户可以输入新密码。

![密码重置流](./media/add-password-reset-policy/password-reset-flow.png)

密码重置流适用于 Azure AD B2C 中使用 [电子邮件地址](identity-provider-local.md#email-sign-in) 或 [用户名](identity-provider-local.md#username-sign-in) 的本地帐户，用于登录。

将用户迁移到使用随机密码 Azure AD B2C 后，常见的做法是让用户验证其电子邮件地址，并在首次登录时重置密码。 在管理员更改密码后，还经常强制用户重置其密码;若要启用此功能，请参阅 [强制密码重置](force-password-reset.md) 。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>自助密码重置 (建议) 

新的密码重置体验现在是注册或登录策略的一部分。 当用户选择 " **忘记了密码？** " 链接时，会立即将其发送到 "忘记的密码" 体验。 你的应用程序不再需要处理 [AADB2C90118 错误代码](#password-reset-policy-legacy)，并且你无需使用单独的密码重置策略。

::: zone pivot="b2c-user-flow"

自助服务密码重置体验可以配置为 **(建议的登录)** 或 **注册并登录 (推荐的)** 用户流。 如果没有此类用户流，请创建 [登录并注册](add-sign-up-and-sign-in-policy.md) 用户流。 

若要为注册或登录用户流启用自助密码重置，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
1. 选择“用户流”。
1. 选择要自定义的 **建议**) 类型的注册或登录用户流 (。
1. 在左侧菜单的 " **设置** " 下，选择 " **属性**"。
1. 在 " **密码复杂性**" 下，选择 " **自助密码重置**"。
1. 选择“保存”。
1. 在左侧菜单中的 " **自定义** " 下，选择 " **页面布局**"。
1. 在 **页面布局版本** 中，选择 " **2.1.2** " 或更高版本。
1. 选择“保存”。

::: zone-end

::: zone pivot="b2c-custom-policy"

以下部分介绍如何将自助服务密码体验添加到自定义策略。 该示例基于 [自定义策略初学者包](./custom-policy-get-started.md)中包含的策略文件。 

> [!TIP]
> 可在 [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset)上找到 "使用密码重置注册或登录" 策略的完整示例。

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>表示用户选择了 "忘记密码了？"连接

若要向策略表明用户已选择 " **忘记了密码？** " 链接，请定义一个布尔声明。 此声明可用于将用户旅程定向到忘记的密码技术配置文件。 也可以将此声明颁发给令牌，以便应用程序知道用户已通过忘记密码的密码进行登录。

在声明 [架构](claimsschema.md)中声明声明。 打开策略的扩展文件， 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。

1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 [ClaimsSchema](claimsschema.md) 元素。 如果该元素不存在，请添加该元素。
1. 将以下声明添加到 **ClaimsSchema** 元素中。 

```XML
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="isForgotPassword">
      <DisplayName>isForgotPassword</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="upgrade-the-page-layout-version"></a>升级页面布局版本

[页面布局版本](contentdefinitions.md#migrating-to-page-layout) `2.1.2` 在注册或登录旅程内启用自助服务密码重置流需要。

1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 " [ContentDefinitions](contentdefinitions.md) " 元素。 如果该元素不存在，请添加该元素。
1. 修改 Id 为 **signuporsignin.xml** 的 **ContentDefinition** 元素中的 **DataURI** 元素，如下所示。

```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks> -->
```

若要启动 `isForgotPassword` 声明，请使用声明转换技术配置文件。 稍后将引用此技术配置文件。 调用时，它会将声明的值设置 `isForgotPassword` 为 `true` 。 查找 `ClaimsProviders` 元素。 如果该元素不存在，请添加该元素。 然后添加以下声明提供程序：  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

`SelfAsserted-LocalAccountSignin-Email`技术配置文件 `setting.forgotPasswordLinkOverride` definers 在用户旅程中执行密码重置声明交换。 

### <a name="add-the-password-reset-sub-journey"></a>添加密码重置 sub 旅程

你的旅程现在包含用户登录、注册和执行密码重置功能的功能。 为了更好地组织用户旅程，可以使用 [sub 旅程](subjourneys.md) 来处理密码重置流。

将从用户旅程中调用 sub 旅程，并将执行特定的步骤，为用户提供密码重置体验。 使用 `Call` 类型 sub 旅程，以便在 sub 旅程完成后，控制权将返回给启动了 sub 旅程的业务流程步骤。

查找 `SubJourneys` 元素。 如果该元素不存在，请将其添加到 `User Journeys` 元素之后。 然后添加以下 sub 旅程：

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>准备用户旅程

你需要连接 " **忘记密码** 了" 的链接。 为此，请在 **CombinedSignInAndSignUp** 步骤的 **claimsexchange** 元素中引用忘记的密码 sub 旅程 Id。

如果没有 **CombinedSignInAndSignUp** 的自定义用户旅程，请使用以下过程复制现有的注册或登录用户旅程。 否则，请继续下一节。

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
2. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
3. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
4. 通过粘贴在步骤2中复制的 **UserJourney** 元素的全部内容，创建 **UserJourneys** 元素的子元素。
5. 重命名用户旅程的 Id。 例如 `Id="CustomSignUpSignIn"`。

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>将忘记的密码链接连接到忘记的密码 sub 旅程 

在用户旅程中，你可以将忘记的密码 sub 旅程表示为 **claimsexchange**。 添加此元素会连接 " **忘记密码** 了" 的链接。

1. 在用户旅程中，查找包含或的业务流程步骤 `Type="CombinedSignInAndSignUp"` 元素 `Type="ClaimsProviderSelection"` 。 它通常是第一个业务流程步骤。 **ClaimsProviderSelections** 元素包含用户可以用来登录的标识提供程序的列表。 添加以下行：
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. 在下一个业务流程步骤中，添加一个 **ClaimsExchange** 元素。 添加以下行：

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```

### <a name="set-the-user-journey-to-be-executed"></a>设置要执行的用户旅程

修改或创建用户旅程后，请在 " **信赖方** " 部分指定将为此自定义策略执行 Azure AD B2C 的旅程。 在 [RelyingParty](relyingparty.md) 元素中，找到 **DefaultUserJourney** 元素。 更新  **DefaultUserJourney ReferenceId** ，使其与添加 **ClaimsProviderSelections** 的用户旅程的 ID 匹配。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>指示已忘记的密码流到你的应用

您的应用程序可能需要检测用户是否已通过忘记密码的用户流登录。 **IsForgotPassword** 声明包含一个布尔值，指示此值，可在发送到应用程序的令牌中发出。 如有必要，请将添加 `isForgotPassword` 到 " **信赖方** " 部分中的 "输出声明"。 应用程序可以检查 `isForgotPassword` 声明以确定用户是否重置了其密码。

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```


### <a name="upload-the-custom-policy"></a>上传自定义策略

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在“策略”下，选择“Identity Experience Framework”。 
1. 选择 " **上载自定义策略**"，然后上传按以下顺序更改的两个策略文件：
   1. 扩展策略，例如 `TrustFrameworkExtensions.xml` 。
   2. 例如，信赖方策略 `SignUpSignIn.xml` 。

::: zone-end

### <a name="test-the-password-reset-flow"></a>测试密码重置流

1. 选择要测试的建议) 类型的注册或登录用户流 (。
1. 选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“运行用户流”。
1. 从注册或登录页上，选择 **"忘记了密码？"**。
1. 验证你之前创建的帐户的电子邮件地址，然后选择 " **继续**"。
1. 现在可以更改用户的密码。 更改密码，然后选择“继续”。 令牌将返回到 `https://jwt.ms` 并显示出来。
1. 检查返回标记的 `isForgotPassword` 声明值。 如果存在并设置为 true，则表示用户已重置密码。

## <a name="password-reset-policy-legacy"></a>旧)  (密码重置策略

如果未启用 [自助密码重置](#self-service-password-reset-recommended) 体验，单击此链接不会自动触发密码重置用户流。 而是将错误代码 `AADB2C90118` 返回给应用程序。 应用程序需要通过重新初始化身份验证库来处理此错误代码，以验证 Azure AD B2C 密码重置用户流。

在下图中：

1. 在应用程序中，用户单击 "登录"。 该应用程序将启动一个授权请求，并将用户 Azure AD B2C 以完成登录。 授权请求指定注册或登录策略名称，如 **B2C_1_signup_signin**。
1. 用户选择 " **忘记了密码？** " 链接。 Azure AD B2C 将向应用程序返回 AADB2C90118 错误代码。
1. 应用程序处理错误代码，并启动新的授权请求。 授权请求指定密码重置策略名称，如 **B2C_1_pwd_reset**。

![密码重置流](./media/add-password-reset-policy/password-reset-flow-legacy.png)

若要查看示例，请查看一个简单的 [ASP.NET 示例](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)，该示例演示了用户流的链接。

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>创建密码重置用户流

若要让应用程序的用户重置其密码，你需要创建一个密码重置用户流。

1. 在 Azure AD B2C 租户概述菜单中，选择“用户流”，然后选择“新建用户流”。
1. 在“创建用户流”页面上，选择“密码重置”用户流 。 
1. 在“选择版本”下，选择“建议”，然后选择“创建”  。
1. 输入该用户流的 **名称**。 例如 *passwordreset1*。
1. 对于“标识提供者”，请启用“使用电子邮件地址重置密码”。
1. 在 " **应用程序声明**" 下，选择 " **显示更多** "，然后选择要返回到应用程序的授权令牌中的声明。 例如，选择“用户的对象 ID”。
1. 选择“确定”。
1. 选择“创建”以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择您创建的用户流以打开其 "概述" 页，然后选择 " **运行用户流**"。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击 " **运行用户流**"，验证你之前创建的帐户的电子邮件地址，然后选择 " **继续**"。
1. 你现在可以更改用户的密码。 更改密码，然后选择“继续”。 令牌将返回到 `https://jwt.ms` 并显示出来。

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>创建密码重置策略

自定义策略是上传到 Azure AD B2C 租户的一组 XML 文件，用于定义用户旅程。 我们提供了包含若干预先构建的策略的初学者包，其中包括：注册和登录、密码重置和配置文件编辑策略。 有关详细信息，请参阅 [Azure AD B2C 中的自定义策略入门](custom-policy-get-started.md)。

::: zone-end

## <a name="next-steps"></a>后续步骤

设置 [强制密码重置](force-password-reset.md)。


