---
title: Azure Percept Vision 和视觉模块疑难解答
description: 获取视觉 AI 原型设计体验中一些更常见问题的故障排除技巧。
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 6bc06ed6850c247641423ef365a86dd9d8aec90a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225971"
---
# <a name="troubleshoot-azure-percept-vision-and-vision-modules"></a>Azure Percept Vision 和视觉模块疑难解答

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

## <a name="check-the-runtime-status-of-azureeyemodule"></a>检查 azureeyemodule 的运行时状态

如果 WebStreamModule 有问题，请确保 azureeyemodule（处理视觉模型推理）正在运行。 若要检查运行时状态，请执行以下操作：

1. 转到 [Azure 门户](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)，然后转到“所有资源” >  *\<your IoT hub>*  > “IoT Edge” >  *\<your device ID>* 。 
1. 选择“模块”选项卡，查看所有已安装模块的运行时状态。

   :::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="显示设备模块运行时状态屏幕的屏幕截图。" lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

1. 如果 azureeyemodule 的运行时状态未列为“正在运行”，请选择“设置模块” > “azureeyemodule”。 
1. 在“模块设置”页上，将“所需状态”设置为“正在运行”并选择“更新”。

    :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="显示“模块设置”配置屏幕的屏幕截图。":::

## <a name="change-how-often-messages-are-sent-from-the-azureeyemodule"></a>更改从 azureeyemodule 发送消息的频率

订阅层可能会限制可从设备发送到 IoT 中心的消息数。 例如，免费层将消息数限制为每天 8,000 条。 达到该限制后，azureeyemodule 将停止运行，你可能会收到此错误：

|错误消息|
|------|
|IotHub "xxxxxxxxx"上的消息总数超过了分配的配额。允许的最大消息计数: "8000"，当前消息计数: "xxxx"。已阻止此中心的发送和接收操作，直到 UTC 时间的明天。请考虑增加此中心的单位数以增加配额。|

使用 azureeyemodule 模块孪生，可以更改发送消息的间隔速率。 为间隔速率输入的值指示每条消息的发送频率（以毫秒为单位）。 该数字越大，每条消息之间的时间就越长。 例如，如果将间隔速率设置为 12,000，则意味着每 12 秒发送一条消息。 对于整天运行的模型，以此速率每天将生成 7,200 条消息，这一数量低于免费层限制。 选择的值取决于你需要的视觉模型响应速度。

> [!NOTE]
> 更改消息间隔速率不会影响每条消息的大小。 消息大小取决于几个不同的因素，例如模型类型和在每条消息中检测到的对象数。 因此，很难确定消息大小。

请按照以下步骤更新消息间隔：

1. 登录到 [Azure 门户](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home)并打开“所有资源”。

1. 在“所有资源”页上，选择安装期间已预配到开发工具包的 IoT 中心名称。

1. 在 IoT 中心页左侧的“自动设备管理”下，选择“IoT Edge”。 在“IoT Edge 设备”页上，找到开发工具包的设备 ID。 选择开发工具包的设备 ID，打开“IoT Edge 设备”页。

1. 在“模块”选项卡上，选择“azureeyemodule”。

1. 在“azureeyemodule”页上，打开“模块标识孪生”。

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="模块页的屏幕截图。" lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. 向下滚动到“属性”
1. 查找“TelemetryInterval”并将其替换为“TelemetryIntervalNeuralNetworkMs” 

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline-02.png" alt-text="“模块标识孪生”属性的屏幕截图。" lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. 将 TelemetryIntervalNeuralNetworkMs 值更新为所需的值

1. 选择“保存”图标。

## <a name="view-device-rtsp-video-stream"></a>查看设备 RTSP 视频流

在 [Azure Percept Studio](./how-to-view-video-stream.md) 或 [VLC 媒体播放器](https://www.videolan.org/vlc/index.html)中查看设备的 RTSP 视频流。

若要在 VLC 媒体播放器中打开 RTSP 流，请转到“媒体” > “打开网络流” > “rtsp://[设备 IP 地址]:8554/result”。

如果 RTSP 的部分流被灰盒阻止，则你可能正在尝试通过较差的网络连接查看。 检查该连接是否有足够的带宽用于传输视频流。

## <a name="next-steps"></a>后续步骤

有关 Azure Percept DK 实例故障排除的详细信息，请参阅[常规故障排除指南](./troubleshoot-dev-kit.md)。