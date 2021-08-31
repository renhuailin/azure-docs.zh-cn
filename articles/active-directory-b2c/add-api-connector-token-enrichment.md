---
title: 令牌扩充 - Azure Active Directory B2C
description: 使用 API 通过外部源中的声明扩充令牌。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/04/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 524315a0fa07b6c965d473688942c6e69be38494
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778189"
---
# <a name="enrich-tokens-with-claims-from-external-sources-using-api-connectors"></a>使用 API 连接器通过外部源中的声明扩充令牌

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) 可让标识开发人员使用 [API 连接器](api-connectors-overview.md)将与 RESTful API 的交互集成到其用户流中。 本演练结束时，你将能够创建一个与 API 交互的 Azure AD B2C 用户流，以使用来自外部源的信息来扩充令牌。

::: zone pivot="b2c-user-flow"

可以使用应用于“发送令牌之前（预览）”步骤的 API 连接器，通过外部源中的信息来扩充应用程序的令牌。 当用户登录或注册时，Azure AD B2C 将调用 API 连接器中配置的 API 终结点，该终结点可以查询下游服务（如云服务、自定义用户存储、自定义权限系统、旧标识系统等）中用户的信息。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

可以使用[示例](api-connector-samples.md#api-connector-rest-api-samples)之一创建 API 终结点。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-api-connector"></a>创建 API 连接器

若要使用某个 [API 连接器](api-connectors-overview.md)，首先需要创建该 API 连接器，然后在用户流中启用它。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure AD B2C”。
4. 选择“API 连接器”，再选择“新建 API 连接器”。 

   ![基本 API 连接器配置的屏幕截图](media/add-api-connector-token-enrichment/api-connector-new.png)

5. 为调用提供显示名称。 例如，通过外部源扩充令牌。
6. 为 API 调用提供“终结点 URL”。
7. 选择“身份验证类型”，并配置用于调用 API 的身份验证信息。 了解如何[保护 API 连接器](secure-rest-api.md)。

   ![API 连接器身份验证配置的屏幕截图](media/add-api-connector-token-enrichment/api-connector-config.png)

8. 选择“保存”。

## <a name="enable-the-api-connector-in-a-user-flow"></a>在用户流中启用 API 连接器

遵循以下步骤将 API 连接器添加到注册用户流。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure AD B2C”。
4. 选择“用户流”，然后选择要将 API 连接器添加到的用户流。
5. 选择“API 连接器”，然后选择要在用户流中的“开始发送令牌之前（预览）”步骤中调用的 API 终结点 ：

   ![为用户流步骤选择 API 连接器的屏幕截图](media/add-api-connector-token-enrichment/api-connectors-user-flow-select.png)

6. 选择“保存”。

此步骤仅适用于注册和登录（推荐）、注册（推荐）和登录（推荐”  用户流。

## <a name="example-request-sent-to-the-api-at-this-step"></a>执行此步骤时发送到 API 的示例请求

当在登录和注册期间即将颁发令牌时，将调用此步骤中的 API 连接器。 

API 连接器具体化为 **HTTP POST** 请求，在 JSON 正文中以键值对的形式发送用户特性（“声明”）。 特性的序列化方式与 [Microsoft Graph](/graph/api/resources/user#properties) 用户属性类似。 

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "objectId": "ab3ec3b2-a435-45e4-b93a-56a005e88bb7",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "objectId": "ab3ec3b2-a435-45e4-b93a-56a005e88bb7",
 "client_id": "231c70e8-8424-48ac-9b5d-5623b9e4ccf3",
 "step": "PreTokenIssuance",
 "ui_locales":"en-US"
}
```

发送到 API 的声明取决于为用户定义的信息。

只有“Azure AD B2C” > “用户特性”体验中列出的用户属性和自定义特性才可以在请求中发送。 

自定义特性以 **extension_\<extensions-app-id>_CustomAttribute** 格式存在于目录中。 你的 API 应期望收到相同序列化格式的声明。 有关自定义特性的详细信息，请参阅[在 Azure AD B2C 中定义自定义特性](user-flow-custom-attributes.md)。

此外，这些声明通常在此步骤的所有请求中发送：
- UI 区域设置（“ui_locales”）- 最终用户在其设备上配置的区域设置。 API 可以使用此设置返回国际化的响应。
- 步骤（“step”）- 调用 API 连接器的用户流上的步骤或点。 此步骤的值为 `
- 客户端 ID（“client_id”）- 最终用户在用户流中进行身份验证的应用程序的 `appId` 值。 这不是资源应用程序在访问令牌中的 `appId`。
- objectId - 用户的标识符。 可以使用该值查询下游服务，获取有关用户的信息。
  
> [!IMPORTANT]
> 如果调用 API 终结点时某个声明没有值，则不会将该声明发送到 API。 应将 API 设计为显式检查并处理请求中没有声明的情况。

## <a name="expected-response-types-from-the-web-api-at-this-step"></a>执行此步骤时来自 Web API 的预期响应类型

在用户流期间，当 Web API 从 Azure AD 收到 HTTP 请求时，它可以返回“继续响应。”

### <a name="continuation-response"></a>继续响应

继续响应表示用户流应继续下一步：颁发令牌。

在继续响应中，API 可以返回其他声明。 你希望在令牌中返回的 API 所返回的声明必须是内置声明或[定义为自定义属性](user-flow-custom-attributes.md)，并且必须在用户流的“应用程序声明”配置中选中。 

令牌中的声明值将是 API 返回的值，而不是目录中的值。 API 响应无法覆盖某些声明值。 API 可以返回的声明对应于在“用户属性”下找到的集，但 `email` 除外。

> [!NOTE]
> 仅在初始身份验证期间调用 API。 使用刷新令牌以无提示方式获取新的访问令牌或 ID 令牌时，令牌将包括初始身份验证期间评估的值。 

## <a name="example-response"></a>示例响应

### <a name="example-of-a-continuation-response"></a>继续响应的示例

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| 参数                                          | 类型              | 必需 | 说明                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 版本     | 字符串 | 是      | API 的版本。                                                    |
| action                                             | 字符串            | 是      | 值必须是 `Continue`。                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | 否       | 如果已将值选作“应用程序声明”，则可以在令牌中返回这些值。                                        |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | 否       | 声明不需要包含 `_<extensions-app-id>_`，它是可选的。 如果已将值选作“应用程序声明”，则可以在令牌中返回这些值。  |

::: zone-end

::: zone pivot="b2c-custom-policy"

在此方案中，我们通过与企业业务线工作流集成来丰富用户的令牌数据。 在使用本地帐户或联合帐户注册或登录期间，Azure AD B2C 调用 REST API 从远程数据源获取用户的扩展配置文件数据。 在此示例中，Azure AD B2C 发送用户的唯一标识符，即 objectId。 然后，REST API 返回用户的帐户余额（随机数字）。 使用此示例作为与你自己的 CRM 系统、市场营销数据库或任何业务线工作流集成的起点。

还可以将交互设计为验证技术配置文件。 当 REST API 将在屏幕上验证数据并返回声明时，这非常合适。 有关详细信息，请参阅[演练：向注册用户流添加 API 连接器](add-api-connector.md)。

## <a name="prerequisites"></a>先决条件

- 完成[自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)中的步骤。 应准备好一个有效的自定义策略，以便使用本地帐户注册和登录。
- 了解如何[在 Azure AD B2C 自定义策略中集成 REST API 声明交换](api-connectors-overview.md)。

## <a name="prepare-a-rest-api-endpoint"></a>准备 REST API 终结点

在本演练中，应具有 REST API 用于验证是否已在后端系统中注册用户的 Azure AD B2C objectId。 如果已注册，则 REST API 会返回用户帐户余额。 否则，REST API 会在目录中注册新帐户并返回起始余额 `50.00`。

以下 JSON 代码演示 Azure AD B2C 将发送到 REST API 终结点的数据。 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

REST API 验证数据后，就必须返回 HTTP 200 (Ok)，其中包含以下 JSON 数据：

```json
{
    "balance": "760.50"
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
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>添加 RESTful API 技术配置文件 

[RESTful 技术配置文件](restful-technical-profile.md)支持与你自己的 RESTful 服务交互。 Azure AD B2C 在 `InputClaims` 集合中将数据发送到 RESTful 服务，在 `OutputClaims` 集合中接收返回的数据。 在 <em>`TrustFrameworkExtensions.xml`</em> 文件中查找 ClaimsProviders 元素，并按如下所示添加新的声明提供程序 ：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
``` 

在本示例中，`userLanguage` 将在 JSON 有效负载中以 `lang` 的形式发送到 REST 服务。 `userLanguage` 声明的值包含当前用户语言 ID。 有关详细信息，请参阅[声明解析程序](claim-resolver-overview.md)。

### <a name="configure-the-restful-api-technical-profile"></a>配置 RESTful API 技术配置文件 

部署 REST API 后，设置 `REST-GetProfile` 技术配置文件的元数据以反映你自己的 REST API，包括：

- **ServiceUrl**。 设置 REST API 终结点的 URL。
- **SendClaimsIn**。 指定如何将输入声明发送到 RESTful 声明提供程序。
- **AuthenticationType**。 设置 RESTful 声明提供程序所执行的身份验证类型。 
- **AllowInsecureAuthInProduction**。 在生产环境中，请确保将此元数据设置为 `true`
    
有关更多配置，请参阅 [RESTful 技术配置文件元数据](restful-technical-profile.md#metadata)。

`AuthenticationType` 和 `AllowInsecureAuthInProduction` 上的注释指定了在移到生产环境时应进行的更改。 若要了解如何保护用于生产的 RESTful API，请参阅[保护 RESTful API](secure-rest-api.md)。

## <a name="add-an-orchestration-step"></a>添加业务流程步骤

[用户旅程](userjourneys.md)指定策略允许信赖方应用程序为用户获取所需声明的显式路径。 用户旅程表示为成功事务必须遵循的业务流程序列。 可以添加或减少业务流程步骤。 在这种情况下，你将添加新的业务流程步骤，该步骤用于在用户通过 REST API 调用注册或登录后扩充提供给应用程序的信息。

1. 打开策略的基文件。 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkBase.xml`</em>。
1. 搜索 `<UserJourneys>` 元素。 复制整个元素，然后将其删除。
1. 打开策略的扩展文件， 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。
1. 将 `<UserJourneys>` 粘贴到扩展文件中，紧随 `<ClaimsProviders>` 元素之后。
1. 找到 `<UserJourney Id="SignUpOrSignIn">`，然后在最后一个业务流程步骤之前添加以下步骤。

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. 通过将 `Order` 更改为 `8` 来重构最后一个业务流程步骤。 最后两个业务流程步骤应如下所示：

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. 对 ProfileEdit 和 PasswordReset 用户旅程重复最后两个步骤 。


## <a name="include-a-claim-in-the-token"></a>在令牌中包括声明 

若要将 `balance` 声明返回到信赖方应用程序，请将输出声明添加到 <em>`SocialAndLocalAccounts/``SignUpOrSignIn.xml`</em> 文件。 添加输出声明会在用户旅程成功后向令牌发出声明，并将其发送到应用程序。 修改信赖方部分中的技术配置文件元素，以将 `balance` 添加为输出声明。
 
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

对 ProfileEdit.xml 和 PasswordReset.xml 用户旅程重复此步骤 。

保存已更改的文件：TrustFrameworkBase.xml、TrustFrameworkExtensions.xml、SignUpOrSignin.xml、ProfileEdit.xml 和 PasswordReset.xml。 

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用包含 Azure AD 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册” 。
1. 选择“标识体验框架”。
1. 选择“上传自定义策略”，然后上传已更改的策略文件：TrustFrameworkBase.xml、TrustFrameworkExtensions.xml、SignUpOrSignin.xml、ProfileEdit.xml 和 PasswordReset.xml。 
1. 选择已上传的注册或登录策略，并单击“立即运行”按钮。
1. 现在，应该可以使用电子邮件地址或 Facebook 帐户注册。
1. 发送回应用程序的令牌包含 `balance` 声明。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="best-practices-and-how-to-troubleshoot"></a>最佳做法和故障排除方法

### <a name="using-serverless-cloud-functions"></a>使用无服务器云函数

无服务器函数（例如 [Azure Functions 中的 HTTP 触发器](../azure-functions/functions-bindings-http-webhook-trigger.md)）可让用户创建与 API 连接器配合使用的 API 终结点。 无服务器云函数还可以调用其他 Web API、数据存储和其他云服务，以实现复杂的方案。

### <a name="best-practices"></a>最佳做法

请确保：
* API 遵循上面所述的 API 请求和响应协定。 
* API 连接器的 **终结点 URL** 指向正确的 API 终结点。
* API 会显式检查它所依赖的已收到声明是否有 null 值。
* API 会实现[保护 API 连接器](secure-rest-api.md)中概述的身份验证方法。
* API 可以尽快做出响应，以确保用户体验流畅。
    * Azure AD B2C 最多会等待 20 秒来接收响应。 如果未收到任何内容，它会再次尝试（重试）调用 API。
    * 如果使用无服务器函数或可缩放的 Web 服务，请使用能够在生产环境中让 API 保持“清醒”或“热身”状态的托管计划。 对于 Azure Functions，建议在生产中至少使用[高级计划](../azure-functions/functions-scale.md)。
* 确保 API 的高可用性。
* 监视和优化下游 API、数据库或 API 其他依赖项的性能。
  
[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

### <a name="use-logging"></a>使用日志记录

一般情况下，使用 Web API 服务（例如 [Application insights](../azure-functions/functions-monitoring.md)）启用的日志记录工具来监视 API 是否出现意外错误代码、异常和性能不佳的情况会很有帮助。
* 监视非 HTTP 200 或 400 的 HTTP 状态代码。
* 401 或 403 HTTP 状态代码通常指示身份验证出现了问题。 反复检查 API 的身份验证层以及 API 连接器中的相应配置。
* 如果需要，请在开发中使用更主动的日志记录级别（例如“跟踪”或“调试”）。
* 监视 API 的响应时间是否太长。 

此外，Azure AD B2C 通过用户流记录用户身份验证期间发生的 API 事务的元数据。 若要查找这些数据：
1. 转到“Azure AD B2C”
2. 在“活动”下，选择“审核日志” 。
3. 筛选列表视图：对于“日期”，请选择所需的时间间隔，对于“活动”，选择“一个 API 作为用户流的一部分进行调用”  。
4. 检查单个日志。 每一行代表在用户流期间尝试调用的 API 连接器。 如果 API 调用失败并发生重试，它仍表示为单个行。 `numberOfAttempts` 指示 API 被调用的次数。 此值可为 `1` 或 `2`。 日志中详细介绍了有关 API 调用的其他信息。

   ![使用 API 连接器事务的示例审核日志的屏幕截图](media/add-api-connector-token-enrichment/example-anonymized-audit-log.png)

::: zone-end

## <a name="next-steps"></a>后续步骤

::: zone pivot="b2c-user-flow"

- 参考我们的[示例](api-connector-samples.md#api-connector-rest-api-samples)帮助自己入门。
- [保护 API 连接器](secure-rest-api.md)

::: zone-end

::: zone pivot="b2c-custom-policy"

若要了解如何保护 API，请参阅以下文章：

- [演练：在 Azure AD B2C 用户旅程中以业务流程步骤的形式集成 REST API 声明交换](add-api-connector-token-enrichment.md)
- [保护 RESTful API](secure-rest-api.md)
- [参考：RESTful 技术配置文件](restful-technical-profile.md)

::: zone-end


