---
title: Azure Percept 语音模块接口工作流
description: 介绍 Azure Percept 语音模块的工作流和可用方法
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 7/19/2021
ms.custom: template-concept
ms.openlocfilehash: 8dec6a6c4162225b4f5e9ddf3ce74b739fd39562
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222831"
---
# <a name="azure-percept-speech-module-interface-workflow"></a>Azure Percept 语音模块接口工作流

本文介绍 Azure Percept 语音模块如何与 IoT 中心交互。 这种交互是通过模块孪生和模块方法进行的。 此外，本文还列出了用于调用语音模块的直接方法调用。

## <a name="speech-module-interaction-with-iot-hub-via-module-twin-and-module-method"></a>通过模块孪生和模块方法实现语音模块与 IoT 中心的交互
- IoT 中心使用模块孪生来部署语音模块设置，这些设置将保存在属性中。 语音模块可以通过模块孪生报告属性将设备信息和遥测数据更新到 IoT 中心。
- IoT 中心可以通过模块方法将控制请求发送到语音模块。
- IoT 中心可以通过模块方法获取语音模块状态。

有关更多详细信息，请参阅[了解并在 IoT 中心内使用模块孪生](../iot-hub/iot-hub-devguide-module-twins.md)。


## <a name="speech-module-states"></a>语音模块状态
- IoTInitialized：指示 IoT 模块已初始化，并且语音模块与边缘中心模块之间的网络已连接。
- Authenticating：正在处理 Azure 音频设备身份验证。
- Authenticated：已完成 Azure 音频设备身份验证。 如果失败，则 IoT 中心会收到错误消息。
- MicDiscovering：开始通过 ALSA 接口枚举麦克风阵列。
- MicDiscovered：已完成枚举麦克风阵列。 如果失败，则 IoT 中心会收到错误消息。
- SpeechConfigured：CC 配置已完成。 如果失败，则 IoT 中心会收到错误消息。
- SpeechStarted：指示机器人已配置并正在运行。
- SpeechStopped：指示机器人已停止。
- DeviceRemoved：指示删除了 Azure 音频设备。


## <a name="speech-bot-states"></a>语音机器人状态
仅支持在 SpeechStarted 语音模块状态下查询语音机器人状态。
- Ready：KWS 已准备就绪，正在等待语音激活。
- Listening：机器人正在收听语音输入。
- Thinking：机器人正在等待响应。
- Speaking：机器人收到了响应，并正在说出响应。

## <a name="interaction-between-iot-hub-and-the-speech-module"></a>IoT 中心与语音模块之间的交互 
本部分介绍 IoT 中心如何与语音模块交互。 如图所示，有三种类型的消息。
- 使用所需属性进行部署，使用报告属性进行更新
- 模块方法调用
- 更新遥测数据

:::image type="content" source="media/speech-module-interface-workflow/speech-module-diagram.png" alt-text="该示意图显示 IoT 中心与语音模块之间的交互":::

IoT 中心结合两个参数调用模块方法：
- 模块方法名称（区分大小写）
- 方法有效负载

语音模块在响应中提供以下信息：
- 状态代码
    - 0 = 空闲
    - 102 = 正在处理
    - 200 = 成功
    - 202 = 挂起
    - 500 = 失败
    - 501 = 不存在
- 状态有效负载

下面是模块方法 GetModuleState 的用法示例：
1. 结合以下参数调用该方法：
    - 字符串："GetModuleState"
    - 未指定
1. 响应：
    - 状态代码：200
    - 有效负载："DeviceRemoved"

## <a name="next-steps"></a>后续步骤
在[使用 Azure IoT 中心配置语音助理应用程序](./how-to-configure-voice-assistant.md)时，请尝试运用这些概念。