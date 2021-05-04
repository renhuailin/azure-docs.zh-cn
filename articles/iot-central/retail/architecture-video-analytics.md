---
title: Azure IoT Central 视频分析对象和移动检测 | Microsoft Docs
description: 了解如何使用 IoT Central 中的“视频分析 - 对象和移动检测”应用程序模板生成 IoT Central 应用程序。 此模板使用实时视频分析和联网相机。
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 48808f762536390287bae40e8af3849da20b81c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91874282"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>“视频分析 - 对象和移动检测”应用程序体系结构

“视频分析 - 对象和移动检测”应用程序模板可让你生成包括实时视频分析功能的 IoT 解决方案。

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="视频分析对象和移动检测组件示意图概览。":::

视频分析解决方案的关键组件包括：

## <a name="live-video-analytics-lva"></a>实时视频分析 (LVA)

LVA 提供了一个平台，可用于构建跨越边缘和云的智能视频应用程序。 该平台可让你构建跨越边缘和云的智能视频应用程序。 此平台提供了捕获、记录和分析实时视频的功能，并且可以将结果（视频和/或视频分析）发布到 Azure 服务。 Azure 服务可以在云或边缘中运行。 该平台可用于通过视频分析增强 IoT 解决方案。

有关详细信息，请参阅 GitHub 上的[实时视频分析](https://github.com/Azure/live-video-analytics)。

## <a name="iot-edge-lva-gateway-module"></a>IoT Edge LVA 网关模块

IoT Edge LVA 网关模块可将相机实例化为新设备，并使用 IoT 设备客户端 SDK 将其直接连接到 IoT Central。

在此参考实现中，设备使用来自边缘的对称密钥连接到解决方案。 有关设备连接的详细信息，请参阅[连接到 Azure IoT Central](../core/concepts-get-connected.md)

## <a name="media-graph"></a>媒体图

媒体图可让你定义从何处捕获媒体、如何处理媒体以及在何处交付结果。 可以通过按所需方式连接组件或节点来配置媒体图。 有关详细信息，请参阅 GitHub 上的[媒体图](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph)。

## <a name="next-steps"></a>后续步骤

建议你在下一步了解[如何使用“视频分析 - 对象和移动检测”应用程序模板部署 IoT Central 应用程序](tutorial-video-analytics-deploy.md)。
