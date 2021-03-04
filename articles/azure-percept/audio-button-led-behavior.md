---
title: Azure Percept 音频按钮和 LED 行为
description: 了解有关 Azure Percept 音频的按钮和 LED 状态的详细信息
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 1d956e33a84b5509c16400c8f5f8e813d116411a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095742"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Azure Percept 音频按钮和 LED 行为

请参阅以下指南，获取有关 Azure Percept 音频的按钮和 LED 状态的信息。

## <a name="button-behavior"></a>按钮行为

您可以使用按钮来控制设备的行为。

|按钮状态|  行为|
|------------|----------|
|静音|  按向麦克风数组静音/取消静音。 按钮事件在按下时触发。|
|PTT/磅|   按 PTT，绕过关键字发现状态并激活命令侦听状态。 再次按，停止代理的活动对话框并还原为关键字发现状态。 按钮事件在按下时触发。 仅当在代理正在通话时（而不是在代理正在侦听或使用时）按下按钮时，才可以使用磅。|

## <a name="led-behavior"></a>LED 行为

您可以使用 LED 指示器来了解设备所处的状态。

|LED|   LED 状态|  耳 SoM 状态|
|---|------------|----------------| 
|L02|   1x 白色，静态打开 |开机 |
|L02|   1x 白色，0.5 Hz 闪烁|  正在进行身份验证 |
|L01 & L02 & L03|   3倍速，静态开启|     正在等待关键字|
|L01 & L02 & L03|   LED 阵列闪烁，20fps | 正在侦听或讲述|
|L01 & L02 & L03|   LED 阵列赛车，20fps|    想法|
|L01 & L02 & L03|   3倍速，静态开启 | 静音|

## <a name="next-steps"></a>后续步骤

有关 Azure Percept 音频设备的故障排除提示，请参阅此 [指南](./troubleshoot-audio-accessory-speech-module.md)。