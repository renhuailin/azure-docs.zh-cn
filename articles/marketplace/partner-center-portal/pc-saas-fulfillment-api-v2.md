---
title: Microsoft 商业市场中的 SaaS 履行 API v2
description: 了解如何在 Microsoft AppSource 和 Azure 市场中使用履行 API 版本 2 来创建和管理 SaaS 产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: saasguide
ms.author: souchak
ms.openlocfilehash: 194d9465d43de33f1f05e9587d2e166e9d2831f1
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455126"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>商业市场中的 SaaS 履行 API 版本 2

本文详细介绍了一些可让合作伙伴在 Microsoft AppSource 和 Azure 市场中销售其软件即服务 (SaaS) 产品/服务的 API。 发布者需要实现与这些 API 的集成才能在合作伙伴中心发布可交易的 SaaS 产品/服务。

## <a name="managing-the-saas-subscription-life-cycle"></a>管理 SaaS 订阅生命周期

在最终用户购买 SaaS 订阅后，商业市场将管理该订阅的整个生命周期。 它使用登陆页、履行 API、操作 API 和 Webhook 作为机制来驱动实际的 SaaS 订阅激活、使用、更新和取消。 最终用户的帐单基于 Microsoft 维护的 SaaS 订阅状态。 

### <a name="states-of-a-saas-subscription"></a>SaaS 订阅的状态

下图显示了 SaaS 订阅的状态和适用的操作。

![显示市场中的软件即服务订阅生命周期的示意图。](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>已购买但尚未激活 (PendingFulfillmentStart)

最终用户（或云解决方案提供商）在商业市场中购买 SaaS 产品/服务后，发布者应会收到通知。 然后，发布者可以在发布者端为最终用户创建并配置新的 SaaS 帐户。

创建帐户的过程如下：

1. 在 Microsoft AppSource 或 Azure 门户成功购买后，客户选择适用于 SaaS 产品/服务的“配置”按钮。 或者，客户可以使用他们在购买后不久即会收到的电子邮件中的“配置”按钮。
2. 然后，Microsoft 将在新的浏览器标签页中打开包含令牌参数（商业市场中的购买标识令牌）的登陆页 URL，通知合作伙伴有人购买了产品/服务。

此类调用的示例为 `https://contoso.com/signup?token=<blob>`，而在合作伙伴中心，此 SaaS 产品/服务的登陆页 URL 配置为 `https://contoso.com/signup`。 此令牌为发布者提供一个用于唯一标识 SaaS 购买项和客户的 ID。

>[!NOTE]
>在客户从 Microsoft 端启动配置过程之前，发布者不会收到 SaaS 购买通知。

登陆页 URL 必须每天不间断打开并运行，并且随时可以接收来自 Microsoft 的新调用。 如果登陆页不可用，则客户无法注册并开始使用 SaaS 服务。

接下来，发布者必须通过调用 [SaaS 解析 API](#resolve-a-purchased-subscription) 并输入令牌作为 `x-ms-marketplace-token header` 头参数的值，将令牌传回给 Microsoft。 调用解析 API 后，将交换令牌以获取 SaaS 购买项的详细信息，例如购买项的唯一 ID、购买的产品/服务 ID 和购买的计划 ID。

在登陆页上，客户应通过 Azure Active Directory (Azure AD) 单一登录 (SSO) 登录到新的或现有的 SaaS 帐户。

发布者应实现 SSO，以提供 Microsoft 所需的用户体验来完成此流。 配置 SSO 时，请确保使用多租户 Azure AD 应用程序，并允许使用工作和学校帐户或个人 Microsoft 帐户。 此要求仅适用于登陆页，适用于使用 Microsoft 凭据登录时重定向到 SaaS 服务的用户。 并非每次登录到 SaaS 服务都需要执行 SSO。

> [!NOTE]
>如果 SSO 要求管理员必须授予对某个应用的权限，则合作伙伴中心内的产品/服务说明必须披露需要管理员级别的访问权限。 披露此信息的目的是为了符合[商业市场认证策略](/legal/marketplace/certification-policies#10003-authentication-options)。

登录后，客户应在发布者端完成 SaaS 配置。 然后，发布者必须调用[激活订阅 API](#activate-a-subscription)，以向 Azure 市场发送信号，指出 SaaS 帐户预配已完成。
此操作将启动客户的计费周期。 如果激活订阅 API 调用失败，则不会向客户收取购买费用。


![显示用于预配方案的 API 调用的示意图。](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>活动(已订阅)

“活动(已订阅)”是已预配的 SaaS 订阅的稳定状态。 在 Microsoft 端处理完[激活订阅 API](#activate-a-subscription) 调用后，SaaS 订阅将标记为“已订阅”。 现在，客户可以在发布者端使用该 SaaS 服务，并开始产生费用。

当 SaaS 订阅已处于活动状态时，客户可以从 Azure 门户或 Microsoft 365 管理中心选择“管理 SaaS”体验。 此操作还会导致 Microsoft 结合 token 参数调用登陆页 URL，如同在“激活”流中一样。 发布者应区分现有 SaaS 帐户的新购买项和管理，并相应地处理此登陆页 URL 调用。

#### <a name="being-updated-subscribed"></a>正在更新(已订阅)

此操作表示 Microsoft 和发布者正在处理对现有活动 SaaS 订阅的更新。 可通过以下方式发起这种更新：

- 客户从商业市场发起。
- 云解决方案提供商从商业市场发起。
- 客户从发布者的 SaaS 站点发起（但不适用于云解决方案提供商进行的购买）。

可对 SaaS 订阅进行两种类型的更新：

- 当客户选择订阅的另一个计划时更新计划。
- 当客户更改订阅的购买座席数量时更新数量。

只能更新活动订阅。 当订阅正在更新时，其在 Microsoft 端的状态仍保持为“活动”状态。

##### <a name="update-initiated-from-the-commercial-marketplace"></a>从商业市场发起的更新

在此流中，客户从 Azure 门户或 Microsoft 365 管理中心更改订阅计划或座席数量。

1. 输入更新后，Microsoft 将结合 action 和其他相关参数的值调用发布者的 Webhook URL，该 URL 是在合作伙伴中心的“连接 Webhook”字段中配置的。 
1. 发布者端应该对 SaaS 服务进行所需的更改，并在完成后通过调用[更新操作状态 API](#update-the-status-of-an-operation) 来通知 Microsoft。
1. 如果发送修补命令时出现失败状态，则更新过程不会在 Microsoft 端完成。 SaaS 订阅将保留现有的计划和座席数量。

> [!NOTE]
> 收到 Webhook 通知后的 10 秒时限内，发布者应调用 PATCH 来[更新操作状态 API](#update-the-status-of-an-operation) 并返回失败/成功响应。 如果在 10 秒内未收到操作状态 PATCH，则更改计划将自动修补为“成功”。 

下图显示了从商业市场发起的更新方案的 API 调用序列。

![显示从市场发起的更新的 API 调用的示意图。](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>从发布者发起的更新

在此流中，客户更改从 SaaS 服务本身购买的订阅计划或座席数量。 

1. 发布者代码必须在发布者端进行请求的更改之前，调用[更改计划 API](#change-the-plan-on-the-subscription) 和/或[更改数量 API](#change-the-quantity-of-seats-on-the-saas-subscription)。 

1. Microsoft 将应用对订阅所做的更改，然后通过“连接 Webhook”通知发布者应用相同的更改。

1. 只有在应用这些更改后，发布者才能对 SaaS 订阅进行所需的更改，并在更改完成后调用[更新操作状态 API](#update-the-status-of-an-operation) 来通知 Microsoft。

下图显示了从发布者端发起的更新方案的 API 调用序列。

![显示从发布者端发起的更新的 API 调用的示意图。](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>已暂停(已暂停)

此状态表示尚未收到客户的 SaaS 服务付款。 Microsoft 会向发布者通知 SaaS 订阅状态的此项更改。 该通知是在将 action 参数设置为 Suspended 的情况下，通过调用 Webhook 发出的 。

发布者不一定可以在发布者端对 SaaS 服务进行更改。 我们建议发布者向订阅已暂停的客户提供此信息，并限制或阻止客户访问 SaaS 服务。 有可能永远收不到付款。

Microsoft 为客户提供 30 天宽限期，过后会自动取消订阅。 当订阅处于“已暂停”状态时：

* 合作伙伴或 ISV 必须使 SaaS 帐户保持可恢复状态，以便可以还原完整功能，且不丢失任何数据或设置。
* 合作伙伴或 ISV 应该预料到在宽限期收到付款后，客户会请求恢复订阅，同时也要预料到在宽限期结束后客户请求取消预配订阅。 这两个请求都将通过 Webhook 机制发送。

在发布者采取任何措施之前，订阅状态在 Microsoft 端将更改为“已暂停”。 只能暂停处于活动状态的订阅。

#### <a name="reinstated-suspended"></a>已恢复(已暂停)

此操作指示客户的付款方式再次生效，已经为 SaaS 订阅付款，并且订阅正在恢复。 在这种情况下： 

1. Microsoft 会在将 action 参数设置为 Reinstate 值的情况下调用 Webhook 。
1. 发布者需确保订阅在发布者端完全恢复正常运行。
1. 发布者调用[修补操作 API](#update-the-status-of-an-operation) 并返回成功状态。
1. 恢复过程成功，再次向客户计收 SaaS 订阅费。 

如果发送修补命令时出现失败状态，则恢复过程不会在 Microsoft 端完成，并且订阅将保持“已暂停”状态 。

只能恢复已暂停的订阅。 在恢复已暂停的 SaaS 订阅时，其状态仍保持为“已暂停”。 完成此操作后，订阅状态将变为“活动”。

#### <a name="renewed-subscribed"></a>已续订(已订阅)

在一个月或一年的订阅期限结束时，Microsoft 会自动续订 SaaS 订阅。 对于所有 SaaS 订阅，自动续订设置的默认值为 true。 将按照固定的时间间隔继续续订处于活动状态的 SaaS 订阅。 续订订阅时，Microsoft 不会通知发布者。 客户可以通过 Microsoft 365 管理门户关闭 SaaS 订阅自动续订。 在这种情况下，当前计费期限结束时会自动取消 SaaS 订阅。 客户也可以随时取消 SaaS 订阅。

只会自动续订处于活动状态的订阅。 在续订过程中以及在自动续订成功后，订阅将保持活动状态。 续订后，订阅期限的开始和结束日期将更新为新期限的日期。

如果由于付款问题而导致自动续订失败，订阅将变为“已暂停”状态，发布者将收到通知。

#### <a name="canceled-unsubscribed"></a>已取消(已取消订阅) 

当客户或云解决方案提供商在发布者站点、Azure 门户或 Microsoft 365 管理中心显式执行了订阅取消操作时，订阅将进入此状态。 如果在处于“已暂停”状态 30 天后客户仍未支付逾期款项，则也可能会隐式取消其订阅。

在发布者收到取消 Webhook 调用后，他们应将客户数据至少保留七天，以备按请求予以恢复。 只有在保留期过后，才能删除客户数据。

可以在 SaaS 订阅生命周期中的任何时间点取消该订阅。 取消订阅后，无法将它重新激活。

## <a name="api-reference"></a>API 参考

本部分介绍 SaaS 订阅 API 和操作 API。

订阅 API 应该用于处理从购买到取消的整个 SaaS 订阅生命周期。

操作 API 应该用于：

* 验证和确认已处理的 Webhook 调用。
* 获取等待操作的应用（正在等待发布者确认）列表。

> [!NOTE]
> 我们即将强制要求使用 TLS 版本 1.2 作为 HTTPS 通信的最低版本。 请确保在代码中使用此 TLS 版本。 TLS 版本 1.0 和 1.1 即将弃用。

### <a name="subscription-apis"></a>订阅 API

#### <a name="resolve-a-purchased-subscription"></a>解析已购买的订阅

通过解析终结点，发布者可将商业市场中的购买标识令牌（在[已购买但尚未激活](#purchased-but-not-yet-activated-pendingfulfillmentstart)部分中称为令牌）交换为持久性的已购买 SaaS 订阅 ID 及其详细信息。

当客户重定向到合作伙伴的登陆页 URL 时，客户标识令牌将在此 URL 调用中作为 token 参数传递。 发布者应使用此令牌，并发出请求来解析此令牌。 解析 API 响应包含 SaaS 订阅 ID 以及用于唯一标识购买项的其他详细信息。 随登陆页 URL 调用一起提供的令牌的有效期通常为 24 个小时。 如果你收到的令牌已过期，我们建议你向最终用户提供以下指导：

“我们无法识别此购买项。 请在 Azure 门户或 Microsoft 365 管理中心重新打开此 SaaS 订阅，然后再次选择‘配置帐户’或‘管理帐户’”。

调用解析 API 会返回所有处于受支持状态的 SaaS 订阅的订阅详细信息和状态。

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用 2018-08-31。   |

*请求标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。 |
|  `x-ms-correlationid` |  在客户端上执行的操作的唯一字符串值。 此参数将来自客户端操作的所有事件与服务器端的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `authorization`     |  唯一的访问令牌，用于标识发出此 API 调用的发布者。 当发布者按照[基于 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述检索了令牌值时，格式为 `"Bearer <accessaccess_token>"`。 |
|  `x-ms-marketplace-token`  | 要解析的购买标识 token 参数。  当客户重定向到 SaaS 合作伙伴的网站时，该令牌将传入到登陆页 URL 调用中（例如：`https://contoso.com/signup?token=<token><authorization_token>`）。 <br> <br>  请注意，要编码的 token 值是登陆页 URL 的一部分，因此在将它用作此 API 调用中的参数之前，需要先对其进行解码。  <br> <br> 下面是 URL 中的编码字符串示例：`contoso.com/signup?token=ab%2Bcd%2Fef`，其中的 token 为 `ab%2Bcd%2Fef`。  此令牌在解码后为：`Ab+cd/ef` |
| | |

响应代码：

代码：200 基于提供的 `x-ms-marketplace-token` 返回唯一的 SaaS 订阅标识符。

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

代码：400 错误的请求。 `x-ms-marketplace-token` 缺失、格式不正确、无效或已过期。

代码：403 禁止。 授权令牌无效、已过期或未提供。  请求正在尝试访问某个产品/服务的 SaaS 订阅，而发布该产品/服务时使用的 Azure AD 应用 ID 不同于创建授权令牌时所用的应用 ID。

此错误体现出的症状通常是无法正常执行 [SaaS 注册](pc-saas-registration.md)。

代码：500 内部服务器错误。  重试 API 调用。  如果错误依然出现，请联系 [Microsoft 支持人员](https://go.microsoft.com/fwlink/?linkid=2165533)。

#### <a name="activate-a-subscription"></a>激活订阅

为最终用户配置 SaaS 帐户后，发布者必须在 Microsoft 端调用激活订阅 API。  除非此 API 调用成功，否则不会向客户计费。

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  --------   |  ---------------  |
| `ApiVersion`  |  使用 2018-08-31。   |
| `subscriptionId` | 购买的 SaaS 订阅的唯一标识符。  此 ID 是使用[解析 API](#resolve-a-purchased-subscription) 解析商业市场授权令牌后获取的。
 |

*请求标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。  如果未提供此值，将在响应标头中生成并提供一个值。 |
| `x-ms-correlationid` |  在客户端上执行的操作的唯一字符串值。  此字符串将来自客户端操作的所有事件与服务器端的事件相关联。  如果未提供此值，将在响应标头中生成并提供一个值。 |
| `authorization`      |  唯一的访问令牌，用于标识发出此 API 调用的发布者。 当发布者按照[基于 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述检索了令牌值时，格式为 `"Bearer <access_token>"`。 |

请求有效负载示例：

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

响应代码：

代码：200 订阅在 Microsoft 端标记为“已订阅”。

此调用没有响应正文。

代码：400 错误的请求: 验证失败。

* 请求有效负载中不存在 `planId`。
* 请求有效负载中的 `planId` 与购买的项不匹配。
* 请求有效负载中的 `quantity` 与购买的项不匹配
* SaaS 订阅处于“已订阅”或“已暂停”状态 。

代码：403 禁止。 授权令牌无效、已过期或未提供。 请求正在尝试访问某个产品/服务的 SaaS 订阅，而发布该产品/服务时使用的 Azure AD 应用 ID 不同于创建授权令牌时所用的应用 ID。

此错误体现出的症状通常是无法正常执行 [SaaS 注册](pc-saas-registration.md)。

代码：404 找不到。 SaaS 订阅处于“已取消订阅”状态。

代码：500 内部服务器错误。  重试 API 调用。  如果错误依然出现，请联系 [Microsoft 支持人员](https://go.microsoft.com/fwlink/?linkid=2165533)。

#### <a name="get-list-of-all-subscriptions"></a>获取所有订阅的列表

此 API 检索发布者在商业市场中发布的所有产品/服务的所有已购买 SaaS 订阅的列表。  将返回处于所有可能状态的 SaaS 订阅。 另外还会返回已取消订阅的 SaaS 订阅，因为此信息尚未在 Microsoft 端删除。

该 API 分页返回结果，每页包含 100 条结果。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  --------   |  ---------------  |
| `ApiVersion`  |  使用 2018-08-31。  |
| `continuationToken`  | 可选参数。 若要检索第一页结果，请将其留空。  使用 `@nextLink` 参数中返回的值检索下一页。 |

*请求标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。 |
| `x-ms-correlationid` |  在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。 |
| `authorization`      |  唯一的访问令牌，用于标识发出此 API 调用的发布者。  当发布者按照[基于 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述检索了令牌值时，格式为 `"Bearer <access_token>"`。 |

响应代码：

代码：200 基于发布者的授权令牌，返回此发布者发布的所有产品/服务的所有现有订阅的列表。

响应正文示例：

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

如果找不到此发布者的任何已购买 SaaS 订阅，则返回空响应正文。

代码：403 禁止。 授权令牌不可用、无效或已过期。

此错误体现出的症状通常是无法正常执行 [SaaS 注册](pc-saas-registration.md)。 

代码：500 内部服务器错误。 重试 API 调用。  如果错误依然出现，请联系 [Microsoft 支持人员](https://go.microsoft.com/fwlink/?linkid=2165533)。

#### <a name="get-subscription"></a>获取订阅

此 API 检索发布者在商业市场中发布的某个 SaaS 产品/服务的指定已购买 SaaS 订阅。 使用此调用将按 ID 获取特定 SaaS 订阅的所有可用信息，而不是通过调用用于获取所有订阅的列表的 API 来获取这些信息。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   使用 2018-08-31。 |
| `subscriptionId`     |  购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业市场授权令牌后获取的。 |

*请求标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。 |
|  `x-ms-correlationid` |  在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。 |
|  `authorization`     | 唯一的访问令牌，用于标识发出此 API 调用的发布者。 当发布者按照[基于 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述检索了令牌值时，格式为 `"Bearer <access_token>"`。  |

响应代码：

代码：200 基于提供的 `subscriptionId` 返回 SaaS 订阅的详细信息。

响应正文示例：

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

代码：403 禁止。 授权令牌无效、已过期或未提供。 请求正在尝试访问某个产品/服务的 SaaS 订阅，而发布该产品/服务时使用的 Azure AD 应用 ID 不同于创建授权令牌时所用的应用 ID。

此错误体现出的症状通常是无法正常执行 [SaaS 注册](pc-saas-registration.md)。 

代码：404 找不到。  找不到具有指定 `subscriptionId` 的 SaaS 订阅。

代码：500 内部服务器错误。  重试 API 调用。  如果错误依然出现，请联系 [Microsoft 支持人员](https://go.microsoft.com/fwlink/?linkid=2165533)。

#### <a name="list-available-plans"></a>列出可用计划

此 API 检索某个 SaaS 产品/服务的所有计划，这些计划由此产品/服务的特定购买项的 `subscriptionId` 标识。  使用此调用可以获取 SaaS 订阅受益人可为订阅更新的所有私用和公用计划的列表。  返回的计划可在已购买计划所在的同一地理位置中使用。

除了返回已购买的计划外，此调用还返回可供该客户使用的计划列表。  可以在发布者站点上向最终用户提供该列表。  最终用户可将订阅计划更改为返回的列表中的任一计划。  将计划更改为列表中不包含的计划将会失败。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用 2018-08-31。  |
|  `subscriptionId`    |  购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业市场授权令牌后获取的。 |

*请求标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。  如果未提供此值，将在响应标头中生成并提供一个值。 |
|  `x-ms-correlationid`  |  在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。 |
|  `authorization`     |  唯一的访问令牌，用于标识发出此 API 调用的发布者。  当发布者按照[基于 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述检索了令牌值时，格式为 `"Bearer <access_token>"`。  |

响应代码：

代码：200 返回现有 SaaS 订阅的所有可用计划的列表，包括已购买的计划。

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

如果找不到 `subscriptionId`，则返回空响应正文。

代码：403 禁止。 授权令牌无效、已过期或未提供。  请求正在尝试访问某个产品/服务的 SaaS 订阅，而发布该产品/服务时使用的 Azure AD 应用 ID 可能不同于创建授权令牌时所用的应用 ID。

此错误体现出的症状通常是无法正常执行 [SaaS 注册](pc-saas-registration.md)。 

代码：500 内部服务器错误。  重试 API 调用。  如果错误依然出现，请联系 [Microsoft 支持人员](https://go.microsoft.com/fwlink/?linkid=2165533)。

#### <a name="change-the-plan-on-the-subscription"></a>更改订阅的计划

使用此 API 可将为 SaaS 订阅购买的现有计划更新为新计划（公用或私用）。  在发布者端更改在商业市场中购买的 SaaS 订阅的计划后，发布者必须调用此 API。

只能对处于“活动”状态的订阅调用此 API。  任何计划都可更改为任何其他现有计划（公用或私用），但不能更改为其自身。  对于私用计划，必须在合作伙伴中心将客户的租户定义为计划受众的一部分。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用 2018-08-31。  |
| `subscriptionId`     | 购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业市场授权令牌后获取的。 |

*请求标头：*
 
|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `x-ms-correlationid`  | 在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `authorization`     |  唯一的访问令牌，用于标识发出此 API 调用的发布者。  当发布者按照[基于 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述检索了令牌值时，格式为 `"Bearer <access_token>"`。 |

请求有效负载示例：

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

响应代码：

代码：202 已接受并以异步方式处理更改计划请求。  合作伙伴应轮询 Operation-Location URL，以确定更改计划请求是成功还是失败。  应每隔几秒轮询一次，直到收到的最终操作状态为 Failed、Succeed 或 Conflict  。  最终操作状态应该很快就会返回，但某些情况下可能要在几分钟后才返回。

即将在商业市场端成功完成操作时，合作伙伴还会获得 Webhook 通知。  只有在收到通知后，发布者才能在发布者端进行计划更改。

响应头：

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  用于获取操作状态的 URL。  例如，`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`。 |

代码：400 错误的请求: 验证失败。

* 新计划不存在，或者不可用于此特定 SaaS 订阅。
* 新计划与当前计划相同。
* SaaS 订阅状态不是“已订阅”。
* SaaS 订阅的更新操作未包含在 `allowedCustomerOperations` 中。

代码：403 禁止。 授权令牌无效、已过期或未提供。  请求正在尝试访问某个产品/服务的 SaaS 订阅，而发布该产品/服务时使用的 Azure AD 应用 ID 不同于创建授权令牌时所用的应用 ID。

此错误体现出的症状通常是无法正常执行 [SaaS 注册](pc-saas-registration.md)。

代码：404 找不到。  找不到具有 `subscriptionId` 的 SaaS 订阅。

代码：500 内部服务器错误。  重试 API 调用。  如果错误依然出现，请联系 [Microsoft 支持人员](https://go.microsoft.com/fwlink/?linkid=2165533)。

>[!NOTE]
>每次可以更改计划或座席数量，但不能同时更改两者。

>[!Note]
>只有在收到最终用户对更改的显式批准后，才能调用此 API。

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>更改 SaaS 订阅的座席数量

使用此 API 可以更新（增加或减少）为 SaaS 订阅购买的座席数量。  在发布者端更改在商业市场中创建的 SaaS 订阅的座席数量后，发布者必须调用此 API。

座席数量不能超过当前计划中允许的数量。  如果超过，则发布者应该先更改计划，然后再更改座席数量。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用 2018-08-31。  |
|  `subscriptionId`     | 购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业市场授权令牌后获取的。  |

*请求标头：*
 
|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `x-ms-correlationid`  | 在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `authorization`     | 唯一的访问令牌，用于标识发出此 API 调用的发布者。  当发布者按照[基于 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述检索了令牌值时，格式为 `"Bearer <access_token>"`。  |

请求有效负载示例：

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

响应代码：

代码：202 已接受并以异步方式处理更改数量请求。 合作伙伴应轮询 Operation-Location URL，以确定更改数量请求是成功还是失败。  应每隔几秒轮询一次，直到收到的最终操作状态为 Failed、Succeed 或 Conflict  。  最终操作状态应该很快就会返回，但某些情况下可能要在几分钟后才返回。

即将在商业市场端成功完成操作时，合作伙伴还会获得 Webhook 通知。  只有在收到通知后，发布者才能在发布者端进行数量更改。

响应头：

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  链接到资源以获取操作状态。  例如，`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`。  |

代码：400 错误的请求: 验证失败。

* 新数量大于或小于当前计划限制。
* 缺少新数量。
* 新数量与当前数量相同。
* SaaS 订阅状态不是“已订阅”。
* SaaS 订阅的更新操作未包含在 `allowedCustomerOperations` 中。

代码：403 禁止。  授权令牌无效、已过期或未提供。  请求正在尝试访问一个不属于当前发布者的订阅。

此错误体现出的症状通常是无法正常执行 [SaaS 注册](pc-saas-registration.md)。 

代码：404 找不到。  找不到具有 `subscriptionId` 的 SaaS 订阅。

代码：500 内部服务器错误。  重试 API 调用。  如果错误依然出现，请联系 [Microsoft 支持人员](https://go.microsoft.com/fwlink/?linkid=2165533)。

>[!Note]
>每次只能更改计划或数量，而不能同时更改两者。

>[!Note]
>只有在收到最终用户对更改的显式批准后，才能调用此 API。

#### <a name="cancel-a-subscription"></a>取消订阅

使用此 API 可以取消订阅指定的 SaaS 订阅。  发布者不一定要使用此 API，我们建议将客户定向到商业市场来取消 SaaS 订阅。

如果发布者决定在发布者端取消在商业市场中购买的 SaaS 订阅，则必须调用此 API。  完成此调用后，订阅的状态将在 Microsoft 端变为“已取消订阅”。

如果在以下宽限期内取消订阅，则不会向客户计费：

* 对于按月订阅：激活后的 24 小时内。
* 对于按年订阅：激活后的 14 天内。

如果在上述宽限期过后取消订阅，则会向客户计费。  取消后，客户立即就会失去在 Microsoft 端访问 SaaS 订阅的权限。 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Delete `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用 2018-08-31。  |
|  `subscriptionId`     | 购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业市场授权令牌后获取的。  |

*请求标头：*
 
|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `x-ms-correlationid`  | 在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `authorization`     |  唯一的访问令牌，用于标识发出此 API 调用的发布者。  当发布者按照[基于 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述检索了令牌值时，格式为 `"Bearer <access_token>"`。 |

响应代码：

代码：202 已接受并以异步方式处理取消订阅请求。  合作伙伴应轮询 Operation-Location URL，以确定此请求是成功还是失败。  应每隔几秒轮询一次，直到收到的最终操作状态为 Failed、Succeed 或 Conflict  。  最终操作状态应该很快就会返回，但某些情况下可能要在几分钟后才返回。

在商业市场端成功完成操作时，合作伙伴还会获得 Webhook 通知。  只有在收到通知后，发布者才能在发布者端取消订阅。

响应头：

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  链接到资源以获取操作状态。  例如，`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`。 |

代码：400 错误的请求。  删除操作未包含在此 SaaS 订阅的 `allowedCustomerOperations` 列表中。

代码：403 禁止。  授权令牌无效、已过期或不可用。 请求正在尝试访问某个产品/服务的 SaaS 订阅，而发布该产品/服务时使用的 Azure AD 应用 ID 不同于创建授权令牌时所用的应用 ID。

此错误体现出的症状通常是无法正常执行 [SaaS 注册](pc-saas-registration.md)。

代码：404 找不到。  找不到具有 `subscriptionId` 的 SaaS 订阅。

代码：500 内部服务器错误。 重试 API 调用。  如果错误依然出现，请联系 [Microsoft 支持人员](https://go.microsoft.com/fwlink/?linkid=2165533)。

### <a name="operations-apis"></a>操作 API

#### <a name="list-outstanding-operations"></a>列出未完成的操作 

获取指定 SaaS 订阅的等待中操作列表。  发布者应该通过调用[操作修补 API](#update-the-status-of-an-operation) 来确认返回的操作。

目前只会返回恢复操作作为此 API 调用的响应。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  使用 2018-08-31。         |
|    `subscriptionId` | 购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业市场授权令牌后获取的。  |

*请求标头：*
 
|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `x-ms-correlationid` |  在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `authorization`     |  当发布者按照[基于 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述检索了令牌值时，格式为 `"Bearer <access_token>"`。  |

响应代码：

代码：200 返回针对指定 SaaS 订阅的等待中恢复操作。

*响应有效负载示例：*

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

如果没有任何恢复操作等待执行，则返回空 JSON。

代码：400 错误的请求: 验证失败。

代码：403 禁止。 授权令牌无效、已过期或未提供。  请求正在尝试访问某个产品/服务的 SaaS 订阅，而发布该产品/服务时使用的 Azure AD 应用 ID 不同于创建授权令牌时所用的应用 ID。

此错误体现出的症状通常是无法正常执行 [SaaS 注册](pc-saas-registration.md)。 

代码：404 找不到。  找不到具有 `subscriptionId` 的 SaaS 订阅。

代码：500 内部服务器错误。 重试 API 调用。  如果错误依然出现，请联系 [Microsoft 支持人员](https://go.microsoft.com/fwlink/?linkid=2165533)。

#### <a name="get-operation-status"></a>获取操作状态

发布者可以使用此 API 跟踪指定异步操作的状态：Unsubscribe、ChangePlan 或 ChangeQuantity  。

此 API 调用的 `operationId` 可以从 Operation-Location 返回的值、获取等待中操作 API 调用，或者在 Webhook 调用中收到的 `<id>` 参数值检索到。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用 2018-08-31。  |
|  `subscriptionId`    |  购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业市场授权令牌后获取的。 |
|  `operationId`       |  要检索的操作的唯一标识符。 |

*请求标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。  如果未提供此值，将在响应标头中生成并提供一个值。 |
|  `x-ms-correlationid` |  在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。  |
|  `authorization`     |  唯一的访问令牌，用于标识发出此 API 调用的发布者。  当发布者按照[基于 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述检索了令牌值时，格式为 `"Bearer <access_token>"`。  |

响应代码：

代码：200 获取指定 SaaS 操作的详细信息。 

*响应有效负载示例：*

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
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

代码：403 禁止。 授权令牌无效、已过期或未提供。  请求正在尝试访问某个产品/服务的 SaaS 订阅，而发布该产品/服务时使用的 Azure AD 应用 ID 不同于创建授权令牌时所用的应用 ID。

此错误体现出的症状通常是无法正常执行 [SaaS 注册](pc-saas-registration.md)。 

代码：404 找不到。  

* 找不到具有 `subscriptionId` 的订阅。
* 找不到具有 `operationId` 的操作。

代码：500 内部服务器错误。  重试 API 调用。  如果错误依然出现，请联系 [Microsoft 支持人员](https://go.microsoft.com/fwlink/?linkid=2165533)。

#### <a name="update-the-status-of-an-operation"></a>更新操作的状态

使用此 API 可以更新等待中操作的状态，以在发布者端指示该操作是成功还是失败。

此 API 调用的 `operationId` 可以从 Operation-Location 返回的值、获取等待中操作 API 调用，或者在 Webhook 调用中收到的 `<id>` 参数值检索到。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查询参数：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  使用 2018-08-31。  |
|   `subscriptionId`   |  购买的 SaaS 订阅的唯一标识符。  此 ID 是使用解析 API 解析商业市场授权令牌后获取的。  |
|   `operationId`      |  要完成的操作的唯一标识符。 |

*请求标头：*

|  参数         | 值             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。  如果未提供此值，将在响应标头中生成并提供一个值。 |
|   `x-ms-correlationid` |  在客户端上执行的操作的唯一字符串值。  此参数将来自客户端操作的所有事件与服务器端的事件关联起来。  如果未提供此值，将在响应标头中生成并提供一个值。 |
|  `authorization`     |  唯一的访问令牌，用于标识发出此 API 调用的发布者。  当发布者按照[基于 Azure AD 应用获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述检索了令牌值时，格式为 `"Bearer <access_token>"`。 |

请求有效负载示例：

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

响应代码：

代码：200 用于告知已在合作伙伴端完成某项操作的调用。  例如，此响应可以指出已在发布者端完成座席数量或计划的更改。

代码：403
- 已禁止。  授权令牌不可用、无效或已过期。 请求可能正在尝试访问一个不属于当前发布者的订阅。
- 已禁止。  授权令牌无效、已过期或未提供。  请求正在尝试访问某个产品/服务的 SaaS 订阅，而发布该产品/服务时使用的 Azure AD 应用 ID 不同于创建授权令牌时所用的应用 ID。

此错误体现出的症状通常是无法正常执行 [SaaS 注册](pc-saas-registration.md)。

代码：404 找不到。

* 找不到具有 `subscriptionId` 的订阅。
* 找不到具有 `operationId` 的操作。

代码：409 冲突。  例如，已履行较新的更新。

代码：500 内部服务器错误。  重试 API 调用。  如果错误依然出现，请联系 [Microsoft 支持人员](https://go.microsoft.com/fwlink/?linkid=2165533)。

## <a name="implementing-a-webhook-on-the-saas-service"></a>在 SaaS 服务上实现 Webhook

在合作伙伴中心创建可交易的 SaaS 产品/服务时，合作伙伴需提供要用作 HTTP 终结点的连接 Webhook URL。  Microsoft 使用 POST HTTP 调用来调用此 Webhook，以通知发布者以下事件将在 Microsoft 端发生：

* 当 SaaS 订阅处于“已取消订阅”状态时：
    * ChangePlan 
    * ChangeQuantity
    * 挂起
    * 取消订阅
* 当 SaaS 订阅处于“已暂停”状态时：
    * 恢复
    * 取消订阅

发布者必须在 SaaS 服务中实现一个 Webhook，使 SaaS 订阅状态与 Microsoft 端保持一致。  在根据 Webhook 通知执行操作之前，SaaS 服务必须调用获取操作 API 来验证并授权 Webhook 调用和有效负载数据。  处理 Webhook 调用后，发布者应立即向 Microsoft 返回 HTTP 200。  此值确认发布者已成功收到 Webhook 调用。

>[!Note]
>Webhook URL 服务必须每天不间断地运行，并且随时可以接收来自 Microsoft 的新调用。  Microsoft 确实允许对 Webhook 调用使用重试策略（在 8 小时内可重试 500 次），但如果发布者不接受该调用并返回响应，则 Webhook 通知中所提到的操作最终会在 Microsoft 端失败。

Webhook 有效负载示例：

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
  "status": "InProgress"
}
```

## <a name="development-and-testing"></a>开发和测试

若要开始开发过程，我们建议在发布者端创建虚构的 API 响应。  这些响应可以基于本文中提供的示例响应。

当发布者已准备好进行端到端测试时：

* 将 SaaS 产品/服务发布到受限预览版受众，并将其保留在预览阶段。
* 将计划价格设置为 0，以免在测试期间触发实际计费。  另一种做法是设置非零价格，并在 24 小时内取消所有测试购买项。
* 确保端到端地调用所有流，以模拟真实的客户场景。
* 如果合作伙伴想要测试完整的购买和计费流，可以使用定价高于 $0 的产品/服务进行测试。  这会对购买项计费，并生成发票。

可以根据产品/服务的发布位置，从 Azure 门户或 Microsoft AppSource 站点触发购买流。

从发布者端测试更改计划、更改数量和取消订阅操作  。  在 Microsoft 端，可以从 Azure 门户和管理中心（用于管理 Microsoft AppSource 购买项的门户）触发取消订阅操作。  只能从管理中心触发更改数量和计划操作。

## <a name="get-support"></a>获取支持

有关发布者支持选项，请参阅[在合作伙伴中心为商业市场计划提供支持](../support.md)。

## <a name="next-steps"></a>后续步骤

有关商业市场中 SaaS 产品/服务的其他选项，请参阅[商业市场计量服务 API](../marketplace-metering-service-apis.md)。

查看并使用[适用于不同编程语言的客户端和示例](https://github.com/microsoft/commercial-marketplace-samples)。