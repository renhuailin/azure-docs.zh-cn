---
title: Azure Percept 音频设备概述
description: 了解有关 Azure Percept 音频的详细信息
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 85eb4ed0832c9384fcd05154833bc21f83f4adf2
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426342"
---
# <a name="introduction-to-azure-percept-audio"></a>Azure Percept 音频简介

Azure Percept 音频是一个附件设备，可将语音 AI 功能添加到 Azure Percept DK。 它包含预配置的音频处理器和四麦克风线性阵列，使你能够使用 Azure 认知服务将语音命令、关键字辨识和远场语音应用到本地侦听设备。 借助 Azure Percept 音频，设备制造商可以将视觉功能以外的 Azure Percept DK 扩展到新的智能语音激活设备。 它与 Azure Percept DK、Azure Percept Studio 和其他 Azure Edge 管理服务现成集成。 它可在 [Microsoft 在线商店](https://go.microsoft.com/fwlink/p/?LinkId=2155270)购买。

:::image type="content" source="./media/overview-azure-percept-audio/percept-audio.png" alt-text="Azure Percept 音频设备。":::

## <a name="azure-percept-audio-components"></a>Azure Percept 音频组件

Azure Percept 音频包含以下主要组件：

- 具有四麦克风线性阵列的生产就绪的 Azure Percept 音频设备 (SoM)，并通过 XMOS 编解码器完成音频处理
- 开发人员板（内插板）（包括 2 个按钮、3 个 LED、Micro USB 和 3.5 mm 音频插孔）
- 所需线缆：FPC 电缆、USB Micro Type-B 转 USB-A
- 欢迎卡
- 集成了 80/20 1010 系列装载的机械装载板

## <a name="compute-capabilities"></a>计算功能 

Azure Percept 音频通过语音堆栈传递音频输入，此语音堆栈以混合边缘-云方式在 Azure Percept DK 载板的 CPU 上运行。 因此，Azure Percept 音频需要包含支持语音堆栈的 OS 的载板才能执行。 

处理过程如下所示： 

- Azure Percept 音频：捕获和转换音频，并将其发送到 DK 和音频插孔。

- Azure Percept DK：语音堆栈执行波束赋形和回音消除，并处理传入音频以优化语音。 然后，它将执行关键字辨识。

- 云：处理自然语言命令和短语，关键字验证，并重新训练。 

- 脱机：如果设备处于脱机状态，它将检测关键字并捕获 Internet 连接状态遥测。 由于无法执行云中的关键字验证，因此可能会出现关键字辨识的错误接受率增大。 

## <a name="getting-started"></a>入门

- [对 Azure Percept DK 进行组装](./quickstart-percept-dk-unboxing.md)
- [完成 Azure Percept DK 安装体验](./quickstart-percept-dk-set-up.md)
- [将 Azure Percept 音频设备连接到 devkit](./quickstart-percept-audio-setup.md)

## <a name="build-a-no-code-prototype"></a>生成无代码原型

使用 Azure Percept 语音助理模板在 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 中生成[无代码语音解决方案](./tutorial-no-code-speech.md)，用于酒店、医疗保健、库存和汽车方案。

### <a name="manage-your-no-code-speech-solution"></a>管理无代码语音解决方案

- [在 IoT 中心配置语音助理](./how-to-manage-voice-assistant.md)
- [在 Azure Percept Studio 中配置语音助理](./how-to-configure-voice-assistant.md)
- [Azure Percept Audio 故障排除](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>其他技术信息

- [Azure Percept 音频数据表](./azure-percept-audio-datasheet.md)
- [按钮和 LED 行为](./audio-button-led-behavior.md)

## <a name="next-steps"></a>后续步骤

在 [Microsoft 在线商店](https://go.microsoft.com/fwlink/p/?LinkId=2155270)订购 Azure Percept 音频设备。
