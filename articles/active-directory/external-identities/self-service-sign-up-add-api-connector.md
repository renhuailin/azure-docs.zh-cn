---
title: 将 API 连接器添加到自助注册流 - Azure AD
description: 配置要在用户流中使用的 Web API。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 07/13/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 456e02b5d84dfde9534a62ea909da513ff8f1c81
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746150"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>向用户流添加 API 连接器

若要使用某个 [API 连接器](api-connectors-overview.md)，首先需要创建该 API 连接器，然后在用户流中启用它。

> [!IMPORTANT]
>
> - 从 2021 年 7 月 12 日开始，如果 Azure AD B2B 客户设置了新的 Google 集成，将其用于自定义应用程序或业务线应用程序的自助注册，则在身份验证转移到系统 Web 视图之前，无法使用 Google 标识进行身份验证。 [了解详细信息](google-federation.md#deprecation-of-web-view-sign-in-support)。
> - 从 2021 年 9 月 30 日开始，Google 将[弃用嵌入式 Web 视图登录支持](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 如果你的应用使用嵌入式 Web 视图对用户进行身份验证，而你将 Google 联合身份验证与 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 或 Azure AD B2B 配合使用来进行[外部用户邀请](google-federation.md)或[自助注册](identity-providers.md)，则 Google Gmail 用户将无法进行身份验证。 [了解详细信息](google-federation.md#deprecation-of-web-view-sign-in-support)。

## <a name="create-an-api-connector"></a>创建 API 连接器

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 依次选择“所有 API 连接器”、“新建 API 连接器”。 

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connector-new.png" alt-text="在创建体验期间提供 API 连接器的基本配置，例如目标 URL 和显示名称。":::

5. 为调用提供显示名称。 例如“检查审批状态”。
6. 为 API 调用提供“终结点 URL”。
7. 选择“身份验证类型”，并配置用于调用 API 的身份验证信息。 了解如何[保护 API 连接器](self-service-sign-up-secure-api-connector.md)。

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connector-config.png" alt-text="在创建体验期间提供 API 连接器的身份验证配置。":::

8. 选择“保存”。

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

此外，声明通常在所有请求中发送：
- UI 区域设置（“ui_locales”）- 最终用户的区域设置，正如在其设备上配置的那样。 API 可以使用此设置返回国际化的响应。
<!-- - **Step ('step')** - The step or point on the user flow that the API connector was invoked for. Values include:
  - `PostFederationSignup` - corresponds to "After federating with an identity provider during sign-up"
  - `PostAttributeCollection` - corresponds to "Before creating the user"
- **Client ID ('client_id')** - The `appId` value of the application that an end-user is authenticating to in a user flow. This is *not* the resource application's `appId` in access tokens. -->
- 电子邮件地址（“email”）或[标识（“identities”）](/graph/api/resources/objectidentity)- API 可以使用这些声明来标识向应用程序进行身份验证的最终用户。 

> [!IMPORTANT]
> 如果调用 API 终结点时某个声明没有值，则不会将该声明发送到 API。 应将 API 设计为显式检查并处理请求中没有声明的情况。

## <a name="enable-the-api-connector-in-a-user-flow"></a>在用户流中启用 API 连接器

遵循以下步骤将 API 连接器添加到自助注册用户流。

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 选择“用户流”，然后选择要将 API 连接器添加到的用户流。
5. 选择“API 连接器”，然后选择要在执行用户流中的以下步骤时调用的 API 终结点：

   - 在注册期间与标识提供者进行联合之后
   - **创建用户之前**

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png" alt-text="选择将哪个 API 连接器用于用户流中的某个步骤，例如“创建用户之前”。":::

6. 选择“保存”。

## <a name="after-federating-with-an-identity-provider-during-sign-up"></a>在注册期间与标识提供者进行联合之后

在注册过程的此步骤中，在用户使用标识提供者（例如 Google、Facebook 和 Azure AD）进行身份验证之后，会立即调用 API 连接器。 此步骤优先于特性收集页，后者是向用户显示的用于收集用户特性的表单。

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
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | 否       | 声明不需要包含 `_<extensions-app-id>_`，它是可选的。 返回的值可以覆盖从用户收集的值。  |

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

:::image type="content" source="media/api-connectors-overview/blocking-page-response.png" alt-text="一个示例图像，显示 API 返回阻止响应后的最终用户体验的外观。":::

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
| 状态      | 整数/字符串 | 是      | 其值必须为 `400` 或 `"400"`（表示验证错误响应）。  |
| userMessage | 字符串  | 是      | 要向用户显示的消息。                                            |

> [!NOTE]
> HTTP 状态代码必须是“400”，此外，响应正文中必须包含“status”值。

**收到验证错误响应时的最终用户体验**

:::image type="content" source="media/api-connectors-overview/validation-error-postal-code.png" alt-text="一个示例图像，显示 API 返回验证错误响应后的最终用户体验的外观。":::

## <a name="best-practices-and-how-to-troubleshoot"></a>最佳做法和故障排除方法

### <a name="using-serverless-cloud-functions"></a>使用无服务器云函数

无服务器函数（例如 [Azure Functions 中的 HTTP 触发器](../../azure-functions/functions-bindings-http-webhook-trigger.md)）可让用户创建与 API 连接器配合使用的 API 终结点。 [举例](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)而言，可以使用无服务器云函数来执行验证逻辑，并仅限通过特定的电子邮件域进行注册。 无服务器云函数还可以调用其他 Web API、数据存储和其他云服务，以实现复杂的方案。

### <a name="best-practices"></a>最佳做法
请确保：
* API 遵循上面所述的 API 请求和响应协定。 
* API 连接器的 **终结点 URL** 指向正确的 API 终结点。
* API 会显式检查它所依赖的已收到声明是否有 null 值。
* 你的 API 会实现[保护 API 连接器](self-service-sign-up-secure-api-connector.md)中概述的身份验证方法。
* API 可以尽快做出响应，以确保用户体验流畅。
    * Azure AD 最多会等待 20 秒来接收响应。 如果未收到任何内容，它会再次尝试（重试）调用 API。
    * 如果使用无服务器函数或可缩放的 Web 服务，请使用能够在生产环境中让 API 保持“清醒”或“热身”状态的托管计划。 对于 Azure Functions，建议至少使用[高级计划](../../azure-functions/functions-scale.md)
* 确保 API 的高可用性。
* 监视和优化下游 API、数据库或 API 的其他依赖项的性能。
* 终结点必须符合 Azure AD TLS 和密码的安全要求。 有关详细信息，请参阅 [TLS 和密码套件要求](../../active-directory-b2c/https-cipher-tls-requirements.md)。 
 
### <a name="use-logging"></a>使用日志记录

一般情况下，使用 Web API 服务（例如 [Application insights](../../azure-functions/functions-monitoring.md)）启用的日志记录工具来监视 API 是否出现意外错误代码、异常和性能不佳的情况会很有帮助。
* 监视非 HTTP 200 或 400 的 HTTP 状态代码。
* 401 或 403 HTTP 状态代码通常指示身份验证出现了问题。 反复检查 API 的身份验证层以及 API 连接器中的相应配置。
* 如果需要，请在开发中使用更主动的日志记录级别（例如“跟踪”或“调试”）。
* 监视 API 的响应时间是否太长。 

## <a name="next-steps"></a>后续步骤
- 了解如何[将自定义审批工作流添加到自助注册](self-service-sign-up-add-approvals.md)
- 参考[快速入门示例](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)帮助自己入门。
