---
title: 设置 Azure Percept Audio 设备
description: 了解如何将 Azure Percept 音频设备连接到 Azure Percept DK
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 8b6ec3ba8473be8e924d3c4b8ae17ccbdcf6568f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221839"
---
# <a name="set-up-the-azure-percept-audio-device"></a>设置 Azure Percept Audio 设备

Azure Percept 音频可直接与 Azure Percept DK 一起使用。 无需进行任何特殊设置。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK (devkit)
- Azure Percept 音频
- [Azure 订阅](https://azure.microsoft.com/free/)
- [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)：你已将 devkit 连接到 Wi-Fi 网络，创建了 IoT 中心，并已将 devkit 连接到 IoT 中心
- 可连接到 3.5 毫米音频插孔的扬声器或耳机（可选）

## <a name="connecting-your-devices"></a>连接设备

1. 使用附带的 Micro USB 到 USB Type-A 电缆将 Azure Percept Audio 设备连接到 Azure Percept DK 载板。 将电缆的 Micro USB 端连接到音频内插板（开发人员），并将 Type-A 端连接到 Percept DK 载板。

1. （可选）通过音频插孔（标记为“输出线”）将扬声器或耳机连接到 Azure Percept Audio 设备。 这样即可听到音频响应。

1. 通过将 devkit 连接到电源适配器，让 devkit 通电。 LED L02 将变为白色闪烁，这表示设备已开机且正在进行身份验证。

1. 等待身份验证过程完成，最多需要 5 分钟。

1. 看到下述 LED 状态之一时，就可开始创建原型：

    - LED L02 将变为白色常亮，这表示身份验证已完成，但 devkit 未配置关键字。
    - 全部三个 LED 都变为蓝色，这表示身份验证已完成且 devkit 已配置关键字。

## <a name="next-steps"></a>后续步骤

在 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 中创建[无代码语音解决方案](./tutorial-no-code-speech.md)。
