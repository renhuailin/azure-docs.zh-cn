---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 359c5f93516ea6f0561865bd86e4f51dedb4c3a5
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358187"
---
1. 在 Visual Studio Code 中，浏览到 src/edge。 你将看到创建的 .env 文件以及一些部署模板文件。

    部署模板是指具有某些占位符值的边缘设备的部署清单。 该 .env 文件具有这些变量的值。
1. 接下来，浏览到 src/cloud-to-device-console-app 文件夹。 在这里，你将看到创建的 appsettings.json 文件以及其他一些文件：

    * c2d-console-app.csproj：这是 Visual Studio Code 的项目文件。
    * operations.json：该文件列出了你想要程序运行的不同操作。
    * Program.cs：此示例程序代码：

        * 加载应用设置。
        * 调用 IoT Edge 上的实时视频分析模块的直接方法来创建拓扑，实例化图形并激活图形。
        * 暂停以在“终端”窗口中检查图形输出，并在“输出”窗口中检查发送到 IoT 中心的事件 。
        * 停用图形实例，删除图形实例，并删除图形拓扑。
