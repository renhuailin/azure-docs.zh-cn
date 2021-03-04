---
title: 查看 Azure Percept 深色的 RTSP 视频流
description: 了解如何从 Azure Percept 深色查看 RTSP 视频流
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 5f77e99dc5c34867fef2b0ac47c709824fa4477d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095997"
---
# <a name="view-your-azure-percept-dks-rtsp-video-stream"></a>查看 Azure Percept 深色的 RTSP 视频流

按照此指南查看 azure Percept Studio 内 Azure Percept 深色的 RTSP 视频流。 从视觉 AI 模型部署到设备的推断将可在 web 流中查看。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK (devkit)
- [Azure 订阅](https://azure.microsoft.com/free/)
- [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)：你已将 devkit 连接到 Wi-Fi 网络，创建了 IoT 中心，并已将 devkit 连接到 IoT 中心

## <a name="view-the-rtsp-video-stream"></a>查看 RTSP 视频流

1. 打开开发工具包。

1. 转到 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)。

1. 在 "概述" 页的左侧，单击 " **设备**"。

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Azure Percept Studio 概述屏幕。" lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. 从列表中选择你的 devkit。

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Azure Percept Studio 中可用设备的屏幕截图。":::

1. 单击 " **查看设备流**"。

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="显示可用的视觉项目操作的设备页的屏幕截图。":::

    这会打开一个单独的选项卡，其中显示 Azure Percept 深色的实时 web 流。

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="设备 web 流的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

了解如何查看 [Azure PERCEPT 深色遥测](./how-to-view-telemetry.md)数据。