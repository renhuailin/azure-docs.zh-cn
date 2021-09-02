---
title: 将视觉 AI 模型部署到 Azure Percept DK
description: 了解如何将视觉 AI 模型从 Azure Percept Studio 部署到 Azure Percept DK
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 56e41e180600b9a50a79ac2c2ae49a9678642e25
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221205"
---
# <a name="deploy-a-vision-ai-model-to-azure-percept-dk"></a>将视觉 AI 模型部署到 Azure Percept DK

按照本指南将视觉 AI 模型从 Azure Percept Studio 内部署到 Azure Percept DK。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK (devkit)
- [Azure 订阅](https://azure.microsoft.com/free/)
- [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)：你已将 devkit 连接到 Wi-Fi 网络，创建了 IoT 中心，并已将 devkit 连接到 IoT 中心

## <a name="model-deployment"></a>模型部署

1. 打开开发工具包。

1. 转到 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)。

1. 在“概述”页的左侧，单击“设备”。

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Azure Percept Studio 概述屏幕。" lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. 从列表中选择 devkit。

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Percept 设备列表。":::

1. 如果要部署一个预先训练的示例视觉模型，请在下一页上单击“部署示例模型”。 如果要部署现有的[自定义无代码视觉解决方案](./tutorial-nocode-vision.md)，请单击“部署自定义视觉项目”。

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="部署的模型选项。":::

1. 如果你选择部署无代码视觉解决方案，请选择你的项目和首选模型迭代，然后单击“部署”。

1. 如果选择部署示例模型，请选择模型，然后单击“部署到设备”。

1. 模型部署成功后，屏幕右上角会显示一条状态消息。 要查看实际作用的模型推理，请单击状态消息中的“查看流”链接，查看来自开发工具包的 Vision SoM 的 RTSP 视频流。

## <a name="next-steps"></a>后续步骤

了解如何查看 [Azure Percept DK 遥测](how-to-view-telemetry.md)。