---
title: Azure Percept Audio 按钮和 LED 状态
description: 了解有关 Azure Percept Audio 的按钮和 LED 状态的详细信息
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 15c76d9729f64867d11e8d91be2be3c9c47c531f
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122066757"
---
# <a name="azure-percept-audio-button-and-led-states"></a>Azure Percept Audio 按钮和 LED 状态

有关 Azure Percept Audio 设备的按钮和 LED 状态的信息，请参阅以下指南。

## <a name="button-behavior"></a>按钮行为

使用按钮来控制设备的行为。

|按钮状态|行为|
|------------|----------|
|静音|按下此按钮可将麦克风阵列静音/取消静音。 如果按下按钮，则会在松开按钮时触发按钮事件。|
|PTT/PTS|按下“PTT”可跳过关键字辨识状态并激活命令聆听状态。 再次按下，即可关闭代理的活动对话框并将其还原为关键字焦点状态。 如果按下按钮，则会在松开按钮时触发按钮事件。 仅当代理正在通话时（而不是在代理正在侦听或使用时）按下按钮时，PTS 才会运行。|

## <a name="led-states"></a>LED 状态

使用 LED 指示灯来了解设备目前的状态。

|LED|LED 状态|Ear SoM 状态|
|---|------------|----------------|
|L02|1x 白色，静态开启|开机 |
|L02|1x 白色，0.5 Hz 闪烁|正在进行身份验证 |
|L01 & L02 & L03|3x 蓝色，静态开启|关键字待定|
|L01 & L02 & L03|LED 阵列闪烁，20fps |正在侦听或说话|
|L01 & L02 & L03|LED 阵列 racing，20fps|正在思考|
|L01 & L02 & L03|3x 红色，静态开启 |静音|

## <a name="understanding-ear-som-led-indicators"></a>了解 Ear SoM LED 指示器
可以使用 LED 指示器来了解设备所处的状态。 设备开机并且模块完全初始化需要大约 4-5 分钟。 完成初始化步骤后，你将看到：

1. 中央的白色 LED 亮起（静态）：设备已通电。
1. 中央的白色 LED 亮起（闪烁）：正在进行身份验证。
1. 中央的白色 LED 亮起（静态）：设备已通过身份验证，但未配置关键字。
1. 部署完演示并且设备准备好供使用后，所有三个 LED 都将变为蓝色。


## <a name="troubleshooting-led-issues"></a>排查 LED 问题
- 如果中央的 LED 白色常亮，请尝试[使用模板创建语音助理](./tutorial-no-code-speech.md)。
- 如果中央的 LED 始终闪烁，则表明有身份验证问题。 请尝试以下故障排除步骤：
    1. 请确保 USB-A 和 micro USB 连接受到保护 
    1. 查看[语音模块是否正在运行](./troubleshoot-audio-accessory-speech-module.md#checking-runtime-status-of-the-speech-module)
    1. 重启设备
    1. [收集日志](./troubleshoot-audio-accessory-speech-module.md#collecting-speech-module-logs)并将其附加到支持请求
    1. 查看开发工具包是否正在运行最新软件，并应用更新（如果可用）。

## <a name="next-steps"></a>后续步骤

有关 Azure Percept Audio 设备的故障排除提示，请参阅[此指南](./troubleshoot-audio-accessory-speech-module.md)。