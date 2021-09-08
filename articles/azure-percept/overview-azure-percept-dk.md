---
title: Azure Percept DK 和视觉设备概述
description: 详细了解 Azure Percept DK 和 Azure Percept Vision
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: a51f05fe29a4a0d3dda9057ca0eb7c4d3de494fb
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221992"
---
# <a name="azure-percept-dk-and-vision-device-overview"></a>Azure Percept DK 和视觉设备概述

Azure Percept DK 是一种边缘 AI 开发工具包，旨在通过 [Azure Percept Studio](./overview-azure-percept-studio.md) 开发视觉和音频 AI 解决方案。 Azure Percept DK 可在 [Microsoft 在线商店](https://go.microsoft.com/fwlink/p/?LinkId=2155270)购买。

> [!div class="nextstepaction"]
> [立即购买](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="key-features"></a>主要功能

- 运行边缘上的 AI。 通过内置的硬件加速，开发工具包可以在没有连接到云的情况下运行 AI 模型。

- 内置硬件信任根安全性。 详细了解 [Azure Percept 安全性](./overview-percept-security.md)。

- 与 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 和其他 Azure 服务（例如，Azure IoT 中心、Azure 认知服务和[实时视频分析](../media-services/live-video-analytics-edge/overview.md)）无缝集成。

- 与 [Azure Percept Audio](./overview-azure-percept-audio.md)（用于构建 AI 音频解决方案的可选配件）兼容。

- 支持第三方 AI 工具（例如 ONNX 和 TensorFlow）。

- 与 80/20 导轨系统集成，这样就可以进行无限的设备装载配置。 详细了解 [80/20 集成](./overview-8020-integration.md)。

## <a name="hardware-components"></a>硬件组件

- Azure Percept DK 承载板：
    - NXP iMX8m 处理器
    - 受信任的平台模块 (TPM) 版本 2.0
    - Wi-Fi 和蓝牙连接
    - 有关详细信息，请参阅 [Azure Percept DK 数据表](./azure-percept-dk-datasheet.md)

- Azure Percept Vision 模块系统 (SoM)：
    - Intel Movidius Myriad X (MA2085) 视觉处理单元 (VPU)
    - RGB 相机传感器
    - 有关详细信息，请参阅 [Azure Percept Vision 数据表](./azure-percept-vision-datasheet.md)

## <a name="getting-started-with-azure-percept-dk"></a>Azure Percept DK 入门

- 安装开发工具包：
    - [对 Azure Percept DK 进行拆箱和组装](./quickstart-percept-dk-unboxing.md)
    - [完成 Azure Percept DK 安装体验](./quickstart-percept-dk-set-up.md)

- 开始构建视觉解决方案和音频解决方案：
    - [在 Azure Percept Studio 中创建无代码视觉解决方案](./tutorial-nocode-vision.md)
    - [在 Azure Percept Studio 中创建无代码语音解决方案](./tutorial-no-code-speech.md)（需要 Azure Percept Audio 配件）

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从 Microsoft 在线商店购买 Azure Percept DK](https://go.microsoft.com/fwlink/p/?LinkId=2155270)