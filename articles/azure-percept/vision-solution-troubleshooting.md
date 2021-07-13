---
title: 排查 Azure Percept Vision 和视觉模块的问题
description: 获取视觉 AI 原型设计体验中一些更常见问题的故障排除技巧。
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 28ac7d0d7079d8ba8c9483e7da816430295941c9
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112027850"
---
# <a name="vision-solution-troubleshooting"></a>视觉解决方案故障排除

本文提供了有关如何在 Azure Percept Studio 中排查无代码视觉解决方案问题的信息。

## <a name="delete-a-vision-project"></a>删除视觉项目

1. 转到[自定义视觉项目](https://www.customvision.ai/projects)页。

1. 将鼠标悬停在要删除的项目上，然后选择垃圾桶图标以删除该项目。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="显示自定义视觉中的“项目”页的屏幕截图，其中突出显示了“删除”图标。":::

## <a name="check-which-modules-are-on-a-device"></a>检查设备上有哪些模块

1. 转到 [Azure 门户](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)。

1. 选择“IoT 中心”图标。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="显示“Azure 门户”主页的屏幕截图，其中突出显示了“IoT 中心”图标。" lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. 选择目标设备连接到的 IoT 中心。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="显示 IoT 中心列表的屏幕截图。":::

1. 选择“IoT Edge”，然后在“设备 ID”选项卡下选择你的设备。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="显示 IoT Edge 主页的屏幕截图。":::

1. 设备模块显示在“模块”选项卡上的列表中。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="显示所选设备的 IoT Edge 页面的屏幕截图，其中显示了“模块”选项卡内容。" lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>删除设备

1. 转到 [Azure 门户](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)。

1. 选择“IoT 中心”图标。

1. 选择目标设备连接到的 IoT 中心。

1. 选择“IoT Edge”并选中目标设备 ID 旁边的复选框。 选择“删除”以删除设备。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="显示“删除”按钮的屏幕截图，其中突出显示了 IoT Edge 主页。":::

## <a name="eye-module-troubleshooting-tips"></a>Eye 模块故障排除技巧

以下故障排除技巧可帮助解决视觉 AI 原型设计体验中的一些更常见问题。

### <a name="check-the-runtime-status-of-azureeyemodule"></a>检查 azureeyemodule 的运行时状态

如果 WebStreamModule 有问题，请确保 azureeyemodule（处理视觉模型推理）正在运行。 若要检查运行时状态，请执行以下操作：

1. 转到 [Azure 门户](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)，然后转到“所有资源” >  *\<your IoT hub>*  > “IoT Edge” >  *\<your device ID>* 。 
1. 选择“模块”选项卡，查看所有已安装模块的运行时状态。

   :::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="显示设备模块运行时状态屏幕的屏幕截图。" lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

1. 如果 azureeyemodule 的运行时状态未列为“正在运行”，请选择“设置模块” > “azureeyemodule”。 
1. 在“模块设置”页上，将“所需状态”设置为“正在运行”并选择“更新”。

    :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="显示“模块设置”配置屏幕的屏幕截图。":::

### <a name="update-telemetryintervalneuralnetworkms"></a>更新 TelemetryIntervalNeuralNetworkMs

如果你看到以下计数限制错误，则需要更新 azureeyemodule 模块孪生设置中的 TelemetryIntervalNeuralNetworkMs 值。

|错误消息|
|------|
|IotHub“xxxxxxxxx”上的消息总数超出了分配的配额。 允许的最大消息数：8000，当前消息数：xxxx。 对于此中心，发送和接收操作将被阻止，直到下一个 UTC 日期。 考虑增加此中心的单位以增加配额。|

TelemetryIntervalNeuralNetworkMs 确定从神经网络发送消息的频率。 消息以毫秒为单位发送。 Azure 订阅每天发送一定的消息。

消息量取决于订阅层。 如果你发现自己由于发送了过多消息而遭锁定，请将此值增加到更高的数值。 12,000 意味着每 12 秒钟发送一次消息。 此数量表示每天为你提供 7,200 条消息，这个数量在免费订阅的 8,000 消息限制之内。

若要更新 TelemetryIntervalNeuralNetworkMs 值，请执行以下操作：

1. 登录到 [Azure 门户](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home)并打开“所有资源”。

1. 在“所有资源”页上，选择安装期间已预配到开发工具包的 IoT 中心名称。

1. 在 IoT 中心页左侧的“自动设备管理”下，选择“IoT Edge”。 在“IoT Edge 设备”页上，找到开发工具包的设备 ID。 选择开发工具包的设备 ID，打开“IoT Edge 设备”页。

1. 在“模块”选项卡上，选择“azureeyemodule”。

1. 在“azureeyemodule”页上，打开“模块标识孪生”。

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="模块页的屏幕截图。" lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. 向下滚动到“属性”。 “正在运行”和“日志记录”属性目前处于不活动状态。

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline.png" alt-text="“模块标识孪生”属性的屏幕截图。" lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. 根据需要更新 TelemetryIntervalNeuralNetworkMs 值，然后选择“保存”图标。

## <a name="view-device-rtsp-video-stream"></a>查看设备 RTSP 视频流

在 [Azure Percept Studio](./how-to-view-video-stream.md) 或 [VLC 媒体播放器](https://www.videolan.org/vlc/index.html)中查看设备的 RTSP 视频流。

若要在 VLC 媒体播放器中打开 RTSP 流，请转到“媒体” > “打开网络流” > “rtsp://[设备 IP 地址]:8554/result”。

## <a name="next-steps"></a>后续步骤

有关 Azure Percept DK 实例故障排除的详细信息，请参阅[常规故障排除指南](./troubleshoot-dev-kit.md)。