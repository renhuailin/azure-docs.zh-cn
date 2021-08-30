---
title: Azure 事件网格传送和重试
description: 介绍 Azure 事件网格如何传送事件以及如何处理未送达的消息。
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: a6055a99e717dd379dc6bd43411c73456bdaede8
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730324"
---
# <a name="event-grid-message-delivery-and-retry"></a>事件网格消息传送和重试
事件网格提供持久传送。 它会尝试为每个匹配的订阅至少立即传递每个消息一次。 如果订阅方的终结点没有确认收到事件或有故障发生，事件网格会根据固定的“重试计划”和“重试策略”重试传递 。 默认情况下，事件网格模块一次向一个订阅方传递一个事件。 但有效负载是一个包含单个事件的数组。

> [!NOTE]
> 事件网格不保证事件传送的顺序，因此订阅者可能会收到不按顺序的事件。 

## <a name="retry-schedule"></a>重试计划
当 EventGrid 收到有关尝试传递事件的错误时，EventGrid 会根据错误类型决定它是应当重试传递，对该事件进行死信处理，还是删除该事件。 

如果订阅的终结点所返回的错误是不能通过重试来解决的配置相关错误（例如，如果终结点已删除），则 EventGrid 将对该事件进行死信处理，或者删除该事件（如果未配置死信）。

下表说明不会发生重试的终结点类型和错误类型：

| 终结点类型 | 错误代码 |
| --------------| -----------|
| Azure 资源 | 400 错误的请求、413 请求实体太大、403 禁止访问 | 
| Webhook | 400 错误的请求、413 请求实体太大、403 禁止访问、404 未找到、401 未授权 |
 
> [!NOTE]
> 如果没有为终结点配置死信，则出现上述错误时就会删除事件。 如果你不想删除这些类型的事件，请考虑配置死信。

如果订阅的终结点所返回的错误不在上述列表中，则 EventGrid 会使用下面所述的策略执行重试：

传送消息后，事件网格将等待 30 秒以接收响应。 如果终结点在 30 秒后未发出响应，消息将排入队列等待重试。 对于事件传送，事件网格使用指数性的回退重试策略。 事件网格会尽量按以下计划重试传送：

- 10 秒
- 30 秒
- 1 分钟
- 5 分钟
- 10 分钟
- 30 分钟
- 1 小时
- 3 小时
- 6 小时
- 每 12 小时到 24 小时


如果终结点在 3 分钟内做出了响应，则事件网格会尽量尝试从重试队列中删除事件，但仍可能会收到重复项。

事件网格为所有重试步骤添加小的随机性，在某个终结点持续运行不正常、停机很长时间，或者看起来已过载的情况下，会适时跳过某些重试。

## <a name="retry-policy"></a>重试策略
当使用以下两个配置创建事件订阅时，可自定义重试策略。 如果事件达到任一重试策略限制，模块会将其删除。 

- **最大尝试次数** - 值必须是介于 1 和 30 之间的整数。 默认值为 30。
- **事件生存时间 (TTL)** - 值必须是介于 1 和 1440 之间的整数。 默认值为 1440 分钟

如需用于配置这些设置的示例 CLI 和 PowerShell 命令，请参阅[设置重试策略](manage-event-delivery.md#set-retry-policy)。

## <a name="output-batching"></a>输出批处理 
默认情况下，事件网格单独将每个事件发送给订阅者。 订阅者接收包含单个事件的数组。 你可以将事件网格配置为批量处理要传送的事件，以在高吞吐量方案中提高 HTTP 性能。 默认情况下批处理处于关闭状态，但可以根据订阅打开。

### <a name="batching-policy"></a>批处理策略
批量传送有两个设置：

* **每批最大事件数** - 事件网格每批将传送的最大事件数。 永远不会超过此数目，但是，如果在发布时没有更多事件，则可能会传送较少的事件。 如果只有较少的事件，事件网格不会为了创建某个批而延迟事件传送。 必须介于 1 到 5,000 之间。
* **首选批大小(KB)** - 批大小的目标上限 (KB)。 与最大事件数类似，如果发布时没有更多的事件，则批大小可能会较小。 如果单个事件大于首选大小，则批可能会大于首选批大小。 例如，如果首选大小为 4 KB，并且一个 10 KB 的事件推送到了事件网格，则 10 KB 事件将会在其自己的批中传送，而不会被删除。

可以通过门户、CLI、PowerShell 或 SDK 以每事件订阅为基础配置批量传送。

### <a name="batching-behavior"></a>批处理行为

* 全或无

  事件网格使用“全或无”语义运行。 它不支持批处理传送部分成功。 订阅者应注意，只为每批请求他们可在 60 秒内合理处理的尽可能多的事件。

* 乐观批处理

  批处理策略设置对批处理行为的限制并不严格，应尽力遵守。 如果事件处理率较低，则会发现批大小小于每批请求的最大事件数。

* 默认设置为关闭

  默认情况下，事件网格仅向每个传送请求添加一个事件。 打开批处理的方法是在事件订阅 JSON 中设置本文前面提到的一个设置。

* 默认值

  创建事件订阅时，不必同时指定设置（每批最大事件数和近似批大小 (KB)）。 如果仅设置一项设置，事件网格将使用（可配置）默认值。 请参阅以下各节，了解默认值以及如何替代它们。

### <a name="azure-portal"></a>Azure 门户： 
![文件传送设置](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
创建事件订阅时，请使用以下参数： 

- **max-events-per-batch** - 每批的最大事件数。 必须是介于 1 和 5000 之间的数字。
- **preferred-batch-size-in-kilobytes** - 首选批大小 (KB)。 必须是介于 1 和 1024 之间的数字。

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

有关将 Azure CLI 与事件网格配合使用的详细信息，请参阅[使用 Azure CLI 将存储事件路由到 Web 终结点](../storage/blobs/storage-blob-event-quickstart.md)。


## <a name="delayed-delivery"></a>延迟传送
当终结点遇到传送失败时，事件网格将开始延迟向该终结点传送和重试事件。 例如，如果发布到某个终结点的前 10 个事件失败，事件网格将假设该终结点遇到问题，并将所有后续重试和新的传送操作延迟一段时间 - 在某些情况下，会延迟几个小时。 

从功能上讲，延迟传送的目的是保护不正常的终结点以及事件网格系统。 如果不采用退让机制并延迟向不正常的终结点传送事件，事件网格的重试策略和卷功能可能很容易使系统瘫痪。

## <a name="dead-letter-events"></a>死信事件
当事件网格无法在特定时间段内或在尝试传递事件一定次数后传递事件时，它可以将未传递的事件发送到存储帐户。 此过程称为“死信处理”。 满足以下条件之一时，事件网格会将事件视为死信。 

- 事件未在生存期内传递。 
- 尝试传递事件的次数已超出限制。

如果满足上述任一条件，则会将该事件删除或视为死信。  默认情况下，事件网格不启用死信处理。 若要启用该功能，在创建事件订阅时必须指定一个存储帐户来存放未送达的事件。 你将从此存储帐户中拉取事件来解决传递问题。

事件网格已进行所有重试尝试后会将事件发送到死信位置。 如果事件网格收到 400（错误请求）或 413（请求实体太大）响应代码，它会立即计划事件以进行死信处理。 这些响应代码指示事件传送将永远不会成功。

仅在下一次计划的传递尝试时检查生存时间是否过期。 因此，即使生存时间在下一次计划的传递尝试之前到期，也只会在下一次传递时检查事件到期时间，然后再检查死信。 

最后一次尝试发送事件与发送到死信位置之间有五分钟的延迟。 此延迟旨在减少 Blob 存储操作的数量。 如果死信位置已四小时不可用，则会丢弃该事件。

在设置死信位置之前，必须有一个包含容器的存储帐户。 在创建事件订阅时，需要提供此容器的终结点。 终结点的格式如下：`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

你可能希望在事件发送到死信位置时收到通知。 若要使用事件网格来响应未送达的事件，请为死信 blob 存储[创建事件订阅](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 每当死信 blob 存储收到未送达的事件时，事件网格都会通知处理程序。 处理程序使用你希望采取的、用于协调未送达的事件的操作进行响应。 有关设置死信位置和重试策略的示例，请参阅[死信和重试策略](manage-event-delivery.md)。

## <a name="delivery-event-formats"></a>传递事件格式
本部分提供了不同传递架构格式（事件网格架构、CloudEvents 1.0 架构和自定义架构）的事件和死信事件的示例。 有关这些格式的详细信息，请参阅[事件网格架构](event-schema.md)和 [CloudEvents 1.0 架构](cloud-event-schema.md)这两篇文章。 

### <a name="event-grid-schema"></a>事件网格架构

#### <a name="event"></a>事件 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>死信事件

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>CloudEvents 1.0 架构

#### <a name="event"></a>事件

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>死信事件

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>自定义架构

#### <a name="event"></a>事件

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>死信事件
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>消息传送状态

事件网格使用 HTTP 响应代码确认已接收事件。 

### <a name="success-codes"></a>成功代码

事件网格 **仅** 将以下 HTTP 响应代码视为传送成功。 所有其他状态代码被视为传送失败，将会相应地重试传送或将事件加入死信队列。 收到成功状态代码后，事件网格认为传送已完成。

- 200 正常
- 201 Created
- 202 已接受
- 203 非权威信息
- 204 无内容

### <a name="failure-codes"></a>失败代码

不在上述集合 (200-204) 内的所有其他代码会被视为失败，将会重试（如果需要）。 某些代码已关联到下面所述的特定重试策略，所有其他代码遵循标准的指数退让模型。 请务必注意，由于事件网格体系结构的高度并行化特性，重试行为是不确定的。 

| 状态代码 | 重试行为 |
| ------------|----------------|
| 400 错误的请求 | 不重试 |
| 401 未授权 | 在 5 分钟或更长时间后为 Azure 资源终结点进行重试 |
| 403 禁止访问 | 不重试 |
| 404 未找到 | 在 5 分钟或更长时间后为 Azure 资源终结点进行重试 |
| 408 请求超时 | 2 分钟或更长时间后重试 |
| 413 请求实体太大 | 不重试 |
| 503 服务不可用 | 30 秒或更长时间后重试 |
| 所有其他 | 10 秒或更长时间后重试 |

## <a name="custom-delivery-properties"></a>自定义传递属性
通过事件订阅，可以设置已传递事件中包含的 HTTP 头。 使用此功能，可设置目标所需的自定义标头。 创建事件订阅时，最多可以设置 10 个标头。 每个标头值不应大于 4096 (4K) 字节。 可以对传递到以下目标的事件设置自定义标头：

- Webhook
- Azure 服务总线主题和队列
- Azure 事件中心
- 中继混合连接

有关详细信息，请参阅[自定义传送属性](delivery-properties.md)。 

## <a name="next-steps"></a>后续步骤

* 若要查看事件传送的状态，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 若要自定义事件传送选项，请参阅[死信和重试策略](manage-event-delivery.md)。
