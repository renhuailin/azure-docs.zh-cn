---
title: 将 API 连接器添加到自助注册流 - Azure AD
description: 配置要在用户流中使用的 Web API。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bafbbb8faee98bacbd6d1f314c1411ce2593296a
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108316274"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>向用户流添加 API 连接器

若要使用某个 [API 连接器](api-connectors-overview.md)，首先需要创建该 API 连接器，然后在用户流中启用它。

> [!IMPORTANT]
> 从 2021 年下半年开始，Google 将[弃用 Web 视图登录支持](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 如果正在对 B2B 邀请或 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 使用 Google 联合身份验证，或者正在将自助注册与 Gmail 一起使用，那么当你的应用通过嵌入的 Web 视图对用户进行身份验证时，Google Gmail 用户将无法登录。 [了解详细信息](google-federation.md#deprecation-of-web-view-sign-in-support)。

## <a name="create-an-api-connector"></a>创建 API 连接器

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 依次选择“所有 API 连接器”、“新建 API 连接器”。 

   ![添加新的 API 连接器](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. 为调用提供显示名称。 例如“检查审批状态”。
6. 为 API 调用提供“终结点 URL”。
7. 选择“身份验证类型”，并配置用于调用 API 的身份验证信息。 有关保护 API 的选项，请参阅以下部分。

    ![配置 API 连接器](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. 选择“保存”。

## <a name="securing-the-api-endpoint"></a>保护 API 终结点
可以使用 HTTP 基本身份验证或 HTTPS 客户端证书身份验证（预览版）来保护 API 终结点。 对于这两种情况，都要提供凭据，供 Azure Active Directory 在调用 API 终结点时使用。 然后，API 终结点将检查凭据并执行授权决策。

### <a name="http-basic-authentication"></a>HTTP 基本身份验证
HTTP 基本身份验证在 [RFC 2617](https://tools.ietf.org/html/rfc2617) 中进行定义。 Azure Active Directory 使用 `Authorization` 头中的客户端凭据（`username` 和 `password`）发送 HTTP 请求。 凭据的格式是 base64 编码的字符串 `username:password`。 然后，API 将检查这些值，以确定是否要拒绝 API 调用。

### <a name="https-client-certificate-authentication-preview"></a>HTTPS 客户端证书身份验证（预览版）

> [!IMPORTANT]
> 此功能目前为预览版，不附带服务级别协议。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

客户端证书身份验证是一种基于证书的相互身份验证，其中，客户端向服务器提供客户端证书来证明自己的身份。 在这种情况下，Azure Active Directory 将使用你上传的证书作为 API 连接器配置的一部分。 此操作在 SSL 握手期间发生。 然后 API 服务可限制只有拥有适当证书的服务才能访问。 客户端证书是一种 PKCS12 (PFX) X.509 数字证书。 在生产环境中，必须由证书颁发机构为此证书签名。 

若要创建证书，可以使用 [Azure 密钥保管库](../../key-vault/certificates/create-certificate.md)，其中提供了适用于自签名证书的选项，以及与证书颁发者提供程序相集成以便为证书签名的选项。 建议的设置包括：
- **使用者**：`CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **内容类型**：`PKCS #12`
- **生存期操作类型**：`Email all contacts at a given percentage lifetime` 或 `Email all contacts a given number of days before expiry`
- **密钥类型**：`RSA`
- **密钥大小**：`2048`
- **可导出的私钥**：`Yes`（以便能够导出 pfx 文件）

然后，你可以[导出该证书](../../key-vault/certificates/how-to-export-certificate.md)。 还可以使用 PowerShell 的 [New-SelfSignedCertificate cmdlet](../../active-directory-b2c/secure-rest-api.md#prepare-a-self-signed-certificate-optional) 来生成自签名证书。

获得证书后，可以将其上传为 API 连接器配置的一部分。 请注意，只需为受密码保护的证书文件指定密码。

API 必须根据发送的客户端证书实现授权，以便保护 API 终结点。 有关 Azure 应用服务和 Azure Functions 的信息，请参阅[配置 TLS 相互身份验证](../../app-service/app-service-web-configure-tls-mutual-auth.md)，了解如何通过 API 代码启用和验证证书。  还可以使用 Azure API 管理来保护 API，并使用策略表达式根据所需的值[检查客户端证书属性](
../../api-management/api-management-howto-mutual-certificates-for-clients.md)。
 
建议针对证书过期时间设置提醒警报。 你将需要生成新证书，并重复上述步骤。 部署新证书时，API 服务可以临时继续接受旧证书和新证书。 若要将新证书上传到现有的 API 连接器，请在“所有 API 连接器”下选择该 API 连接器，然后单击“上传新证书”。  Azure Active Directory 会自动使用最新上传的未过期且已超过开始日期的证书。

### <a name="api-key"></a>API 密钥
某些服务使用“API 密钥”机制来模糊化开发期间对 HTTP 终结点的访问。 对于 [Azure Functions](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)，可以通过包含 `code` 作为“终结点 URL”中的查询参数来实现此目的。 例如 `https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b>。 

在生产环境中不应单独使用此机制。 因此，始终需要基本身份验证或证书身份验证的配置。 如果你不想要出于开发目的实现任何身份验证方法（不建议），可以选择基本身份验证，并为 `username` 和 `password` 使用临时值，在 API 中实现授权时，API 可以忽视这些值。

## <a name="the-request-sent-to-your-api"></a>发送到 API 的请求
API 连接器具体化为 **HTTP POST** 请求，在 JSON 正文中以键值对的形式发送用户特性（“声明”）。 特性的序列化方式与 [Microsoft Graph](/graph/api/resources/user#properties) 用户属性类似。 

**示例请求**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
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

只有“Azure Active Directory” > “外部标识” > “自定义用户特性”体验中列出的用户属性和自定义特性才可以在请求中发送。  

自定义特性以 **extension_\<extensions-app-id>_AttributeName** 格式存在于目录中。 你的 API 应期望收到相同序列化格式的声明。 有关自定义特性的详细信息，请参阅[定义自助注册流的自定义特性](user-flow-add-custom-attributes.md)。

此外，默认会在所有请求中发送 **UI 区域设置（“ui_locales”）** 声明。 此声明提供用户设备上配置的用户区域设置，API 可以使用此区域设置来返回国际化的响应。

> [!IMPORTANT]
> 如果调用 API 终结点时某个声明没有值，则不会将该声明发送到 API。 应将 API 设计为显式检查并处理请求中没有声明的情况。

> [!TIP] 
> 在某个用户的帐户尚未在你的租户中创建之前，API 可以使用 [**标识（“identities”）**](/graph/api/resources/objectidentity)和 **电子邮件地址（“email”）** 声明来识别该用户。 当用户使用标识提供者（例如 Google 或 Facebook）进行身份验证时，将发送“identities”声明。 始终会发送“email”。

## <a name="enable-the-api-connector-in-a-user-flow"></a>在用户流中启用 API 连接器

遵循以下步骤将 API 连接器添加到自助注册用户流。

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 选择“用户流”，然后选择要将 API 连接器添加到的用户流。
5. 选择“API 连接器”，然后选择要在执行用户流中的以下步骤时调用的 API 终结点：

   - **使用标识提供者登录之后**
   - **创建用户之前**

   ![将 API 添加到用户流](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. 选择“保存”。

## <a name="after-signing-in-with-an-identity-provider"></a>使用标识提供者登录之后

在注册过程的此步骤中，在用户使用标识提供者（例如 Google、Facebook 和 Azure AD）进行身份验证之后，会立即调用 API 连接器。 此步骤优先于特性收集页，后者是向用户显示的用于收集用户特性的表单。 如果用户正在使用本地帐户注册，则不会调用此步骤。

### <a name="example-request-sent-to-the-api-at-this-step"></a>执行此步骤时发送到 API 的示例请求
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
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

在注册过程的此步骤中，在显示属性集合页之后，将调用 API 连接器（如果包含了连接器）。 在 Azure AD 中创建用户帐户之前始终会调用此步骤。 

### <a name="example-request-sent-to-the-api-at-this-step"></a>执行此步骤时发送到 API 的示例请求

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
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
| 版本                                            | 字符串            | 是      | API 的版本。                                                                                                                                                                                                                                                                |
| action                                             | 字符串            | 是      | 值必须是 `Continue`。                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | 否       | 如果已将值选作 API 连接器配置中的“要接收的声明”以及用户流的“用户特性”，则这些值可以存储在目录中。  如果已将值选作“应用程序声明”，则可以在令牌中返回这些值。                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | 否       | 返回的声明不需要包含 `_<extensions-app-id>_`。 返回的值可以覆盖从用户收集的值。 如果已将返回的值配置为应用程序的一部分，则也可以在令牌中将其返回。  |

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

![示例阻止页](./media/api-connectors-overview/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>验证错误响应的示例

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
}
```

| 参数   | 类型    | 必需 | 说明                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| 版本     | 字符串  | 是      | API 的版本。                                                    |
| action      | 字符串  | 是      | 值必须是 `ValidationError`。                                           |
| 状态      | Integer | 是      | 必须为值 `400`（表示验证错误响应）。                        |
| userMessage | 字符串  | 是      | 要向用户显示的消息。                                            |

> [!NOTE]
> HTTP 状态代码必须是“400”，此外，响应正文中必须包含“status”值。

**收到验证错误响应时的最终用户体验**

![示例验证页](./media/api-connectors-overview/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>最佳做法和故障排除方法

### <a name="using-serverless-cloud-functions"></a>使用无服务器云函数
无服务器函数（例如 Azure Functions 中的 HTTP 触发器）可让用户方便地创建与 API 连接器配合使用的 API 终结点。 [举例](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)而言，可以使用无服务器云函数来执行验证逻辑，并仅限通过特定的电子邮件域进行注册。 无服务器云函数还可以调用其他 Web API、用户存储和其他云服务，以实现更复杂的方案。

### <a name="best-practices"></a>最佳做法
请确保：
* API 遵循上面所述的 API 请求和响应协定。 
* API 连接器的 **终结点 URL** 指向正确的 API 终结点。
* API 显式检查收到的声明的 null 值。
* API 可以尽快做出响应，以确保用户体验流畅。
    * 如果使用无服务器函数或可缩放的 Web 服务，请使用能够在生产环境中保持 API“清醒”或“热身”的 托管计划。 对于 Azure Functions，建议使用[高级计划](../../azure-functions/functions-scale.md)

### <a name="use-logging"></a>使用日志记录
一般情况下，使用 Web API 服务（例如 [Application insights](../../azure-functions/functions-monitoring.md)）启用的日志记录工具来监视 API 是否出现意外错误代码、异常和性能不佳的情况会很有帮助。
* 监视非 HTTP 200 或 400 的 HTTP 状态代码。
* 401 或 403 HTTP 状态代码通常指示身份验证出现了问题。 反复检查 API 的身份验证层以及 API 连接器中的相应配置。
* 如果需要，请在开发中使用更主动的日志记录级别（例如“跟踪”或“调试”）。
* 监视 API 的响应时间是否太长。

## <a name="next-steps"></a>后续步骤
- 了解如何[将自定义审批工作流添加到自助注册](self-service-sign-up-add-approvals.md)
- 参考[快速入门示例](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)帮助自己入门。