---
title: 使用 Azure Active Directory B2C 保护 Azure API 管理 API
description: 了解如何使用 Azure Active Directory B2C 颁发的访问令牌来保护 Azure API 管理 API 终结点。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6dd813d2bd046d6b4256bd17b447b8d5660bdea2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564049"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>使用 Azure AD B2C 保护 Azure API 管理 API

了解如何仅限经过 Azure Active Directory B2C (Azure AD B2C) 身份验证的客户端访问 Azure API 管理 API。 按照本文中的说明在 Azure API 管理中创建并测试一个入站策略，该策略仅允许那些包含 Azure AD B2C 颁发的有效访问令牌的请求进行访问。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保已准备好以下资源：

* 一个 [Azure AD B2C 租户](tutorial-create-tenant.md)
* [在你的租户中注册的应用程序](tutorial-register-applications.md)
* [在你的租户中创建的用户流](tutorial-create-user-flows.md)
* Azure API 管理中的一个[已发布的 API](../api-management/import-and-publish.md)
* （可选）一个用来测试安全访问的 [Postman 平台](https://www.getpostman.com/)

## <a name="get-azure-ad-b2c-application-id"></a>获取 Azure AD B2C 应用程序 ID

使用 Azure AD B2C 保护 Azure API 管理中的 API 时，你需要为你在 Azure API 管理中创建的[入站策略](../api-management/api-management-howto-policies.md)提供多个值。 首先，请记下以前在 Azure AD B2C 租户中创建的应用程序的 ID。 如果你使用所创建的应用程序来满足先决条件，请使用 webbapp1 的应用程序 ID。

若要在 Azure AD B2C 租户中注册应用程序，可以使用新的、统一的“应用注册”体验，也可以使用旧版“应用程序”体验。 详细了解[新的注册体验](./app-registrations-training-guide.md)。

# <a name="app-registrations"></a>[应用注册](#tab/app-reg-ga/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在左侧窗格中，选择“Azure AD B2C”。 另外，你还可以选择“所有服务”，然后搜索并选择“Azure AD B2C”。 
1. 选择“应用注册”，然后选择“拥有的应用程序”选项卡 。
1. 记录 webapp1 或你之前创建的其他应用程序的“应用程序(客户端) ID”列中的值。

# <a name="applications-legacy"></a>[应用程序(旧版)](#tab/applications-legacy/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在左侧窗格中，选择“Azure AD B2C”。 另外，你还可以选择“所有服务”，然后搜索并选择“Azure AD B2C”。 
1. 在“管理”下选择“应用程序(旧版)”。 
1. 记录 webapp1 或你之前创建的其他应用程序的“应用程序 ID”列中的值。

* * *

## <a name="get-a-token-issuer-endpoint"></a>获取令牌颁发者终结点

接下来，获取某个 Azure AD B2C 用户流的已知配置 URL。 还需要获取你想在 Azure API 管理中支持的令牌颁发者终结点 URI。

1. 在 [Azure 门户](https://portal.azure.com)中，转到你的 Azure AD B2C 租户。
1. 在“策略”下，选择“用户流” 。
1. 选择一个现有策略（例如 B2C_1_signupsignin1），然后选择“运行用户流”。
1. 记录页面顶部附近的“运行用户流”标题下显示的超链接中的 URL。 此 URL 是用户流的 OpenID Connect 已知发现终结点。在下一部分，当你在 Azure API 管理中配置入站策略时，将要使用此 URL。

    ![屏幕截图显示了 Azure 门户的“运行用户流”页上的已知 URI 超链接。](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. 选择该超链接以转到 OpenID Connect 已知配置页。
1. 在浏览器中打开的页面上，记录 `issuer` 值。 例如：

    `https://<tenant-name>.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    在下一部分，当你在 Azure API 管理中配置 API 时，将要使用此值。

现在，你应已记下了要在下一部分使用的两个 URL：OpenID Connect 已知配置终结点 URL，以及颁发者 URI。 例如：

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration
https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-the-inbound-policy-in-azure-api-management"></a>在 Azure API 管理中配置入站策略

现已准备好在 Azure API 管理中添加用于验证 API 调用的入站策略。 通过添加一项用于验证访问令牌中的受众和颁发者的 [JSON Web 令牌 (JWT) 验证](../api-management/api-management-access-restriction-policies.md#ValidateJWT)策略，可以确保只接受带有有效令牌的 API 调用。

1. 在 [Azure 门户](https://portal.azure.com)中，转到你的 Azure API 管理实例。
1. 选择“API”。
1. 选择要使用 Azure AD B2C 保护的 API。
1. 选择“设计”选项卡。
1.  在“入站处理”下，选择 \</\> 打开策略代码编辑器。
1. 将以下 `<validate-jwt>` 标记放入 `<inbound>` 策略中，然后执行以下操作：

    a. 使用策略的已知配置 URL 更新 `<openid-config>` 元素中的 `url` 值。  
    b. 使用你之前在 B2C 租户中创建的应用程序（例如 webapp1）的应用程序 ID 更新 `<audience>` 元素。  
    c. 使用前面记下的令牌颁发者终结点更新 `<issuer>` 元素。

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>验证安全 API 访问

若要确保只有经过身份验证的调用方可以访问 API，可以通过使用 [Postman](https://www.getpostman.com/) 调用该 API 来验证 Azure API 管理配置。

若要调用该 API，你需要 Azure AD B2C 颁发的访问令牌和 Azure API 管理订阅密钥。

### <a name="get-an-access-token"></a>获取访问令牌

首先需要由 Azure AD B2C 颁发的令牌，该令牌将在 Postman 的 `Authorization` 标头中使用。 可以使用你作为先决条件之一创建的注册/登录用户流的“立即运行”功能来获取一个令牌。

1. 在 [Azure 门户](https://portal.azure.com)中，转到你的 Azure AD B2C 租户。
1. 在“策略”下，选择“用户流” 。
1. 选择一个现有的注册/登录用户流（例如 B2C_1_signupsignin1）。
1. 对于“应用程序”，请选择“webapp1”。
1. 对于“回复 URL”，请选择 `https://jwt.ms`。
1. 选择“运行用户流”。

    ![屏幕截图显示了 Azure 门户中的注册/登录用户流的“运行用户流”窗格。](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. 完成登录过程。 你应会重定向到 `https://jwt.ms`。
1. 记录浏览器中显示的已编码令牌值。 此令牌值将用于 Postman 中的 Authorization 标头。

    ![屏幕截图显示了 jwt.ms 上显示的已编码令牌值。](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-an-api-subscription-key"></a>获取 API 订阅密钥

调用已发布 API 的客户端应用程序（在本例中为 Postman）必须在其对该 API 发出的 HTTP 请求中包含有效的 API 管理订阅密钥。 若要获取 Postman HTTP 请求中包含的订阅密钥：

1. 在 [Azure 门户](https://portal.azure.com)中，转到你的 Azure API 管理服务实例。
1. 选择 **订阅**。
1. 选择“产品: 无限制”旁边的省略号 (...)，然后选择“显示/隐藏密钥”。 
1. 记录该产品的“主密钥”。 此密钥将用于 Postman 的 HTTP 请求中的 `Ocp-Apim-Subscription-Key` 标头。

![屏幕截图显示了 Azure 门户中的“订阅密钥”页，其中选中了“显示/隐藏密钥”。](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>测试安全 API 调用

使用已记录的访问令牌和 Azure API 管理订阅密钥，你现在可以测试是否已正确配置对 API 的安全访问。

1. 在 [Postman](https://www.getpostman.com/) 中创建新的 `GET` 请求。 对于请求 URL，请指定作为先决条件之一发布的 API 的发言人列表终结点。 例如：

    `https://contosoapim.azure-api.net/conference/speakers`

1. 接下来添加以下标头：

    | 密钥 | 值 |
    | --- | ----- |
    | `Authorization` | 之前记录的已编码令牌值，带有 `Bearer ` 前缀（包括“Bearer”后面的空格） |
    | `Ocp-Apim-Subscription-Key` | 之前记录的 Azure API 管理订阅密钥。 |
    | | |

    GET 请求 URL 和标头 应类似于下图中显示的相应项：

    ![Postman UI 的屏幕截图，其中显示了 GET 请求 URL 和标头。](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. 在 Postman 中，选择“发送”按钮以执行请求。 如果已正确配置所有内容，应会收到一个 JSON 响应，其中包含一系列会议发言人（此处内容不完整）：

    ```json
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>测试不安全的 API 调用

成功发出请求后，接下来请测试失败案例，以确保按预期拒绝使用无效令牌对 API 发出调用。 执行此测试的一种方法是在令牌值中添加或更改一些字符，然后像之前一样运行相同的 `GET` 请求。

1. 在令牌值中添加几个字符以模拟无效令牌。 例如，可以将“INVALID”添加到令牌值，如下所示：

    ![Postman UI 的“标头”部分的屏幕截图，其中显示了添加到令牌的字符串“INVALID”。](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. 选择“发送”按钮以执行该请求。 使用无效令牌时，预期的结果是 `401` 未授权状态代码：

    ```json
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

如果看到 `401` 状态代码，则表示已验证以下事实：只有具有 Azure AD B2C 颁发的有效访问令牌的调用方才能向 Azure API 管理 API 成功发出请求。

## <a name="support-multiple-applications-and-issuers"></a>支持多个应用程序和颁发者

通常有多个应用程序会与单个 REST API 交互。 若要使 API 接受用于多个应用程序的令牌，请将其应用程序 ID 添加到 Azure API 管理入站策略中的 `<audiences>` 元素。

```xml
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

同样，若要支持多个令牌颁发者，请将其终结点 URI 添加到 Azure API 管理入站策略中的 `<issuers>` 元素。

```xml
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>迁移到 b2clogin.com

如果你的某个 Azure API 管理 API 可验证旧 `login.microsoftonline.com` 终结点颁发的令牌，则应迁移该 API 及调用它的应用程序，以使用 [b2clogin.com](b2clogin.md) 颁发的令牌。

可以遵循以下常规过程来执行分步迁移：

1. 在 Azure API 管理入站策略中添加对 b2clogin.com 和 login.microsoftonline.com 颁发的令牌的支持。
1. 一次更新一个应用程序，以从 b2clogin.com 终结点获取令牌。
1. 当所有应用程序都能够正确地从 b2clogin.com 获取令牌后，请从 API 中删除对 login.microsoftonline.com 颁发的令牌的支持。

下面的 Azure API 管理入站策略示例说明了如何接受 b2clogin.com 和 login.microsoftonline.com 颁发的令牌。 另外，此策略还支持来自两个应用程序的 API 请求。

```xml
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>后续步骤

有关 Azure API 管理策略的其他信息，请参阅 [Azure API 管理策略参考索引](../api-management/api-management-policies.md)。

若要了解如何将基于 OWIN 的 Web API 及其应用程序迁移到 b2clogin.com，请参阅[将基于 OWIN 的 Web API 迁移到 b2clogin.com](multiple-token-endpoints.md)。
