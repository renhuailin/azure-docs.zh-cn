---
title: 将 API 连接器添加到用户流（预览版）
description: 配置要在用户流中使用的 API 连接器。
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 05/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1913b6cf14aaf31d610adcf446dbe91326e02ff1
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108742906"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow"></a>将 API 连接器添加到注册用户流

作为开发者或 IT 管理员，你可以使用 API 连接器将注册用户流与 REST API 集成，以自定义注册体验，并与外部系统集成。 完成本演练后，就可以创建与 [REST API 服务](api-connectors-overview.md)交互的 Azure AD B2C 用户流。 

::: zone pivot="b2c-user-flow"

在这种情况下，REST API 会验证电子邮件地址的域是否为 fabrikam.com 或 fabricam.com。 用户提供的显示名称超过五个字符。 然后返回具有静态值的职务。 

> [!IMPORTANT]
> 用于注册的 API 连接器是 Azure AD B2C 的一项公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

::: zone-end

::: zone pivot="b2c-custom-policy"

在这种情况下，我们将添加用户在 Azure AD B2C 注册页面中输入会员号的功能。 REST API 验证是否已将电子邮件和会员号组合映射到促销代码。 如果 REST API 找到该用户的促销代码，它将被返回到 Azure AD B2C。 最后，促销代码将插入到令牌声明中，以供应用程序使用。

也可以将交互设计为业务流程步骤。 当 REST API 不会验证屏幕上的数据并始终返回声明时，这非常合适。 有关详细信息，请参阅[演练：在 Azure AD B2C 用户旅程中以业务流程步骤的形式集成 REST API 声明交换](custom-policy-rest-api-claims-exchange.md)。

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="create-an-api-connector"></a>创建 API 连接器

若要使用某个 [API 连接器](api-connectors-overview.md)，首先需要创建该 API 连接器，然后在用户流中启用它。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure AD B2C”。
4. 依次选择“API 连接器(预览版)”、“新建 API 连接器”。 

   ![添加新的 API 连接器](./media/add-api-connector/api-connector-new.png)

5. 为调用提供显示名称。 例如“验证用户信息”。
6. 为 API 调用提供“终结点 URL”。
7. 选择“身份验证类型”，并配置用于调用 API 的身份验证信息。 有关保护 API 的选项，请参阅以下部分。

    ![配置 API 连接器](./media/add-api-connector/api-connector-config.png)

8. 选择“保存”。

## <a name="securing-the-api-endpoint"></a>保护 API 终结点
可以使用 HTTP 基本身份验证或 HTTPS 客户端证书身份验证（预览版）来保护 API 终结点。 对于这两种情况，都要提供凭据，供 Azure AD B2C 在调用 API 终结点时使用。 然后，API 终结点将检查凭据并执行授权决策。

### <a name="http-basic-authentication"></a>HTTP 基本身份验证
HTTP 基本身份验证在 [RFC 2617](https://tools.ietf.org/html/rfc2617) 中进行定义。 Azure AD B2C 使用 `Authorization` 头中的客户端凭据（`username` 和 `password`）发送 HTTP 请求。 凭据的格式是 base64 编码的字符串 `username:password`。 然后，API 将检查这些值，以确定是否要拒绝 API 调用。

### <a name="https-client-certificate-authentication-preview"></a>HTTPS 客户端证书身份验证（预览版）

> [!IMPORTANT]
> 此功能目前为预览版，不附带服务级别协议。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

客户端证书身份验证是一种基于证书的相互身份验证，其中，客户端向服务器提供客户端证书来证明自己的身份。 在这种情况下，Azure AD B2C 将使用你上传的证书作为 API 连接器配置的一部分。 此操作在 TLS/SSL 握手期间发生。 然后 API 服务可限制只有拥有适当证书的服务才能访问。 客户端证书是一种 PKCS12 (PFX) X.509 数字证书。 在生产环境中，必须由证书颁发机构为此证书签名。 

若要创建证书，可以使用 [Azure 密钥保管库](../key-vault/certificates/create-certificate.md)，其中提供了适用于自签名证书的选项，以及与证书颁发者提供程序相集成以便为证书签名的选项。 建议的设置包括：
- **使用者**：`CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **内容类型**：`PKCS #12`
- **生存期操作类型**：`Email all contacts at a given percentage lifetime` 或 `Email all contacts a given number of days before expiry`
- **密钥类型**：`RSA`
- **密钥大小**：`2048`
- **可导出的私钥**：`Yes`（以便能够导出 pfx 文件）

然后，你可以[导出该证书](../key-vault/certificates/how-to-export-certificate.md)。 还可以使用 PowerShell 的 [New-SelfSignedCertificate cmdlet](../active-directory-b2c/secure-rest-api.md#prepare-a-self-signed-certificate-optional) 来生成自签名证书。

获得证书后，可以将其上传为 API 连接器配置的一部分。 请注意，只需为受密码保护的证书文件指定密码。

API 必须根据发送的客户端证书实现授权，以便保护 API 终结点。 对于 Azure 应用服务和 Azure Functions，请参阅[配置 TLS 相互身份验证](../app-service/app-service-web-configure-tls-mutual-auth.md)来了解如何通过 API 代码启用和验证证书。  还可以通过 Azure API 管理使用策略表达式针对所需的值[检查客户端证书属性](
../api-management/api-management-howto-mutual-certificates-for-clients.md)。

建议针对证书过期时间设置提醒警报。 你将需要生成新证书，并重复上述步骤。 部署新证书时，API 服务可以临时继续接受旧证书和新证书。 若要将新证书上传到现有 API 连接器，请在“API 连接器”下选择该 API 连接器，然后单击“上传新证书”。  Azure Active Directory 会自动使用最新上传的未过期且已超过开始日期的证书。

### <a name="api-key"></a>API 密钥

某些服务使用“API 密钥”机制来模糊化开发期间对 HTTP 终结点的访问。 对于 [Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)，可以通过包含 `code` 作为“终结点 URL”中的查询参数来实现此目的。 例如 `https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b>。 

在生产环境中不应单独使用此机制。 因此，始终需要基本身份验证或证书身份验证的配置。 如果你不想要出于开发目的实现任何身份验证方法（不建议），可以选择基本身份验证，并为 `username` 和 `password` 使用临时值，在 API 中实现授权时，API 可以忽视这些值。

## <a name="the-request-sent-to-your-api"></a>发送到 API 的请求
API 连接器具体化为 **HTTP POST** 请求，在 JSON 正文中以键值对的形式发送用户特性（“声明”）。 特性的序列化方式与 [Microsoft Graph](/graph/api/resources/user#properties) 用户属性类似。 

**示例请求**
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
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

只有“Azure AD B2C” > “用户特性”体验中列出的用户属性和自定义特性才可以在请求中发送。 

自定义特性以 **extension_\<extensions-app-id>_CustomAttribute** 格式存在于目录中。 你的 API 应期望收到相同序列化格式的声明。 有关自定义特性的详细信息，请参阅[在 Azure AD B2C 中定义自定义特性](user-flow-custom-attributes.md)。

此外，默认会在所有请求中发送 **UI 区域设置（“ui_locales”）** 声明。 此声明提供用户设备上配置的用户区域设置，API 可以使用此区域设置来返回国际化的响应。

> [!IMPORTANT]
> 如果调用 API 终结点时某个声明没有值，则不会将该声明发送到 API。 应将 API 设计为显式检查并处理请求中没有声明的情况。

> [!TIP] 
> 在某个用户的帐户尚未在你的租户中创建之前，API 可以使用 [**标识（“identities”）**](/graph/api/resources/objectidentity)和 **电子邮件地址（“email”）** 声明来识别该用户。 

## <a name="enable-the-api-connector-in-a-user-flow"></a>在用户流中启用 API 连接器

遵循以下步骤将 API 连接器添加到注册用户流。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure AD B2C”。
4. 选择“用户流”，然后选择要将 API 连接器添加到的用户流。
5. 选择“API 连接器”，然后选择要在执行用户流中的以下步骤时调用的 API 终结点：

   - **使用标识提供者登录之后**
   - **创建用户之前**

   ![将 API 添加到用户流](./media/add-api-connector/api-connectors-user-flow-select.png)

6. 选择“保存”。

## <a name="after-signing-in-with-an-identity-provider"></a>使用标识提供者登录之后

在注册过程的此步骤中，在用户使用标识提供者（例如 Google、Facebook 和 Azure AD）进行身份验证之后，会立即调用 API 连接器。 此步骤优先于特性收集页，后者是向用户显示的用于收集用户特性的表单。 如果用户正在使用本地帐户注册，则不会调用此步骤。

### <a name="example-request-sent-to-the-api-at-this-step"></a>执行此步骤时发送到 API 的示例请求
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
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

发送到 API 的确切声明取决于标识提供者提供的信息。 始终会发送“email”。

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>执行此步骤时来自 Web API 的预期响应类型

在运行用户流期间当 Web API 从 Azure AD 收到 HTTP 请求时，它可以返回以下响应：

- 继续响应
- 阻止响应

#### <a name="continuation-response"></a>继续响应

继续响应表示用户流应继续下一步：特性收集页。

在继续响应中，API 可以返回声明。 如果 API 返回了某个声明，该声明将执行以下操作：

- 预先填充特性收集页中的输入字段。

参阅[继续响应](#example-of-a-continuation-response)的示例。

#### <a name="blocking-response"></a>阻止响应

收到阻止响应会退出用户流。 API 可以有目的性地发出阻止响应，通过向用户显示一个阻止页来停止用户流的继续运行。 阻止页将显示 API 提供的 `userMessage`。

参阅[阻止响应](#example-of-a-blocking-response)的示例。

## <a name="before-creating-the-user"></a>创建用户之前

在显示属性集合页之后，将调用注册过程中的此步骤的 API 连接器（如果已包含一个连接器）。 在创建用户帐户之前始终会调用此步骤。

### <a name="example-request-sent-to-the-api-at-this-step"></a>执行此步骤时发送到 API 的示例请求

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
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

发送到 API 的确切声明取决于从用户收集的信息或者标识提供者提供的信息。

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>执行此步骤时来自 Web API 的预期响应类型

在运行用户流期间当 Web API 从 Azure AD 收到 HTTP 请求时，它可以返回以下响应：

- 继续响应
- 阻止响应
- 验证响应

#### <a name="continuation-response"></a>继续响应

继续响应表示用户流应继续下一步：在目录中创建用户。

在继续响应中，API 可以返回声明。 如果 API 返回了某个声明，该声明将执行以下操作：

- 替代已在特性收集页中分配给声明的任何值。

参阅[继续响应](#example-of-a-continuation-response)的示例。

#### <a name="blocking-response"></a>阻止响应
收到阻止响应会退出用户流。 API 可以有目的性地发出阻止响应，通过向用户显示一个阻止页来停止用户流的继续运行。 阻止页将显示 API 提供的 `userMessage`。

参阅[阻止响应](#example-of-a-blocking-response)的示例。

### <a name="validation-error-response"></a>验证错误响应
 当 API 以验证错误响应做出响应时，用户流将停留在特性收集页，并向用户显示 `userMessage`。 然后，用户可以编辑并重新提交表单。 这种类型的响应可用于输入验证。

参阅[验证错误响应](#example-of-a-validation-error-response)的示例。

## <a name="example-responses"></a>示例响应

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
| action                                             | 字符串            | 是      | 值必须是 `Continue`。                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | 否       | 返回的值可以覆盖从用户收集的值。 如果已将值选作“应用程序声明”，则可以在令牌中返回这些值。                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | 否       | 声明不需要包含 `_<extensions-app-id>_`。 返回的值可以覆盖从用户收集的值。 如果已将值选作“应用程序声明”，则可以在令牌中返回这些值。  |

### <a name="example-of-a-blocking-response"></a>阻止响应的示例

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| 参数   | 类型   | 必需 | 说明                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| 版本     | 字符串 | 是      | API 的版本。                                                    |
| action      | 字符串 | 是      | 值必须是 `ShowBlockPage`                                              |
| userMessage | 字符串 | 是      | 要向用户显示的消息。                                            |

**收到阻止响应时的最终用户体验**

![示例阻止页](./media/add-api-connector/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>验证错误响应的示例

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code."
}
```

| 参数   | 类型    | 必需 | 说明                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| 版本     | 字符串  | 是      | API 的版本。                                                    |
| action      | 字符串  | 是      | 值必须是 `ValidationError`。                                           |
| 状态      | 整数/字符串 | 是      | 其值必须为 `400` 或 `"400"`（表示验证错误响应）。  |
| userMessage | 字符串  | 是      | 要向用户显示的消息。                                            |

> [!NOTE]
> HTTP 状态代码必须是“400”，此外，响应正文中必须包含“status”值。

**收到验证错误响应时的最终用户体验**

![示例验证页](./media/add-api-connector/validation-error-postal-code.png)


::: zone-end

::: zone pivot="b2c-custom-policy"


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

::: zone-end

## <a name="best-practices-and-how-to-troubleshoot"></a>最佳做法和故障排除方法

### <a name="using-serverless-cloud-functions"></a>使用无服务器云函数

无服务器函数（例如 Azure Functions 中的 HTTP 触发器）可让用户创建与 API 连接器配合使用的 API 终结点。 [举例](code-samples.md#api-connectors)而言，可以使用无服务器云函数来执行验证逻辑，并仅限通过特定的电子邮件域进行注册。 无服务器云函数还可以调用其他 Web API、用户存储和其他云服务，以实现更复杂的方案。

### <a name="best-practices"></a>最佳做法
请确保：
* API 遵循上面所述的 API 请求和响应协定。 
* API 连接器的 **终结点 URL** 指向正确的 API 终结点。
* API 显式检查收到的声明的 null 值。
* API 可以尽快做出响应，以确保用户体验流畅。
    * 如果使用无服务器函数或可缩放的 Web 服务，请使用能够在生产环境中保持 API“清醒”或“热身”的 托管计划。 对于 Azure Functions，建议使用[高级计划](../azure-functions/functions-scale.md)
 
### <a name="use-logging"></a>使用日志记录

一般情况下，使用 Web API 服务（例如 [Application insights](../azure-functions/functions-monitoring.md)）启用的日志记录工具来监视 API 是否出现意外错误代码、异常和性能不佳的情况会很有帮助。
* 监视非 HTTP 200 或 400 的 HTTP 状态代码。
* 401 或 403 HTTP 状态代码通常指示身份验证出现了问题。 反复检查 API 的身份验证层以及 API 连接器中的相应配置。
* 如果需要，请在开发中使用更主动的日志记录级别（例如“跟踪”或“调试”）。
* 监视 API 的响应时间是否太长。

## <a name="next-steps"></a>后续步骤

::: zone pivot="b2c-user-flow"

- 参考我们的[示例](code-samples.md#api-connectors)帮助自己入门。
- [保护 API 连接器](secure-rest-api.md)

::: zone-end

::: zone pivot="b2c-custom-policy"

- [演练：在 Azure AD B2C 用户旅程中以业务流程步骤的形式集成 REST API 声明交换](custom-policy-rest-api-claims-exchange.md)
- [保护 API 连接器](secure-rest-api.md)
- [参考：RESTful 技术配置文件](restful-technical-profile.md)

::: zone-end
