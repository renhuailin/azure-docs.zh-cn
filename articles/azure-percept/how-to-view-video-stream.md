---
title: 查看 Azure Percept 深色的 RTSP 视频流
description: 了解如何从 Azure Percept 深色的视觉 SoM 查看 RTSP 视频流
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 20fb8495e17d4294351a50c3bc97436de9f03450
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661871"
---
# <a name="view-your-azure-percept-dks-rtsp-video-stream"></a>查看 Azure Percept 深色的 RTSP 视频流

按照此指南进行操作，查看 azure Percept Studio 内 Azure Percept 深色的远景视频流。 推断从视觉 AI 模型部署到设备，可在 webstream 中查看。

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

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Azure Percept Studio 概述屏幕。":::

1. 单击 " **查看设备流**"。

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="Azure Percept Studio 概述屏幕。":::

    这会打开一个单独的选项卡，其中显示了来自 Azure Percept 深色的远景 webstream 的实时的选项卡。

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="Azure Percept Studio 概述屏幕。":::

## <a name="next-steps"></a>后续步骤

了解如何查看 [Azure PERCEPT 深色遥测](./how-to-view-telemetry.md)数据。