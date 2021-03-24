---
title: Tutorial:如何部署 Azure IoT Central 的“视频分析 - 对象和运动检测”应用程序模板
description: 教程 - 本指南汇总了使用“视频分析 - 对象和运动检测”应用程序模板部署 Azure IoT Central 应用程序的步骤。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: c578da7e83a39f84e72b550038bd87dde3c61d28
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2021
ms.locfileid: "101727458"
---
# <a name="tutorial-how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Tutorial:如何使用“视频分析 - 对象和运动检测”应用程序模板部署 IoT Central 应用程序

若要简要了解关键的“视频分析 - 对象和运动检测”应用程序组件，请查看[“对象和运动检测视频分析”应用程序体系结构](architecture-video-analytics.md)。

下面的视频演示了如何使用“视频分析 - 对象和运动检测”应用程序模板来部署 IoT Central 解决方案：

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

在此教程系列中，你将了解如何：

> [!div class="checklist"]
> * 部署应用程序
> * 部署连接到应用程序的 IoT Edge 实例
> * 监视和管理应用程序

## <a name="prerequisites"></a>先决条件

建议使用 Azure 订阅。 或者，可使用 7 天免费试用版。 如果没有 Azure 订阅，则可在 [Azure 注册页](https://aka.ms/createazuresubscription)上创建一个。

## <a name="deploy-the-application"></a>部署应用程序

完成以下步骤，使用视频分析应用程序模板部署 IoT Central 应用程序：

1. 完成[在 Azure IoT Central 中创建视频分析应用程序 (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) 或[在 Azure IoT Central 中创建视频分析 (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) 教程来执行以下任务：
    - 创建 Azure 媒体服务帐户。
    - 通过“视频分析 - 对象和运动检测”应用程序模板创建 IoT Central 应用程序。
    - 在 IoT Central 应用程序中配置网关设备。 相机设备可通过网关连接到应用程序。

1. 完成[创建用于视频分析的 IoT Edge 实例 (Linux VM)](tutorial-video-analytics-iot-edge-vm.md) 或[教程：创建用于视频分析的 IoT Edge 实例 (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) 来执行以下任务：
    - 在已安装 Azure IoT Edge 运行时的情况下创建 Azure VM。- 准备 IoT Edge 安装来托管视频分析模块。
    - 将 IoT Edge 设备连接到 IoT Central 应用程序。

1. 完成[监视和管理视频分析应用程序](tutorial-video-analytics-manage.md)教程来执行以下任务：
    - 将对象和运动检测相机添加到 IoT Central 应用程序中的网关。
    - 开始相机处理。
    - 安装本地媒体播放器来查看 AMS 中捕获的视频。
    - 查看已捕获且显示所检测对象的视频。
    - 进行整理。

## <a name="clean-up-resources"></a>清理资源

应用程序使用完毕后，可以删除创建的所有资源，如下所示：

1. 在 IoT Central 应用程序中，请导航至“管理”部分中的“你的应用程序”页面 。 然后选择“删除”。
1. 在 Azure 门户中，删除“lva-rg”资源组。
1. 在本地计算机上，停止“amp-viewer”Docker 容器。

## <a name="next-steps"></a>后续步骤

现在你概要了解了部署和使用视频分析应用程序模板的步骤，接着若要开始操作，请查看

> [!div class="nextstepaction"]
> [在 Azure IoT Central 中创建视频分析应用程序 (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) 或

> [!div class="nextstepaction"]
> [在 Azure IoT Central 中创建视频分析 (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md)。