---
title: UserInfo 终结点 | Microsoft Docs
description: 在 Azure Active Directory B2C 的自定义策略中定义 UserInfo 终结点。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 209d2aafbecc81a3c06866569f66edec6e3f43a4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128566570"
---
# <a name="userinfo-endpoint"></a>UserInfo 终结点

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

UserInfo 终结点是 [OpenID Connect 标准](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC) 规范的一部分，用于返回有关已通过身份验证的用户的声明。 使用 [EndPoint](relyingparty.md#endpoints) 元素在信赖方策略中定义 UserInfo 终结点。  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>UserInfo 终结点概述

用户信息 UserJourney 指定：

- **授权**：使用持有者令牌保护 UserInfo 终结点。 颁发的访问令牌显示在 UserInfo 终结点的 Authorization 标头中。 策略指定用于验证传入令牌并提取声明（例如用户的 objectId）的技术配置文件。 用户的 objectId 用于检索在 UserInfo 终结点旅程的响应中返回的声明。 
- **业务流程步骤**： 
  - 业务流程步骤用于收集有关用户的信息。 用户旅程会根据传入访问令牌中的声明调用 [Azure Active Directory 技术配置文件](active-directory-technical-profile.md)来检索有关用户的数据，例如，按 objectId 读取用户。 
  - **可选的业务流程步骤** - 可以添加更多的业务流程步骤（例如 REST API 技术配置文件）来检索有关用户的详细信息。 
  - **UserInfo 颁发者** - 指定 UserInfo 终结点返回的声明列表。

## <a name="create-a-userinfo-endpoint"></a>创建 UserInfo 终结点

### <a name="1-add-the-token-issuer-technical-profile"></a>1.添加 Token Issuer 技术配置文件

1. 打开 *TrustFrameworkExtensions.xml* 文件。
1. 如果尚不存在该文件，则添加 ClaimsProvider 元素及其子元素作为 BuildingBlocks 元素下的第一个元素。
1. 添加以下声明提供程序：

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Token Issuer</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="UserInfoIssuer">
            <DisplayName>JSON Issuer</DisplayName>
            <Protocol Name="None" />
            <OutputTokenFormat>JSON</OutputTokenFormat>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId"/>
              <InputClaim ClaimTypeReferenceId="givenName"/>
              <InputClaim ClaimTypeReferenceId="surname"/>
              <InputClaim ClaimTypeReferenceId="displayName"/>
              <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
            </InputClaims>
          </TechnicalProfile>
          <TechnicalProfile Id="UserInfoAuthorization">
            <DisplayName>UserInfo authorization</DisplayName>
            <Protocol Name="None" />
            <InputTokenFormat>JWT</InputTokenFormat>
            <Metadata>
              <!-- Update the Issuer and Audience below -->
              <!-- Audience is optional, Issuer is required-->
              <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
              <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
              <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
              <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email"/>
              <!-- Optional claims to read from the access token. -->
              <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ``` 

1. **UserInfoIssuer** 技术配置文件中的 InputClaims 节指定要返回的属性。 在用户旅程结束时，会调用 UserInfoIssuer 技术配置文件。 
1. **UserInfoAuthorization** 技术配置文件会验证签名、颁发者名称和令牌受众，并从入站令牌中提取声明。 请更改以下元数据，使之反映你的环境：
    1. **issuer** - 此值必须与访问令牌声明中的 `iss` 声明相同。 Azure AD B2C 颁发的令牌使用 `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/` 格式的颁发者。 详细了解[令牌自定义](configure-tokens.md)。
    1. **IdTokenAudience** - 必须与访问令牌声明中的 `aud` 声明相同。 在 Azure AD B2C 中，`aud` 声明是信赖方应用程序的 ID。 此值为集合，支持多个使用逗号分隔符的值。

        在以下访问令牌中，`iss` 声明值为 `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/`。 `aud` 声明值为 `22222222-2222-2222-2222-222222222222`。

        ```json
        {
          "exp": 1605549468,
          "nbf": 1605545868,
          "ver": "1.0",
          "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
          "sub": "44444444-4444-4444-4444-444444444444",
          "aud": "22222222-2222-2222-2222-222222222222",
          "acr": "b2c_1a_signup_signin",
          "nonce": "defaultNonce",
          "iat": 1605545868,
          "auth_time": 1605545868,
          "name": "John Smith",
          "given_name": "John",
          "family_name": "Smith",
          "tid": "11111111-1111-1111-1111-111111111111"
        }
        ```
    
1.  **UserInfoAuthorization** 技术配置文件的 OutputClaims 元素指定要从访问令牌中读取的属性。 ClaimTypeReferenceId 是对声明类型的引用。 可选的 PartnerClaimType 是在访问令牌中定义的声明的名称。



### <a name="2-add-the-userjourney-element"></a>2.添加 UserJourney 元素 

[UserJourney](userjourneys.md) 元素定义用户与应用程序进行交互时使用的路径。 如果 **UserJourneys** 元素不具有标识为 `UserInfoJourney` 的 **UserJourney**，则添加一个：

```xml
<!-- 
<UserJourneys> -->
  <UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
    <Authorization>
      <AuthorizationTechnicalProfiles>
        <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
      </AuthorizationTechnicalProfiles>
    </Authorization>
    <OrchestrationSteps >
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>objectId</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges UserIdentity="false">
          <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
    </OrchestrationSteps>
  </UserJourney>
<!-- 
</UserJourneys> -->
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3.将终结点包括到信赖方策略中

若要在信赖方应用程序中包括 UserInfo 终结点，请将 [Endpoint](relyingparty.md#endpoints) 元素添加到 SocialAndLocalAccounts/SignUpOrSignIn.xml 文件中。 

```xml
<!--
<RelyingParty> -->
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
<!-- 
</RelyingParty> -->
```

完成的信赖方元素将如下所示：

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4.上传文件

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“标识体验框架”。
1. 在“自定义策略”页上，选择“上传自定义策略” 。
1. 选择“覆盖自定义策略(若已存在)”，然后搜索并选择 TrustframeworkExtensions.xml 文件。
1. 单击“上载” 。
1. 针对信赖方文件（例如 *SignUpOrSignIn.xml*）重复步骤 5 到 7。

## <a name="calling-the-userinfo-endpoint"></a>调用 UserInfo 终结点

UserInfo 终结点使用标准 OAuth2 持有者令牌 API，该 API 通过使用在获取应用程序令牌时收到的访问令牌来调用。 它返回一个 JSON 响应，其中包含有关用户的声明的。 UserInfo 终结点在 Azure AD B2C 上托管，具体位置如下：

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

/.well-known 配置终结点（OpenID Connect 发现文档）会列出 `userinfo_endpoint` 字段。 可以使用 /.well-known 配置终结点以编程方式发现 UserInfo 终结点，具体位置如下： 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>测试策略

1. 在“自定义策略”下，选择已将 UserInfo 终结点与之集成的策略。 例如，B2C_1A_SignUpOrSignIn。
1. 选择“立即运行”。 
1. 在“选择应用程序”下，选择你之前注册的应用程序。 对于“选择回复 URL”，请选择 `https://jwt.ms`。 有关详细信息，请参阅[在 Azure Active Directory B2C 中注册 Web 应用程序](tutorial-register-applications.md)。
1. 使用电子邮件地址或社交帐户注册或登录。
1. 从 [https://jwt.ms](https://jwt.ms) 网站复制采用编码格式的 id_token。 你可以使用此方法向 UserInfo 终结点提交 GET 请求并检索用户信息。
1. 向 UserInfo 终结点提交 GET 请求并检索用户信息。

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your access token>
```

成功的响应如下所示：

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="provide-optional-claims"></a>提供可选声明

要为应用提供更多声明，请按照以下步骤操作：

1. [添加用户属性并自定义用户输入](configure-user-input.md)。
1. 将[信赖方策略技术配置文件](relyingparty.md#technicalprofile) OutputClaims 元素修改为要提供的声明。 使用 `DefaultValue` 属性设置默认值。 也可将默认值设置为[声明解析器](claim-resolver-overview.md)，例如 `{Context:CorrelationId}`。 要强制使用默认值，请将 `AlwaysUseDefaultValue` 属性设置为 `true`。 以下示例使用默认值添加城市声明。
    
    ```xml
    <RelyingParty>
      ...
      <TechnicalProfile Id="PolicyProfile">
        ...
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="city" DefaultValue="Berlin" />
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```
  
1. 将 UserInfoIssuer 技术配置文件 InputClaims 元素修改为要提供的声明。 使用 `PartnerClaimType` 属性将声明返回的名称更改为应用。 以下示例添加了城市声明并更改了一些声明的名称。

    ```xml
    <TechnicalProfile Id="UserInfoIssuer">
      ...
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="city" />
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="familyName" />
        <InputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <InputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email" />
      </InputClaims>
      ...
    </TechnicalProfile>
    ```

## <a name="next-steps"></a>后续步骤

- 可以在 [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint) 上找到 UserInfo 终结点策略的示例。

::: zone-end
