---
title: Azure 事件网格 - 对已传递事件设置自定义标头
description: 介绍如何对已传递事件设置自定义标头（或传递属性）。
ms.topic: conceptual
ms.date: 08/13/2021
ms.openlocfilehash: 3600d74d91ad218f3fcab99002762d605fba3139
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831348"
---
# <a name="custom-delivery-properties"></a>自定义传递属性
通过事件订阅，可以设置已传递事件中包含的 HTTP 头。 使用此功能，可设置目标所需的自定义标头。 创建事件订阅时，最多可以设置 10 个标头。 每个标头值不应大于 4096 (4K) 字节。

可以对传递到以下目标的事件设置自定义标头：

- Webhook
- Azure 服务总线主题和队列
- Azure 事件中心
- 中继混合连接

在 Azure 门户中创建事件订阅时，可使用“传递属性”选项卡设置自定义 HTTP 头。 在此页中可以设置固定的和动态的标头值。

## <a name="setting-static-header-values"></a>设置静态标头值
若要设置一个值固定的标头，请在相应字段中提供标头的名称及其值：

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="传递属性 - 静态":::

提供敏感数据时，可能需要选中“是否为机密?”。 Azure 门户上就不会显示敏感数据。 

## <a name="setting-dynamic-header-values"></a>设置动态标头值
可根据传入事件中的属性设置标头的值。 使用 JsonPath 语法来引用传入事件的属性值，以用作传出请求中的标头的值。 例如，若要使用事件数据中的传入事件属性 system 的值设置名为“Channel”的标头的值，请按以下方式配置事件订阅 ：

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="传递属性 - 动态":::

## <a name="use-azure-cli"></a>使用 Azure CLI
在使用 `az eventgrid event-subscription create` 命令创建订阅时请使用 `--delivery-attribute-mapping` 参数。 以下是一个示例：

```azurecli
az eventgrid event-subscription create -n es1 \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1
    --endpoint-type storagequeue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/sa1/queueservices/default/queues/q1 \
    --enable-advanced-filtering-on-arrays true
    --delivery-attribute-mapping staticproperty1 static somestaticvalue2 true 
    --delivery-attribute-mapping staticproperty2 static somestaticvalue3 false 
    --delivery-attribute-mapping dynamicproperty1 dynamic data.key1
```

## <a name="examples"></a>示例
本节提供几个使用传递属性的示例。

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>使用持有者令牌设置授权标头（非规范示例）

为授权标头设置值，以使用 Webhook 处理程序识别请求。 如果不[使用 Azure Active Directory 保护 Webhook](secure-webhook-delivery.md)，可设置授权标头。

| 标头名称   | 标头类型 | 标头值 |
| :--           | :--         | :--            |
|`Authorization` | 静态 | `BEARER SlAV32hkKG...`|

传出请求现在应包含在事件订阅上设置的标头：

```console
POST /home.html HTTP/1.1
Host: acme.com

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> 如果目标是 Webhook，定义授权标头是一个明智的选择。 它不应用于[使用资源 ID 订阅的函数](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination)、服务总线、事件中心和混合连接，因为这些目标在与事件网格一起使用时支持它们自己的身份验证方案。

### <a name="service-bus-example"></a>服务总线示例
Azure 服务总线支持在发送单个消息时使用以下消息属性。 

| 标头名称 | 标头类型 |
| :-- | :-- |
| `MessageId` | 动态 |  
| `PartitionKey` | 静态或动态 |
| `SessionId` | 静态或动态 |
| `CorrelationId` | 静态或动态 |
| `Label` | 静态或动态 |
| `ReplyTo` | 静态或动态 | 
| `ReplyToSessionId` | 静态或动态 |
| `To` |静态或动态 |
| `ViaPartitionKey` | 静态或动态 |

> [!NOTE]
> - `MessageId` 的默认值是事件网格事件的内部 ID。 你可以替代它。 例如，`data.field`。
> - 只能设置 `SessionId` 或 `MessageId`。 

### <a name="event-hubs-example"></a>事件中心示例

如果需要将事件发布到某个事件中心内的特定分区，请在事件订阅上设置 `ParitionKey` 属性，以指定用于标识目标事件中心分区的分区键。

| 标头名称 | 标头类型 |
| :-- | :-- |
|`PartitionKey` | 静态 |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>为传出到 Azure 存储队列的事件配置生存时间
对于 Azure 存储队列目标，只能配置传出消息在传递到 Azure 存储队列后的生存时间。 如果未提供任何时间，则消息的默认生存时间为 7 天。 你还可以将事件设置为永不过期。

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="传递属性 - 存储队列":::

## <a name="next-steps"></a>后续步骤
有关事件传递的详细信息，请参阅以下文章：

- [传递和重试](delivery-and-retry.md)
- [Webhook 事件传送](webhook-event-delivery.md)
- [事件筛选](event-filtering.md)
