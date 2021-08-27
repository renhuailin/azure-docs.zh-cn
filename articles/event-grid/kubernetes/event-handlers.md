---
title: Kubernetes 上的 Azure 事件网格 - 事件处理程序和目标
description: 本文介绍 Kubernetes 上的 Azure 事件网格支持的不同类型的事件处理程序和目标
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: b1052b996fd9da8452d0f23d60fc7ea53676f713
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769820"
---
# <a name="event-handlers-destinations-in-event-grid-on-kubernetes"></a>Kubernetes 上的事件网格中的事件处理程序和目标
事件处理程序是用于公开终结点的系统，并且是事件网格发送事件的目标。 事件处理程序在接收到事件后会对其进行操作，并使用事件有效负载执行某些逻辑，这可能会导致新事件发生。

创建事件订阅是配置事件网格以将事件发送到目标的方法。 若要做到这一点，可使用 [Azure CLI](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create)、[管理 SDK](../sdk-overview.md#management-sdks) 或使用 [2020-10-15-preview API](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update) 版本的直接 HTTP 调用。

通常，Kubernetes 上的事件网格可通过 Webhook 将事件发送到任何目标。 Webhook 是事件网格可以访问的服务或工作负载公开的 HTTP 终结点。 Webhook 可以是托管在同一群集、同一网络空间、云中、本地或事件网格可以覆盖的任何位置的工作负载。 

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

通过 Webhook，事件网格支持 Kubernetes 群集上托管的下列目标：

* 支持 Azure Arc 的 Kubernetes 上的 Azure 应用服务。 
* 支持 Azure Arc 的 Kubernetes 上的 Azure Functions。 
* 支持 Azure Arc 的 Kubernetes 上的 Azure 逻辑应用。

除了 Webhook，Kubernetes 上的事件网格还可以将事件发送到 Azure 上托管的下列目标：

- 使用 Webhook 的 Azure 事件网格
- 仅使用 Webhook 的 Azure Functions
- 使用 Azure 资源管理器资源 ID 的 Azure 事件中心
- 使用 Azure 资源管理器资源 ID 的 Azure 服务总线主题或队列
- 使用 Azure 资源管理器资源 ID 的 Azure 存储队列



## <a name="feature-parity"></a>功能奇偶一致性
借助 Azure 事件网格对事件订阅的支持，Kubernetes 上的事件网格可提供良好的功能奇偶一致性。 以下列表枚举了事件订阅功能的主要差异。 除了这些差异外，在管理 Kubernetes 上的事件网格的事件订阅时，可以使用 Azure 事件网格的 [REST API 版本 2020-10-15-preview](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions) 作为参考。

1. 使用 [REST API 版本 2020-10-15-preview](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions)。
2. 不支持 [Azure Functions 的 Azure 事件网格触发器](../../azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp%2Cconsole)。 可以使用 Webhook 目标类型向 Azure Functions 传送事件。
3. 不提供[死信位置](../manage-event-delivery.md#set-dead-letter-location)支持。 这意味着无法在事件订阅有效负载中使用 ``properties.deadLetterDestination``。
4. 尚不支持将 Azure 中继的混合连接作为目标。
5. 仅支持 CloudEvents 架构。 支持的架构值为“[CloudEventSchemaV1_0](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update#eventdeliveryschema)”。 CloudEvents 架构是可扩展的，并且基于开放标准。  
6. 标签 ([properties.labels](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update#request-body)) 不适用于 Kubernetes 上的事件网格。 因此，它们不可用。
7. 不支持[与资源标识一起传送](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update#deliverywithresourceidentity)。 因此，不支持[事件订阅标识](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update#eventsubscriptionidentity)的所有属性。
8. 尚不支持[目标终结点验证](../webhook-event-delivery.md#endpoint-validation-with-event-grid-events)。

## <a name="event-filtering-in-event-subscriptions"></a>事件订阅中的事件筛选
配置事件订阅的另一个重要方面是选择要传送到目标的事件。 有关详细信息，请参阅[事件筛选](filter-events.md)。

## <a name="sample-destination-configurations"></a>示例目标配置

以下是一些基本的示例配置，具体取决于预期目标。

## <a name="webhook"></a>WebHook
要发布到 WebHook 终结点，请将 `endpointType` 设置为 `WebHook` 并提供以下信息：

* endpointUrl：Webhook 终结点 URL

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure 事件网格

要发布到 Azure 事件网格云终结点，请将 `endpointType` 设置为 `WebHook` 并提供以下信息：

* endpointUrl：云中的 Azure 事件网格主题 URL，API 版本参数设置为 2018-01-01，`aeg-sas-key` 设置为 URL 编码的 SAS 密钥。 

   ```json
    {
        "properties": {
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01&aeg-sas-key=urlencoded(sas-key-value)"
                }
            }
        }
    }
   ```

## <a name="event-hubs"></a>事件中心

要发布到事件中心，请将 `endpointType` 设置为 `eventHub` 并提供以下信息：

* resourceId：特定事件中心的资源 ID。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "resourceId": "<Azure Resource ID of your event hub>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>服务总线队列

要将内容发布到服务总线队列，请将 `endpointType` 设置为 `serviceBusQueue` 并提供以下信息：

* resourceId：特定服务总线队列的资源 ID。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "resourceId": "<Azure Resource ID of your Service Bus queue>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>服务总线主题

要将内容发布到服务总线主题，请将 `endpointType` 设置为 `serviceBusTopic` 并提供以下信息：

* resourceId：特定服务总线主题的资源 ID。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "resourceId": "<Azure Resource ID of your Service Bus topic>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>存储队列

要发布到存储队列，请将 `endpointType` 设置为 `storageQueue` 并提供以下信息：

* queueName：要将内容发布到其中的 Azure 存储队列的名称。
* resourceID：包含队列的存储帐户的 Azure 资源 ID。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "resourceId": "<Azure Resource ID of your Storage account>"
              }
            }
          }
        }
    ```

## <a name="next-steps"></a>后续步骤
* 将[筛选器配置](filter-events.md)添加到事件订阅，以选择要传送的事件。 
* 若要了解 Azure Arc for Kubernetes 上的事件网格支持的架构，请参阅 [Kubernetes 上的事件网格 - 事件架构](event-schemas.md)。
