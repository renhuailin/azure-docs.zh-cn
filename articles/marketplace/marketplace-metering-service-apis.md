---
title: 计量服务 API - Microsoft 商业市场
description: 通过使用事件 API，可以发出 Microsoft AppSource 和 Azure 市场中 SaaS 产品/服务的使用事件。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 8a8f6052ba2ce66e22208972fa84b7af1f48dd3e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739104"
---
# <a name="marketplace-metered-billing-apis"></a>市场按流量计费 API

当发布者为要在合作伙伴中心发布的产品/服务创建自定义计量维度时，应使用按流量计费 API。 如果购买的任何产品/服务具有一项或多项计划包含用于发出使用事件的自定义维度，则需要与按流量计费 API 集成。

要详细了解如何创建适用于 SaaS 的自定义计量维度，请参阅 [SaaS 按流量计费](partner-center-portal/saas-metered-billing.md)。

要详细了解如何使用托管应用计划为 Azure 应用程序产品/服务创建自定义计量维度，请参阅[配置 Azure 应用程序产品/服务设置详细信息](azure-app-offer-setup.md#configure-your-azure-application-offer-setup-details)。

## <a name="enforcing-tls-12-note"></a>强制使用 TLS 1.2 说明

我们强制要求使用 TLS 1.2 版本作为 HTTPS 通信的最低版本。 请确保在代码中使用此 TLS 版本。 TLS 1.0 和 1.1 版本已弃用，连接尝试将被拒绝。

## <a name="metered-billing-single-usage-event"></a>按流量计费的单一使用事件

发布者应调用使用事件 API，以针对为特定客户所购计划（订阅的）活动资源发出使用事件。 发布产品/服务时，将为发布者定义的计划的每个自定义维度分别发出使用事件。

在一个日历日中，每小时只可发出一个使用事件。 例如，在今天上午 8:15，你可以发出一个使用事件。 如果此事件被接受，则下一个使用事件将从今天上午 9:00 开始接受。 如果在今天 8:15 到 8:59:59 之间再发送一个事件，它将因为重复而被拒绝。 应累积一小时内使用的所有单位，然后在一个事件中将其发出。

在一个日历日中，针对每项资源每小时只可发出一个使用事件。 如果在一小时内使用了多个单位，则累积该小时内使用的所有单位，然后在一个事件中将其发出。 只能发出最近 24 小时内的使用事件。 如果在 8:00 到 8:59:59 之间的任何时刻发出一个使用事件（且它被接受）然后在当天 8:00 到 8:59:59 之间又发送了一个事件，它将因为重复而被拒绝。

**POST**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*查询参数：*

| 参数 | 建议          |
| ---------- | ---------------------- |
| `ApiVersion` | 使用 2018-08-31。 |
| | |

*请求标头：*

| Content-type       | 使用 `application/json`  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | 用于跟踪来自客户端的请求的唯一字符串值，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| `x-ms-correlationid` | 客户端上的操作的唯一字符串值。 此参数将来自客户端操作的所有事件与服务器端的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。 |
| `authorization`   | 唯一的访问令牌，用于标识发出此 API 调用的 ISV。 如果发布者按如下所述检索令牌值，则格式为 `"Bearer <access_token>"`： <br> <ul> <li> [使用 HTTP POST 获取令牌](partner-center-portal/pc-saas-registration.md#get-the-token-with-an-http-post)（针对 SaaS）。 </li> <li> [身份验证策略](marketplace-metering-service-authentication.md)（针对托管应用程序）。 </li> </ul> |
| | |

请求正文示例：

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId` 对于发出自定义计量的 SaaS 应用和托管应用具有不同的含义。 

对于 Azure 应用程序托管应用计划，`resourceId` 是托管应用 `resource group Id`。 可在[使用 Azure 管理的标识令牌](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)中找到用于提取它的示例脚本。 

对于 SaaS 产品/服务，`resourceId` 是 SaaS 订阅 ID。 有关 SaaS 订阅的更多详细信息，请参阅[列出订阅](partner-center-portal/pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)。

### <a name="responses"></a>响应

代码：200<br>
没问题。 Microsoft 端已接受并记录发出的使用情况，供进一步处理和计费。

响应有效负载示例： 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

代码：400 <br>
请求错误。

* 未提供请求数据或提供了无效数据。
* `effectiveStartTime` 是在 24 小时前。 事件已过期。
* SaaS 订阅未处于“已订阅”状态。

响应有效负载示例： 

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

代码：403<br>

已禁止。 未提供授权令牌，令牌无效或已过期。  或者请求正在尝试访问某个产品/服务的订阅，而发布该产品/服务时使用的 Azure AD 应用 ID 不同于创建授权令牌时所用的应用 ID。

代码：409<br>
冲突。 已成功报告了指定资源 ID、有效使用日期和时间的使用事件。

响应有效负载示例： 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>按流量计费的批量使用事件

通过批量使用事件 API，可一次性发出多项已购资源的使用事件。 你还可使用它为同一资源发出多个使用事件，只要日历时间不同即可。 一个批次中的最大事件数为 25。

**POST:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*查询参数：*

| 参数  | 建议     |
| ---------- | -------------------- |
| `ApiVersion` | 使用 2018-08-31。 |

*请求标头：*

| Content-type       | 使用 `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | 用于跟踪来自客户端的请求的唯一字符串值，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| `x-ms-correlationid` | 客户端上的操作的唯一字符串值。 此参数将来自客户端操作的所有事件与服务器端的事件关联起来。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| `authorization`      | 唯一的访问令牌，用于标识发出此 API 调用的 ISV。 如果发布者按如下所述检索令牌值，则格式为 `Bearer <access_token>`： <br> <ul> <li> [使用 HTTP POST 获取令牌](partner-center-portal/pc-saas-registration.md#get-the-token-with-an-http-post)（针对 SaaS）。 </li> <li> [身份验证策略](./marketplace-metering-service-authentication.md)（针对托管应用程序）。 </li> </ul> |
| | |


请求正文示例：

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId` 对于发出自定义计量的 SaaS 应用和托管应用具有不同的含义。 

对于 Azure 应用程序托管应用计划，`resourceId` 是托管应用 `resource group Id`。 可在[使用 Azure 管理的标识令牌](marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)中找到用于提取它的示例脚本。 

对于 SaaS 产品/服务，`resourceId` 是 SaaS 订阅 ID。 有关 SaaS 订阅的更多详细信息，请参阅[列出订阅](partner-center-portal/pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)。

### <a name="responses"></a>响应

代码：200<br>
没问题。 Microsoft 端已接受并记录批量发出的使用情况，供进一步处理和计费。 返回的响应列表中包含该批次中每个事件的状态。 你应遍历响应有效负载，了解在批量事件中发送的各个使用事件的响应。

响应有效负载示例： 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

`BatchUsageEvent` API 响应中引用状态代码的说明：

| 状态代码  | 说明 |
| ---------- | -------------------- |
| `Accepted` | 已接受。 |
| `Expired` | 使用已过期。 |
| `Duplicate` | 提供了重复的使用情况。 |
| `Error` | 错误代码。 |
| `ResourceNotFound` | 提供的使用资源无效。 |
| `ResourceNotAuthorized` | 你无权提供此资源的使用情况。 |
| `ResourceNotActive` | 资源已挂起或从未激活。 |
| `InvalidDimension` | 用于传递使用情况的维度对于此产品/服务或计划无效。 |
| `InvalidQuantity` | 传递的数量小于或等于 0。 |
| `BadArgument` | 缺少输入，或输入格式不正确。 |

代码：400<br>
请求错误。 该批次中包含的使用事件超过 25 个。

代码：403<br>
已禁止。 未提供授权令牌，令牌无效或已过期。  或者请求正在尝试访问某个产品/服务的订阅，而发布该产品/服务时使用的 Azure AD 应用 ID 不同于创建授权令牌时所用的应用 ID。

## <a name="development-and-testing-best-practices"></a>开发和测试最佳做法

要测试自定义计量的发出情况，请实现与计量 API 的集成，为已发布的 SaaS 产品/服务创建计划并定义自定义维度，其中每单位价格为零。 然后将此产品/服务作为预览版发布，以便只有有限数量的用户能够访问和测试集成。

还可对现有的有效产品/服务使用专用计划，在测试期间只有有限数量的用户可访问该计划。

## <a name="get-support"></a>获取支持

请遵照[合作伙伴中心商业市场计划的支持](support.md)中的说明，了解发布者支持选项并使用 Microsoft 启动支持工单。

## <a name="next-steps"></a>后续步骤

要详细了解计量服务 API，请参阅[市场计量服务 API 常见问题解答](marketplace-metering-service-apis-faq.yml)。
