---
title: 获取语音设备 SDK
titleSuffix: Azure Cognitive Services
description: “语音服务”适用于多种设备和音频源。 现在，可以通过匹配的硬件和软件进一步利用语音应用程序。 本文介绍如何访问语音设备 SDK 并开始开发。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: pafarley
ms.openlocfilehash: e94f2c1b0340507e8cb254b48dd949dab699cd1d
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543684"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>获取认知服务语音设备 SDK

语音设备 SDK 是一个预优化库，旨在与特制的开发工具包和各种麦克风阵列配置配合使用。

## <a name="choose-a-development-kit"></a>选择开发工具包

|设备|规格|说明|方案|
|--|--|--|--|
|[Azure Percept Audio DK](/azure/azure-percept/overview-azure-percept-audio)<br>[安装](/azure/azure-percept/quickstart-percept-dk-unboxing) / [快速入门](/azure/azure-percept/quickstart-percept-audio-setup)![Azure Percept Audio DK](./media/speech-devices-sdk/azure-percept-audio.png)|带有 XMOS 编解码器的 4 mic 线性阵列。 <br> Linux| 一个附件设备，可将语音人工智能 (AI) 功能添加到边缘设备。 它包含预配置的音频处理器和四麦克风线性阵列，以便在 Azure 认知服务的帮助下使用语音命令、关键字辨识和远场语音。 此设备随附 Azure Percept DK、Azure Percept Studio 和其他 Azure 边缘管理服务，可与我们最强大且精简的一体化语音设备 SDK 顺利集成。|对话听录, 机器人, 智能构建, 制造业, 农业|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[设置](../../kinect-dk/set-up-azure-kinect-dk.md) / [快速入门](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 麦克风阵列 RGB 和深度相机。 <br>[Windows](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows)/[Linux](./speech-devices-sdk-quickstart.md?pivots=platform-linux%253fpivots%253dplatform-linux)|具有高级人工智能 (AI) 传感器的空间计算开发者工具包可用于构建复杂的计算机视觉和语音模型。 它将同类最佳的空间麦克风阵列和深度相机与摄像机和方向传感器都组合在一个具有多种模式、选项和 SDK 的小型设备中，可与 Azure 认知服务顺利集成。|对话听录, 机器人, 智能构建|
|[Urbetter 开发工具包](http://www.urbetter.com/products_56/278.html)![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 麦克风阵列、ARM SOC、WIFI、以太网、HDMI、USB 摄像头。 <br>Linux|行业级语音设备 SDK，适用于 Microsoft 麦克风阵列并支持扩展 I/O，如 HDMI/以太网和更多 USB 外围设备 <br> [联系 Urbetter](http://www.urbetter.com/products_56/278.html)|对话听录、教育、医院、机器人、OTT Box、语音代理、汽车餐厅|
|[Roobo 智能音频开发工具包](http://ddk.roobo.com)<br>[设置](speech-devices-sdk-roobo-v1.md) / [快速入门](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)![Roobo 智能音频开发工具包](media/speech-devices-sdk/device-roobo-v1.jpg)|7 麦克风阵列、ARM SOC、WIFI、音频输出、IO。 <br>[Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)|首个适用于 Microsoft 麦克风阵列和前端处理 SDK 的语音设备 SDK，用于开发高质量的听录和语音方案|对话听录、智能扬声器、语音代理、可穿戴设备|
|Roobo 智能音频开发工具包 2<br>[设置](speech-devices-sdk-roobo-v2.md)<br>![Roobo 智能音频开发工具包 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 麦克风阵列、ARM SOC、WIFI、蓝牙、IO。 <br>Linux|第二代语音设备 SDK，在经济高效的参考设计中提供替代操作系统和更多功能。|对话听录、智能扬声器、语音代理、可穿戴设备|


## <a name="download-the-speech-devices-sdk"></a>下载语音设备 SDK

下载[语音设备 SDK](./speech-devices-sdk.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [语音设备 SDK 入门](./speech-devices-sdk-quickstart.md?pivots=platform-android)
