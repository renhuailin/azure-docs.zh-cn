---
title: Azure Percept 音频设备概述
description: 了解有关 Azure Percept 音频的详细信息
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: a63f471498667f58fc80f89323ad93b0feb8bc95
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662192"
---
# <a name="introduction-to-azure-percept-audio"></a>Azure Percept 音频简介

Azure Percept 音频是一个附件设备，可将语音 AI 功能添加到 Azure Percept 深色。 它包含预配置的语音 AI 加速器和四麦克风线性阵列，使你能够将自定义命令、关键字发现和 far 现场语音应用到本地侦听设备。 借助 azure Percept 音频，设备制造商可以将 Azure Percept 深色视觉功能扩展到新的智能语音激活设备。 它与 Azure Percept 深色、Azure Percept Studio 和其他 Azure edge 管理服务集成在一起。 它可在 [Microsoft 在线商店](https://go.microsoft.com/fwlink/p/?LinkId=2155270)购买。

![Azure Percept 音频设备。](./media/overview-azure-percept-audio/percept-audio.png)


## <a name="azure-percept-audio-components"></a>Azure Percept 音频组件

Azure Percept 音频包含以下主要组件：

- 生产就绪 Azure Percept 音频设备 (SoM) 具有四个麦克风线性阵列
- 开发人员板 (包括2x 按钮、3倍速 Led、USB 微版和 3.5 mm 音频插孔) 
- 所需电缆：软电缆、USB 微类型-B 到 USB-A
- 欢迎卡
- 集成了 80/20 1010 系列的机械安装板


<!---

## How it works

Azure Percept Audio passes the audio input to the Azure Percept DK carrier board in a hybrid edge-cloud manner. Specifically,

- The Azure Percept Audio device: processes the incoming speech input to the clearest format by executing beam forming and echo cancellation befor sending the input to the Azure Percept DK. 
- The Azure Percept DK uses edge processing to perform keyword spotting and then sends the relevant inputs to Azure speech services.
- Cloud: Processing of natural language commands and phrases, in addition to keyword verification and retraining.
- Offline: If the device is offline it will detect the keyword and capture telemetry that there is no internet connection at the time of the command. It will not be able to weed out false accepts since it cannot perform keyword verification.

-->

## <a name="getting-started"></a>入门

- [组装 Azure Percept 深色](./quickstart-percept-dk-unboxing.md)
- [完成 Azure Percept 深色安装体验](./quickstart-percept-dk-set-up.md)
- [将 Azure Percept 音频设备连接到 devkit](./quickstart-percept-audio-setup.md)

### <a name="build-a-no-code-prototype"></a>生成无代码原型

使用 Azure Percept voice 助手模板构建 [无代码语音解决方案](./tutorial-no-code-speech.md) ，用于宾馆、医疗保健、库存和汽车方案。

## <a name="additional-technical-information"></a>其他技术信息

- [Azure Percept 音频数据表](./azure-percept-audio-datasheet.md)

## <a name="next-steps"></a>后续步骤

订购 [Microsoft 在线商店](https://go.microsoft.com/fwlink/p/?LinkId=2155270)的 Azure Percept 音频设备。