---
title: Azure Percept Audio 按钮和 LED 行为
description: 了解有关 Azure Percept Audio 的按钮和 LED 状态的详细信息
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 169c3e7be60d5bf1efb7046aa92316a472fd4518
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2021
ms.locfileid: "107930850"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Azure Percept Audio 按钮和 LED 行为

有关 Azure Percept Audio 设备的按钮和 LED 状态的信息，请参阅以下指南。

## <a name="button-behavior"></a>按钮行为

使用按钮来控制设备的行为。

|按钮状态|行为|
|------------|----------|
|静音|按下此按钮可将麦克风阵列静音/取消静音。 如果按下按钮，则会在松开按钮时触发按钮事件。|
|PTT/PTS|按下“PTT”可跳过关键字辨识状态并激活命令聆听状态。 再次按下，即可关闭代理的活动对话框并将其还原为关键字焦点状态。 如果按下按钮，则会在松开按钮时触发按钮事件。 仅当代理正在通话时（而不是在代理正在侦听或使用时）按下按钮时，PTS 才会运行。|

## <a name="led-behavior"></a>LED 行为

使用 LED 指示灯来了解设备目前的状态。

|LED|LED 状态|Ear SoM 状态|
|---|------------|----------------|
|L02|1x 白色，静态开启|开机 |
|L02|1x 白色，0.5 Hz 闪烁|正在进行身份验证 |
|L01 & L02 & L03|3x 蓝色，静态开启|关键字待定|
|L01 & L02 & L03|LED 数组闪烁，20fps |正在侦听或说话|
|L01 & L02 & L03|LED 阵列 racing，20fps|正在思考|
|L01 & L02 & L03|3x 红色，静态开启 |静音|

## <a name="next-steps"></a>后续步骤

有关 Azure Percept Audio 设备的故障排除提示，请参阅[此指南](./troubleshoot-audio-accessory-speech-module.md)。