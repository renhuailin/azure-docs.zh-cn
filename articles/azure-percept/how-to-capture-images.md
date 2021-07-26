---
title: 在 Azure Percept Studio 中捕获无代码视觉解决方案的图像
description: 如何在 Azure Percept Studio 中使用 Azure Percept DK 捕获图像
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 2c7116d7e62afe6a59ad48bbb28dabf3c05bc311
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108754390"
---
# <a name="capture-images-for-a-vision-project-in-azure-percept-studio"></a>在 Azure Percept Studio 中捕获视觉项目的图像

按照本指南的说明，使用 Azure Percept DK 捕获现有视觉项目的图像。 如果你还没有创建视觉项目，请参阅[无代码视觉教程](./tutorial-nocode-vision.md)。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK (devkit)
- [Azure 订阅](https://azure.microsoft.com/free/)
- [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)：你已将 devkit 连接到 Wi-Fi 网络，创建了 IoT 中心，并已将 devkit 连接到 IoT 中心
- [无代码视觉项目](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>捕获图像

1. 打开开发工具包。

1. 转到 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)。

1. 在概述页的左侧，选择“设备”。

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Azure Percept Studio 概述屏幕。" lightbox="./media/how-to-capture-images/overview-devices.png":::

1. 从列表中选择你的开发工具包。

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Percept 设备列表。":::

1. 在“设备”页上，选择“捕获项目的图像”。

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="列出可用操作的 Percept 设备页面。":::

1. 在“图像捕获”窗口中，按以下步骤操作：

    1. 在“项目”下拉菜单中，选择要为其收集图像的视觉项目。

    1. 选择“查看设备流”以确保正确放置了视觉 SoM 的相机。

    1. 选择“拍照”以捕获图像。

    1. 或者，选中“自动图像捕获”旁边的框，为图像捕获设置计时器：

        1. 在“捕获速率”下选择首选图像处理速率。
        1. 在“目标”下选择要收集的图像总数。

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="图像捕获屏幕。":::

所有图像都可在[自定义视觉](https://www.customvision.ai/)中访问。

## <a name="next-steps"></a>后续步骤

[测试和重新训练无代码视觉模型](../cognitive-services/custom-vision-service/test-your-model.md)。