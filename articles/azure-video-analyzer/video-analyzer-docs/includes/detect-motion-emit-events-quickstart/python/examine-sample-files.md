---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 03/18/2021
ms.author: faneerde
ms.openlocfilehash: e4f7402b89b0210562ea3d441f4fc8e691eb0e93
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385131"
---
1. 在 Visual Studio Code 中，转到 src/edge。 你将看到 .env 文件以及一些部署模板文件。

    部署模板是指边缘设备的部署清单，其中使用变量指定某些属性。 该 .env 文件包含这些变量的值。
1. 转到 src/cloud-to-device-console-app 文件夹。 你可在此处看到 appsettings.json 文件和一些其他文件：

    * operations.json - 希望程序运行的操作的列表。
    * main.py - 示例程序代码。 此代码：

      * 加载应用设置。
      * 调用 Azure 视频分析器模块所公开的直接方法。 可以通过调用模块的[直接方法](../../../direct-methods.md)来使用该模块分析实时视频流。
      * 暂停以检查“终端”窗口中程序的输出，并检查“输出”窗口中模块生成的事件 。
      * 调用直接方法以清理资源。

