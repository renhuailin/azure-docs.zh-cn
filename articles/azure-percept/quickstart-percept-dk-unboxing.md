---
title: 将 Azure Percept DK 组件拆箱并进行组装
description: 了解如何打开、连接并开启 Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: 40751401760d877fe3feab39f3fea1f2fbeee54b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664183"
---
# <a name="quickstart-unbox-and-assemble-your-azure-percept-dk-components"></a>快速入门：将 Azure Percept DK 组件拆箱并进行组装

收到 Azure Percept DK 后，请参考本指南以获取有关连接组件和开启设备的信息。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK (devkit)
- P7 螺丝刀（可选，用于将电源线连接器固定到载板）

## <a name="unbox-and-assemble-your-device"></a>将设备拆箱并进行组装

1. 将 Azure Percept DK 组件拆箱。

    devkit 包含载板、Azure Percept 视觉 SoM、包含天线和电缆的附件盒，以及带有十六进制密钥的欢迎卡。

1. 连接 devkit 组件。

    > [!NOTE]
    > 电源适配器端口位于载板的右侧。 其余端口（2x USB-A、1x USB-C、1x HDMI 和 1x Ethernet）和重置按钮位于载板的左侧。

    1. 手动将两个 Wi-Fi 天线固定到载板上。

    1. 使用 USB-C 电缆将视觉 SoM 连接到载板的 USB-C 端口。

    1. 将电源线连接到电源适配器。

    1. 从设备上取下所有剩余的塑料包装。

    1. 将电源适配器/电缆连接到载板和墙壁插座。 若要将电源线连接器完全固定到载板，请使用 P7 螺丝刀（不包括在 devkit 中）拧紧连接器螺钉。

    1. 将电源线插入墙壁插座后，设备将自动接通电源。 载板左侧的重置按钮将亮起。 请给设备一些启动时间。

        > [!NOTE]
        > 重置按钮用于关闭或重置连接到电源插座的设备。 停电时，设备将自动重置并重新启动。

## <a name="next-steps"></a>后续步骤

现在，你的 devkit 已连接并接通电源，请参阅 Azure Percept DK 安装体验演练以完成设备设置。 通过设置体验，你可以将 devkit 连接到 Wi-Fi 网络，设置 SSH 登录名，创建 IoT 中心，并将 devkit 预配到 Azure 帐户。 完成设备设置后，即可开始创建原型。