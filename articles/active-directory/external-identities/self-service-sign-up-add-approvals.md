---
title: 将自定义审批添加到自助注册流 - Azure AD
description: 为外部标识自助注册中的自定义审批工作流添加 API 连接器 - Azure Active Directory (Azure AD)
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
ms.openlocfilehash: c5d12c45554897a9acfef1d32a2216d1eb410133
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459551"
---
# <a name="add-a-custom-approval-workflow-to-self-service-sign-up"></a>将自定义审批工作流添加到自助注册

使用 [API 连接器](api-connectors-overview.md)可将自己的自定义审批工作流与自助注册相集成，以便可以管理要在租户中创建哪些来宾用户帐户。

本文提供演示如何与审批系统相集成的示例。 在此示例中，自助注册用户流会在注册过程中收集用户数据，并将其传递到审批系统。 然后，审批系统可以：

- 自动审批用户并允许 Azure AD 创建用户帐户。
- 触发手动评审。 如果请求得到批准，则审批系统将使用 Microsoft Graph 来预配用户帐户。 审批系统还可以通知用户已创建其帐户。

> [!IMPORTANT]
>
> - 从 2021 年 7 月 12 日开始，如果 Azure AD B2B 客户设置了新的 Google 集成，将其用于自定义应用程序或业务线应用程序的自助注册，则在身份验证转移到系统 Web 视图之前，无法使用 Google 标识进行身份验证。 [了解详细信息](google-federation.md#deprecation-of-web-view-sign-in-support)。
> - 从 2021 年 9 月 30 日开始，Google 将[弃用嵌入式 Web 视图登录支持](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 如果你的应用使用嵌入式 Web 视图对用户进行身份验证，而你将 Google 联合身份验证与 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 或 Azure AD B2B 配合使用来进行[外部用户邀请](google-federation.md)或[自助注册](identity-providers.md)，则 Google Gmail 用户将无法进行身份验证。 [了解详细信息](google-federation.md#deprecation-of-web-view-sign-in-support)。

## <a name="register-an-application-for-your-approval-system"></a>为审批系统注册应用程序

需要在 Azure AD 租户中将审批系统注册为应用程序，使它可以在 Azure AD 中进行身份验证，并拥有创建用户的权限。 详细了解 [Microsoft Graph 的身份验证和授权基础知识](/graph/auth/auth-concepts)。

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，依次选择“应用注册”、“新建注册”。 
4. 输入应用程序的 **名称**，例如“注册审批”。

   <!-- ![Register an application for the approval system](./self-service-sign-up-add-approvals/approvals/register-an-approvals-application.png) -->

5. 选择“注册”。 可将其他字段保留默认值。

   ![突出显示“注册”按钮的屏幕截图。](media/self-service-sign-up-add-approvals/register-approvals-app.png)

6. 在左侧菜单中的“管理”下，依次选择“API 权限”、“添加权限”。  
7. 在“请求 API 权限”页上，依次选择“Microsoft Graph”、“应用程序权限”。  
8. 在“选择权限”下展开“用户”，然后选中“User.ReadWrite.All”复选框。   此权限允许审批系统在审批时创建用户。 然后选择“添加权限”。

   ![注册应用程序页面](media/self-service-sign-up-add-approvals/request-api-permissions.png)

9. 在“API 权限”页上，依次选择“为 (你的租户名称) 授予管理员同意”、“是”。  
10. 在左侧菜单中的“管理”下，依次选择“证书和机密”、“新建客户端机密”。  
11. 输入机密的 **说明**（例如“审批客户端机密”），然后选择客户端机密的 **过期时间**。 然后选择“添加”  。
12. 复制客户端机密的值。

    ![复制客户端机密以便在审批系统中使用](media/self-service-sign-up-add-approvals/client-secret-value-copy.png)

13. 将审批系统配置为使用“应用程序 ID”作为客户端 ID，并使用生成的 **客户端机密** 在 Azure AD 中进行身份验证。

## <a name="create-the-api-connectors"></a>创建 API 连接器

接下来，为自助注册用户流[创建 API 连接器](self-service-sign-up-add-api-connector.md#create-an-api-connector)。 审批系统 API 需要两个连接器和相应的终结点，如以下示例所示。 这些 API 连接器执行以下操作：

- **检查审批状态**。 在用户使用标识提供者登录后立即向审批系统发送调用，以检查该用户是否存在现有的审批请求或者已被拒绝。 如果审批系统仅执行自动审批决策，则可能不需要此 API 连接器。 “检查审批状态”API 连接器的示例。

  ![检查审批状态 - API 连接器配置](./media/self-service-sign-up-add-approvals/check-approval-status-api-connector-config-alt.png)

- **请求审批** - 在用户填写特性收集页之后，但在用户帐户创建之前，向审批系统发送调用以请求审批。 可以自动同意或手动评审审批请求。 “请求审批”API 连接器的示例。 

  ![“请求审批”API 连接器配置](./media/self-service-sign-up-add-approvals/create-approval-request-api-connector-config-alt.png)

若要创建这些连接器，请遵循[创建 API 连接器](self-service-sign-up-add-api-connector.md#create-an-api-connector)中的步骤。

## <a name="enable-the-api-connectors-in-a-user-flow"></a>在用户流中启用 API 连接器

现在，使用以下步骤将 API 连接器添加到自助注册用户流：

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 选择“用户流”，然后选择要为其启用 API 连接器的用户流。
5. 选择“API 连接器”，然后选择要在执行用户流中的以下步骤时调用的 API 终结点：

   - 在登录期间使用标识提供者进行联合身份验证之后：选择审批状态 API 连接器，例如“检查审批状态”。
   - **创建用户之前**：选择审批请求 API 连接器，例如“请求审批”。

   ![将 API 添加到用户流](./media/self-service-sign-up-add-approvals/api-connectors-user-flow-api.png)

6. 选择“保存”。

## <a name="control-the-sign-up-flow-with-api-responses"></a>使用 API 响应控制注册流

调用审批系统后，审批系统可以使用其响应来控制注册流。 

### <a name="request-and-responses-for-the-check-approval-status-api-connector"></a>“检查审批状态”API 连接器的请求和响应

API 从“检查审批状态”API 连接器收到的请求示例：

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google, Facebook, and Email One Time Passcode identity providers 
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

#### <a name="continuation-response-for-check-approval-status"></a>“检查审批状态”的继续响应

如果出现以下情况，“检查审批状态”API 终结点应返回一个继续响应：

- 用户以前未曾请求审批。

继续响应的示例：

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

#### <a name="blocking-response-for-check-approval-status"></a>“检查审批状态”的阻止响应

如果出现以下情况，“检查审批状态”API 终结点应返回一个阻止响应：

- 用户审批有待处理。
- 用户被拒绝，并且不应允许其再次请求审批。

下面是阻止响应的示例：

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your access request is already processing. You'll be notified when your request has been approved.",
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
}
```

### <a name="request-and-responses-for-the-request-approval-api-connector"></a>“请求审批”API 连接器的请求和响应

API 从“请求审批”API 连接器收到的 HTTP 请求示例：

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

#### <a name="continuation-response-for-request-approval"></a>“请求审批”的继续响应

如果出现以下情况，“请求审批”API 终结点应返回一个继续响应：

- 可以 **自动审批** 用户。

继续响应的示例：

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

> [!IMPORTANT]
> 如果收到了继续响应，Azure AD 将创建用户帐户并将用户定向到应用程序。

#### <a name="blocking-response-for-request-approval"></a>“请求审批”的阻止响应

如果出现以下情况，“请求审批”API 终结点应返回一个阻止响应：

- 用户审批请求已创建，目前正在等待处理。
- 自动拒绝了用户审批请求。

下面是阻止响应的示例：

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your account is now waiting for approval. You'll be notified when your request has been approved.",
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
}
```

响应中的 `userMessage` 将向用户显示，例如：

![等待处理的审批页示例](./media/self-service-sign-up-add-approvals/approval-pending.png)

## <a name="user-account-creation-after-manual-approval"></a>手动审批后创建用户帐户

收到手动审批后，自定义审批系统将使用 [Microsoft Graph](/graph/use-the-api) 创建一个[用户](/graph/azuread-users-concept-overview)帐户。 审批系统预配用户帐户的方式取决于用户使用的标识提供者。

### <a name="for-a-federated-google-or-facebook-user-and-email-one-time-passcode"></a>对于 Google 或 Facebook 联合用户和电子邮件一次性密码

> [!IMPORTANT]
> 若要使用此方法，审批系统应显式检查 `identities`、`identities[0]` 和 `identities[0].issuer` 是否存在，以及 `identities[0].issuer` 是否等于“facebook”、“google”或“mail”。

如果用户是使用 Google 或 Facebook 帐户或者电子邮件一次性密码登录的，则你可以使用[用户创建 API](/graph/api/user-post-users?tabs=http)。

1. 审批系统从用户流接收 HTTP 请求。

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. 审批系统使用 Microsoft Graph 创建用户帐户。

```http
POST https://graph.microsoft.com/v1.0/users
Content-type: application/json

{
 "userPrincipalName": "johnsmith_outlook.com#EXT@contoso.onmicrosoft.com",
 "accountEnabled": true,
 "mail": "johnsmith@outlook.com",
 "userType": "Guest",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value"
}
```

| 参数                                           | 必需 | 说明                                                                                                                                                            |
| --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| userPrincipalName                                   | 是      | 可以通过提取发送到 API 的 `email` 声明，将 `@` 字符替换为 `_`，然后将其附加到 `#EXT@<tenant-name>.onmicrosoft.com` 的前面来生成此参数值。 |
| accountEnabled                                      | 是      | 必须设置为 `true`。                                                                                                                                                 |
| mail                                                | 是      | 等效于发送到 API 的 `email` 声明。                                                                                                               |
| userType                                            | 是      | 必须是 `Guest`。 将此用户指定为来宾用户。                                                                                                                 |
| identities                                          | 是      | 联合标识信息。                                                                                                                                    |
| \<otherBuiltInAttribute>                            | 否       | 其他内置特性，例如 `displayName`、`city`，等等。 参数名称与 API 连接器发送的参数相同。                            |
| \<extension\_\{extensions-app-id}\_CustomAttribute> | 否       | 有关用户的自定义特性。 参数名称与 API 连接器发送的参数相同。                                                            |

### <a name="for-a-federated-azure-active-directory-user-or-microsoft-account-user"></a>对于 Azure Active Directory 联合用户或 Microsoft 帐户用户

如果用户使用联合 Azure Active Directory 帐户或 Microsoft 帐户登录，则必须使用[邀请 API](/graph/api/invitation-post) 创建用户，然后选择性地使用[用户更新 API](/graph/api/user-update) 向用户分配其他特性。

1. 审批系统从用户流接收 HTTP 请求。

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. 审批系统使用 API 连接器提供的 `email` 创建邀请。

```http
POST https://graph.microsoft.com/v1.0/invitations
Content-type: application/json

{
    "invitedUserEmailAddress": "johnsmith@fabrikam.onmicrosoft.com",
    "inviteRedirectUrl" : "https://myapp.com"
}
```

响应示例：

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    ...
    "invitedUser": {
        "id": "<generated-user-guid>"
    }
}
```

3. 审批系统根据收集的用户特性，使用受邀用户的 ID 更新用户帐户（可选）。

```http
PATCH https://graph.microsoft.com/v1.0/users/<generated-user-guid>
Content-type: application/json

{
    "displayName": "John Smith",
    "city": "Redmond",
    "extension_<extensions-app-id>_AttributeName": "custom attribute value"
}
```

## <a name="next-steps"></a>后续步骤

- 参考我们的 [Azure Function 快速入门示例](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)帮助自己入门。
- 查看[带有手动审批流程的来宾用户自助注册示例](code-samples-self-service-sign-up.md#custom-approval-workflows)。
