---
title: 将事件网格事件转发到 IoTHub - Azure 事件网格 IoT Edge | Microsoft Docs
description: 将事件网格事件转发到 IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: 30fa58a79068a1dc367f4ac330efd01685a4401c
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112415581"
---
# <a name="tutorial-forward-events-to-iothub"></a>教程：将事件转发到 IoTHub

本文逐步讲解使用路由将事件网格事件转发到其他 IoT Edge 模块 (IoTHub) 所需的步骤。 执行此操作的原因如下：

* 继续将任何现有已就位的投资用于 edgeHub 路由
* 希望仅通过 IoT 中心路由设备的所有事件

若要完成本教程，需要理解以下概念：

- [事件网格概念](concepts.md)
- [IoT Edge 中心](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>先决条件 
若要完成本教程，您需要：

* Azure 订阅 - 创建[免费帐户](https://azure.microsoft.com/free)（如果还没有的话）。 
* **Azure IoT 中心和 IoT Edge 设备** - 按照 [Linux](../../iot-edge/quickstart-linux.md) 或 [Windows 设备](../../iot-edge/quickstart.md)快速入门中的步骤进行操作（如果尚未安装）。

[!INCLUDE [event-grid-deploy-iot-edge](../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>创建主题

作为事件发布者，需要创建事件网格主题。 主题是指之后发布者可将事件发送到的终结点。

1. 创建包含以下内容的 topic4.json。 有关有效负载的详细信息，请参阅 [API 文档](api.md)。

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. 运行下面的命令来创建主题。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. 运行下面的命令，以验证主题是否已成功创建。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   示例输出：

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>创建事件订阅

订阅服务器可以注册发布到主题的事件。 若要接收任何事件，需要为感兴趣的主题创建事件网格订阅。

[!INCLUDE [event-grid-deploy-iot-edge](../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 创建包含以下内容的 subscription4.json。 有关有效负载的详细信息，请参阅 [API 文档](api.md)。

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

   >[!NOTE]
   > `endpointType` 指定订阅服务器为 `edgeHub`。 `outputName` 指定事件网格模块将匹配此订阅的事件路由到 edgeHub 的输出。 例如，与上述订阅匹配的事件将写入 `/messages/modules/eventgridmodule/outputs/sampleSub4`。
2. 运行以下命令以创建订阅。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. 运行下面的命令，以验证订阅是否已成功创建。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    示例输出：

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>设置 Edge 中心路由

更新 Edge 中心路由，以将要转发的事件订阅事件转发到 IoTHub，如下所示：

1. 登录到 [Azure 门户](https://ms.portal.azure.com)
1. 导航到“IoT 中心”。
1. 从菜单中选择“IoT Edge”。
1. 在设备列表中选择目标设备的 ID。
1. 选择“设置模块”  。
1. 选择“下一步”转到路由部分。
1. 在路由中，添加新的路由

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  例如，

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > 上述路由会将要转发的与此订阅匹配的任何事件转发到 IoT 中心。 可以使用 [Edge 中心路由](../../iot-edge/module-composition.md)功能来进一步筛选，并将事件网格事件路由到其他 IoT Edge 模块。

## <a name="setup-iot-hub-route"></a>设置 IoT 中心路由

请参阅 [IoT 中心路由教程](../../iot-hub/tutorial-routing.md)从 IoT 中心设置路由，以便从事件网格模块查看转发的事件。 将 `true` 用于查询，使教程易于操作。  



## <a name="publish-an-event"></a>发布事件

1. 创建包含以下内容的 event4.json。 有关有效负载的详细信息，请参阅 [API 文档](api.md)。

    ```json
        [
          {
            "id": "eventId-iothub-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```

1. 运行以下命令以发布事件：

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>验证事件传送

请参阅 IoT 中心[路由教程](../../iot-hub/tutorial-routing.md)，了解查看事件的步骤。

## <a name="cleanup-resources"></a>清理资源

* 在 Edge 上运行以下命令，以删除主题及其所有订阅：

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* 此外也删除在云中设置 IoTHub 路由时创建的所有资源。

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了事件网格主题、Edge 中心订阅和发布了事件。 了解了转发到 Edge 中心的基本步骤后，请参阅以下文章：

* 若要排查与在 IoT Edge 上使用 Azure 事件网格相关的问题，请参阅[故障排除指南](troubleshoot.md)。
* 使用 [Edge 中心](../../iot-edge/module-composition.md)路由筛选器来对事件进行分区
* 在 [linux](persist-state-linux.md) 或 [Windows](persist-state-windows.md) 上设置事件网格模块的持久性
* 遵循这篇[文档](configure-client-auth.md)来配置客户端身份验证
* 遵循这篇[教程](forward-events-event-grid-cloud.md)在云中将事件转发到 Azure 事件网格
* [在 Edge 上监视主题和订阅](monitor-topics-subscriptions.md)