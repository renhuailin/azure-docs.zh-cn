---
title: Azure Percept 音频入门
description: 了解如何将 Azure Percept 音频设备连接到 Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 588ebde85b6012ddbfb88ca8305fc735b7a0ba41
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097986"
---
# <a name="azure-percept-audio-setup"></a>Azure Percept 音频设置

Azure Percept 音频可直接与 Azure Percept DK 一起使用。 无需进行任何特殊设置。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK (devkit)
- Azure Percept 音频
- [Azure 订阅](https://azure.microsoft.com/free/)
- [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)：你已将 devkit 连接到 Wi-Fi 网络，创建了 IoT 中心，并已将 devkit 连接到 IoT 中心
- 可以连接到 3.5 毫米音频插孔的扬声器或耳机（可选）

## <a name="connecting-your-devices"></a>连接设备

1. 使用附带的 Micro USB 到 USB Type-A 电缆将 Azure Percept Audio 设备连接到 Azure Percept DK 载板。 将电缆的 Micro USB 端连接到内插板（开发人员），并将 Type-A 端连接到 Percept DK 载板。
1. （可选）通过音频插孔（标记为“输出线”）将扬声器或耳机连接到 Azure Percept Audio。 这样即可听到语音助理的音频响应。 如果未连接扬声器或耳机，你仍可在演示窗口中查看文本形式的响应。 

1. 开启 devkit。 内插板上的 LED L02 将更改为闪烁白色，以指示设备已开机且 Audio SoM 正在进行身份验证。

1. 等待身份验证过程完成，最多可能需要 3 分钟。

1. 当你看到以下情况之一时，就可以开始创建原型了：

    - LED L02 将更改为稳定白色。 这表示身份验证已完成，且 devkit 尚未配置关键字。
    - 所有三个 LED 均变为蓝色。 这表示身份验证已完成，且 devkit 已配置关键字。

## <a name="next-steps"></a>后续步骤

创建[无代码语音解决方案](./tutorial-no-code-speech.md)。
