---
title: 事件处理程序和目标 - Azure 事件网格 IoT Edge | Microsoft Docs
description: Edge 上事件网格中的事件处理程序和目标
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: b449368a94da95727729fe6100915e55ce106ecb
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110370439"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Edge 上事件网格中的事件处理程序和目标

事件处理程序是事件进行后续操作或处理事件的位置。 借助 Edge 上的事件网格模块，事件处理程序可以位于同一边缘设备、另一台设备或云中。 可以使用任何 WebHook 来处理事件，或将事件发送到某个本机处理程序（如 Azure 事件网格）。

本文介绍如何配置每种情况。

## <a name="webhook"></a>WebHook

要发布到 WebHook 终结点，请将 `endpointType` 设置为 `WebHook` 并提供以下信息：

* endpointUrl：WebHook 终结点 URL

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

要发布到 Azure 事件网格云终结点，请将 `endpointType` 设置为 `eventGrid` 并提供以下信息：

* endpointUrl：云中的事件网格主题 URL
* sasKey：事件网格主题的 SAS 密钥
* topicName：用于标记到事件网格的所有传出事件的名称。 在发布到事件网格域主题时，主题名称很有用。

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                 "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
    }
   ```

## <a name="iot-edge-hub"></a>IoT Edge 中心

要发布到 Edge 中心模块，请将 `endpointType` 设置为 `edgeHub` 并提供以下信息：

* outputName：事件网格模块将匹配此订阅的事件路由到 edgeHub 的输出。 例如，与以下订阅匹配的事件将写入 /messages/modules/eventgridmodule/outputs/sampleSub4。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "edgeHub",
              "properties": {
                "outputName": "sampleSub4"
              }
            }
          }
        }
    ```

## <a name="event-hubs"></a>事件中心

要发布到事件中心，请将 `endpointType` 设置为 `eventHub` 并提供以下信息：

* connectionString：作为你的目标的特定事件中心的连接字符串，通过共享访问策略生成。

    >[!NOTE]
    > 连接字符串必须特定于实体。 使用命名空间连接字符串将不起作用。 可以通过以下方式生成特定于实体的连接字符串：在 Azure 门户中，导航到要将内容发布到其中的特定事件中心，然后单击“共享访问策略”来生成新的特定于实体的连接字符串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>服务总线队列

要将内容发布到服务总线队列，请将 `endpointType` 设置为 `serviceBusQueue` 并提供以下信息：

* connectionString：作为你的目标的特定服务总线队列的连接字符串，通过共享访问策略生成。

    >[!NOTE]
    > 连接字符串必须特定于实体。 使用命名空间连接字符串将不起作用。 可以通过以下方式生成特定于实体的连接字符串：在 Azure 门户中，导航到要将内容发布到其中的特定服务总线队列，然后单击“共享访问策略”来生成新的特定于实体的连接字符串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>服务总线主题

要将内容发布到服务总线主题，请将 `endpointType` 设置为 `serviceBusTopic` 并提供以下信息：

* connectionString：作为你的目标的特定服务总线主题的连接字符串，通过共享访问策略生成。

    >[!NOTE]
    > 连接字符串必须特定于实体。 使用命名空间连接字符串将不起作用。 可以通过以下方式生成特定于实体的连接字符串：在 Azure 门户中，导航到要将内容发布到其中的特定服务总线主题，然后单击“共享访问策略”来生成新的特定于实体的连接字符串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>存储队列

要发布到存储队列，请将 `endpointType` 设置为 `storageQueue` 并提供以下信息：

* queueName：要将内容发布到其中的存储队列的名称。
* connectionString：存储队列所在的存储帐户的连接字符串。

    >[!NOTE]
    > 不同于事件中心、服务总线队列和服务总线主题，用于存储队列的连接字符串不特定于实体。 相反，它必须是存储帐户的连接字符串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```