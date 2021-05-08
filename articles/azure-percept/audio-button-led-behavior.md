---
title: Azure Percept Audio 按钮和 LED 行为
description: 了解有关 Azure Percept Audio 的按钮和 LED 状态的详细信息
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 1d956e33a84b5509c16400c8f5f8e813d116411a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095742"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Azure Percept Audio 按钮和 LED 行为

有关 Azure Percept Audio 的按钮和 LED 状态的信息，请参阅以下指南。

## <a name="button-behavior"></a>按钮行为

可以使用按钮来控制设备的行为。

|按钮状态|  行为|
|------------|----------|
|静音|  按下此按钮可将麦克风阵列静音/取消静音。 如果按下按钮，则会在松开按钮时触发按钮事件。|
|PTT/PTS|   按下“PTT”可跳过关键字辨识状态并激活命令聆听状态。 再按一次可停止代理的活动对话并恢复到关键字辨识状态。 如果按下按钮，则会在松开按钮时触发按钮事件。 PTS 仅在代理讲话时按下按钮时工作，而在代理正在聆听或思考时不工作。|

## <a name="led-behavior"></a>LED 行为

可以使用 LED 指示器来了解设备所处的状态。

|LED|   LED 状态|  Ear SoM 状态|
|---|------------|----------------| 
|L02|   1x 白色，静态开启 |开机 |
|L02|   1x 白色，0.5 Hz 闪烁|  正在进行身份验证 |
|L01 和 L02 和 L03|   3x 蓝色，静态开启|     关键字待定|
|L01 和 L02 和 L03|   LED 数组闪烁，20fps | 正在聆听或正在说话|
|L01 和 L02 和 L03|   LED 阵列 racing，20fps|    正在思考|
|L01 和 L02 和 L03|   3x 红色，静态开启 | 静音|

## <a name="next-steps"></a>后续步骤

有关 Azure Percept Audio 设备的故障排除提示，请参阅[此指南](./troubleshoot-audio-accessory-speech-module.md)。