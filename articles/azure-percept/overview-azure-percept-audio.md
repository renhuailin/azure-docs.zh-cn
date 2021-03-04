---
title: Azure Percept 音频设备概述
description: 了解有关 Azure Percept 音频的详细信息
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 8884663b3f0e861e62f48c3aab680f0f31e74428
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098377"
---
# <a name="introduction-to-azure-percept-audio"></a>Azure Percept 音频简介

Azure Percept 音频是一个附件设备，可将语音 AI 功能添加到 Azure Percept 深色。 它包含预配置的音频处理器和四麦克风线性阵列，使你能够使用 Azure 认知服务将语音命令、关键字发现和远距离现场语音应用到本地侦听设备。 借助 azure Percept 音频，设备制造商可以将 Azure Percept 深色以外的视觉功能扩展到新的智能语音激活设备。 它与 Azure Percept 深色、Azure Percept Studio 和其他 Azure edge 管理服务集成在一起。 它可在 [Microsoft 在线商店](https://go.microsoft.com/fwlink/p/?LinkId=2155270)购买。

:::image type="content" source="./media/overview-azure-percept-audio/percept-audio.png" alt-text="Azure Percept 音频设备。":::

## <a name="azure-percept-audio-components"></a>Azure Percept 音频组件

Azure Percept 音频包含以下主要组件：

- 生产就绪的 Azure Percept 音频设备 (SoM) 具有四个麦克风线性阵列，并通过 XMOS 编解码器完成音频处理
- 开发人员 (转接) 板 (包括2x 按钮、3倍速 Led、微型 USB 和 3.5 mm 音频插孔) 
- 所需电缆： FPC 电缆、USB 微类型-B 到 USB-A
- 欢迎卡
- 集成了 80/20 1010 系列的机械安装板

## <a name="compute-capabilities"></a>计算功能 

Azure Percept 音频通过语音堆栈传递音频输入，此语音堆栈在混合边缘-云方式中运行在 Azure Percept 深色的托架上。 因此，Azure Percept 音频需要包含支持语音堆栈的操作系统的运营商板才能执行。 

处理过程如下所示： 

- Azure Percept 音频：执行横梁，并处理传入的音频以优化语音并发送到深。  

- Azure Percept 深色：语音堆栈执行关键字发现。  

- Cloud：处理自然语言命令和短语，关键字验证，并重新训练。 

- 脱机：如果设备处于脱机状态，它将检测关键字并捕获 internet 连接状态遥测。 由于无法执行云中的关键字验证，因此可能会出现关键字发现的错误接受率增大。 

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

## <a name="build-a-no-code-prototype"></a>生成无代码原型

使用 Azure Percept voice 助手模板构建 [无代码语音解决方案](./tutorial-no-code-speech.md) ，用于宾馆、医疗保健、库存和汽车方案。

### <a name="manage-your-no-code-speech-solution"></a>管理你的无代码语音解决方案

- [在 Iot 中心配置语音助手](./how-to-manage-voice-assistant.md)
- [在 Azure Percept Studio 中配置语音助理](./how-to-configure-voice-assistant.md)
- [Azure Percept Audio 故障排除](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>其他技术信息

- [Azure Percept 音频数据表](./azure-percept-audio-datasheet.md)
- [按钮和 LED 行为](./audio-button-led-behavior.md)

## <a name="next-steps"></a>后续步骤

订购 [Microsoft 在线商店](https://go.microsoft.com/fwlink/p/?LinkId=2155270)的 Azure Percept 音频设备。
