---
title: 在本地发布和订阅事件 - Azure 事件网格 IoT Edge | Microsoft Docs
description: 使用 Webhook 和 IoT Edge 上的事件网格在本地发布和订阅事件
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: d965d5a65fa6f5015b1e39b22238f3aa6fd1d7b7
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413509"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>教程：在本地发布和订阅事件

本文将指导你完成使用 IoT Edge 上的事件网格发布和订阅事件所需的所有步骤。

> [!NOTE]
> 若要了解有关 Azure 事件网格主题和订阅的信息，请参阅 [事件网格概念](concepts.md)。

## <a name="prerequisites"></a>先决条件 
若要完成本教程，您需要：

* **Azure 订阅** - 如果还没有帐户，请创建一个 [免费帐户](https://azure.microsoft.com/free)。 
* **Azure IoT 中心和 IoT Edge 设备** - 如果还未安装，请按照 [Linux](../../iot-edge/quickstart-linux.md) 或 [Windows 设备](../../iot-edge/quickstart.md)快速入门中的步骤进行操作。

## <a name="deploy-event-grid-iot-edge-module"></a>部署事件网格 IoT Edge 模块

有多种方法可以将模块部署到 IoT Edge 设备，并且所有这些方法都适用于 IoT Edge 上的 Azure 事件网格。 本文介绍从 Azure 门户部署 IoT Edge 上的事件网格的步骤。

>[!NOTE]
> 在本教程中，将部署不带持久性的事件网格模块。 这意味着，本教程中创建的任何主题和订阅都将在重新部署模块时被删除。 有关如何设置持久性的详细信息，请参阅以下文章：[Linux 中的持久状态](persist-state-linux.md)或 [Windows 中的持久状态](persist-state-windows.md)。 对于生产工作负载，建议安装带有持久性的事件网格模块。


### <a name="select-your-iot-edge-device"></a>选择 IoT Edge 设备

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 导航到 IoT 中心。
1. 从“自动设备管理”部分的菜单中选择“IoT Edge” 。 
1. 在设备列表中单击目标设备的 ID
1. 选择“设置模块”  。 请将页面保持打开状态。 将在该页面继续执行下一部分中的步骤。

### <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 Azure 门户提供部署清单的创建向导，无需你手动构建 JSON 文档。  它分为三步：添加模块、指定路由和评审部署  。

### <a name="add-modules"></a>添加模块

1. 在“部署模块”部分，选择“添加” 
1. 从下拉列表的模块类型中选择“IoT Edge 模块”
1. 提供容器的名称、映像、容器创建选项：

   * **名称**：eventgridmodule
   * **映像 URI**：`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **容器创建选项**：

   [!INCLUDE [event-grid-edge-module-version-update](../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. 单击“保存” 
 1. 继续下一部分，在一起部署 Azure 事件网格订阅服务器模块之前先添加这些模块。

    >[!IMPORTANT]
    > 在本教程中，将部署已禁用客户端身份验证的事件网格模块。 对于生产工作负荷，建议启用客户端身份验证。 有关如何安全配置事件网格模块的详细信息，请参阅 [安全性和身份验证](security-authentication.md)。
    > 
    > 如果使用 Azure VM 作为边缘设备，请添加入站端口规则以允许端口 4438 上有入站流量。 有关添加规则的说明，请参阅[如何打开 VM 的端口](../../virtual-machines/windows/nsg-quickstart-portal.md)。
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>部署事件网格订阅服务器 IoT Edge 模块

本部分介绍了如何部署另一个 IoT 模块，使其充当事件处理程序，可将事件发送到该模块。

### <a name="add-modules"></a>添加模块

1. 在“部署模块”部分中，再次选择“添加” 。 
1. 从下拉列表的模块类型中选择“IoT Edge 模块”
1. 提供容器的名称、映像和容器创建选项：

   * **名称**：subscriber
   * **映像 URI**：`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **容器创建选项**：无
1. 单击“保存” 
1. 单击“下一步”转到路由部分

 ### <a name="setup-routes"></a>设置路由

保留默认路由，然后选择“下一步”，转到评审部分

### <a name="submit-the-deployment-request"></a>提交部署请求

1. 评审部分介绍了根据上述部分中的选择所创建的 JSON 部署清单。 确认 JSON 中列出了两个模块：“eventgridmodule”和“订阅服务器”。  
1. 审阅部署信息，然后选择“提交”。 提交部署后，返回到“设备”页。
1. 在“模块部分”，验证是否列出了“eventgrid”和“订阅服务器”两个模块。   然后，验证“在部署中指定”和“由设备报告”列设置为“是”。  

    可能需要等待一段时间，该模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。

## <a name="create-a-topic"></a>创建主题

作为事件的发布服务器，需要创建事件网格主题。 在 Azure 事件网格中，主题指的是发布服务器可将事件发送到的终结点。

1. 创建包含以下内容的 topic.json。 有关有效负载的详细信息，请参阅 [API 文档](api.md)。

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. 请运行以下命令，以便创建网格主题。 确认显示的 HTTP 状态代码为 `200 OK`。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. 运行以下命令以验证是否已成功创建主题。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   示例输出：

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>创建事件订阅

订阅服务器可以注册发布到主题的事件。 若要接收任何事件，需要为感兴趣的主题创建事件网格订阅。

[!INCLUDE [event-grid-deploy-iot-edge](../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 创建包含以下内容的 subscription.json。 有关有效负载的详细信息，请参阅 [API 文档](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > **endpointType** 属性指定订阅服务器是“Webhook”。  “EndpointUrl”指定订阅服务器侦听事件的 URL。 此 URL 对应于之前部署的 Azure 订阅服务器示例。
2. 运行以下命令以针对主题创建订阅。 确认显示的 HTTP 状态代码为 `200 OK`。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. 运行以下命令以验证是否已成功创建订阅。 应返回 HTTP 状态代码 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    示例输出：

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>发布事件

1. 创建包含以下内容的 event.json。 有关有效负载的详细信息，请参阅 [API 文档](api.md)。

    ```json
        [
          {
            "id": "eventId-func-0",
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
1. 运行以下命令，以发布事件。

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>验证事件传送

1. 通过 SSH 或 RDP 连接到 IoT Edge VM。
1. 检查订阅服务器日志：

    在 Windows 上，运行以下命令：

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   在 Linux 上，运行以下命令：

    ```sh
    sudo docker logs subscriber
    ```

    示例输出：

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>清理资源

* 运行以下命令以删除主题及其所有订阅。

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* 从 IoT Edge 设备中删除订阅服务器模块。


## <a name="next-steps"></a>后续步骤
在本教程中，你创建了事件网格主题、订阅和已发布的事件。 至此，你已了解基本步骤，现在可以参阅以下文章： 

- 若要排查与使用 IoT Edge 上的 Azure 事件网格相关的问题，请参阅[故障排除指南](troubleshoot.md)。
- 创建/更新具有[筛选器](advanced-filtering.md)的订阅。
- 在 [Linux](persist-state-linux.md) 或 [Windows](persist-state-windows.md) 上启用事件网格模块的持久性
- 遵循[文档](configure-client-auth.md)配置客户端身份验证
- 遵循本[教程](pub-sub-events-webhook-cloud.md)将事件转接到云中的 Azure Functions
- [在 IoT Edge 上响应 Blob 存储事件](react-blob-storage-events-locally.md)
- [监视边缘上的主题和订阅](monitor-topics-subscriptions.md)

