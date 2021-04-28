---
title: REST API 声明交换作为验证
titleSuffix: Azure AD B2C
description: 演练如何创建与 RESTful 服务交互的 Azure AD B2C 用户旅程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 89542a0a30bacf5cb45755fb578b22ce5c34ba4e
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108070706"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>演练：在 Azure AD B2C 用户旅程中集成 REST API 声明交换来验证用户输入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

支撑 Azure Active Directory B2C (Azure AD B2C) 的 Identity Experience Framework (IEF) 可让标识开发人员在用户旅程中将某种交互与 RESTful API 集成。  完成本演练后，就可以创建与 [RESTful 服务](api-connectors-overview.md)交互的 Azure AD B2C 用户旅程以验证用户输入。

在这种情况下，我们将添加用户在 Azure AD B2C 注册页面中输入会员号的功能。 通过将此数据发送到 REST API，我们将验证电子邮件和会员号的组合是否映射到促销代码。 如果 REST API 找到该用户的促销代码，它将被返回到 Azure AD B2C。 最后，促销代码将插入到令牌声明中，以供应用程序使用。

也可以将交互设计为业务流程步骤。 当 REST API 不会验证屏幕上的数据并始终返回声明时，这非常合适。 有关详细信息，请参阅[演练：在 Azure AD B2C 用户旅程中以业务流程步骤的形式集成 REST API 声明交换](custom-policy-rest-api-claims-exchange.md)。

## <a name="prerequisites"></a>先决条件

- 完成[自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)中的步骤。 应准备好一个有效的自定义策略，以便使用本地帐户注册和登录。
- 了解如何[在 Azure AD B2C 自定义策略中集成 REST API 声明交换](api-connectors-overview.md)。

## <a name="prepare-a-rest-api-endpoint"></a>准备 REST API 终结点

在本演练中，应具有 REST API 用于验证是否已使用会员 ID 在后端系统中注册电子邮件地址。 如果已注册，则 REST API 应该返回注册促销代码，客户可以使用该代码在应用程序中购买商品。 否则，REST API 应返回 HTTP 409 错误消息：“会员 ID '{loyalty ID}' 与 '{email}' 电子邮件地址未关联。”。

以下 JSON 代码演示 Azure AD B2C 将发送到 REST API 终结点的数据。 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

REST API 验证数据后，就必须返回 HTTP 200 (Ok)，其中包含以下 JSON 数据：

```json
{
    "promoCode": "24534"
}
```

如果验证失败，则 REST API 必须返回带有 `userMessage` JSON 元素的 HTTP 409（冲突）。 IEF 需要 REST API 返回的 `userMessage` 声明。 如果验证失败，则此声明将以字符串的形式呈现给用户。

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

REST API 终结点的设置不在本文的讨论范围内。 我们已创建 [Azure Functions](../azure-functions/functions-reference.md) 示例。 可以在 [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function) 中访问完整的 Azure 函数代码。

## <a name="define-claims"></a>定义声明

声明可在 Azure AD B2C 策略执行过程中提供数据的临时存储。 可以在[声明架构](claimsschema.md)部分中进行声明。 

1. 打开策略的扩展文件， 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。
1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 [ClaimsSchema](claimsschema.md) 元素。 如果该元素不存在，请添加该元素。
1. 将以下声明添加到 ClaimsSchema 元素。  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>添加 RESTful API 技术配置文件 

[RESTful 技术配置文件](restful-technical-profile.md)支持与你自己的 RESTful 服务交互。 Azure AD B2C 在 `InputClaims` 集合中将数据发送到 RESTful 服务，在 `OutputClaims` 集合中接收返回的数据。 查找 ClaimsProviders 元素，并按如下所示添加新的声明提供程序：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

在本示例中，`userLanguage` 将在 JSON 有效负载中以 `lang` 的形式发送到 REST 服务。 `userLanguage` 声明的值包含当前用户语言 ID。 有关详细信息，请参阅[声明解析程序](claim-resolver-overview.md)。

### <a name="configure-the-restful-api-technical-profile"></a>配置 RESTful API 技术配置文件 

部署 REST API 后，设置 `REST-ValidateProfile` 技术配置文件的元数据以反映你自己的 REST API，包括：

- **ServiceUrl**。 设置 REST API 终结点的 URL。
- **SendClaimsIn**。 指定如何将输入声明发送到 RESTful 声明提供程序。
- **AuthenticationType**。 设置 RESTful 声明提供程序所执行的身份验证类型。 
- **AllowInsecureAuthInProduction**。 在生产环境中，请确保将此元数据设置为 `true`
    
有关更多配置，请参阅 [RESTful 技术配置文件元数据](restful-technical-profile.md#metadata)。

`AuthenticationType` 和 `AllowInsecureAuthInProduction` 上的注释指定了在移到生产环境时应进行的更改。 若要了解如何保护用于生产的 RESTful API，请参阅[保护 RESTful API](secure-rest-api.md)。

## <a name="validate-the-user-input"></a>验证用户输入

若要在注册期间获取用户的会员号，必须允许用户在屏幕上输入此数据。 通过将 loyaltyId 输出声明添加到现有的注册技术配置文件部分的 `OutputClaims` 元素，将该输出声明添加到注册页面。 指定输出声明的整个列表，以控制声明在屏幕上的显示顺序。  

将验证技术配置文件引用添加到调用 `REST-ValidateProfile` 的注册技术配置文件。 新验证技术配置文件将添加到基本策略中定义的 `<ValidationTechnicalProfiles>` 集合的顶部。 此行为意味着，只有在成功验证后，Azure AD B2C 才会继续在目录中创建帐户。   

1. 找到 **ClaimsProviders** 元素。 如下所示添加新的声明提供程序：

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>在令牌中包括声明 

若要将促销代码声明返回到信赖方应用程序，请将输出声明添加到 <em>`SocialAndLocalAccounts/` `SignUpOrSignIn.xml`</em> 文件。 输出声明将允许在用户旅程成功后向令牌添加声明，并将被发送到应用程序。 修改信赖方部分中的技术配置文件元素，以将 `promoCode` 添加为输出声明。
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用包含 Azure AD 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册” 。
1. 选择“标识体验框架”。
1. 选择“上传自定义策略”，然后上传已更改的策略文件：*TrustFrameworkExtensions.xml* 和 *SignUpOrSignin.xml*。 
1. 选择已上传的注册或登录策略，并单击“立即运行”按钮。
1. 现在，应该可以使用电子邮件地址注册。
1. 单击“立即注册”链接。
1. 在“你的会员 ID”中，键入 1234，然后单击“继续” 。 此时，应会收到验证错误消息。
1. 更改为其他值，然后单击“继续”。
1. 发送回应用程序的令牌包含 `promoCode` 声明。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>后续步骤

若要了解如何保护 API，请参阅以下文章：

- [演练：在 Azure AD B2C 用户旅程中以业务流程步骤的形式集成 REST API 声明交换](custom-policy-rest-api-claims-exchange.md)
- [保护 RESTful API](secure-rest-api.md)
- [参考：RESTful 技术配置文件](restful-technical-profile.md)