---
title: 教程 - Azure IoT 视频分析 - 对象和移动检测 | Microsoft Docs
description: 本教程介绍如何部署和使用 IoT Central 的视频分析 - 对象和移动检测应用程序模板。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 09/02/2021
ms.openlocfilehash: 026c1794f678c5d194b0e5174986f9f962508647
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473741"
---
# <a name="tutorial-deploy-and-walk-through-the-video-analytics---object-and-motion-detection-application-template"></a>教程：部署并演练视频分析 - 对象和移动检测应用程序模板

了解重要视频分析 - 对象和移动检测应用程序的概述 “视频分析 - 对象和移动检测”应用程序模板可让你生成包括实时视频分析功能的 IoT 解决方案。

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="视频分析对象和移动检测组件示意图概览。":::

视频分析解决方案的关键组件包括：

### <a name="live-video-analytics-lva"></a>实时视频分析 (LVA)

LVA 提供了一个平台，可用于构建跨越边缘和云的智能视频应用程序。 该平台可让你构建跨越边缘和云的智能视频应用程序。 此平台提供了捕获、记录和分析实时视频的功能，并且可以将结果（视频和/或视频分析）发布到 Azure 服务。 Azure 服务可以在云或边缘中运行。 该平台可用于通过视频分析增强 IoT 解决方案。

有关详细信息，请参阅 GitHub 上的[实时视频分析](https://github.com/Azure/live-video-analytics)。

### <a name="iot-edge-lva-gateway-module"></a>IoT Edge LVA 网关模块

IoT Edge LVA 网关模块可将相机实例化为新设备，并使用 IoT 设备客户端 SDK 将其直接连接到 IoT Central。

在此参考实现中，设备使用来自边缘的对称密钥连接到解决方案。 有关设备连接的详细信息，请参阅[连接到 Azure IoT Central](../core/concepts-get-connected.md)

### <a name="media-graph"></a>媒体图

媒体图可让你定义从何处捕获媒体、如何处理媒体以及在何处交付结果。 可以通过按所需方式连接组件或节点来配置媒体图。 有关详细信息，请参阅 GitHub 上的[媒体图](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph)。

下面的视频演示了如何使用“视频分析 - 对象和运动检测”应用程序模板来部署 IoT Central 解决方案：

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

在此教程系列中，你将了解如何：

> [!div class="checklist"]
> * 部署应用程序
> * 部署连接到应用程序的 IoT Edge 实例
> * 监视和管理应用程序

## <a name="prerequisites"></a>先决条件

* 无需满足特定的先决条件，就可以部署此应用。
* 你可以使用免费定价计划或使用 Azure 订阅。

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