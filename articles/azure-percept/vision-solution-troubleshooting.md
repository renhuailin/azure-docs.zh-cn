---
title: 排查 Azure Percept 远景和视觉模块问题
description: 获取有关视觉 AI 原型设计体验的一些更常见问题的疑难解答提示
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: e7351079e7325aa7750dc0d10f0923bc847ccc3c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662164"
---
# <a name="vision-solution-troubleshooting"></a>远景解决方案疑难解答

请参阅以下指南，了解 Azure Percept Studio 中的非代码远景解决方案的故障排除信息。

## <a name="delete-a-vision-project"></a>删除远景项目

1. 转到  https://www.customvision.ai/projects 。

1. 将鼠标悬停在要删除的项目上，然后单击 "垃圾桶" 图标以删除该项目。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="中突出显示了 &quot;删除&quot; 图标自定义视觉中的 &quot;项目&quot; 页。":::

## <a name="check-which-modules-are-on-a-device"></a>检查设备上的模块

1. 转到 [Azure 门户](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)。

1. 单击 " **Iot 中心** " 图标。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="已突出显示 Iot 中心的 Azure 门户主页。" lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. 选择目标设备连接到的 IoT 中心。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="IoT 中心的列表。":::

1. 选择 **IoT Edge** 并在 " **设备 ID** " 选项卡下单击设备。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="IoT Edge 主页。":::

1. 设备模块将在 " **模块** " 选项卡下列出。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="选定设备的 Iot Edge 页面，显示 &quot;模块&quot; 选项卡的内容。" lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>删除设备

1. 转到 [Azure 门户](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)。

1. 单击 " **Iot 中心** " 图标。

1. 选择目标设备连接到的 IoT 中心。

1. 选择 **IoT Edge** 并选中目标设备 ID 旁边的复选框。 单击 "垃圾桶" 图标可删除设备。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Iot Edge 主页中突出显示的删除图标。":::

## <a name="eye-module-troubleshooting-tips"></a>眼睛模块故障排除提示

如果 **WebStreamModule** 有问题，请确保 **azureeyemodule**（这是视觉模型推断）正在运行。 若要检查运行时状态，请转到 [Azure 门户](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)，导航到 "**所有资源**"  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** 。 单击 " **模块** " 选项卡，查看所有已安装模块的运行时状态。

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="设备模块运行时状态屏幕。" lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

如果 **azureeyemodule** 的运行时状态未列为 "**正在运行**"，请单击 "**设置模块**"  ->  **azureeyemodule**。 在 " **模块设置** " 页上，将 **所需状态** 设置为 " **正在运行** " 并单击 " **更新**"

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="模块设置配置屏幕。":::

## <a name="view-device-rtsp-video-stream"></a>查看设备 RTSP 视频流

查看 [Azure Percept Studio](./how-to-view-video-stream.md) 或 [VLC media player](https://www.videolan.org/vlc/index.html)中的设备的 RTSP 视频流。

若要在 VLC media player 中打开 RTSP 流，请打开 "**媒体**" "打开" "  ->  **网络流** RTSP：/  ->  **/[设备 IP 地址]"/result**。

## <a name="next-steps"></a>后续步骤

有关 Azure Percept 深色疑难解答的详细信息，请参阅 [常规故障排除指南](./troubleshoot-dev-kit.md) 。