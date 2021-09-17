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
ms.date: 07/01/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ddf46df1af8dc41e4f50c92eee527ac25e77b08a
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777583"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中设置密码重置流

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="overview"></a>概述

在[注册和登录历程](add-sign-up-and-sign-in-policy.md)中，用户可以使用“忘记密码?”链接重置其自己的密码。 此自助式密码重置流适用于 Azure AD B2C 中使用[电子邮件地址](sign-in-options.md#email-sign-in)或[用户名](sign-in-options.md#username-sign-in)与密码进行登录的本地帐户。

密码重置流涉及以下步骤：

![密码重置流](./media/add-password-reset-policy/password-reset-flow.png)

1\. 在注册和登录页中，用户单击“忘记密码?”链接 。 Azure AD B2C 启动密码重置流。

2\. 用户提供其电子邮件地址并选择“发送验证码” 。 Azure AD B2C 将验证码发送到用户的收件箱。 用户复制电子邮件中的验证码，将其输入到 Azure AD B2C 密码重置页，然后选择“验证代码”。

3\. 然后，用户可以输入新密码。 （验证电子邮件后，用户仍可选择“更改电子邮件”按钮；请参阅下面的[隐藏“更改电子邮件”按钮](#hiding-the-change-email-button)。）

> [!TIP]
> 自助式密码重置流允许用户在忘记密码并希望重置密码时更改其密码。 
> - 如果用户知道自己的密码并想要更改密码，可使用[密码更改流](add-password-change-policy.md)。 
> - 对于想要强制用户重置密码的情况（例如，当用户首次登录时，当用户的密码被管理员重置时，或者在用户使用随机密码迁移到 Azure AD B2C 之后），请使用[强制密码重置](force-password-reset.md)流。

### <a name="hiding-the-change-email-button"></a>隐藏“更改电子邮件”按钮

验证电子邮件后，用户仍可以选择“更改电子邮件”，键入另一个电子邮件地址，然后从头开始重复电子邮件验证过程。 如果你想要隐藏“更改电子邮件”按钮，可以修改 CSS 以在页面上隐藏关联的 HTML 元素。 例如，可将下面的 CSS 条目添加到 selfAsserted.HTML，然后[使用 HTML 模板自定义用户界面](customize-ui-with-html.md)。

```html
<style type="text/css">
   .changeClaims
   {
     visibility: hidden;
   }
</style>
```

请注意，selfasserted.htm 页面中“更改电子邮件”按钮的默认名称为 `changeclaims`。 可以使用浏览器工具（例如 Inspect）通过检查注册页面的源代码找到该按钮名称。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>自助式密码重置（推荐）

新的密码重置体验现在是注册或登录策略的一部分。 当用户选择“忘记密码?”链接时，该用户会立即转到“忘记密码”体验。 应用程序不再需要处理 [AADB2C90118 错误代码](#password-reset-policy-legacy)，并且你不需要单独的密码重置策略。

::: zone pivot="b2c-user-flow"

可为“登录(推荐)”或“注册并登录(推荐)”用户流配置自助式密码重置体验 。 如果没有此类用户流，请创建[登录并注册](add-sign-up-and-sign-in-policy.md)用户流。 

为注册或登录用户流启用自助式密码重置：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
1. 选择“用户流”。
1. 选择想要自定义的注册或登录用户流（“推荐”类型）。
1. 在左侧菜单的 **设置** 下，选择 **属性**。
1. 在“密码配置”下，选择“自助式密码重置” 。
1. 选择“保存”。
1. 在左侧菜单中的“自定义”下，选择“页面布局” 。
1. 在“页面布局版本”中，选择“2.1.3”或更高版本 。
1. 选择“保存”。

::: zone-end

::: zone pivot="b2c-custom-policy"

以下部分介绍如何将自助式服务密码体验添加到自定义策略。 示例基于[自定义策略初学者包](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)中包含的策略文件。 

> [!TIP]
> 可在 [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset) 上找到“使用密码重置注册或登录”策略的完整示例。

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>指示用户选择了“忘记密码?”链接

要向策略指示用户选择了“忘记密码?”链接，请定义一个布尔声明。 此声明可用于将用户旅程定向到“忘记密码”技术配置文件。 也可以向令牌发出此声明，使应用程序知道用户是通过“忘记密码”流登录的。

在[声明架构](claimsschema.md)中宣布声明。 打开策略的扩展文件， 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。

1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 [ClaimsSchema](claimsschema.md) 元素。 如果该元素不存在，请添加该元素。
1. 将以下声明添加到 ClaimsSchema 元素。 

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

需要[页面布局版本](contentdefinitions.md#migrating-to-page-layout) `2.1.2` 才能在注册或登录历程中启用自助式密码重置流。

1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 [ContentDefinitions](contentdefinitions.md) 元素。 如果该元素不存在，请添加该元素。
1. 如下所示，修改 ID 为“api.signuporsignin”的 ContentDefinition 元素内的 DataURI 元素  。

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

若要启动 `isForgotPassword` 声明，请使用声明转换技术配置文件。 此技术配置文件将在之后引用。 调用时，它会将 `isForgotPassword` 声明的值设置为 `true`。 查找 `ClaimsProviders` 元素。 如果该元素不存在，请添加该元素。 然后添加以下声明提供程序：  

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

`SelfAsserted-LocalAccountSignin-Email` 技术配置文件 `setting.forgotPasswordLinkOverride` 定义了用户旅程中要执行的密码重置声明交换。 

### <a name="add-the-password-reset-sub-journey"></a>添加密码重置子历程

现在，历程包含用户登录、注册和执行密码重置的功能。 为了更好地组织用户旅程，可以使用[子历程](subjourneys.md)来处理密码重置流。

子历程将从用户旅程中进行调用，并将执行特定的步骤以向用户提供密码重置体验。 使用 `Call` 类型的子历程，以便在子历程完成后，控制权将返回给启动了子历程的业务流程步骤。

查找 `SubJourneys` 元素。 如果该元素不存在，请将其添加到 `User Journeys` 元素之后。 然后添加以下子历程：

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

需要将“忘记密码?”链接连接到“忘记密码”子历程。 为此，请在 CombinedSignInAndSignUp 步骤的 ClaimsProviderSelection 元素中引用“忘记密码”子历程 ID 。

如果没有带有 CombinedSignInAndSignUp 步骤的自定义用户旅程，请使用以下过程复制现有的注册或登录用户旅程。 否则，请继续下一部分。

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
2. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
3. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
4. 通过粘贴在步骤 2 中复制的 UserJourney 元素的全部内容，创建 UserJourneys 元素的子元素 。
5. 对用户旅程的 ID 进行重命名。 例如 `Id="CustomSignUpSignIn"`。

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>将“忘记密码”链接连接到“忘记密码”子历程 

在用户旅程中，可以将“忘记密码”子历程表示为 ClaimsProviderSelection。 添加此元素可将“忘记密码?”链接连接到“忘记密码”子历程。

1. 在用户旅程中，查找包含 `Type="CombinedSignInAndSignUp"` 或 `Type="ClaimsProviderSelection"` 的业务流程步骤元素。 这通常是第一个业务流程步骤。 ClaimsProviderSelections 元素包含用户可以用来登录的标识提供者列表。 添加以下行：
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. 在下一个业务流程步骤中，添加 ClaimsExchange 元素。 添加以下行：

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```
    
1. 在当前步骤和下一步骤之间添加以下业务流程步骤。 添加的新业务流程步骤会检查 `isForgotPassword` 声明是否存在。 如果该声明存在，它将调用[密码重置子历程](#add-the-password-reset-sub-journey)。 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```
    
1. 添加新的业务流程步骤以后，请按顺序对这些步骤重新编号，不要跳过任何整数（1 到 N）。

### <a name="set-the-user-journey-to-be-executed"></a>设置要执行的用户旅程

修改或创建用户旅程后，请在“信赖方”部分指定 Azure AD B2C 将为此自定义策略执行的历程。 在 [RelyingParty](relyingparty.md) 元素中，找到 DefaultUserJourney 元素。 更新 DefaultUserJourney ReferenceId，使其与添加了 ClaimsProviderSelections 的用户旅程的 ID 匹配 。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>指示应用的“忘记密码”流

应用程序可能需要检测用户是否通过“忘记密码”用户流登录。 isForgotPassword 声明包含一个布尔值来指示这一行为，它可以在发送给应用程序的令牌中发出。 如有必要，向“依赖方”部分的输出声明中添加 `isForgotPassword`。 应用程序可以检查 `isForgotPassword` 声明以确定用户是否重置了其密码。

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```


### <a name="upload-the-custom-policy"></a>上传自定义策略

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在“策略”下，选择“Identity Experience Framework”。 
1. 选择“上传自定义策略”，然后按以下顺序上传已更改的两个策略文件：
   1. 扩展策略，例如 `TrustFrameworkExtensions.xml`。
   2. 信赖方策略，例如 `SignUpSignIn.xml`。

::: zone-end

### <a name="test-the-password-reset-flow"></a>测试密码重置流

1. 选择想要测试的注册或登录用户流（“推荐”类型）。
1. 选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“运行用户流”。
1. 在注册或登录页上，选择 **忘记密码？** 。
1. 验证之前创建的帐户的电子邮件地址，然后选择 **继续**。
1. 现在可以更改用户的密码。 更改密码，然后选择“继续”。 令牌将返回到 `https://jwt.ms` 并显示出来。
1. 检查返回令牌的 `isForgotPassword` 声明值。 如果该值存在且设置为 true，则指示用户已重置密码。

## <a name="password-reset-policy-legacy"></a>密码重置策略（旧版）

如果未启用[自助式密码重置](#self-service-password-reset-recommended)体验，单击此链接不会自动触发密码重置用户流。 而是将错误代码 `AADB2C90118` 返回给应用程序。 应用程序需要通过重新初始化身份验证库来验证 Azure AD B2C 密码重置用户流，从而处理此错误代码。

在下图中：

1. 用户从应用程序中单击“登录”。 该应用发起授权请求，并将用户带到 Azure AD B2C 完成登录。 授权请求指定注册或登录策略名称，如 B2C_1_signup_signin。
1. 用户选择“忘记密码?”链接。 Azure AD B2C 向应用程序返回 AADB2C90118 错误代码。
1. 应用程序处理错误代码，并发起新的授权请求。 授权请求指定密码重置策略名称，如 B2C_1_pwd_reset。

![旧版密码重设用户流](./media/add-password-reset-policy/password-reset-flow-legacy.png)

有关示例，请查看演示用户流链接的[简单 ASP.NET 示例](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)。

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>创建密码重置用户流

若要允许应用程序用户重置其密码，请创建密码重置用户流。

1. 在 Azure AD B2C 租户概述菜单中，选择“用户流”，然后选择“新建用户流”。
1. 在“创建用户流”页面上，选择“密码重置”用户流 。 
1. 在“选择版本”下，选择“建议”，然后选择“创建”  。
1. 输入该用户流的 **名称**。 例如 *passwordreset1*。
1. 对于“标识提供者”，请启用“使用用户名重置密码”或“使用电子邮件地址重置密码”  。
1. 在“多重身份验证”下，如果你希望要求用户使用另一种身份验证方法来验证其身份，请选择方法类型，以及何时要强制执行多重身份验证 (MFA)。 [了解详细信息](multi-factor-authentication.md)。
1. 在“条件访问”下，如果已为 Azure AD B2C 租户配置条件访问策略，并且想要为此用户流启用这些策略，请选择“强制实施条件访问策略”复选框 。 无需指定策略名称。 [了解详细信息](conditional-access-user-flow.md?pivots=b2c-user-flow)。
1. 1. 在“应用程序声明”下，选择“显示更多”，然后选择你希望在发送回应用程序的授权令牌中返回的声明 。 例如，选择“用户的对象 ID”。
1. 选择“确定”。
1. 选择“创建”以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择已创建的用户流以打开其“概述”页，然后选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击“运行用户流”，验证之前创建的帐户的电子邮件地址，然后选择“继续” 。
1. 现在可以更改用户的密码。 更改密码，然后选择“继续”。 令牌将返回到 `https://jwt.ms` 并显示出来。

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>创建密码重置策略

自定义策略是上传到 Azure AD B2C 租户的一组 XML 文件，用于定义用户旅程。 我们提供了初学者包，其中有多个预构建策略，包括注册和登录、密码重置以及配置文件编辑策略。 有关详细信息，请参阅 [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)。

::: zone-end

## <a name="next-steps"></a>后续步骤

设置[强制密码重置](force-password-reset.md)。
