---
title: Azure Percept 音频入门
description: 了解如何将 Azure Percept 音频设备连接到 Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 575107859f56df742ab41a299269c250511022b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664184"
---
# <a name="azure-percept-audio-setup"></a>Azure Percept 音频设置

Azure Percept 音频可直接与 Azure Percept DK 一起使用。 无需进行任何特殊设置。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK (devkit)
- Azure Percept 音频
- [Azure 订阅](https://azure.microsoft.com/free/)
- [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)：你已将 devkit 连接到 Wi-Fi 网络，创建了 IoT 中心，并已将 devkit 连接到 IoT 中心

## <a name="connecting-your-devices"></a>连接设备

1. 使用附带的 USB Micro Type-B 到 USB Type-A 电缆将 Azure Percept 音频设备连接到 Azure Percept DK 载板。 将电缆的 Micro Type-B 端连接到音频 SoM，并将 Type-A 端连接到 Percept DK 载板。

1. 开启 devkit。

    - 音频 SoM 上的 LED L01 将变为稳定的绿色，表示设备已通电。
    - LED L02 将变为绿色闪烁，表示音频 SoM 正在进行身份验证。

1. 等待身份验证过程完成，最多可能需要 3 分钟。

1. 当你看到以下情况之一时，就可以开始创建原型了：

    - LED L01 熄灭，L02 变为白色。 这表示身份验证已完成，且 devkit 尚未配置关键字。
    - 所有三个 LED 均变为蓝色。 这表示身份验证已完成，且 devkit 已配置关键字。

    > [!NOTE]
    > 如果 devkit 未进行身份验证，请联系支持人员。

## <a name="next-steps"></a>后续步骤

创建[无代码语音解决方案](./tutorial-no-code-speech.md)。