---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: b5d950aca0090067d426457cc09495fd0b8344b5
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110383679"
---
1. 在 Visual Studio Code 中，浏览到 src/edge。 你将看到创建的 .env 文件以及一些部署模板文件。

   部署模板是指具有某些占位符值的边缘设备的部署清单。 该 .env 文件具有这些变量的值。
2. 接下来，浏览到 src/cloud-to-device-console-app 文件夹。 在这里，你将看到创建的 appsettings.json 文件以及其他一些文件：

   - c2d-console-app.csproj：这是 Visual Studio Code 的项目文件。
   - operations.json：该文件列出了你想要程序运行的不同操作。
   - Program.cs：此示例程序代码：
     - 加载应用设置。
     - 调用 Azure 视频分析器模块的直接方法来创建拓扑，实例化管道并激活管道。
     - 暂停以在“终端”窗口中检查管道输出，并在“输出”窗口中检查发送到 IoT 中心的事件 。
     - 停用实时管道，删除实时管道，并删除拓扑。
