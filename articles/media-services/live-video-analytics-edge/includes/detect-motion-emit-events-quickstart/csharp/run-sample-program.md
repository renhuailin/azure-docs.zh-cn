---
ms.openlocfilehash: 7b173a5fe639ff21cc8a475edd16d0e3885dacef
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113280043"
---
若要运行该示例代码，请按照以下步骤执行：

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或按 Ctrl+Shift+X），然后搜索“Azure IoT 中心”。
1. 右键单击并选择“扩展设置”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="扩展设置":::
1. 搜索并启用“显示详细消息”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="显示详细消息":::
1. 在 Visual Studio Code 中，转到 src/cloud-to-device-console-app/operations.json。
1. 在 GraphTopologySet 节点上，确保你看到以下值：

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/2.0/topology.json"`
1. 在节点 GraphInstanceSet 和 GraphTopologyDelete 节点上，确保 `topologyName` 的值与图形拓扑中 `name` 属性的值匹配 ：

    `"topologyName" : "MotionDetection"`
    
1. 通过选择 F5 键启动调试会话。 “终端”窗口将显示一些消息。

    当你首次使用 F5 启动调试会话时，系统会提示你输入环境类型和项目。 这样就会在文件夹中创建和配置 launch.json 文件。 在此演示中，请使用以下项：
    * 环境 - .Net Core
    * 项目 - c2d-console-app
    
    创建 launch.json 文件后对其进行编辑。 将控制台配置更改为“integratedTeminal”。
    
    `"console": "integratedTerminal"`
1. operations.json 文件首先调用 `GraphTopologyList` 和 `GraphInstanceList`。 如果在完成先前的快速入门后清理了资源，则该过程将返回空列表，然后暂停。 若要继续，请选择 Enter 键。

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
        "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
        "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
    
    “终端”窗口将显示下一组直接方法调用：
     * 对 `GraphTopologySet` 的调用，该调用使用前面的 `topologyUrl`
     * 对 `GraphInstanceSet` 的调用，该调用使用以下正文：
         
    ```
    {
      "@apiVersion": "2.0",
      "name": "Sample-Graph",
      "properties": {
        "topologyName": "MotionDetection",
        "description": "Sample graph description",
        "parameters": [
          {
            "name": "rtspUrl",
            "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
          },
          {
            "name": "rtspUserName",
            "value": "testuser"
          },
          {
            "name": "rtspPassword",
            "value": "testpassword"
          }
        ]
      }
    }
    ```
     
    * 对 `GraphInstanceActivate` 的调用，用于启动图形实例和视频流。
    * 对 `GraphInstanceList` 的第二次调用，显示图形实例处于正在运行状态。
1. “终端”窗口中的输出会在出现 `Press Enter to continue` 时暂停。 暂时不要选择 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。
1. 切换到 Visual Studio Code 中的“输出”窗口。 可看到 IoT Edge 模块上的实时视频分析正发送到 IoT 中心的消息。 本快速入门中的以下部分将讨论这些消息。
1. 媒体图将继续运行并打印结果。 RTSP 模拟器不断循环源视频。 若要停止媒体图，请返回“终端”窗口，并选择 Enter。 

    接下来会执行一系列调用，以清理资源：

    * 调用 `GraphInstanceDeactivate` 停用图形实例。
    * 调用 `GraphInstanceDelete` 删除该实例。
    * 调用 `GraphTopologyDelete` 删除拓扑。
    * 对 `GraphTopologyList` 的最后一次调用显示该列表为空。
