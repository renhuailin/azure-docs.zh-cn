---
title: WebHook 事件传送
description: 本文介绍如何在使用 Webhook 时进行 WebHook 事件传送和终结点验证。
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 77908b7f36c51ca729915b09cb1e813c978235e3
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614359"
---
# <a name="webhook-event-delivery"></a>Webhook 事件传送
Webhook 是从 Azure 事件网格接收事件的多种方式之一。 当新事件准备就绪时，事件网格服务会向已配置的终结点 POST HTTP 请求，并在请求正文中包含该事件。

与众多支持 Webhook 的其他服务一样，事件网格需要你证明对 Webhook 的所有权，然后才能开始向该终结点传送事件。 此要求可防止恶意用户用大量事件淹没你的终结点。 如果使用下面列出的三项 Azure 服务中的任何一项，Azure 基础结构将自动进行此验证：

- 使用[事件网格连接器](/connectors/azureeventgrid/)的 Azure 逻辑应用
- 通过 [Webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md) 实现 Azure 自动化
- 使用[事件网格触发器](../azure-functions/functions-bindings-event-grid.md)的 Azure Functions

## <a name="endpoint-validation-with-event-grid-events"></a>通过事件网格事件验证终结点
如果使用其他任何类型的终结点（例如基于 HTTP 触发器的 Azure 函数），终结点代码需要参与事件网格的验证握手。 事件网格支持通过两种方式来验证订阅。

1. **同步握手**：在创建事件订阅时，事件网格会将一个订阅验证事件发送到终结点。 此事件的架构与任何其他事件网格事件类似。 此事件的数据部分包括一个 `validationCode` 属性。 你的应用程序会确认验证请求是否针对预期的事件订阅，并在响应中同步返回验证码。 所有事件网格版本都支持此握手机制。

2. **异步握手**：在某些情况下，无法在响应中同步返回 ValidationCode。 例如，如果使用第三方服务（如 [`Zapier`](https://zapier.com) 或 [IFTTT](https://ifttt.com/)），则无法以编程方式使用验证码进行响应。

   从版本 2018-05-01-preview 开始，事件网格支持手动验证握手。 如果你在创建事件订阅时使用的 SDK 或工具使用了 API 版本 2018-05-01-preview 或更高版本，则事件网格将在订阅验证事件的数据部分中发送 `validationUrl` 属性。 若要完成握手，请在事件数据中找到该 URL 并向其发送一个 GET 请求。 你可以使用 REST 客户端或 Web 浏览器。

   所提供的 URL 的有效期为 5 分钟。 在该时间内，事件订阅的预配状态为 `AwaitingManualAction`。 如果在 5 分钟内未完成手动验证，则配置状态被设为 `Failed`。 你将必须在开始手动验证之前重新创建事件订阅。

   此身份验证机制还要求 Webhook 终结点返回 HTTP 状态代码 200，这样它就知道验证事件的 POST 已被接受，然后它就可以进入手动验证模式。 换句话说，如果终结点返回 200，但没有同步返回验证响应，则模式将转换为手动验证模式。 如果在 5 分钟内在验证 URL 上出现 GET，则可以认为验证握手成功。

> [!NOTE]
> 不支持使用自签名证书进行验证。 请改为使用商业证书颁发机构 (CA) 颁发的签名证书。

### <a name="validation-details"></a>验证详细信息

- 在创建/更新事件订阅时，事件网格会将一个订阅验证事件发送到目标终结点。
- 事件包含标头值 `aeg-event-type: SubscriptionValidation`。
- 事件正文具有与其他事件网格事件相同的架构。
- 事件的 `eventType` 属性为 `Microsoft.EventGrid.SubscriptionValidationEvent`。
- 该事件的 `data` 属性包括一个 `validationCode` 属性，其中含有随机生成的字符串。 例如，`validationCode: acb13…`。
- 事件数据还包括 `validationUrl` 属性，其中包含用于手动验证订阅的 URL。
- 该数组仅包含验证事件。 你回显验证代码后，事件网格会以单独的请求发送其他事件。
- EventGrid 数据平面 SDK 包含对应订阅验证事件数据和订阅验证响应的类。

以下示例显示了 SubscriptionValidationEvent 示例：

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/myeventsub/validate?id=0000000000-0000-0000-0000-00000000000000&t=2021-09-01T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2021-00-01T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

为证明终结点所有权，请在 `validationResponse` 属性中回显验证代码，如下例所示：

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

你必须返回 **HTTP 200 OK** 响应状态代码。 **HTTP 202 Accepted** 未被识别为有效的事件网格订阅验证响应。 必须在 30 秒内完成 HTTP 请求。 如果操作未在 30 秒内完成，则该操作将被取消，并可能在 5 秒后重新尝试。 如果所有尝试均失败，系统会将它视为验证握手错误。

另外，还可以通过将 GET 请求发送到验证 URL 来手动验证订阅。 事件订阅将一直处于挂起状态，直到得到验证。 验证 URL 使用端口 553。 如果防火墙规则阻止端口 553，则可能需更新规则才能成功进行手动握手。

有关处理订阅验证握手的示例，请参阅 [C# 示例](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)。

## <a name="endpoint-validation-with-cloudevents-v10"></a>使用 CloudEvents v1.0 验证终结点
CloudEvents v1.0 使用 **HTTP OPTIONS** 方法实现自己的 [滥用保护语义](webhook-event-delivery.md)。 可以在 [此处](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)阅读详细内容。 使用 CloudEvents 架构进行输出时，事件网格可与 CloudEvents v1.0 滥用保护配合使用，取代事件网格验证事件机制。

## <a name="event-subscriptions-considerations"></a>事件订阅注意事项

若要防止在创建订阅的过程中出现问题，请使用此参考来验证主题和订阅架构之间的兼容性。 创建主题时，将定义传入事件架构，并在创建订阅时定义传出事件架构。

> [!NOTE]
> 此兼容性表参考适用于：自定义主题和事件域。

| 传入事件架构 | 传出事件架构 | 支持 |
| ---- | ---- | ---- |
| 事件网格架构 | 事件网格架构 | 是 |
| | 云事件 v1.0 架构 | 是 |
| | 自定义输入架构 | 否 |
| 云事件 v1.0 架构 | 事件网格架构 | 否 |
| | 云事件 v1.0 架构 | 是 |
| | 自定义输入架构 | 否 |
| 自定义输入架构 | 事件网格架构 | 是 |
| | 云事件 v1.0 架构 | 是 |
| | 自定义输入架构 | 是 |

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解如何排查事件订阅验证问题： 

[排查事件订阅验证问题](troubleshoot-subscription-validation.md)
