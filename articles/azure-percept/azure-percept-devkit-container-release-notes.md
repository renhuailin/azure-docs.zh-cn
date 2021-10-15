---
title: Azure Percept DK 容器发行说明
description: Azure Percept DK 容器版本的更改和修补程序信息。
author: amiyouss
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: 5b4e67197e5d43e929ca01c8c81e297e134edf47
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271605"
---
# <a name="azure-percept-dk-container-release-notes"></a>Azure Percept DK 容器发行说明

本页介绍有关 Azure Percept DK 容器版本的更改和修补程序。

若要下载容器更新，请转到 [Azure Percept Studio](https://ms.portal.azure.com/#blade/AzureEdgeDevices/main/overview)，从左侧导航窗格中选择“设备”，选择特定设备，然后选择“视觉和语音”选项卡以启动容器下载。 

## <a name="august-2108-release"></a>8 月 (2108) 版本

- Azureyemodule (mcr.microsoft.com/azureedgedevices/azureeyemodule:2108-1)
    - 已更新到 Intel 最新版本（5 月）删除 MyriadX 相机固件更新。 
    - 启用了 UVC（USB 视频类）相机作为输入源。 有关如何使用 UVC 照相机作为输入源的说明，请参阅[高级开发 github](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule#using-uvcusb-video-class-camera-as-input-source)。 
    - 修复了使用 H.264 原始 RTSP 流时的模块故障。

## <a name="june-2106-release"></a>6 月 (2106) 版本

- Azureyemodule
    - 此版本增加了对将慢速神经网络的推理与视频流进行时间调整的支持。 它会将延迟时间添加到与神经网络延迟大约相等的视频流，但会导致推断（例如边界框）在视频的适当位置绘制。 
    - 若要启用此功能，请将 `TimeAlignRTSP: true` 添加到 IoT 中心 Azure 门户的模块孪生。
- Azureearspeechclientmodule
    - 将 [Speech 1.16 SDK](../cognitive-services/speech-service/devices-sdk-release-notes.md) 集成到了语音模块中，其中包括对语音令牌支持的修补程序，并将 EAR SOM 作为低级别 lib 中的默认支持设备进行集成。
    - 修复了 PnP 检测问题，期间安全 MCU 被删除，但编解码器没有被删除。
    - 解决了带有 PTT/PTS 按钮功能的语音服务超时问题。
    - 安全修补程序
        - 超出了在语音模块中读取接收 TLS 数据的界限。
        - 执行 DFU 时，将重新公开 MCU 和编解码器 USB 端口。
        - 分析 JSON 时的异常处理。
        - 启用所有强化的编译器安全标志。
        - 超出了在语音模块中读取接收 TLS 数据的界限。
        - 对 SSL 和 libcurl 依赖关系进行版本控制，并防止出现易受攻击的版本。
        - 强制执行 HTTPS，并在 curl 连接上固定 TLS CA。

## <a name="april-2104-release"></a>4 月 (2104) 版本

- Azureyemodule
    - 将 IoT 中心消息格式修复为 UTF-8 编码的 JSON（以前是 64 位编码格式）。
    - 修复了自定义视觉分类器 bug（此前，自定义视觉分类器模型不能正常工作，它们将输出张量的错误维度解释为类置信度，这导致总是预测一个类(无论置信度如何)）。
    - 更新了 H.264，以使用 TCP（而不是 UDP）来实现 Azure 视频分析器集成。

## <a name="next-steps"></a>后续步骤

- [如何确定更新策略](./how-to-determine-your-update-strategy.md)
