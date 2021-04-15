---
title: 设置 Azure Percept Audio
description: 了解如何将 Azure Percept 音频设备连接到 Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: fa3dad8cdd38e6db621d8194cc9472430c7c5008
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605784"
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

1. 使用附带的 Micro USB 到 USB Type-A 电缆将 Azure Percept Audio 设备连接到 Azure Percept DK 载板。 将电缆的 Micro USB 端连接到音频内插板（开发人员），并将 Type-A 端连接到 Percept DK 载板。

1. （可选）通过音频插孔（标记为“输出线”）将扬声器或耳机连接到 Azure Percept Audio 设备。 这样即可听到音频响应。

1. 开启 devkit。 音频内插板上的 LED L02 会变为白光闪烁，指示设备已开机且 Audio SoM 正在进行身份验证。

1. 等待身份验证过程完成，最多可能需要 3 分钟。

1. 当你看到以下情况之一时，就可以开始创建原型了：

    - LED L02 会变为纯白色：指示身份验证已完成，但 devkit 尚未配置关键字。
    - 所有三个 LED 都变为蓝色：指示身份验证已完成，且 devkit 已配置关键字。

## <a name="next-steps"></a>后续步骤

在 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 中创建[无代码语音解决方案](./tutorial-no-code-speech.md)。
