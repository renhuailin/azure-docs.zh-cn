---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 03/18/2021
ms.author: faneerde
ms.openlocfilehash: 8eaf734d2f32772f1c24b49b20fa27940fbfbcac
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385108"
---
若要运行该示例代码，请按照以下步骤执行：

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或按 Ctrl+Shift+X），然后搜索“Azure IoT 中心”。
1. 右键单击并选择“扩展设置”。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="../../../media/vscode-common-screenshots/extension-settings.png" alt-text="扩展设置":::

1. 搜索并启用“显示详细消息”。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="../../../media/vscode-common-screenshots/verbose-message.png" alt-text="显示详细消息":::

1. 在 Visual Studio Code 中，转到 src/cloud-to-device-console-app/operations.json。
1. 在 pipelineTopologySet 节点上，确保你看到以下值：

   ```
   "pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/azure-video-analyzer/main//pipelines/live/topologies/motion-detection/topology.json"
   ```

1. 在 `livePipelineSet` 和 `livePipelineDelete` 节点上，确保 topologyName 的值与管道拓扑中的 name 属性的值匹配 ：

   `"topologyName" : "MotionDetection"`

1. 通过选择 F5 键启动调试会话。 “终端”窗口将显示一些消息。
1. operations.json 文件首先调用 `pipelineTopologyList` 和 `livePipelineList`。 如果在完成先前的快速入门后清理了资源，则该过程将返回空列表。

   ```
   -----------------------  Request: pipelineTopologyList  --------------------------------------------------

   {
   "@apiVersion": "1.0"
   }

   ---------------  Response: pipelineTopologyList - Status: 200  ---------------

   {
     "value": []
   }

   --------------------------------------------------------------------------

   ```

   “终端”窗口将显示下一组直接方法调用：

   - 对 `pipelineTopologySet` 的调用，该调用使用前面的 pipelineTopologyUrl
   - 对 `livePipelineSet` 的调用，该调用使用以下正文：

   ```json
   {
     "@apiVersion": "1.0",
     "name": "Sample-Pipeline-1",
     "properties": {
       "topologyName": "MotionDetection",
       "description": "Sample pipeline description",
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

   - 对 `livePipelineActivate` 的调用，用于启动实时管道和视频流。
   - 对 `livePipelineList` 的第二次调用，显示实时管道处于运行状态。

1. “终端”窗口中的输出会在出现 `Press Enter to continue` 时暂停。 暂时不要选择 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。
1. 切换到 Visual Studio Code 中的“输出”窗口。 可看到视频分析器 Edge 模块正发送到 IoT 中心的消息。 本快速入门中的以下部分将讨论这些消息。
1. 实时管道将继续运行并输出结果。 RTSP 模拟器不断循环源视频。 若要停止实时管道，请返回“终端”窗口，并选择 Enter。

   接下来会执行一系列调用，以清理资源：

   - 调用 `livePipelineDeactivate` 停用管道。
   - 调用 `livePipelineDelete` 删除管道。
   - 调用 `pipelineTopologyDelete` 删除拓扑。
   - 对 `pipelineTopologyList` 的最后一次调用显示该列表为空。
