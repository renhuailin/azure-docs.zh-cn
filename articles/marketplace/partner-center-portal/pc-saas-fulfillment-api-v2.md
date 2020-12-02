---
title: Microsoft 商业应用商店中的 SaaS 履单 Api v2
description: 了解如何通过使用履单版本2来创建和管理 Microsoft AppSource 和 Azure Marketplace 上的 SaaS 产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 1ea326cc4537176c0ddcff070f4dc3b3f77f4b58
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512029"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>商业应用商店中的 SaaS 履单 Api 版本2

本文详细介绍了一些 Api，使合作伙伴能够在 Microsoft AppSource 和 Azure Marketplace 中 (SaaS) 产品/服务销售其软件即服务。 需要发布者才能实现与这些 Api 的集成，以在合作伙伴中心发布事务 SaaS 产品/服务。

## <a name="managing-the-saas-subscription-life-cycle"></a>管理 SaaS 订阅生命周期

商业应用商店在最终用户购买 SaaS 订阅后，管理其整个生命周期。 它使用登陆页、履单、操作 Api 和 webhook 作为一种机制来驱动实际的 SaaS 订阅激活、使用情况、更新和取消。 最终用户的帐单基于 Microsoft 维护的 SaaS 订阅的状态。 

### <a name="states-of-a-saas-subscription"></a>SaaS 订阅的状态

下图显示了 SaaS 订阅的状态和适用的操作。

![显示该软件作为 marketplace 中的服务订阅生命周期的示意图。](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>已购买但尚未激活 (*PendingFulfillmentStart*) 

最终用户 (或 CSP) 购买商业应用商店中的 SaaS 产品/服务后，应收到有关购买的通知。 然后，发布者可以在发布者端为最终用户创建和配置新的 SaaS 帐户。

若要创建帐户，请执行以下操作：

1. 客户在 Microsoft AppSource 或 Azure 门户成功购买后，选择可用于 SaaS 产品/服务的 " **配置** " 按钮。 或者，客户可以使用电子邮件中的 " **配置** " 按钮，这些按钮将在购买后很快收到。
2. 然后，Microsoft 通过在新的浏览器选项卡中打开 "登录" 页 URL 并使用令牌参数 (来自商业 marketplace) 的购买标识令牌，通知合作伙伴购买情况。

此类调用的一个示例是 `https://contoso.com/signup?token=<blob>` ，而合作伙伴中心的此 SaaS 产品的登陆页面 URL 配置为 `https://contoso.com/signup` 。 此令牌向发布者提供一个 ID，用于唯一标识 SaaS 购买和客户。

>[!NOTE]
>在客户从 Microsoft 端启动配置过程之前，不会向发布者通知 SaaS 的购买情况。

登录页 URL 必须每天启动并运行一次，并且随时可以接收来自 Microsoft 的新呼叫。 如果登陆页面变为不可用，则客户将无法注册 SaaS 服务并开始使用它。

接下来，发布者必须通过调用 [SaaS 解析 API](#resolve-a-purchased-subscription)并输入令牌作为标头参数的值，将 *令牌* 传递回 Microsoft `x-ms-marketplace-token header` 。 作为解析 API 调用的结果，将交换令牌以获取 SaaS 购买的详细信息，例如采购的唯一 ID、购买的产品/服务 ID 和购买的计划 ID。

在登录页上，客户应通过 Azure Active Directory (Azure AD) 单一登录 (SSO) 登录到新的或现有的 SaaS 帐户。

发布者应该实现 SSO，为 Microsoft 提供此流程所需的用户体验。 配置 SSO 时，请确保使用多租户 Azure AD 应用程序，同时允许使用工作和学校帐户或个人 Microsoft 帐户。 此要求仅适用于登录页面，适用于已登录到 SaaS 服务的用户（如果已在 Microsoft 凭据中登录）。 对于所有登录到 SaaS 服务，不需要 SSO。

> [!NOTE]
>如果 SSO 要求管理员必须授予对应用的权限，则合作伙伴中心提供的产品/服务的说明必须公开管理员级别的访问权限。 此披露旨在符合 [商业 marketplace 认证策略](/legal/marketplace/certification-policies#10003-authentication-options)。

登录后，客户应在发布方上完成 SaaS 配置。 然后，发布者必须调用 [激活订阅 API](#activate-a-subscription) ，才能向 Azure Marketplace 发送向 Azure Marketplace 提供 SaaS 帐户预配的信号。
此操作将启动客户的计费周期。 如果激活订阅 API 调用失败，则不会向客户收取购买费用。


![显示用于预配方案的 P I 调用关系图。](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Active (*订阅* 的) 

*Active (订阅的)* 是预配的 SaaS 订阅的稳定状态。 Microsoft 端处理 [激活订阅 API](#activate-a-subscription) 调用后，SaaS 订阅将标记为已 *订阅*。 客户现在可以在发布者端使用 SaaS 服务，并将对其进行计费。

当 SaaS 订阅已处于活动状态时，客户可从 "Azure 门户" 或 "Microsoft 365 管理中心" 中选择 " **管理 saas 体验** "。 此操作还会导致 Microsoft 通过 *令牌* 参数调用 **登陆页 URL** ，如激活流中所示。 发布者应该区分新的购买和现有 SaaS 帐户的管理，并相应地处理此登陆页 URL 调用。

#### <a name="being-updated-subscribed"></a> (*订阅*) 更新

此操作表示 Microsoft 和发布者正在处理现有活动 SaaS 订阅的更新。 可以通过以下方式启动此类更新：

- 商业应用商店中的客户。
- 来自商业应用商店的 CSP。
- 来自发布者的 SaaS 网站 (的客户，但不能对 CSP 进行购买) 。

有两种类型的更新可用于 SaaS 订阅：

- 当客户选择另一个订阅计划时，更新计划。
- 当客户更改订阅的购买座位数量时，更新数量。

只能更新活动订阅。 当订阅正在更新时，其状态在 Microsoft 端保持为活动状态。

##### <a name="update-initiated-from-the-commercial-marketplace"></a>从商业应用商店启动的更新

在此流中，客户从 Azure 门户或 Microsoft 365 管理中心更改订阅计划或座位数量。

1. 输入更新后，Microsoft 将调用发布者的 webhook URL，该 URL 是在合作伙伴中心的 " **连接 webhook** " 字段中配置的，其中包含适用于 *操作* 和其他相关参数的值。 
1. 发布方应对 SaaS 服务进行所需的更改，并在完成后通过调用 [操作 API 的更新状态](#update-the-status-of-an-operation)通知 Microsoft。
1. 如果修补程序是以 *失败* 状态发送的，则不会在 Microsoft 端完成更新过程。 SaaS 订阅将保留现有计划和座位数量。

> [!NOTE]
> 接收到 webhook 通知后，发布服务器应调用修补程序，以在 *10 秒的时间范围内*[更新操作 API 的状态](#update-the-status-of-an-operation)，并在10秒的时间范围内失败。 如果在10秒内未收到操作状态的修补程序，则会自动将更改计划 *修补为成功*。 

下图显示了从商用 marketplace 启动的更新方案的 API 调用序列。

![显示对 marketplace 启动的更新调用的 P 的关系图。](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>从发布服务器启动的更新

在此流中，客户更改订阅计划或从 SaaS 服务本身购买的座位数量。 

1. 发布者代码在发布方上进行请求的更改之前，必须调用 [更改计划 api](#change-the-plan-on-the-subscription) 和/或 [更改数量 API](#change-the-quantity-of-seats-on-the-saas-subscription) 。 

1. Microsoft 将对订阅应用此更改，然后通过 **连接 Webhook** 通知发布者应用相同的更改。

1. 只应在发布服务器对 SaaS 订阅进行所需的更改，并在通过调用 [操作 API 的更新状态](#update-the-status-of-an-operation)完成更改时通知 Microsoft。

下图显示了从发布服务器端启动的更新方案的 API 调用序列。

![显示对发布服务器端启动的更新调用的 P 的关系图。](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>挂起的 (*挂起*) 

此状态表示尚未收到客户对 SaaS 服务的付款。 Microsoft 将在 SaaS 订阅状态中通知发布者此项更改。 通知是通过调用 webhook 来完成的， *操作* 参数设置为 " *挂起*"。

发布服务器可能或不会对发布服务器端上的 SaaS 服务进行更改。 建议发布者将此信息提供给已暂停的客户，并限制或阻止客户访问 SaaS 服务。 将永远不会收到付款的概率。

Microsoft 为客户提供30天的宽限期，然后才会自动取消订阅。 当订阅处于 *挂起* 状态时：

* 合作伙伴或 ISV 必须使 SaaS 帐户处于可恢复状态，以便在不丢失任何数据或设置的情况下还原完整功能。
* 如果在宽限期内收到付款，或者在宽限期结束时取消设置订阅的请求，则合作伙伴或 ISV 应该会收到恢复订阅的请求。 这两个请求都将通过 webhook 机制发送。

在发布者执行任何操作之前，订阅状态将更改为 "在 Microsoft 端挂起"。 只能挂起活动订阅。

#### <a name="reinstated-suspended"></a>已恢复 (*挂起*) 

此操作指示客户的付款方式再次变为有效，已为 SaaS 订阅支付了费用，并且正在恢复订阅。 在这种情况下： 

1. Microsoft 调用 webhook，并将 *action* 参数设置为 *复原* 值。
1. 发布者确保订阅在发布方端上重新正常运行。
1. 发布者调用具有成功状态的 [修补程序操作 API](#update-the-status-of-an-operation) 。
1. 复原过程成功，并再次对 SaaS 订阅的客户收费。 

如果修补程序是以 *失败* 状态发送的，则 reinstatement 进程将不会在 Microsoft 端完成，订阅将保持 *挂起* 状态。

只能恢复挂起的订阅。 挂起的 SaaS 订阅在恢复时将保持 *挂起* 状态。 完成此操作后，订阅的状态将变为 " *活动*"。

#### <a name="renewed-subscribed"></a>已续订 (*订阅*) 

在一个月或一年的订阅期限结束时，Microsoft 会自动续订 SaaS 订阅。 对于所有 SaaS 订阅，自动续订设置的默认值均为 *true* 。 将继续使用定期节奏续订活动 SaaS 订阅。 续订订阅后，Microsoft 不会通知发布者。 客户可通过 Microsoft 365 管理门户关闭 SaaS 订阅的自动续订。 在这种情况下，将在当前帐单期限结束时自动取消 SaaS 订阅。 客户还可以随时取消 SaaS 订阅。

仅自动续订活动订阅。 订阅在续订过程中保持活动状态，并且自动续订成功。 续订后，订阅期限的开始日期和结束日期将更新为新术语的日期。

如果由于付款问题而导致自动续订失败，订阅将会 *暂停* ，并将通知发布者。

#### <a name="canceled-unsubscribed"></a>取消 *订阅* (取消)  

订阅通过从发布者站点、Azure 门户或 Microsoft 365 管理中心的取消订阅来响应明确的客户或 CSP 操作，从而达到此状态。 还可以隐式取消订阅，这是由于由于未付款的原因，超过30天后处于 " *挂起* " 状态。

在发布服务器收到取消 webhook 调用后，它们应该保留客户数据以在请求至少7天后恢复。 只有这样才能删除客户数据。

可在其生命周期中的任何时间点取消 SaaS 订阅。 取消订阅后，将无法重新激活它。

## <a name="api-reference"></a>API 参考

本部分介绍 SaaS 订阅和操作 Api。

**订阅 api** 应该用于处理从购买到取消的 SaaS 订阅生命周期。

**操作 api** 应该用于：

* 验证并确认处理的 webhook 调用。
* 获取等待由发布服务器确认的应用挂起操作的列表。

> [!NOTE]
> TLS 版本1.2 的版本将尽快作为 HTTPS 通信的最低版本来强制实施。 请确保在代码中使用此 TLS 版本。 TLS 版本1.0 和1.1 即将弃用。

### <a name="subscription-apis"></a>订阅 Api

#### <a name="resolve-a-purchased-subscription"></a>解决已购买的订阅

通过解析终结点，发布者可以将商业 marketplace 中的采购标识令牌交换 (称为已 [购买但尚未激活](#purchased-but-not-yet-activated-pendingfulfillmentstart)的 *令牌*) 到持续购买的 SaaS 订阅 ID 及其详细信息。

当客户重定向到合作伙伴的登陆页 URL 时，客户标识令牌将作为此 URL 调用中的 *令牌* 参数传递。 合作伙伴应使用此令牌并发出请求来解决该问题。 解析 API 响应包含 SaaS 订阅 ID 和用于唯一标识购买的其他详细信息。 用登陆页 URL 调用提供的 *令牌* 通常有效24小时。 如果你收到的 *令牌* 已过期，我们建议你向最终用户提供以下指导：

"我们无法识别此购买情况。 请在 Azure 门户或 Microsoft 365 管理中心重新打开此 SaaS 订阅，并再次选择 "配置帐户" 或 "管理帐户"。

调用解析 API 将返回所有支持状态的 SaaS 订阅的订阅详细信息和状态。

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>发布 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。   |

*请求标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。 |
|  `x-ms-correlationid` |  在客户端上执行的操作的唯一字符串值。 此参数将来自客户端操作的所有事件与服务器端的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `authorization`     |  标识发出此 API 调用的发布服务器的唯一访问令牌。 格式为 `"Bearer <accessaccess_token>"` ：按 [根据 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述，由发布服务器检索令牌值。 |
|  `x-ms-marketplace-token`  | 要解析的购买标识 *标记* 参数。  当客户重定向到 SaaS 合作伙伴的网站时，将在登陆页 URL 调用中传递令牌 (例如： `https://contoso.com/signup?token=<token><authorization_token>`) 。 <br> <br>  请注意，要编码的 *令牌* 值是登陆页面 URL 的一部分，因此需要先对其进行解码，然后才能将其用作此 API 调用中的参数。  <br> <br> 下面是 URL 中编码字符串的示例： `contoso.com/signup?token=ab%2Bcd%2Fef` ，其中 *标记* 为 `ab%2Bcd%2Fef` 。  已解码的相同令牌为： `Ab+cd/ef` |
| | |

*响应代码：*

代码：200基于提供的返回唯一的 SaaS 订阅标识符 `x-ms-marketplace-token` 。

响应正文示例：

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

代码：400错误的请求。 `x-ms-marketplace-token` 缺失、格式不正确、无效或已过期。

代码：403禁止访问。 授权令牌无效、已过期或未提供。  请求尝试访问使用不同 Azure AD 应用 ID 发布的产品/服务的 SaaS 订阅，该 ID 与用于创建授权令牌的应用 ID 不同。

此错误通常是未正确执行 [SaaS 注册](pc-saas-registration.md) 的症状。

代码：500内部服务器错误。  重试 API 调用。  如果错误仍然存在，请联系 [Microsoft 支持](https://partner.microsoft.com/support/v2/?stage=1)部门。

#### <a name="activate-a-subscription"></a>激活订阅

为最终用户配置 SaaS 帐户后，发布者必须在 Microsoft 端调用激活订阅 API。  除非此 API 调用成功，否则不会向客户收费。

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>发布 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  --------   |  ---------------  |
| `ApiVersion`  |  使用2018-08-31。   |
| `subscriptionId` | 已购买的 SaaS 订阅的唯一标识符。  此 ID 是使用 [解析 API](#resolve-a-purchased-subscription)解析商业 marketplace 授权令牌后获取的。
 |

*请求标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。  如果未提供此值，将在响应标头中生成并提供一个值。 |
| `x-ms-correlationid` |  在客户端上执行的操作的唯一字符串值。  此字符串将客户端操作的所有事件与服务器端上的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。 |
| `authorization`      |  标识发出此 API 调用的发布服务器的唯一访问令牌。 格式为 `"Bearer <access_token>"` ：按 [根据 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述，由发布服务器检索令牌值。 |

*请求负载示例：*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*响应代码：*

代码：200订阅在 Microsoft 端标记为已订阅。

此调用没有响应正文。

代码：400请求错误：验证失败。

* `planId` 在请求负载中不存在。
* `planId` 请求负载与购买的负载不匹配。
* `quantity` 请求负载与购买的负载不匹配
* SaaS 订阅处于已 *订阅* 或 *挂起* 状态。

代码：403禁止访问。 授权令牌无效、已过期或未提供。 请求尝试访问使用不同 Azure AD 应用 ID 发布的产品/服务的 SaaS 订阅，该 ID 与用于创建授权令牌的应用 ID 不同。

此错误通常是未正确执行 [SaaS 注册](pc-saas-registration.md) 的症状。

代码：找不到404。 SaaS 订阅处于取消 *订阅状态。*

代码：500内部服务器错误。  重试 API 调用。  如果错误仍然存在，请联系 [Microsoft 支持](https://partner.microsoft.com/support/v2/?stage=1)部门。

#### <a name="get-list-of-all-subscriptions"></a>获取所有订阅的列表

此 API 将为发布者在商业应用商店中发布的所有产品/服务检索所有购买的 SaaS 订阅列表。  将返回所有可能状态的 SaaS 订阅。 还会返回取消订阅的 SaaS 订阅，因为不会在 Microsoft 端删除此信息。

API 返回每页100的分页结果。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>获取 `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  --------   |  ---------------  |
| `ApiVersion`  |  使用2018-08-31。  |
| `continuationToken`  | 可选参数。 若要检索结果的第一页，请保留为空。  使用在参数中返回的值 `@nextLink` 来检索下一页。 |

*请求标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。 |
| `x-ms-correlationid` |  在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。 |
| `authorization`      |  标识进行此 API 调用的发布服务器的唯一访问令牌。  格式为 `"Bearer <access_token>"` ：按 [根据 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述，由发布服务器检索令牌值。 |

*响应代码：*

代码：200基于发布者的授权令牌，返回此发布者所做的所有现有订阅的列表。

*响应正文示例：*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

如果找不到此发布者购买的 SaaS 订阅，则返回空的响应正文。

代码：403禁止访问。 授权令牌不可用、无效或过期。

此错误通常是未正确执行 [SaaS 注册](pc-saas-registration.md) 的症状。 

代码：500内部服务器错误。 重试 API 调用。  如果错误仍然存在，请联系 [Microsoft 支持](https://partner.microsoft.com/support/v2/?stage=1)部门。

#### <a name="get-subscription"></a>获取订阅

此 API 将为发布者在商业应用商店中发布的 SaaS 产品检索指定购买的 SaaS 订阅。 使用此调用通过其 ID 获取特定 SaaS 订阅的所有可用信息，而不是通过调用用于获取所有订阅列表的 API 来获取。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>获取 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   使用2018-08-31。 |
| `subscriptionId`     |  已购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业 marketplace 授权令牌后获取的。 |

*请求标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。 |
|  `x-ms-correlationid` |  在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。 |
|  `authorization`     | 标识进行此 API 调用的发布服务器的唯一访问令牌。 格式为 `"Bearer <access_token>"` ：按 [根据 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述，由发布服务器检索令牌值。  |

*响应代码：*

代码：200基于提供的返回 SaaS 订阅的详细信息 `subscriptionId` 。

*响应正文示例：*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

代码：403禁止访问。 授权令牌无效、已过期或未提供。 请求尝试访问使用不同 Azure AD 应用 ID 发布的产品/服务的 SaaS 订阅，该订阅与用于创建授权令牌的应用 ID 不同。

此错误通常是未正确执行 [SaaS 注册](pc-saas-registration.md) 的症状。 

代码：找不到404。  找不到具有指定的 SaaS 订阅 `subscriptionId` 。

代码：500内部服务器错误。  重试 API 调用。  如果错误仍然存在，请联系 [Microsoft 支持](https://partner.microsoft.com/support/v2/?stage=1)部门。

#### <a name="list-available-plans"></a>列出可用计划

此 API 检索 `subscriptionId` 此产品/服务的特定购买所标识的 SaaS 产品/服务的所有计划。  使用此调用获取 SaaS 订阅受益人可为订阅更新的所有专用和公用计划的列表。  返回的计划将在与已购买计划相同的地理位置提供。

此调用将返回该客户的可用计划的列表，以及已购买的计划。  该列表可在发布者站点上向最终用户显示。  最终用户可以将订阅计划更改为返回的列表中的任何一个计划。  将计划更改为不在列表中的计划将会失败。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>获取 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
|  `subscriptionId`    |  已购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业 marketplace 授权令牌后获取的。 |

*请求标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。  如果未提供此值，将在响应标头中生成并提供一个值。 |
|  `x-ms-correlationid`  |  在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。 |
|  `authorization`     |  标识进行此 API 调用的发布服务器的唯一访问令牌。  格式为 `"Bearer <access_token>"` ：按 [根据 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述，由发布服务器检索令牌值。  |

*响应代码：*

代码：200返回现有 SaaS 订阅的所有可用计划的列表，包括已购买的计划。

响应正文示例：

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

如果 `subscriptionId` 未找到，则返回空的响应正文。

代码：403禁止访问。 授权令牌无效、已过期或未提供。  该请求可能尝试访问使用不同 Azure AD 应用 ID 发布的产品/服务的 SaaS 订阅，该产品 ID 与用于创建授权令牌的应用 ID 不同。

此错误通常是未正确执行 [SaaS 注册](pc-saas-registration.md) 的症状。 

代码：500内部服务器错误。  重试 API 调用。  如果错误仍然存在，请联系 [Microsoft 支持](https://partner.microsoft.com/support/v2/?stage=1)部门。

#### <a name="change-the-plan-on-the-subscription"></a>更改订阅的计划

使用此 API 将为 SaaS 订阅购买的现有计划更新为 (公有或私有) 的新计划。  如果在发布者端为在商业应用商店中购买的 SaaS 订阅更改了计划，则发布服务器必须调用此 API。

只能为 *活动* 订阅调用此 API。  任何计划都可以更改为任何其他现有计划 (公有或私有) ，但不能更改为其自身。  对于私有计划，必须在合作伙伴中心的计划受众中定义客户的租户。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>跳 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
| `subscriptionId`     | 已购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业 marketplace 授权令牌后获取的。 |

*请求标头：*
 
|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `x-ms-correlationid`  | 在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `authorization`     |  标识进行此 API 调用的发布服务器的唯一访问令牌。  格式为 `"Bearer <access_token>"` ：按 [根据 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述，由发布服务器检索令牌值。 |

*请求负载示例：*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*响应代码：*

代码：202已接受并异步处理更改计划的请求。  伙伴应轮询 **操作位置 URL** ，以确定更改计划请求是成功还是失败。  应每隔几秒执行一次轮询，直到为操作收到最终状态 "已 *失败*"、" *成功*" 或 " *冲突* "。  最终操作状态应快速返回，但在某些情况下可能需要几分钟时间。

当操作准备好在商业应用商店端成功完成时，合作伙伴还会获得 webhook 通知。  只应在发布服务器端使计划更改。

*响应标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  用于获取操作状态的 URL。  例如，`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`。 |

代码：400请求错误：验证失败。

* 新计划不存在，或无法用于此特定 SaaS 订阅。
* 新计划与当前计划相同。
* SaaS 订阅状态未 *订阅*。
* 中未包含 SaaS 订阅的更新操作 `allowedCustomerOperations` 。

代码：403禁止访问。 授权令牌无效、已过期或未提供。  请求尝试访问使用不同 Azure AD 应用 ID 发布的产品/服务的 SaaS 订阅，该订阅与用于创建授权令牌的应用 ID 不同。

此错误通常是未正确执行 [SaaS 注册](pc-saas-registration.md) 的症状。

代码：找不到404。  找不到具有的 SaaS 订阅 `subscriptionId` 。

代码：500内部服务器错误。  重试 API 调用。  如果错误仍然存在，请联系 [Microsoft 支持](https://partner.microsoft.com/support/v2/?stage=1)部门。

>[!NOTE]
>可以一次更改一个或多个座位，而不能同时更改两者。

>[!Note]
>只有在对最终用户进行更改的明确批准后，才能调用此 API。

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>更改 SaaS 订阅上的座位数量

使用此 API 更新 (增加或减少) 为 SaaS 订阅购买的座位数量。  当从发布服务器中为在商业应用商店中创建的 SaaS 订阅更改了座位数量时，发布者必须调用此 API。

座位数量不能超过当前计划中允许的数量。  在这种情况下，发布者应在更改座位数量之前更改计划。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>跳 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
|  `subscriptionId`     | 已购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业 marketplace 授权令牌后获取的。  |

*请求标头：*
 
|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `x-ms-correlationid`  | 在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `authorization`     | 标识进行此 API 调用的发布服务器的唯一访问令牌。  格式为 `"Bearer <access_token>"` ：按 [根据 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述，由发布服务器检索令牌值。  |

*请求负载示例：*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*响应代码：*

代码：202已接受并异步处理更改数量的请求。 伙伴应轮询 **操作位置 URL** ，以确定更改数量请求是成功还是失败。  应每隔几秒执行一次轮询，直到为操作收到最终状态 "已 *失败*"、" *成功*" 或 " *冲突* "。  最终操作状态应快速返回，但在某些情况下可能需要几分钟时间。

当操作准备好在商业应用商店端成功完成时，合作伙伴还会获得 webhook 通知。  只应在发布服务器端进行数量更改时才应如此。

*响应标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  链接到资源以获取操作的状态。  例如，`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`。  |

代码：400请求错误：验证失败。

* 新数量大于或小于当前计划限制。
* 缺少新数量。
* 新数量与当前数量相同。
* SaaS 订阅状态为 "未订阅"。
* 中未包含 SaaS 订阅的更新操作 `allowedCustomerOperations` 。

代码：403禁止访问。  授权令牌无效、已过期或未提供。  请求尝试访问不属于当前发布服务器的订阅。

此错误通常是未正确执行 [SaaS 注册](pc-saas-registration.md) 的症状。 

代码：找不到404。  找不到具有的 SaaS 订阅 `subscriptionId` 。

代码：500内部服务器错误。  重试 API 调用。  如果错误仍然存在，请联系 [Microsoft 支持](https://partner.microsoft.com/support/v2/?stage=1)部门。

>[!Note]
>一次只能更改一个计划或数量，而不能同时更改两者。

>[!Note]
>只有在最终用户获得更改的明确批准后，才能调用此 API。

#### <a name="cancel-a-subscription"></a>取消订阅

使用此 API 取消订阅指定的 SaaS 订阅。  发布者不必使用此 API，我们建议将客户定向到商业市场来取消 SaaS 订阅。

如果发布者决定实现在发布者端的商业应用商店中购买的 SaaS 订阅，则必须调用此 API。  完成此调用后，订阅的状态将在 Microsoft *端成为取消订阅状态* 。

如果在以下宽限期内取消订阅，则不会向客户收费：

* 激活后每月订阅24小时。
* 激活后每年的订阅14天。

如果在前一个宽限期内取消订阅，则将向客户收费。  在取消后，客户将立即失去对 Microsoft 端的 SaaS 订阅的访问权限。 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Delete `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
|  `subscriptionId`     | 已购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业 marketplace 授权令牌后获取的。  |

*请求标头：*
 
|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `x-ms-correlationid`  | 在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `authorization`     |  标识发出此 API 调用的发布服务器的唯一访问令牌。  格式为 `"Bearer <access_token>"` ：按 [根据 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述，由发布服务器检索令牌值。 |

*响应代码：*

代码：202已接受并异步处理取消订阅请求。  伙伴应轮询 **操作位置 URL** ，以确定此请求是成功还是失败。  应每隔几秒执行一次轮询，直到为操作收到最终状态 "已 *失败*"、" *成功*" 或 " *冲突* "。  最终操作状态应快速返回，但在某些情况下可能需要几分钟时间。

在商业应用商店端成功完成此操作后，该合作伙伴还会获得 webhook 通知。  只应在发布服务器端取消订阅。

*响应标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  链接到资源以获取操作的状态。  例如，`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`。 |

代码：400错误的请求。  此 SaaS 订阅的 "删除" 不在 `allowedCustomerOperations` 列表中。

代码：403禁止访问。  授权令牌无效、已过期或不可用。 请求尝试访问使用不同 Azure AD 应用 ID 发布的产品/服务的 SaaS 订阅，该订阅与用于创建授权令牌的应用 ID 不同。

此错误通常是未正确执行 [SaaS 注册](pc-saas-registration.md) 的症状。

代码：找不到404。  找不到具有的 SaaS 订阅 `subscriptionId` 。

代码：500内部服务器错误。 重试 API 调用。  如果错误仍然存在，请联系 [Microsoft 支持](https://partner.microsoft.com/support/v2/?stage=1)部门。

### <a name="operations-apis"></a>操作 Api

#### <a name="list-outstanding-operations"></a>列出未完成的操作 

获取指定 SaaS 订阅的挂起操作的列表。  发布服务器应该通过调用 [操作修补程序 API](#update-the-status-of-an-operation)来确认返回的操作。

目前仅 **恢复操作** 作为此 API 调用的响应返回。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>获取 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  使用2018-08-31。         |
|    `subscriptionId` | 已购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业 marketplace 授权令牌后获取的。  |

*请求标头：*
 
|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `x-ms-correlationid` |  在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `authorization`     |  格式为 `"Bearer <access_token>"` ：按 [根据 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述，由发布服务器检索令牌值。  |

*响应代码：*

代码：200返回指定 SaaS 订阅上挂起的恢复操作。

*响应负载示例：*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

如果没有挂起的复原操作，则返回空的 json。

代码：400请求错误：验证失败。

代码：403禁止访问。 授权令牌无效、已过期或未提供。  请求尝试访问使用不同 Azure AD 应用 ID 发布的产品/服务的 SaaS 订阅，该订阅与用于创建授权令牌的应用 ID 不同。

此错误通常是未正确执行 [SaaS 注册](pc-saas-registration.md) 的症状。 

代码：找不到404。  找不到具有的 SaaS 订阅 `subscriptionId` 。

代码：500内部服务器错误。 重试 API 调用。  如果错误仍然存在，请联系 [Microsoft 支持](https://partner.microsoft.com/support/v2/?stage=1)部门。

#### <a name="get-operation-status"></a>获取操作状态

此 API 允许发布服务器跟踪指定的异步操作的状态：  **取消订阅**、 **ChangePlan** 或 **ChangeQuantity**。

`operationId`此 API 调用的可从 **操作-位置**、获取挂起的操作 API 调用的返回值或 `<id>` 在 webhook 调用中收到的参数值检索。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>获取 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
|  `subscriptionId`    |  已购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业 marketplace 授权令牌后获取的。 |
|  `operationId`       |  正在检索的操作的唯一标识符。 |

*请求标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。  如果未提供此值，将在响应标头中生成并提供一个值。 |
|  `x-ms-correlationid` |  在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `authorization`     |  标识发出此 API 调用的发布服务器的唯一访问令牌。  格式为 `"Bearer <access_token>"` ：按 [根据 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述，由发布服务器检索令牌值。  |

*响应代码：*

代码：200获取指定 SaaS 操作的详细信息。 

*响应负载示例：*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeed, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

代码：403禁止访问。 授权令牌无效、已过期或未提供。  请求尝试访问使用不同 Azure AD 应用 ID 发布的产品/服务的 SaaS 订阅，该订阅与用于创建授权令牌的应用 ID 不同。

此错误通常是未正确执行 [SaaS 注册](pc-saas-registration.md) 的症状。 

代码：找不到404。  

* `subscriptionId`找不到具有的订阅。
* `operationId`找不到具有的操作。

代码：500内部服务器错误。  重试 API 调用。  如果错误仍然存在，请联系 [Microsoft 支持](https://partner.microsoft.com/support/v2/?stage=1)部门。

#### <a name="update-the-status-of-an-operation"></a>更新操作的状态

使用此 API 更新挂起操作的状态，以指示该操作在发布服务器端的成功或失败。

`operationId`此 API 调用的可从 **操作-位置**、获取挂起的操作 API 调用的返回值或 `<id>` 在 webhook 调用中收到的参数值检索。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>跳 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  使用2018-08-31。  |
|   `subscriptionId`   |  已购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业 marketplace 授权令牌后获取的。  |
|   `operationId`      |  要完成的操作的唯一标识符。 |

*请求标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。  如果未提供此值，将在响应标头中生成并提供一个值。 |
|   `x-ms-correlationid` |  在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。 |
|  `authorization`     |  标识进行此 API 调用的发布服务器的唯一访问令牌。  格式为 `"Bearer <access_token>"` ：按 [根据 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述，由发布服务器检索令牌值。 |

*请求负载示例：*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*响应代码：*

代码：200调用，通知合作伙伴端上的操作已完成。  例如，此响应可以在发布者端向完成更改座位或计划的信号。

代码：403
- 已禁止。  授权令牌不可用、无效或已过期。 请求可能尝试访问不属于当前发布服务器的订阅。
- 已禁止。  授权令牌无效、已过期或未提供。  请求尝试访问使用不同 Azure AD 应用 ID 发布的产品/服务的 SaaS 订阅，该订阅与用于创建授权令牌的应用 ID 不同。

此错误通常是未正确执行 [SaaS 注册](pc-saas-registration.md) 的症状。

代码：找不到404。

* `subscriptionId`找不到具有的订阅。
* `operationId`找不到具有的操作。

代码：409冲突。  例如，已满足更新的更新。

代码：500内部服务器错误。  重试 API 调用。  如果错误仍然存在，请联系 [Microsoft 支持](https://partner.microsoft.com/support/v2/?stage=1)部门。

## <a name="implementing-a-webhook-on-the-saas-service"></a>在 SaaS 服务上实现 webhook

在合作伙伴中心创建事务 SaaS 产品/服务时，合作伙伴提供要用作 HTTP 终结点的 **连接 Webhook** URL。  此 webhook 由 Microsoft 使用 POST HTTP 调用来调用，通知发布者以下事件发生在 Microsoft 端：

* 如果 SaaS 订阅处于已 *订阅* 状态：
    * ChangePlan 
    * ChangeQuantity
    * 挂起
    * 取消订阅
* SaaS 订阅处于 *挂起* 状态时：
    * 恢复
    * 取消订阅

发布者必须在 SaaS 服务中实现 webhook，以使 SaaS 订阅状态与 Microsoft 端保持一致。  在根据 webhook 通知执行操作之前，要求 SaaS 服务调用获取操作 API 来验证和授权 webhook 调用和负载数据。  处理 webhook 调用后，发布服务器应立即将 HTTP 200 返回到 Microsoft。  此值确认发布服务器已成功接收到 webhook 调用。

>[!Note]
>Webhook URL 服务必须全天候启动并运行，并且随时可以接收来自 Microsoft 时间的新呼叫。  Microsoft 对 webhook 调用重试策略 (500) 重试超过8小时的重试，但如果发布者不接受调用并返回响应，则 webhook 通知的操作最终会在 Microsoft 端失败。

*Webhook 负载示例：*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": " 20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress"
}
```

## <a name="development-and-testing"></a>开发和测试

若要开始开发过程，我们建议在发布方端创建虚拟 API 响应。  这些响应可以基于本文中提供的示例响应。

当发布服务器已准备好进行端到端测试时：

* 将 SaaS 产品/服务发布到受限预览版受众，并将其保留在预览阶段。
* 将计划价格设置为0，以避免在测试期间触发实际计费费用。  另一种方法是设置非零价格，并在24小时内取消所有测试购买。
* 确保所有流都是端到端调用，以模拟真实的客户方案。
* 如果合作伙伴要测试完整的购买和计费流，请通过价格高于 $0 的产品/服务进行测试。  采购将计费，并将生成发票。

可以根据产品/服务的发布位置，从 Azure 门户或 Microsoft AppSource 站点触发采购流。

*更改计划*、 *更改数量* 和 *取消订阅* 操作都是从发布方测试的。  在 Microsoft *，可以从* Azure 门户和管理中心 (门户门户中 Microsoft AppSource 购买) 进行管理。  只能从管理中心触发 *更改数量和计划*。

## <a name="get-support"></a>获取支持

有关发布者支持选项，请参阅 [合作伙伴中心的商业市场计划支持](../support.md) 。

## <a name="next-steps"></a>后续步骤

有关商业应用商店中 SaaS 产品/服务的更多选项，请参阅 [商业 marketplace 计量服务 api](marketplace-metering-service-apis.md) 。

查看并使用本文档所述 Api 之上构建的 [SAAS SDK](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK) 。
