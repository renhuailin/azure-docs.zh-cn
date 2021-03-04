---
title: 在 Azure Percept Studio 中捕获无代码远景解决方案的映像
description: 了解如何在 Azure Percept Studio 中使用 Azure Percept 深色捕获图像，以获取无代码远景解决方案
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 44bf498af52f4d8a0d880dc1f1d5874d5b444cae
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035528"
---
# <a name="capture-images-for-a-vision-project-in-azure-percept-studio"></a>在 Azure Percept Studio 中捕获视觉项目的映像

遵循本指南，使用 azure Percept Studio 中现有远景项目的 Azure Percept 深色的视觉 SoM 捕获映像。 如果尚未创建远景项目，请参阅 [无代码构想教程](./tutorial-nocode-vision.md)。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK (devkit)
- [Azure 订阅](https://azure.microsoft.com/free/)
- [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)：你已将 devkit 连接到 Wi-Fi 网络，创建了 IoT 中心，并已将 devkit 连接到 IoT 中心
- [无代码构想项目](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>捕获映像

1. 打开开发工具包。

1. 转到 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)。

1. 在 "概述" 页的左侧，单击 " **设备**"。

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Azure Percept Studio 概述屏幕。" lightbox="./media/how-to-capture-images/overview-devices.png":::

1. 从列表中选择你的 devkit。

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Percept 设备列表。":::

1. 在 "设备" 页上，单击 " **捕获项目的映像**"。

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="列出可用操作的 Percept 设备页面。":::

1. 在 **映像捕获** 窗口中，执行以下操作：

    1. 在 " **项目** " 下拉菜单中，选择要为其收集映像的远景项目。

    1. 单击 " **查看设备流** " 以确保正确放置了远景 SoM 的相机。

    1. 单击 " **拍摄照片** " 捕获映像。

    1. 或者，选中 " **自动映像捕获** " 旁边的框，为映像捕获设置计时器：

        1. 选择首选的图像处理速率（ **捕获速率**）。
        1. 选择要在 " **目标**" 下收集的映像总数。

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="图像捕获屏幕。":::

所有映像都可在 [自定义视觉](https://www.customvision.ai/)中访问。

## <a name="next-steps"></a>后续步骤

[测试并重新训练您的无代码远景模型](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/test-your-model)。