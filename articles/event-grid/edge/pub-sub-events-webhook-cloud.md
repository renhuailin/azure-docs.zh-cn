---
title: 在云中发布和订阅事件 - Azure 事件网格 IoT Edge | Microsoft Docs
description: 结合使用 Webhook 和 IoT Edge 上的事件网格在云中发布和订阅事件
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 6263a58aebcb7e6eb9d50d18e63257a3deb2cff5
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112416535"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>教程：在云中发布和订阅事件

本文介绍了使用 IoT Edge 上的事件网格发布和订阅事件所需的全部步骤。 本教程使用 Azure 函数作为事件处理程序。 有关其他目标类型，请参阅[事件处理程序](event-handlers.md)。

在继续之前，请先参阅[事件网格概念](concepts.md)，以了解什么是事件网格主题和订阅。

## <a name="prerequisites"></a>先决条件 
若要完成本教程，您需要：

* Azure 订阅 - 创建[免费帐户](https://azure.microsoft.com/free)（如果还没有的话）。 
* Azure IoT 中心和 IoT Edge 设备 - 按照 [Linux](../../iot-edge/quickstart-linux.md) 或 [Windows 设备](../../iot-edge/quickstart.md)快速入门中的步骤操作（如果还没有这样做的话）。

[!INCLUDE [event-grid-deploy-iot-edge](../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>在 Azure 门户中创建 Azure 函数

按照这篇[教程](../../azure-functions/functions-get-started.md)中所述的步骤操作来创建 Azure 函数。 

将代码片段替换为以下代码：

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

在新函数中，选择右上角的“获取函数 URL”，然后依次选择“默认(函数密钥)”和“复制”。 你将在本教程的后面部分使用此函数 URL 值。

> [!NOTE]
> 有关使用 EventGrid 事件触发器响应事件的更多示例和教程，请参阅 [Azure Functions](../../azure-functions/functions-overview.md) 文档。

## <a name="create-a-topic"></a>创建主题

作为事件发布者，你需要创建事件网格主题。 主题是指发布者可以向其发送事件的终结点。

1. 创建包含以下内容的 topic2.json。 若要详细了解有效负载，请参阅 [API 文档](api.md)。

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 运行下面的命令来创建主题。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. 运行下面的命令，以验证主题是否已成功创建。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   示例输出：

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>创建事件订阅

订阅者可以注册发布到主题的事件。 若要接收任何事件，订阅者需要为感兴趣的主题创建事件网格订阅。

[!INCLUDE [event-grid-deploy-iot-edge](../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 创建包含以下内容的 subscription2.json。 若要详细了解有效负载，请参阅 [API 文档](api.md)。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > endpointType 指定订阅者是 Webhook。  endpointUrl 指定订阅者侦听事件的 URL。 此 URL 对应于前面创建的 Azure 函数示例。
2. 运行下面的命令来创建订阅。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. 运行下面的命令，以验证订阅是否已成功创建。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    示例输出：

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>发布事件

1. 创建包含以下内容的 event2.json。 若要详细了解有效负载，请参阅 [API 文档](api.md)。

    ```json
        [
          {
            "id": "eventId-func-1",
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
1. 运行下面的命令来发布事件

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>验证事件传递

可以在函数的“监视”选项下查看在 Azure 门户中传递的事件。

## <a name="cleanup-resources"></a>清理资源

* 运行下面的命令来删除主题及其所有订阅

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* 删除在 Azure 门户中创建的 Azure 函数。

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了事件网格主题、订阅和已发布的事件。 至此，你已了解基本步骤，现在可以参阅以下文章：

* 若要排查与使用 IoT Edge 上的 Azure 事件网格相关的问题，请参阅[故障排除指南](troubleshoot.md)。
* 创建/更新具有[筛选器](advanced-filtering.md)的订阅。
* 在 [Linux](persist-state-linux.md) 或 [Windows](persist-state-windows.md) 上设置事件网格模块的持久性
* 遵循这篇[文档](configure-client-auth.md)来配置客户端身份验证
* 遵循这篇[教程](forward-events-event-grid-cloud.md)在云中将事件转发到 Azure 事件网格
* [在 Edge 上监视主题和订阅](monitor-topics-subscriptions.md)