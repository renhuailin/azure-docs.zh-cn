---
title: Azure Percept DK 概述
description: 了解有关 Azure Percept 深色的详细信息
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 4fd0a7cb575a109d1393527b48de3fa4e3446167
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662156"
---
# <a name="azure-percept-dk-overview"></a>Azure Percept DK 概述

Azure Percept 深色是一种面向开发视觉 AI 概念证明的边缘 AI 开发工具包。 与 [Azure Percept Studio](./overview-azure-percept-studio.md)结合使用时，它成为一个功能强大且易于使用的平台，可用于构建面向各种视觉 ai 应用程序的边缘 ai 解决方案。 它可在 [Microsoft 在线商店](https://go.microsoft.com/fwlink/p/?LinkId=2155270)购买。

:::image type="content" source="./media/overview-azure-percept-dk/dk-image.png" alt-text="影像.":::

## <a name="key-features"></a>关键功能

- 能够 **在边缘运行 AI**。 使用内置的硬件加速，无需连接到云即可运行视觉 AI 模型。
- **内置信任安全的硬件根**。 有关更多详细信息，请参阅 [Azure Percept 安全性](./overview-percept-security.md) 概述。
- **与 [Azure Percept Studio](./overview-azure-percept-studio.md)** 和其他 azure 服务无缝集成。 例如，Azure IoT 中心、Azure 认知服务和 [实时视频分析](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview)
- **支持顶级 AI 平台**。 例如 "ONNX" 和 "TensorFlow"。
- **与 80/20 railing 系统集成**。 更轻松地在生产环境中生成原型。 了解有关 [8/20 集成](./overview-8020-integration.md)的详细信息。

## <a name="hardware-components"></a>硬件组件

- Azure Percept 深色运营商板
    - NXP iMX8m 处理器
    - 受信任的平台模块 (TPM) 版本2。0
    - WiFi 和蓝牙连接
    - 查看完整的 [数据表](./azure-percept-dk-datasheet.md)
- 模块上的 Azure Percept 远景系统 (SoM) 
    - Intel Movidius 无数 X (MA2085)  (VPU) 
    - 能够添加另一个的 RGB 相机传感器
    - 查看完整的 [数据表](./azure-percept-vision-datasheet.md)

## <a name="get-started-with-the-azure-percept-dk"></a>Azure Percept 深色入门

- 完成这些快速入门
    - [取消装箱并组装 Azure Percept 深色](./quickstart-percept-dk-unboxing.md)
    - [设置 Azure Percept 深色并运行第一个视觉 AI 模型](./quickstart-percept-dk-set-up.md)
- 通过这些教程开始构建概念证明
    - [在 Azure Percept Studio 中创建无代码视觉解决方案](./tutorial-nocode-vision.md)
    - [在 Azure Percept Studio 中创建语音助手](./tutorial-no-code-speech.md)

## <a name="next-steps"></a>后续步骤

订购 [Microsoft 在线商店](https://go.microsoft.com/fwlink/p/?LinkId=2155270)的 AZURE Percept 深色。