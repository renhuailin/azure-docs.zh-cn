---
title: TypingDNA 与 Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C 身份验证与 TypingDNA 集成，以便根据用户的键入模式进行身份验证和证明，提供 ID 验证解决方案来强制执行多重身份验证，并确保符合支付服务指令 2 (PSD2) 的 SCA 要求。
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: afaac48d263082a17c7ba58996f4000366de0211
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256542"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的 TypingDNA 配置教程

在本演练中，了解如何将 Azure Active Directory B2C 中的示例在线支付应用与 TypingDNA 应用集成。 通过使用 TypingDNA 应用，Azure AD B2C 客户可以通过击键动态和强大的客户身份验证满足[付款服务指令 2](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) (PSD2) 事务要求。 [在此处](https://www.typingdna.com/)了解有关 TypingDNA 的详细信息。

 Azure AD B2C 使用 TypingDNA 技术捕获用户键入特征，并记录和分析它们以熟悉每一层身份验证。 这增加了一层与身份验证风险有关的保护，并且会评估风险级别。 Azure AD B2C 可以通过调用 Azure AD MFA、强制实施电子邮件验证或适用于你的方案的任何其他自定义逻辑，调用其他机制，以便你更有信心确定用户是他们所声明的身份。

>[!NOTE]
> 此示例策略基于 [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) 初学者包。

## <a name="scenario-description"></a>方案描述

![TypingDNA 体系结构图的屏幕截图](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>注册

1. Azure AD B2C 页面使用 TypingDNA 的 JavaScript 库来记录用户的键入模式。 例如，在初始注册时，将在注册时记录用户名和密码，之后在每次登录时使用此信息进行验证。

2. 当用户提交页面时，TypingDNA 库将计算用户的键入特征。 然后将此信息插入 Azure AD B2C 呈现的隐藏文本字段。 已使用 CSS 隐藏此字段。  

    此[示例包含 HTML 文件](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml)，其中包含 JavaScript 和 CSS 修改，并由 `api.selfasserted.tdnasignin` 和 `api.selfasserted.tdnasignup` 内容定义引用。 请参阅[托管页面内容](./customize-ui-with-html.md#hosting-the-page-content)以托管 HTML 文件。

3. 现在，在用户提交凭据时，Azure AD B2C 在声明包中包含键入模式。 它必须调用你的 API 才能将此数据传递到 TypingDNA REST API 终结点。 此 API 包含在[示例 (typingDNA-API-Interface)](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) 中。
4. 然后，中间层 API 将键入模式数据传递到TypingDNA REST API。 注册时，将调用[检查用户终结点](https://api.typingdna.com/index.html#api-API_Services-GetUser)以确认用户是否不存在，然后调用[保存模式](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern)终结点以保存用户的第一种键入模式。

> [!NOTE]
> 对 TypingDNA REST API 终结点的所有调用都会发送一个 UserId。 此值必须为哈希值。 Azure AD B2C 使用 `HashObjectIdWithEmail` 声明转换通过随机盐和机密对电子邮件进行哈希处理。  

REST API 调用在 `LocalAccountSignUpWithLogonEmail-TDNA` 中使用 `validationTechnicalProfiles` 进行建模：

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>登录

在后续登录时，用户键入模式的计算方式与使用[自定义 HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml) 进行注册时的计算方式相同。 如果键入配置文件位于 Azure AD B2C 声明包中，Azure AD B2C 将调用你的 API 来调用 TypingDNA 的 REST API 终结点。 调用[检查用户](https://api.typingdna.com/index.html#api-API_Services-GetUser)终结点以确认用户是否存在。 接下来，调用[验证模式](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern)终结点以返回 `net_score`。 此 `net_score` 表示键入模式与注册时的原始键入模式的相似程度。

此键入模式在 `SelfAsserted-LocalAccountSignin-Email-TDNA` 中使用 `validationTechnicalProfiles` 进行建模：

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 如果用户获取的键入模式的 `net_score` 较高，则可以使用 TypingDNA [保存键入模式](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern)终结点保存此模式。  

如果你希望 Azure AD B2C 通过你的 API 调用 TypingDNA 保存键入模式终结点，API 必须返回声明 `saveTypingPattern`。

存储库中的示例包含使用以下属性配置的 API (TypingDNA-API-Interface)。

- 训练模式 - 如果用户保存的模式少于两个，则始终提示进行 MFA。

- 如果用户已保存 2 至 5 个模式，且 `net_score` 低于 50，则会提示进行 MFA。

- 如果用户已保存 5 个以上模式，且 `net_score` 低于 65，则会提示进行 MFA。

在你的用例中，应调整这些阈值。

- API 计算 `net_score` 后，应将布尔声明返回到 B2C - `promptMFA`。

- 在前置条件中使用 `promptMFA` 声明来有条件地执行 AZURE AD MFA。

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>加入 TypingDNA

1. [在此处](https://www.typingdna.com/)注册 TypingDNA
2. 登录到 TypingDNA 仪表板，获取“API 密钥”和“API 机密”。 稍后设置 API 接口时需要此信息

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>将 TypingDNA 与 Azure AD B2C 集成

1. 将 [TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) 托管在所选托管提供商处
2. 将 [TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) 解决方案中的所有 `apiKey` 和 `apiSecret` 实例替换为 TypingDNA 仪表板中的凭据
3. 按照[此处](./customize-ui-with-html.md#3-configure-cors)的 CORS 要求，在所选的提供商处托管 HTML 文件
4. 将 `TrustFrameworkExtensions.xml` 文件中的 `api.selfasserted.tdnasignup` 和 `api.selfasserted.tdnasignin` 内容定义的 LoadURI 元素分别替换为托管的 HTML 文件的 URI。
5. 在“Azure 门户”的 Azure AD 边栏选项卡中的“Identity Experience Framework”下，创建 B2C 策略密钥。 使用 `Generate` 选项并将此密钥命名为 `tdnaHashedId`。
6. 替换策略文件中的 TenantId
7. 将所有 TypingDNA REST API 技术配置文件（REST-TDNA-VerifyUser、REST-TDNA-SaveUser、REST-TDNA-CheckUser）中的 ServiceURL 替换为 [TypingDNA-API-Interface API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) 的终结点。
8. 将[策略文件](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy)上传到租户。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 B2C 租户，并选择“Identity Experience Framework”。
2. 选择前面创建的用户流。
3. 选择“运行用户流”

    a. 应用程序 - 选择已注册的应用（例如 JWT）

    b. 回复 URL - 选择重定向 URL

    c. 选择“运行用户流”。
  
4. 完成注册流并创建帐户
5. 注销
6. 完成登录流
7. 生成的 JWT 结果将显示 TypingDNA 结果

## <a name="live-version"></a>实时版本

• 在此测试版本中已禁用 MFA，但你可以查看在身份验证后声明 `promptMFA` 是否提示进行 MFA 的结果。

• 在[此处](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)注册，在[此处](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)登录

## <a name="next-steps"></a>后续步骤

有关更多信息，请参阅以下文章：

- [AAD B2C 中的自定义策略](./custom-policy-overview.md)

- [AAD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)