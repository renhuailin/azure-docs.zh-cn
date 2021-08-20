---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/07/2021
ms.author: juliako
ms.openlocfilehash: d86ad88e122909225b5b64861623ce9b5b2df8af
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444489"
---
作为先决条件的一部分，请将示例代码下载到一个文件夹中。 按照以下步骤检查并编辑示例文件。

1. 在 Visual Studio Code 中，转到 src/edge。 你可看到 .env 文件以及一些部署模板文件。

    部署模板是指边缘设备的部署清单。 它包含一些占位符值。 该 .env 文件包含这些变量的值。
1. 转到 src/cloud-to-device-console-app 文件夹。 你可在此处看到 appsettings.json 文件和一些其他文件：

    * c2d-console-app.csproj - Visual Studio Code 的项目文件。
    * operations.json - 希望程序运行的操作的列表。
    * Program.cs - 示例程序代码。 此代码：

        * 加载应用设置。
        * 调用视频分析器 Edge 模块公开的[直接方法](../../../direct-methods.md)。
        * 暂停以检查“终端”窗口中程序的输出，并检查“输出”窗口中模块生成的事件 。
        * 调用直接方法以清理资源。
1. 编辑 operations.json 文件：
    * 将链接更改为管道： <br/>`"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-motion-file-sink/topology.json" `
    * 在 livePipelineSet 下，编辑管道拓扑的名称以匹配前面链接中的值： <br/>`"topologyName" : "EVRToFilesOnMotionDetection" `
    * 在 PipelineTopologyDelete 下，编辑名称： <br/>`"name": "EVRToFilesOnMotionDetection" `
