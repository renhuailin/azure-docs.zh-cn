---
title: 对 Azure Percept DK 设备进行拆箱和组装
description: 了解如何打开、连接并开启 Azure Percept DK
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: ce47b2463441fdd44c9db72c2dcbf2f8935b15d6
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222983"
---
# <a name="unbox-and-assemble-the-azure-percept-dk-device"></a>对 Azure Percept DK 设备进行拆箱和组装

收到 Azure Percept DK 后，请参考本指南以获取有关连接组件和开启设备的信息。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK (devkit)
- P7 螺丝刀（可选，用于将电源线连接器固定到载板）

## <a name="unbox-and-assemble-your-device"></a>将设备拆箱并进行组装

1. 将 Azure Percept DK 组件拆箱。

    devkit 包含载板、Azure Percept 视觉 SoM、包含天线和电缆的附件盒，以及带有十六进制密钥的欢迎卡。

1. 连接 devkit 组件。

    > [!NOTE]
    > 电源适配器端口位于载板的右侧。 其余端口（2 个 USB-A、1 个 USB-C、1 个 HDMI 和 1 个以太网）和复位按钮位于载板左侧。

    1. 手动将两个 Wi-Fi 天线固定到载板上。

    1. 使用 USB-C 电缆将视觉 SoM 连接到载板的 USB-C 端口。

    1. 将电源线连接到电源适配器。

    1. 从设备上取下所有剩余的塑料包装。

    1. 将电源适配器/电缆连接到载板和墙壁插座。 若要将电源线连接器完全固定到载板，请使用 P7 螺丝刀（不包括在 devkit 中）拧紧连接器螺钉。

    1. 将电源线插入墙壁插座后，设备将自动接通电源。 载板左侧的电源按钮将亮起。 请给设备一些启动时间。

        > [!NOTE]
        > 电源按钮用于关闭或重启连接到电源插座的设备。 如果停电，设备将自动重新启动。

有关 devkit 程序集的直观演示，请参阅以下视频的 0:00 到 0:50：

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>后续步骤

现在，你的 devkit 已连接并接通电源，请参阅 [Azure Percept DK 安装体验演练](./quickstart-percept-dk-set-up.md)以完成设备设置。 通过设置体验，你可以将 devkit 连接到 Wi-Fi 网络，设置 SSH 登录名，创建 IoT 中心，并将 devkit 预配到 Azure 帐户。 完成设备设置后，即可开始创建原型。