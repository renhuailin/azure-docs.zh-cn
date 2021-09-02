---
title: 查看 Azure Percept DK 的模型推理遥测
description: 了解如何在 Azure IoT 资源管理器中查看 Azure Percept DK 的视觉模型推理遥测
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: d2ca288240d9f361f305c026e611140cba941e47
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224082"
---
# <a name="view-your-azure-percept-dks-model-inference-telemetry"></a>查看 Azure Percept DK 的模型推理遥测

按照以下指南，在 [Azure IoT 资源管理器](https://github.com/Azure/azure-iot-explorer/releases)中查看 Azure Percept DK 的视觉模型推理遥测。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK (devkit)
- [Azure 订阅](https://azure.microsoft.com/free/)
- [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)：你已将 devkit 连接到 Wi-Fi 网络，创建了 IoT 中心，并已将 devkit 连接到 IoT 中心
- [已将视觉 AI 模型部署到 Azure Percept DK](./how-to-deploy-model.md)

## <a name="view-telemetry"></a>查看遥测数据

1. 打开开发工具包。

1. 下载并安装 [Azure IoT 资源管理器](https://github.com/Azure/azure-iot-explorer/releases)。 如果你是 Windows 用户，请选择 .msi 文件。

    :::image type="content" source="./media/how-to-view-telemetry/azure-iot-explorer-download.png" alt-text="Azure IoT 资源管理器的下载屏幕。":::

1. 将 IoT 中心连接到 Azure IoT 资源管理器：

    1. 转到 [Azure 门户](https://portal.azure.com)。

    1. 选择“所有资源”，

        :::image type="content" source="./media/how-to-view-telemetry/azure-portal.png" alt-text="Azure 门户主页。":::

    1. 选择 Azure Percept DK 连接到的 IoT 中心。

        :::image type="content" source="./media/how-to-view-telemetry/iot-hub.png" alt-text="Azure 门户中的 IoT 中心列表。":::

    1. 在 IoT 中心页的左侧，选择“共享访问策略”。

        :::image type="content" source="./media/how-to-view-telemetry/shared-access-policies.png" alt-text="显示共享访问策略的 IoT 中心页。":::

    1. 单击“iothubowner”。

        :::image type="content" source="./media/how-to-view-telemetry/iothubowner.png" alt-text="突出显示 iothubowner 的共享访问策略屏幕。":::

    1. 单击“连接字符串 - 主键”旁边的蓝色复制图标。

        :::image type="content" source="./media/how-to-view-telemetry/connection-string.png" alt-text="突出显示连接字符串复制按钮的 iothubowner 窗口。":::

    1. 打开 Azure IoT 资源管理器，然后单击“+添加连接”。

    1. 将连接字符串粘贴到“添加连接字符串”窗口的“连接字符串”框中，然后单击“保存”  。

        :::image type="content" source="./media/how-to-view-telemetry/add-connection-string.png" alt-text="Azure IoT 资源管理器窗口，其中包含用于将连接字符串粘贴到的框。":::

    1. 将 Vision SoM 指向模型推理的对象。

    1. 选择“遥测”。

    1. 单击“启动”，查看来自设备的遥测事件。

## <a name="next-steps"></a>后续步骤
了解如何查看 [Azure Percept DK 视频流](./how-to-view-video-stream.md)。