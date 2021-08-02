---
title: 保护访问和数据
description: 保护对输入、输出、基于请求的触发器、运行历史记录、管理任务的访问，以及对 Azure 逻辑应用中的其他资源的访问
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, azla
ms.topic: conceptual
ms.date: 05/01/2021
ms.openlocfilehash: 50087ed6066ba97a866cc2fd40901397a3825e37
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111983917"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>在 Azure 逻辑应用中保护访问和数据

Azure 逻辑应用依赖 [Azure 存储](../storage/index.yml)来存储和自动[加密静态数据](../security/fundamentals/encryption-atrest.md)。 此加密可保护数据，并帮助你履行组织的安全性和符合性承诺。 默认情况下，Azure 存储使用 Microsoft 托管密钥来加密数据。 有关详细信息，请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)。

若要在 Azure 逻辑应用中进一步控制访问并保护敏感数据，可以在以下方面设置更多安全性：

* [对基于请求的触发器的入站调用的访问](#secure-inbound-requests)
* [对逻辑应用操作的访问](#secure-operations)
* [对运行历史记录输入和输出的访问](#secure-run-history)
* [对参数输入的访问](#secure-action-parameters)
* [对其他服务和系统的出站调用的访问](#secure-outbound-requests)
* [阻止为特定连接器创建连接](#block-connections)
* [逻辑应用隔离指导](#isolation-logic-apps)
* [Azure 逻辑应用的 Azure 安全基线](../logic-apps/security-baseline.md)

有关 Azure 中的安全性的详细信息，请参阅以下主题：

* [Azure 加密概述](../security/fundamentals/encryption-overview.md)
* [Azure 静态数据加密](../security/fundamentals/encryption-atrest.md)
* [Azure 安全基准](../security/benchmarks/overview.md)

<a name="secure-inbound-requests"></a>

## <a name="access-for-inbound-calls-to-request-based-triggers"></a>对基于请求的触发器的入站调用的访问

逻辑应用通过基于请求的触发器（例如[请求](../connectors/connectors-native-reqres.md)触发器或 [HTTP Webhook](../connectors/connectors-native-webhook.md) 触发器）接收的入站调用支持加密，并[至少使用传输层安全性 (TLS) 1.2](https://en.wikipedia.org/wiki/Transport_Layer_Security)（以前称为安全套接字层 (SSL)）进行保护。 逻辑应用在收到对请求触发器的入站调用或对 HTTP Webhook 触发器或操作的回调时强制实施此版本。 如果出现 TLS 握手错误，请确保使用 TLS 1.2。 有关详细信息，请参阅[解决 TLS 1.0 问题](/security/solving-tls1-problem)。

入站调用支持以下密码套件：

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

以下列表包括更多方法，你可以使用它们限制对触发器（这些触发器接收对逻辑应用的入站调用）的访问，以便只有经过授权的客户端才能调用逻辑应用：

* [生成共享访问签名 (SAS)](#sas)
* [启用 Azure Active Directory 开放式身份验证 (Azure AD OAuth)](#enable-oauth)
* [通过 Azure API 管理公开逻辑应用](#azure-api-management)
* [限制入站 IP 地址](#restrict-inbound-ip-addresses)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>生成共享访问签名 (SAS)

逻辑应用的每个请求终结点在终结点的 URL 中都包含一个[共享访问签名 (SAS)](/rest/api/storageservices/constructing-a-service-sas)，该 URL 的格式如下：

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

每个 URL 包含下表中所述的 `sp`、`sv` 和 `sig` 查询参数：

| 查询参数 | 说明 |
|-----------------|-------------|
| `sp` | 指定允许使用的 HTTP 方法的权限。 |
| `sv` | 指定用于生成签名的 SAS 版本。 |
| `sig` | 指定用于对触发器访问进行身份验证的签名。 此签名是使用 SHA256 算法生成的，所有 URL 路径和属性中都包含机密访问密钥。 该密钥永远不会公开或发布，而是一直处于加密状态并存储在逻辑应用中。 逻辑应用仅向那些包含有效签名（使用密钥创建）的触发器授权。 |
|||

对请求终结点的入站调用只能使用一个授权方案，即 SAS 或 [Azure Active Directory 开放式身份验证](#enable-oauth)。 尽管使用一个方案不会禁用另一个方案，但同时使用这两个方案会导致错误，因为服务不知道要选择哪个方案。

有关使用 SAS 保护访问的详细信息，请参阅本主题中的以下部分：

* [重新生成访问密钥](#access-keys)
* [创建具有到期日期的回调 URL](#expiring-urls)
* [使用主密钥或辅助密钥创建 URL](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>重新生成访问密钥

若要随时生成新的安全访问密钥，请使用 Azure REST API 或 Azure 门户。 以前使用旧密钥生成的所有 URL 已失效，不再有权触发逻辑应用。 重新生成后检索的 URL 将使用新访问密钥进行签名。

1. 在 [Azure 门户](https://portal.azure.com)中，打开包含要重新生成密钥的逻辑应用。

1. 在逻辑应用菜单的“设置”下，选择“访问密钥” 。

1. 选择要重新生成的密钥并完成生成过程。

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>创建具有到期日期的回调 URL

如果与其他各方共享基于请求的触发器的终结点 URL，可以生成使用特定密钥并具有过期日期的回调 URL。 这样，就可以无缝滚动更新密钥，或者根据特定的时间跨度将触发逻辑应用的访问限制。 若要为 URL 指定过期日期，请使用[逻辑应用 REST API](/rest/api/logic/workflowtriggers)，例如：

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在正文中，使用 JSON 日期字符串包含 `NotAfter` 属性。 该属性返回仅在 `NotAfter` 日期和时间之前有效的回调 URL。

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>创建附带主密钥或辅助密钥的 URL

生成或列出基于请求的触发器的回调 URL 时，可以指定用于对 URL 进行签名的密钥。 若要生成由特定密钥签名的 URL，请使用[逻辑应用 REST API](/rest/api/logic/workflowtriggers)，例如：

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在正文中，包含值为 `Primary` 或 `Secondary` 的 `KeyType` 属性。 此属性返回由指定的安全密钥签名的 URL。

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-open-authentication-azure-ad-oauth"></a>启用 Azure Active Directory 开放式身份验证 (Azure AD OAuth)

对于针对某个终结点（由基于请求的触发器创建）进行的入站调用，可以通过为逻辑应用定义或添加授权策略来启用 [Azure AD OAuth](../active-directory/develop/index.yml)。 这样，入站调用将使用 OAuth [访问令牌](../active-directory/develop/access-tokens.md)进行授权。

当你的逻辑应用收到包含 OAuth 访问令牌的入站请求时，Azure 逻辑应用服务会将令牌的声明与每个授权策略指定的声明进行比较。 如果令牌的声明与至少一个策略中的所有声明之间存在匹配项，则入站请求的授权成功。 令牌的声明数可以大于授权策略指定的声明数。

#### <a name="considerations-before-you-enable-azure-ad-oauth"></a>启用 Azure AD OAuth 之前的注意事项

* 对请求终结点的入站调用只能使用一个授权方案，即 Azure AD OAuth 或[共享访问签名 (SAS)](#sas)。 尽管使用一个方案不会禁用另一个方案，但同时使用这两个方案会导致错误，因为逻辑应用服务不知道要选择哪个方案。

* Azure AD OAuth 访问令牌仅支持[持有者类型](../active-directory/develop/active-directory-v2-protocols.md#tokens)授权方案，这意味着访问令牌的 `Authorization` 标头必须指定 `Bearer` 类型。

* 逻辑应用限制为最大授权策略数。 每个授权策略还具有最大[声明](../active-directory/develop/developer-glossary.md#claim)数。 有关详细信息，请参阅 [Azure 逻辑应用的限制和配置](../logic-apps/logic-apps-limits-and-config.md#authentication-limits)。

* 授权策略必须至少包含颁发者声明，该声明的值（作为 Azure AD 颁发者 ID）以 `https://sts.windows.net/` 或 `https://login.microsoftonline.com/` (OAuth V2) 开头。

  例如，假定逻辑应用有一个需要两个声明类型（“受众”和“颁发者”）的授权策略。  解码的访问令牌的此示例 [有效负载部分](../active-directory/develop/access-tokens.md#payload-claims)包括两种声明类型，其中 `aud` 是 **受众** 值，`iss` 是 **颁发者** 值：

  ```json
  {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
      "iat": 1582056988,
      "nbf": 1582056988,
      "exp": 1582060888,
      "_claim_names": {
         "groups": "src1"
      },
      "_claim_sources": {
         "src1": {
            "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
         }
      },
      "acr": "1",
      "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
      "amr": [
         "rsa",
         "mfa"
      ],
      "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
      "appidacr": "2",
      "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
      "family_name": "Sophia Owen",
      "given_name": "Sophia Owen (Fabrikam)",
      "ipaddr": "167.220.2.46",
      "name": "sophiaowen",
      "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
      "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
      "puid": "1003000000098FE48CE",
      "scp": "user_impersonation",
      "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
      "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
      "unique_name": "SophiaOwen@fabrikam.com",
      "upn": "SophiaOwen@fabrikam.com",
      "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
      "ver": "1.0"
   }
   ```

#### <a name="enable-azure-ad-oauth-for-your-logic-app"></a>为逻辑应用启用 Azure AD OAuth

针对 Azure 门户或 Azure 资源管理器模板执行以下步骤：

<a name="define-authorization-policy-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

在 [Azure 门户](https://portal.azure.com)中，将一个或多个授权策略添加到逻辑应用中：

1. 在 [Azure 门户](https://portal.microsoft.com)的逻辑应用设计器中查找并打开逻辑应用。

1. 在逻辑应用菜单的“设置”下，选择“授权” 。 打开“授权”窗格后，选择“添加策略”。

   ![选择“授权”>“添加策略”](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. 在每次对请求触发器进行入站调用时提供的访问令牌中，逻辑应用需要一些[声明类型](../active-directory/develop/developer-glossary.md#claim)和值，通过指定这些声明类型和值来提供有关授权策略的信息：

   ![提供授权策略的信息](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | 策略名称 | 是 | 要用于授权策略的名称 |
   | **申请** | 是 | 逻辑应用从入站调用接受的声明类型和值。 声明值限制为[最大字符数](logic-apps-limits-and-config.md#authentication-limits)。 下面是可用的声明类型： <p><p>- 颁发者 <br>- 受众 <br>- **主题** <br>- **JWT ID**（JSON Web 令牌标识符） <p><p>声明列表必须至少包含颁发者声明，该声明的值（作为 Azure AD 颁发者 ID）以 `https://sts.windows.net/` 或 `https://login.microsoftonline.com/` 开头。  有关这些声明类型的详细信息，请参阅 [Azure AD 安全令牌中的声明](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens)。 你还可以指定自己的声明类型和值。 |
   |||

1. 若要添加其他声明，请从以下选项中进行选择：

   * 若要添加其他声明类型，请选择“添加标准声明”，选择声明类型，并指定声明值。

   * 若要添加你自己的声明，请选择“添加自定义声明”。 有关详细信息，请参阅[如何向应用提供可选声明](../active-directory/develop/active-directory-optional-claims.md)。 然后，自定义声明将存储为 JWT ID 的一部分；例如 `"tid": "72f988bf-86f1-41af-91ab-2d7cd011db47"`。 

1. 若要添加其他授权策略，请选择“添加策略”。 重复前面的步骤以设置策略。

1. 完成后，选择“保存”。

1. 若要在基于请求的触发器输出中包含访问令牌的 `Authorization` 标头，请参阅[在请求触发器输出中包含“Authorization”标头](#include-auth-header)。

工作流属性（例如策略）不会显示在 Azure 门户上逻辑应用的代码视图中。 若要以编程方式访问策略，请通过 Azure 资源管理器调用以下 API：`https://management.azure.com/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Logic/workflows/{your-workflow-name}?api-version=2016-10-01&_=1612212851820`。 请务必替换 Azure 订阅 ID、资源组名称和工作流名称的占位符值。

<a name="define-authorization-policy-template"></a>

#### <a name="resource-manager-template"></a>[资源管理器模板](#tab/azure-resource-manager)

在 ARM 模板中，按照以下步骤和语法定义授权策略：

1. 在[逻辑应用的资源定义](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)的 `properties` 部分中，如果不存在任何 `accessControl` 对象，请添加一个包含 `triggers` 对象的该对象。

   有关 `accessControl` 对象的详细信息，请参阅[限制 Azure 资源管理器模板中的入站 IP 范围](#restrict-inbound-ip-template)和 [Microsoft.Logic 工作流模板参考](/azure/templates/microsoft.logic/2019-05-01/workflows)。

1. 在 `triggers` 对象中，添加一个 `openAuthenticationPolicies` 对象，后者包含你在其中定义了一个或多个授权策略的 `policies` 对象。

1. 提供授权策略的名称，将策略类型设置为 `AAD`，并包括一个 `claims` 数组，你在其中指定一个或多个声明类型。

   `claims` 数组至少必须包含颁发者声明类型，你在其中将声明的 `name` 属性设置为 `iss`，并将 `value` 设置为以 `https://sts.windows.net/` 或 `https://login.microsoftonline.com/` 开头的 Azure AD 颁发者 ID。 有关这些声明类型的详细信息，请参阅 [Azure AD 安全令牌中的声明](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens)。 你还可以指定自己的声明类型和值。

1. 若要在基于请求的触发器输出中包含访问令牌的 `Authorization` 标头，请参阅[在请求触发器输出中包含“Authorization”标头](#include-auth-header)。

下面是要遵循的语法：

```json
"resources": [
   {
      // Start logic app resource definition
      "properties": {
         "state": "<Enabled-or-Disabled>",
         "definition": {<workflow-definition>},
         "parameters": {<workflow-definition-parameter-values>},
         "accessControl": {
            "triggers": {
               "openAuthenticationPolicies": {
                  "policies": {
                     "<policy-name>": {
                        "type": "AAD",
                        "claims": [
                           {
                              "name": "<claim-name>",
                              "value": "<claim-value>"
                           }
                        ]
                     }
                  }
               }
            },
         },
      },
      "name": "[parameters('LogicAppName')]",
      "type": "Microsoft.Logic/workflows",
      "location": "[parameters('LogicAppLocation')]",
      "apiVersion": "2016-06-01",
      "dependsOn": [
      ]
   }
   // End logic app resource definition
],
```

---

<a name="include-auth-header"></a>

#### <a name="include-authorization-header-in-request-trigger-outputs"></a>在请求触发器输出中包括“Authorization”标头

如果逻辑应用[启用 Azure Active Directory 开放式身份验证 (Azure AD OAuth)](#enable-oauth) 来授权入站调用访问基于请求的触发器，你可以使请求触发器或 HTTP Webhook 触发器输出包括 OAuth 访问令牌的 `Authorization` 标头。 在触发器的基础 JSON 定义中，添加 `operationOptions` 属性并将其设置为 `IncludeAuthorizationHeadersInOutputs`。 下面是请求触发器的示例：

```json
"triggers": {
   "manual": {
      "inputs": {
         "schema": {}
      },
      "kind": "Http",
      "type": "Request",
      "operationOptions": "IncludeAuthorizationHeadersInOutputs"
   }
}
```

有关详细信息，请参阅以下主题：

* [触发器和操作类型的架构参考 - 请求触发器](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)
* [触发器和操作类型的架构参考 - HTTP Webhook 触发器](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [触发器和操作类型的架构参考 - 操作选项](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)

<a name="azure-api-management"></a>

### <a name="expose-your-logic-app-with-azure-api-management"></a>通过 Azure API 管理公开逻辑应用

有关更多身份验证协议和选项，请考虑使用 Azure API Management 将逻辑应用公开为 API。 此服务可为任何终结点提供丰富的监视、安全性、策略和文档功能。 API 管理可以公开逻辑应用的公共或专用终结点。 若要授予对此终结点的访问权限，可以使用 Azure Active Directory Open Authorization (Azure AD OAuth)、客户端证书或其他安全标准。 当 API Management 收到请求时，此服务会将请求发送到逻辑应用，并会进行任何必要的转换或限制。 若要仅让 API 管理调用你的逻辑应用，可以[限制你的逻辑应用的入站 IP 地址](#restrict-inbound-ip)。

有关详细信息，请查看以下文档：

* [关于 API 管理](../api-management/api-management-key-concepts.md)
* [在 Azure API 管理中使用 OAuth 2.0 授权和 Azure AD 保护 Web API 后端](../api-management/api-management-howto-protect-backend-with-aad.md)
* [使用 API 管理中的客户端证书身份验证确保 API 安全](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [API 管理身份验证策略](../api-management/api-management-authentication-policies.md)

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>限制入站 IP 地址

除了共享访问签名 (SAS) 以外，还可以限制可调用逻辑应用的特定客户端。 例如，如果使用 [Azure API 管理](../api-management/api-management-key-concepts.md)来管理请求终结点，则可将逻辑应用限制为仅接受来自[你创建的 API 管理服务实例](../api-management/get-started-create-service-instance.md)的 IP 地址的请求。

无论指定的 IP 地址如何，都仍可通过[逻辑应用 REST API: 工作流触发器 - 运行](/rest/api/logic/workflowtriggers/run)请求或 API 管理来运行具有基于请求的触发器的逻辑应用。 不过，这种情况下仍需要针对 Azure REST API 进行[身份验证](../active-directory/develop/authentication-vs-authorization.md)。 所有事件都会显示在 Azure 审核日志中。 请确保相应地设置访问控制策略。

若要限制逻辑应用的入站 IP 地址，请针对 Azure 门户或 Azure 资源管理器模板执行以下步骤：

<a name="restrict-inbound-ip-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

在 [Azure 门户](https://portal.azure.com)中，此筛选器会影响触发器和操作，这与门户中“允许的入站 IP 地址”下的说明相反。 若要分别为触发器和操作设置此筛选器，请在逻辑应用的 Azure 资源管理器模板中使用 `accessControl` 对象，或者使用[逻辑应用 REST API：工作流 -“创建”或“更新”操作](/rest/api/logic/workflows/createorupdate)。

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用的菜单中，在“设置”下，选择“工作流设置”。

1. 在“访问控制配置”部分的“允许的入站 IP 地址”下，选择方案的路径： 

   * 若要使你的逻辑应用只能通过内置的 [Azure 逻辑应用操作](../logic-apps/logic-apps-http-endpoint.md)作为嵌套逻辑应用调用，请选择“仅限其他逻辑应用”。仅当使用 **Azure 逻辑应用** 操作调用嵌套的逻辑应用时，此选项才起作用。

     此选项会将一个空数组写入逻辑应用资源，并要求只有使用内置 **Azure 逻辑应用** 操作的父逻辑应用发出的调用才能触发嵌套逻辑应用。

   * 若要使你的逻辑应用只能通过 HTTP 操作作为嵌套应用进行调用，请选择“特定 IP 范围”，而不要选择“仅限其他逻辑应用”。 当“触发器的 IP 范围”框出现时，请输入父逻辑应用的[出站 IP 地址](../logic-apps/logic-apps-limits-and-config.md#outbound)。 有效的 IP 范围使用以下格式：*x.x.x.x/x* 或 *x.x.x.x-x.x.x.x*。

     > [!NOTE]
     > 如果使用“仅限其他逻辑应用”选项和 HTTP 操作来调用嵌套逻辑应用，调用将被阻止，并出现“401 未授权”错误。

   * 如果你想要限制来自其他 IP 的入站调用，当“触发器的 IP 范围”框出现时，请指定触发器接受的 IP 地址范围。 有效的 IP 范围使用以下格式：*x.x.x.x/x* 或 *x.x.x.x-x.x.x.x*。

1. （可选）在“仅限提供的 IP 地址发出从运行历史记录中获取输入和输出消息的调用”下，可以指定能够访问运行历史记录中输入和输出消息的入站调用的 IP 地址范围。

<a name="restrict-inbound-ip-template"></a>

#### <a name="resource-manager-template"></a>[资源管理器模板](#tab/azure-resource-manager)

在 ARM 模板中，在逻辑应用的资源定义中使用 `accessControl` 节指定允许的入站 IP 地址范围。 在此节中，适当地使用 `triggers`、`actions` 和可选的 `contents` 节，方法是包含具有 `addressRange` 属性的 `allowedCallerIpAddresses` 节，并将属性值设置为 *x.x.x.x/x* 或 *x.x.x.x-x.x.x.x* 格式的允许 IP 范围。

* 如果嵌套的逻辑应用使用“仅限其他逻辑应用”选项（该选项仅允许来自使用内置 Azure 逻辑应用操作的其他逻辑应用的入站调用），请将 `allowedCallerIpAddresses` 属性设置为空数组 ( **[]** )，并省略 `addressRange` 属性。

* 如果嵌套的逻辑应用对其他入站调用使用“特定 IP 范围”选项（例如，使用 HTTP 操作的其他逻辑应用），请包含 `allowedCallerIpAddresses` 节，并将 `addressRange` 属性设置为允许的 IP 范围。

此示例显示了一个嵌套逻辑应用的资源定义，该嵌套逻辑应用只允许来自使用内置 Azure 逻辑应用操作的逻辑应用的入站调用：

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": []
               },
               "actions": {
                  "allowedCallerIpAddresses": []
               },
               // Optional
               "contents": {
                  "allowedCallerIpAddresses": []
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

此示例显示了一个嵌套逻辑应用的资源定义，该嵌套逻辑应用允许来自使用 HTTP 操作的逻辑应用的入站调用：

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

---

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>对逻辑应用操作的访问

可以仅允许特定的用户或组运行特定的任务，例如管理、编辑和查看逻辑应用。 若要控制其权限，请使用 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)，这样就能够为 Azure 订阅中的成员分配自定义角色或内置角色：

* [逻辑应用参与者](../role-based-access-control/built-in-roles.md#logic-app-contributor)：允许管理逻辑应用，但不允许更改其访问权限。

* [逻辑应用操作员](../role-based-access-control/built-in-roles.md#logic-app-operator)：允许你读取、启用和禁用逻辑应用，但不能对其进行编辑或更新。

要防止他人更改或删除逻辑应用，可以使用 [Azure 资源锁](../azure-resource-manager/management/lock-resources.md)。 此功能可以防止他人更改或删除生产资源。

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>对运行历史记录数据的访问

在逻辑应用运行期间，所有数据都是使用传输层安全性 (TLS) [在传输过程中加密的](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)以及[静态](../security/fundamentals/encryption-atrest.md)加密的。 运行逻辑应用后，可以查看该运行的历史记录，包括已运行的步骤以及每个操作的状态、持续时间、输入和输出。 此丰富的详细信息可让你深入了解逻辑应用的运行方式，以及开始解决出现的任何问题的位置。

查看逻辑应用的运行历史记录时，逻辑应用将对该访问进行身份验证，然后提供指向请求的输入和输出以及每个运行的响应的链接。 但是，对于处理任何密码、机密、密钥或其他敏感信息的操作，你需要阻止他人查看和访问该数据。 例如，如果逻辑应用从 [Azure Key Vault](../key-vault/general/overview.md) 获取机密，以便在对 HTTP 操作进行身份验证时使用，则你需要在视图中隐藏该机密。

若要控制对逻辑应用运行历史记录中的输入和输出的访问，可使用以下选项：

* [按 IP 地址范围限制访问](#restrict-ip)。

  此选项可帮助你基于来自特定 IP 地址范围的请求保护对运行历史记录的访问。

* [使用模糊处理保护运行历史记录中的数据](#obfuscate)。

  在许多触发器和操作中，可以保护逻辑应用的运行历史记录中的输入和/或输出。

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>按 IP 地址范围限制访问

可以限制对逻辑应用运行历史记录中的输入和输出的访问，以便只有来自特定 IP 地址范围的请求才能查看这些数据。

例如，若要阻止任何人访问输入和输出，请指定类似于 `0.0.0.0-0.0.0.0` 的 IP 地址范围。 只有拥有管理员权限的人员才能去除此限制，使“实时”访问逻辑应用的数据成为可能。

若要指定允许的 IP 范围，请针对 Azure 门户或 Azure 资源管理器模板执行以下步骤：

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用的菜单中，在“设置”下，选择“工作流设置”。

1. 在“访问控制配置” > “允许的入站 IP 地址”下，选择“特定 IP 范围”  。

1. 在“内容的 IP 范围”下，指定可以访问输入和输出中内容的 IP 地址范围。

   有效的 IP 范围使用这些格式：x.x.x.x/x 或 x.x.x.x-x.x.x.x 

#### <a name="resource-manager-template"></a>[资源管理器模板](#tab/azure-resource-manager)

在 ARM 模板中逻辑应用的资源定义中使用 `accessControl` 节和 `contents` 节指定 IP 范围，例如：

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

---

<a name="obfuscate"></a>

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>使用模糊处理保护运行历史记录中的数据

许多触发器和操作具有用于保护逻辑应用的运行历史记录中的输入和/或输出的设置。 在使用这些设置帮助保护此数据之前，请查看以下注意事项：

* 遮盖触发器或操作的输入或输出时，逻辑应用不会将安全数据发送到 Azure Log Analytics。 此外，不能将[跟踪的属性](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)添加到该触发器或操作进行监视。

* [用于处理工作流历史记录的逻辑应用 API](/rest/api/logic/) 不会返回受保护的输出。

* 若要保护可遮盖输入或显式遮盖输出的操作的输出，请在该操作中手动打开“安全输出”。

* 请确保在你希望运行历史记录来遮盖该数据的下游操作中打开“安全输入”或“安全输出” 。

  安全输出设置

  在触发器或操作中手动打开“安全输出”时，逻辑应用会隐藏运行历史记录中的这些输出。 如果下游操作显式使用这些安全输出作为输入，则逻辑应用会隐藏运行历史记录中的此操作的输入，但不会启用操作的“安全输入”设置。

  ![用作输入的受保护输出，以及对大多数操作的下游影响](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  “撰写”、“分析 JSON”和“响应”操作仅提供“保护输入”设置。 启用后，此设置也会隐藏这些操作的输出。 如果这些操作显式使用上游的受保护输出作为输入，则逻辑应用会隐藏这些操作的输入和输出，但不会启用这些操作的“保护输入”设置。 如果下游操作显式使用“撰写”、“分析 JSON”或“响应”操作中隐藏的输出作为输入，则逻辑应用不会隐藏此下游操作的输入或输出。

  ![用作输入的受保护输出，以及对特定操作的下游影响](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  安全输入设置

  在触发器或操作中手动打开“安全输入”时，逻辑应用会隐藏运行历史记录中的这些输入。 如果下游操作显式使用该触发器或操作中的可见输出作为输入，则逻辑应用会隐藏运行历史记录中的此下游操作的输入，但不会启用此操作中的“安全输入”，并且不会隐藏此操作的输出。

  ![受保护的输入以及对大多数操作的下游影响](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  如果“撰写”、“分析 JSON”和“响应”操作显式使用具有受保护输入的触发器或操作中的可见输出，则逻辑应用将隐藏这些操作的输入和输出，但不会启用这些操作的“保护输入”设置。 如果下游操作显式使用“撰写”、“分析 JSON”或“响应”操作中隐藏的输出作为输入，则逻辑应用不会隐藏此下游操作的输入或输出。

  ![受保护的输入以及对特定操作的下游影响](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>保护设计器中的输入和输出

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

   ![在逻辑应用设计器中打开逻辑应用](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. 在要保护敏感数据的触发器或操作中，选择省略号 ( **...** ) 按钮，然后选择“设置”。

   ![打开触发器或操作设置](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. 打开“安全输入”和/或“安全输出” 。 完成后，选择“完成”。

   ![打开“安全输入”或“安全输出”](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   操作或触发器现在显示标题栏中的锁图标。

   ![操作或触发器标题栏显示锁形图标](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   表示以前操作的安全输出的令牌也显示锁图标。 例如，从动态内容列表中选择要在操作中使用的此类输出时，该令牌将显示锁图标。

   ![为安全输出选择令牌](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. 运行逻辑应用后，可以查看该运行的历史记录。

   1. 在逻辑应用的”概述“窗格中，选择要查看的运行。

   1. 在“逻辑应用运行”窗格中，展开要查看的操作。

      如果选择同时遮盖输入和输出，这些值现在将处于隐藏状态。

      ![隐藏运行历史记录中的输入和输出](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>保护代码视图中的输入和输出

在基础触发器或操作定义中，使用以下两个值中的一个或两个值添加或更新 `runtimeConfiguration.secureData.properties` 数组：

* `"inputs"`：在运行历史记录中保护输入。
* `"outputs"`：保护运行历史记录中的输出。

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>对参数输入的访问

如果跨不同的环境进行部署，请考虑参数化工作流定义中因这些环境而有所不同的值。 这样，便可以通过使用 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)部署逻辑应用来避免使用硬编码数据，通过定义安全的参数来保护敏感数据，并使用[参数文件](../azure-resource-manager/templates/parameter-files.md)通过[模板的参数](../azure-resource-manager/templates/parameters.md)将该数据作为单独输入传递。

例如，如果使用 [Azure Active Directory 开放式身份验证](#azure-active-directory-oauth-authentication) (Azure AD OAuth) 对 HTTP 操作进行身份验证，则可以定义并遮盖接受用于身份验证的客户端 ID 和客户端机密的参数。 若要在逻辑应用中定义这些参数，请使用逻辑应用的工作流定义中的 `parameters` 部分，以及用于部署的资源管理器模板。 若要帮助保护在编辑逻辑应用或查看运行历史记录时不希望显示的参数值，请使用 `securestring` 或 `secureobject` 类型定义参数并根据需要使用编码。 具有此类型的参数不会随资源定义一起返回，并且在部署后无法通过查看资源来访问这些参数。 若要在运行时访问这些参数值，请在工作流定义中使用 `@parameters('<parameter-name>')` 表达式。 此表达式仅在运行时进行计算，由[工作流定义语言](../logic-apps/logic-apps-workflow-definition-language.md)描述。

> [!NOTE]
> 如果在请求标头或正文中使用参数，查看逻辑应用的运行历史记录和传出的 HTTP 请求时，该参数可能是可见的。 请务必同时相应地设置内容访问策略。
> 还可以使用[模糊处理](#obfuscate)在运行历史记录中隐藏输入和输出。 始终不能通过输入或输出看见授权标头。 因此，如果在此处使用机密，则无法检索该机密。

有关详细信息，请参阅本主题中的以下部分：

* [在工作流定义中保护参数](#secure-parameters-workflow)
* [使用模糊处理保护运行历史记录中的数据](#obfuscate)

如果[使用资源管理器模板自动执行逻辑应用部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，则可以使用 `securestring` 和 `secureobject` 类型定义在部署时计算的安全[模板参数](../azure-resource-manager/templates/parameters.md)。 若要定义模板参数，请使用模板的顶级 `parameters` 节，该节不同于工作流定义的 `parameters` 节。 若要提供模板参数的值，请使用单独的[参数文件](../azure-resource-manager/templates/parameter-files.md)。

例如，如果使用机密，则可以定义并使用可在部署时从 [Azure Key Vault](../key-vault/general/overview.md) 检索这些机密的受保护模板参数。 然后，可以在参数文件中引用 Key Vault 和机密。 有关详细信息，请参阅以下主题：

* [在部署时使用 Azure Key Vault 传递敏感值](../azure-resource-manager/templates/key-vault-parameter.md)
* 本主题后面的[在 Azure 资源管理器模板中保护参数](#secure-parameters-deployment-template)

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>保护工作流定义中的参数

若要在逻辑应用工作流定义中保护敏感信息，请使用受保护的参数，以使该信息在保存逻辑应用后不可见。 例如，假设某个 HTTP 操作需要使用用户名和密码进行基本身份验证。 在工作流定义中，`parameters` 节使用 `securestring` 类型定义 `basicAuthPasswordParam` 和 `basicAuthUsernameParam` 参数。 然后，操作定义将引用 `authentication` 节中的这些参数。

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>在 Azure 资源管理器模板中保护参数

逻辑应用的[资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)包含多个 `parameters` 节。 若要保护密码、密钥、机密和其他敏感信息，请使用 `securestring` 或 `secureobject` 类型在模板级别和工作流定义级别定义安全的参数。 然后，可将这些值存储在 [Azure Key Vault](../key-vault/general/overview.md) 中，并使用[参数文件](../azure-resource-manager/templates/parameter-files.md)来引用 Key Vault 和机密。 模板便会在部署时检索该信息。 有关详细信息，请参阅[在部署时使用 Azure Key Vault 传递敏感值](../azure-resource-manager/templates/key-vault-parameter.md)。

下面详细介绍了这些 `parameters` 部分：

* 在模板的最高级别，`parameters` 节定义了模板在部署时使用的值的参数。 例如，这些值可能包含特定部署环境的连接字符串。 然后，你可将这些值存储在单独的[参数文件](../azure-resource-manager/templates/parameter-files.md)中，以方便更改这些值。

* 在逻辑应用的资源定义内部、工作流定义外部，`parameters` 节指定了工作流定义参数的值。 在此节中，可以使用引用模板参数的模板表达式来分配这些值。 这些表达式将在部署时计算。

* 在工作流定义中，`parameters` 节定义了逻辑应用在运行时使用的参数。 然后，你可以使用在运行时计算的工作流定义表达式，在逻辑应用工作流中引用这些参数。

此示例模板包含使用 `securestring` 类型的多个受保护参数定义：

| 参数名称 | 说明 |
|----------------|-------------|
| `TemplatePasswordParam` | 一个模板参数，它接受随后要传递到工作流定义的 `basicAuthPasswordParam` 参数的密码 |
| `TemplateUsernameParam` | 一个模板参数，它接受随后要传递到工作流定义的 `basicAuthUserNameParam` 参数的用户名 |
| `basicAuthPasswordParam` | 一个工作流定义参数，它接受 HTTP 操作中用于基本身份验证的密码 |
| `basicAuthUserNameParam` | 一个工作流定义参数，它接受 HTTP 操作中用于基本身份验证的用户名 |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-outbound-requests"></a>

## <a name="access-for-outbound-calls-to-other-services-and-systems"></a>对其他服务和系统的出站调用的访问

根据目标终结点的功能，[HTTP 触发器或 HTTP 操作](../connectors/connectors-native-http.md)发送的出站调用支持加密，并使用[传输层安全性 (TLS) 1.0、1.1 或 1.2](https://en.wikipedia.org/wiki/Transport_Layer_Security)（以前称为安全套接字层 (SSL)）进行保护。 逻辑应用会就是否使用受支持的最高可能版本一事与目标终结点进行协商。 例如，如果目标终结点支持 1.2 版，则 HTTP 触发器或操作会首先使用 1.2 版。 否则，连接器将使用下一个受支持的最高版本。

下面是与 TLS/SSL 自签名证书有关的信息：

* 对于全局多租户 Azure 逻辑应用环境中的逻辑应用，HTTP 操作不允许自签名的 TLS/SSL 证书。 如果你的逻辑应用向服务器发出 HTTP 调用并提供了 TLS/SSL 自签名证书，则 HTTP 调用将失败并出现 `TrustFailure` 错误。

* 对于单租户 Azure 逻辑应用环境中的逻辑应用，HTTP 操作支持自签名的 TLS/SSL 证书。 但是，必须为此身份验证类型完成一些额外的步骤。 否则，调用会失败。 有关详细信息，请参阅[单租户 Azure 逻辑应用的 TSL/SSL 证书身份验证](../connectors/connectors-native-http.md#tsl-ssl-certificate-authentication)。

  如果要将客户端证书或 Azure Active Directory Open Authorization (Azure AD OAuth) 与“证书”凭据类型一起使用，则仍必须为此身份验证类型完成一些额外的步骤。 否则，调用会失败。 有关详细信息，请参阅[单租户 Azure 逻辑应用的客户端证书或凭据类型为“证书”的 Azure Active Directory Open Authorization (Azure AD OAuth)](../connectors/connectors-native-http.md#client-certificate-authentication)。

* 对于[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中的逻辑应用，HTTP 连接器允许使用自签名证书完成 TLS/SSL 握手。 但是，必须先使用逻辑应用 REST API 为现有 ISE 或新 ISE [启用自签名证书支持](../logic-apps/create-integration-service-environment-rest-api.md#request-body)，并在 `TrustedRoot` 位置安装公共证书。

下面提供了更多方法，它们有助于保护对逻辑应用发送的调用进行处理的终结点：

* [针对出站请求添加身份验证](#add-authentication-outbound)。

  当使用 HTTP 触发器或操作来发送出站调用时，可以针对逻辑应用发送的请求添加身份验证。 例如，可以选择以下身份验证类型：

  * [基本身份验证](#basic-authentication)

  * [客户端证书身份验证](#client-certificate-authentication)

  * [Active Directory OAuth 身份验证](#azure-active-directory-oauth-authentication)

  * [托管标识身份验证](#managed-identity-authentication)

* 限制来自逻辑应用 IP 地址的访问。

  从逻辑应用对终结点发出的所有调用都来源于基于逻辑应用区域的指定 IP 地址。 可以添加仅接受来自这些 IP 地址的请求的筛选规则。 若要获取这些 IP 地址，请参阅 [Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md#firewall-ip-configuration)。

* 提高与本地系统的连接的安全性。

  Azure 逻辑应用可与这些服务集成，来帮助提供更安全可靠的本地通信。

  * 本地数据网关

    Azure 逻辑应用中的许多托管连接器都可以安全连接到本地系统，例如，文件系统、SQL、SharePoint 和 DB2。 网关通过 Azure 服务总线发送来自加密通道上的本地源的数据。 所有流量最初都是网关代理的安全出站流量。 了解[本地数据网关的工作原理](logic-apps-gateway-install.md#gateway-cloud-service)。

  * 通过 Azure API 管理进行连接

    [Azure API 管理](../api-management/api-management-key-concepts.md)提供本地连接选项，例如站点到站点虚拟专用网络和 [ExpressRoute](../expressroute/expressroute-introduction.md) 集成，用于保护代理和与本地系统的通信。 如果你有一个提供对你的本地系统的访问权限的 API，并且通过创建 [API 管理服务实例](../api-management/get-started-create-service-instance.md)公开了该 API，则可通过在逻辑应用设计器中选择内置的 API 管理触发器或操作在逻辑应用的工作流中调用该 API。

    > [!NOTE]
    > 连接器仅显示那些你有权在其中进行查看和连接操作的 API 管理服务，但不显示基于消耗的 API 管理服务。

    1. 在逻辑应用设计器的搜索框中输入 `api management`。 根据你是要添加触发器还是操作来选择步骤：<p>

       * 如果要添加始终是工作流中的第一个步骤的触发器，请选择“选择 Azure API 管理触发器”。

       * 如果要添加操作，请选择“选择 Azure API 管理操作”。

       此示例将添加一个触发器：

       ![添加 Azure API 管理触发器](./media/logic-apps-securing-a-logic-app/select-api-management.png)

    1. 选择你之前创建的 API 管理服务实例。

       ![选择 API 管理服务实例](./media/logic-apps-securing-a-logic-app/select-api-management-service-instance.png)

    1. 选择要使用的 API 调用。

       ![选择现有 API](./media/logic-apps-securing-a-logic-app/select-api.png)

<a name="add-authentication-outbound"></a>

### <a name="add-authentication-to-outbound-calls"></a>针对出站调用添加身份验证

HTTP 和 HTTPS 终结点支持各种身份验证。 在用于向这些终结点发送出站调用或请求的某些触发器和操作上，可以指定身份验证类型。 在逻辑应用设计器中，支持选择身份验证类型的触发器和操作具有“身份验证”属性。 但是，此属性可能并不总会默认显示。 在这种情况下，请在触发器或操作中打开“添加新参数”列表，然后选择“身份验证”。 

> [!IMPORTANT]
> 若要保护你的逻辑应用处理的敏感信息，请使用受保护的参数，并根据需要对数据进行编码。
> 有关使用和保护参数的详细信息，请参阅[对参数输入的访问](#secure-action-parameters)。

<a name="authentication-types-supported-triggers-actions"></a>

#### <a name="authentication-types-for-triggers-and-actions-that-support-authentication"></a>支持身份验证的触发器和操作的身份验证类型

此表标识了可以在其中选择身份验证类型的触发器和操作上可用的身份验证类型：

| 身份验证类型 | 支持的触发器和操作 |
|---------------------|--------------------------------|
| [基本](#basic-authentication) | Azure API 管理、Azure 应用服务、HTTP、HTTP + Swagger、HTTP Webhook |
| [客户端证书](#client-certificate-authentication) | Azure API 管理、Azure 应用服务、HTTP、HTTP + Swagger、HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API 管理、Azure 应用服务、Azure Functions、HTTP、HTTP + Swagger、HTTP Webhook |
| [原始](#raw-authentication) | Azure API 管理、Azure 应用服务、Azure Functions、HTTP、HTTP + Swagger、HTTP Webhook |
| [托管的标识](#managed-identity-authentication) | **内置触发器和操作** <p><p>Azure API 管理、Azure 应用服务、Azure Functions、HTTP、HTTP Webhook <p><p>**托管的连接器** <p><p>Azure AD 标识保护、Azure 自动化、Azure 容器实例、Azure 数据资源管理器、Azure 数据工厂、Azure Data Lake、Azure 事件网格、Azure IoT Central V3、Azure 密钥保管库、Azure 资源管理器、Azure Sentinel、使用 Azure AD 的 HTTP <p><p>**注意**：对托管连接器的支持目前以预览版提供。 |
|||

<a name="basic-authentication"></a>

#### <a name="basic-authentication"></a>基本身份验证

如果[基本](../active-directory-b2c/secure-rest-api.md)选项可用，请指定以下属性值：

| 属性（设计器） | 属性 (JSON) | 必须 | Value | 说明 |
|---------------------|-----------------|----------|-------|-------------|
| **身份验证** | `type` | 是 | 基本 | 要使用的身份验证类型 |
| **用户名** | `username` | 是 | <*user-name*>| 用于对目标服务终结点访问进行身份验证的用户名 |
| **密码** | `password` | 是 | <*password*> | 用于对目标服务终结点访问进行身份验证的密码 |
||||||

使用[安全参数](#secure-action-parameters)处理和保护敏感信息时，例如，在[用于自动执行部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，可以使用表达式在运行时访问这些参数值。 此示例 HTTP 操作定义将身份验证 `type` 指定为 `Basic`，并使用 [parameters() 函数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)获取参数值：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

#### <a name="client-certificate-authentication"></a>客户端证书身份验证

如果[客户端证书](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)选项可用，请指定以下属性值：

| 属性（设计器） | 属性 (JSON) | 必须 | Value | 说明 |
|---------------------|-----------------|----------|-------|-------------|
| **身份验证** | `type` | 是 | **客户端证书** <br>或 <br>`ClientCertificate` | 可使用的身份验证类型。 可以使用 [Azure API 管理](../api-management/api-management-howto-mutual-certificates.md)来管理证书。 <p></p>**注意**：对于入站和出站调用，自定义连接器不支持基于证书的身份验证。 |
| **Pfx** | `pfx` | 是 | <*encoded-pfx-file-content*> | 个人信息交换 (PFX) 文件中的 base64 编码内容 <p><p>若要将 PFX 文件转换为 base64 编码格式，可以使用 PowerShell 并执行以下步骤： <p>1.将证书内容保存到某个变量中： <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2.使用 `ToBase64String()` 函数转换证书内容，并将该内容保存到某个文本文件中： <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` <p><p>**故障排除**：如果使用 `cert mmc/PowerShell` 命令，可能会出现以下错误： <p><p>`Could not load the certificate private key. Please check the authentication certificate password is correct and try again.` <p><p>若要解决此错误，请尝试使用 `openssl` 命令将 PFX 文件转换为 PEM 文件，再转换回来： <p><p>`openssl pkcs12 -in certificate.pfx -out certificate.pem` <br>`openssl pkcs12 -in certificate.pem -export -out certificate2.pfx` <p><p>然后，在获得证书的新转换 PFX 文件的 base64 编码字符串后，便可以在 Azure 逻辑应用中使用该字符串。 |
| **密码** | `password`| 否 | <password-for-pfx-file> | 用于访问 PFX 文件的密码 |
|||||

使用[安全参数](#secure-action-parameters)处理和保护敏感信息时，例如，在[用于自动执行部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，可以使用表达式在运行时访问这些参数值。 此示例 HTTP 操作定义将身份验证 `type` 指定为 `ClientCertificate`，并使用 [parameters() 函数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)获取参数值：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> 如果在单租户 Azure 逻辑应用中有逻辑应用（标准）资源，并且想要使用带有 TSL/SSL 证书、客户端证书或凭据类型为 `Certificate` 的 Azure Active Directory Open Authorization (Azure AD OAuth)，请确保为此身份验证类型完成额外的设置步骤。 否则，调用会失败。 有关详细信息，请参阅[单租户环境中的身份验证](../connectors/connectors-native-http.md#single-tenant-authentication)。

有关使用客户端证书身份验证保护服务的详细信息，请参阅以下主题：

* [使用 Azure API 管理中的客户端证书身份验证提高 API 的安全性](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [使用 Azure API 管理中的客户端证书身份验证提高后端服务的安全性](../api-management/api-management-howto-mutual-certificates.md)
* [使用客户端证书提高 RESTfuL 服务的安全性](../active-directory-b2c/secure-rest-api.md)
* [应用程序身份验证的证书凭据](../active-directory/develop/active-directory-certificate-credentials.md)
* [在 Azure 应用服务中通过代码使用 TLS/SSL 证书](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

#### <a name="azure-active-directory-open-authentication"></a>Azure Active Directory 开放式身份验证

在请求触发器上，可以使用 [Azure Active Directory 开放式身份验证 (Azure AD OAuth)](../active-directory/develop/index.yml)，在为逻辑应用[设置 Azure AD 授权策略](#enable-oauth)后对传入调用进行身份验证。 对于提供 Active Directory OAuth 身份验证类型供你选择的所有其他触发器和操作，请指定以下属性值：

| 属性（设计器） | 属性 (JSON) | 必须 | Value | 说明 |
|---------------------|-----------------|----------|-------|-------------|
| **身份验证** | `type` | 是 | **Active Directory OAuth** <br>或 <br>`ActiveDirectoryOAuth` | 可使用的身份验证类型。 逻辑应用当前遵循 [OAuth 2.0 协议](../active-directory/develop/v2-overview.md)。 |
| 颁发机构 | `authority` | 否 | <*URL-for-authority-token-issuer*> | 提供访问令牌的颁发机构的 URL。 此值默认为 `https://login.windows.net`。 |
| 租户 | `tenant` | 是 | <*tenant-ID*> | Azure AD 租户的租户 ID |
| **受众** | `audience` | 是 | <*resource-to-authorize*> | 要用于授权的资源，例如 `https://management.core.windows.net/` |
| **客户端 ID** | `clientId` | 是 | <*client-ID*> | 请求授权的应用的客户端 ID |
| **凭据类型** | `credentialType` | 是 | 证书 <br>或 <br>机密 | 客户端用来请求授权的凭据类型。 此属性和值不会显示在逻辑应用的基础定义中，但确定了为选定凭据类型显示的属性。 |
| **机密** | `secret` | 是，但仅适用于“机密”凭据类型 | <*client-secret*> | 用于请求授权的客户端密码 |
| **Pfx** | `pfx` | 是，但仅适用于“证书”凭据类型 | <*encoded-pfx-file-content*> | 个人信息交换 (PFX) 文件中的 base64 编码内容 |
| **密码** | `password` | 是，但仅适用于“证书”凭据类型 | <*password-for-pfx-file*> | 用于访问 PFX 文件的密码 |
|||||

使用[安全参数](#secure-action-parameters)处理和保护敏感信息时，例如，在[用于自动执行部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，可以使用表达式在运行时访问这些参数值。 此示例 HTTP 操作定义将身份验证 `type` 指定为 `ActiveDirectoryOAuth`，将凭据类型指定为 `Secret`，并使用 [parameters() function](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 获取参数值：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> 如果在单租户 Azure 逻辑应用中有逻辑应用（标准）资源，并且想要使用带有 TSL/SSL 证书、客户端证书或凭据类型为 `Certificate` 的 Azure Active Directory Open Authorization (Azure AD OAuth)，请确保为此身份验证类型完成额外的设置步骤。 否则，调用会失败。 有关详细信息，请参阅[单租户环境中的身份验证](../connectors/connectors-native-http.md#single-tenant-authentication)。

<a name="raw-authentication"></a>

#### <a name="raw-authentication"></a>原始身份验证

如果 **原始** 选项可用，在必须使用不遵循 [OAuth 2.0 协议](https://oauth.net/2/)的 [身份验证方案](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml)时，可以使用此身份验证类型。 使用此类型可以手动创建要与传出请求一起发送的授权标头值，并指定触发器或操作中的标头值。

例如，下面是遵循 [OAuth 1.0 协议](https://tools.ietf.org/html/rfc5849)的 HTTPS 请求的示例标头：

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

在支持原始身份验证的触发器或操作中指定以下属性值：

| 属性（设计器） | 属性 (JSON) | 必须 | Value | 说明 |
|---------------------|-----------------|----------|-------|-------------|
| **身份验证** | `type` | 是 | 原始 | 要使用的身份验证类型 |
| **值** | `value` | 是 | <*authorization-header-value*> | 要用于身份验证的授权标头值 |
||||||

使用[安全参数](#secure-action-parameters)处理和保护敏感信息时，例如，在[用于自动执行部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，可以使用表达式在运行时访问这些参数值。 此示例 HTTP 操作定义将身份验证 `type` 指定为 `Raw` 并使用 [parameters() function](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 获取参数值：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

#### <a name="managed-identity-authentication"></a>托管标识身份验证

当[托管标识](../active-directory/managed-identities-azure-resources/overview.md)选项可用于[支持托管标识身份验证的触发器或操作](#add-authentication-outbound)时，逻辑应用可以使用系统分配的标识或手动创建的单个用户分配标识，对访问受 Azure Active Directory (Azure AD) 保护的 Azure 资源的操作进行身份验证，而无需使用凭据、机密或 Azure AD 令牌。 你无需管理机密或直接使用 Azure AD 令牌，Azure 会为你管理此标识，并且会帮助你保护凭据。 详细了解[支持 Azure AD 身份验证的托管标识的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

1. 在逻辑应用可以使用托管标识之前，请按照[使用 Azure 逻辑应用中的托管标识对 Azure 资源的访问进行身份验证](../logic-apps/create-managed-service-identity.md)中的步骤进行操作。 这些步骤将在逻辑应用上启用托管标识，并设置该标识对目标 Azure 资源的访问权限。

1. 在 Azure 函数可以使用托管标识之前，请先[为 Azure 函数启用身份验证](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions)。

1. 在支持使用托管标识的触发器或操作中提供以下信息：

   **内置触发器和操作**

   | 属性（设计器） | 属性 (JSON) | 必须 | Value | 说明 |
   |---------------------|-----------------|----------|-------|-------------|
   | **身份验证** | `type` | 是 | **托管标识** <br>或 <br>`ManagedServiceIdentity` | 要使用的身份验证类型 |
   | **托管标识** | `identity` | 是 | * 系统分配的托管标识 <br>或 <br>`SystemAssigned` <p><p>* <user-assigned-identity-name> | 要使用的托管标识 |
   | **受众** | `audience` | 是 | <*target-resource-ID*> | 要访问的目标资源的资源 ID。 <p>例如，`https://storage.azure.com/` 使用于身份验证的[访问令牌](../active-directory/develop/access-tokens.md)对所有存储帐户都有效。 但是，还可以为特定存储帐户指定根服务 URL，如 `https://fabrikamstorageaccount.blob.core.windows.net`。 <p>**注意**：“受众”属性可能已在某些触发器或操作中隐藏。 若要显示此属性，请在触发器或操作中打开“添加新参数”列表，然后选择“受众” 。 <p><p>**重要说明**：请确保此目标资源 ID 完全匹配 Azure AD 所需的值，包括任何必需的尾部反斜杠。 因此，所有 Azure Blob 存储帐户的 `https://storage.azure.com/` 资源 ID 都需要尾部反斜杠。 但是，特定存储帐户的资源 ID 不需要尾部反斜杠。 若要查找这些资源 ID，请参阅[支持 Azure AD 的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。 |
   |||||

   使用[安全参数](#secure-action-parameters)处理和保护敏感信息时，例如，在[用于自动执行部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，可以使用表达式在运行时访问这些参数值。 例如，此 HTTP 操作定义将身份验证 `type` 指定为 `ManagedServiceIdentity`，并使用 [parameters() 函数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)获取参数值：

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

   **托管连接器触发器和操作**

   | 属性（设计器） | 必须 | Value | 说明 |
   |---------------------|----------|-------|-------------|
   | **连接名称** | 是 | <*connection-name*> ||
   | **托管的标识** | 是 | **系统分配的托管标识** <br>或 <br> <*用户分配的托管标识名称*> | 要使用的身份验证类型 |
   |||||


<a name="block-connections"></a>

## <a name="block-creating-connections"></a>阻止创建连接

如果组织不允许使用 Azure 逻辑应用中的连接器连接到特定资源，则可以在逻辑应用工作流中使用 [Azure Policy](../governance/policy/overview.md) [阻止为特定连接器创建这些连接的功能](../logic-apps/block-connections-connectors.md)。 有关详细信息，请参阅[在 Azure 逻辑应用中阻止特定连接器创建的连接](../logic-apps/block-connections-connectors.md)。

<a name="isolation-logic-apps"></a>

## <a name="isolation-guidance-for-logic-apps"></a>逻辑应用隔离指导

可以在 [Azure 政府](../azure-government/documentation-government-welcome.md)中，使用在 [Azure 政府影响级别 5 隔离指导](../azure-government/documentation-government-impact-level-5.md#azure-logic-apps)和[美国国防部云计算安全要求指南 (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html) 中所述的区域支持所有影响级别的 Azure 逻辑应用。 为了满足这些要求，逻辑应用支持在具有专用资源的环境中创建和运行工作流的功能，因此可以减轻逻辑应用中其他 Azure 租户造成的性能影响，并避免与其他租户共享计算资源。

* 若要运行自己的代码或执行 XML 转换，请[创建并调用一个 Azure 函数](../logic-apps/logic-apps-azure-functions.md)，而不要使用[内联代码功能](../logic-apps/logic-apps-add-run-inline-code.md)或提供[用作映射的程序集](../logic-apps/logic-apps-enterprise-integration-maps.md)。 此外，为函数应用设置托管环境，以符合隔离要求。

  例如，若要满足影响级别 5 要求，请使用[“隔离”定价层](../app-service/overview-hosting-plans.md)以及同样也使用“隔离”定价层的[应用服务环境 (ASE)](../app-service/environment/intro.md)，在[应用服务计划](../azure-functions/dedicated-plan.md)中创建函数应用。  在此环境中，函数应用将在专用的 Azure 虚拟机和专用的 Azure 虚拟网络中运行，这些资源在计算隔离的基础上为应用提供网络隔离，并提供最大程度的横向扩展功能。 有关详细信息，请参阅 [Azure 政府云影响级别 5 隔离指导 - Azure Functions](../azure-government/documentation-government-impact-level-5.md#azure-functions)。

  有关详细信息，请查看以下文档：

  * [Azure 应用服务计划](../app-service/overview-hosting-plans.md)
  * [Azure Functions 网络选项](../azure-functions/functions-networking-options.md)
  * [适用于虚拟机的 Azure 专用主机](../virtual-machines/dedicated-hosts.md)
  * [Azure 中的虚拟机隔离](../virtual-machines/isolation.md)
  * [将专用 Azure 服务部署到虚拟网络](../virtual-network/virtual-network-for-azure-services.md)

* 根据使用的是[多租户还是单租户 Azure 逻辑应用](logic-apps-overview.md#resource-environment-differences)，可以使用以下选项：

  * 借助基于单租户的逻辑应用，你可以通过为入站流量设置专用终结点在逻辑应用工作流和 Azure 虚拟网络之间进行私密且安全的通信，并将虚拟网络集成用于出站流量。 有关详细信息，请参阅[使用专用终结点保护虚拟网络和单租户 Azure 逻辑应用之间的流量](secure-single-tenant-workflow-virtual-network-private-endpoint.md)。

  * 借助基于多租户的逻辑应用，你可以在[集成服务环境 (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) 中创建和运行逻辑应用。 这样，你的逻辑应用就可以在专用资源上运行，并且可以访问受 Azure 虚拟网络保护的资源。 要更好地控制 Azure 存储使用的加密密钥，可以通过 [Azure 密钥保管库](../key-vault/general/overview.md)设置、使用和管理自己的密钥。 此功能也称为“创建自己的密钥”(BYOK)，密钥称为“客户管理的密钥”。 有关详细信息，请参阅[设置客户管理的密钥以便为 Azure 逻辑应用中的集成服务环境 (ISE) 加密静态数据](../logic-apps/customer-managed-keys-integration-service-environment.md)。

  > [!IMPORTANT]
  > 某些 Azure 虚拟网络使用专用终结点（[Azure 专用链接](../private-link/private-link-overview.md)）来提供对 Azure PaaS 服务（例如 Azure 存储、Azure Cosmos DB 或 Azure SQL 数据库）、合作伙伴服务或托管在 Azure 上的客户服务的访问。
  >
  > 如果工作流需要访问使用专用终结点的虚拟网络，并且你想使用逻辑应用（消耗）资源类型开发这些工作流，则必须在 ISE 中创建和运行逻辑应用。 但是，如果要使用逻辑应用（标准）资源类型开发这些工作流，则不需要 ISE。 
  > 相反，工作流可以通过将专用终结点用于入站流量并将虚拟网络集成用于出站流量，与虚拟网络进行私密且安全的通信。 有关详细信息，请参阅[使用专用终结点保护虚拟网络和单租户 Azure 逻辑应用之间的流量](secure-single-tenant-workflow-virtual-network-private-endpoint.md)。

有关隔离的详细信息，请参阅以下文档：

* [Azure 公有云中的隔离](../security/fundamentals/isolation-choices.md)
* [Azure 中高度敏感的 IaaS 应用的安全性](/azure/architecture/reference-architectures/n-tier/high-security-iaas)

## <a name="next-steps"></a>后续步骤

* [Azure 逻辑应用的 Azure 安全基线](../logic-apps/security-baseline.md)
* [自动化 Azure 逻辑应用的部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [监视逻辑应用](../logic-apps/monitor-logic-apps-log-analytics.md)