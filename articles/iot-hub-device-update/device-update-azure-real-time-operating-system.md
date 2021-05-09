---
title: Azure 实时操作系统的设备更新 | Microsoft Docs
description: Azure 实时操作系统的设备更新入门
author: ValOlson
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: b6fcbf7d31e76965cfd8b2944b426dce94b8aa75
ms.sourcegitcommit: f6b76df4c22f1c605682418f3f2385131512508d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108324502"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-azure-real-time-operating-system-rtos"></a>有关使用 Azure 实时操作系统 (RTOS) 的 Azure IoT 中心的设备更新教程

本教程逐步介绍如何在 Azure RTOS NetX Duo 中创建 IoT 中心代理的设备更新。 此外，它还提供了简单的 API 让开发人员在其应用程序中集成设备更新功能。 学习主要半导体评估板的[示例](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU)，其中包括有关如何配置、生成更新并以无线 (OTA) 方式将其部署到设备的入门指南。

在本教程中，将了解如何：
> [!div class="checklist"]
> * 入门
> * 标记设备
> * 创建设备组
> * 部署映像更新
> * 监视更新部署

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
* 对 IoT 中心的访问权限。 建议使用 S1（标准）层或更高层级。
* 链接到 IoT 中心的设备更新实例和帐户。 按照指南[创建并链接](/azure/iot-hub-device-update/create-device-update-account)一个设备更新帐户（如果尚未这样做）。

## <a name="get-started"></a>入门

每个特定于评估板的示例 Azure RTOS 项目都包含了有关如何在该评估板上使用 IoT 中心设备更新的代码和文档。 
1. 从 [Azure RTOS 和设备更新示例](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU)下载特定于评估板的示例文件。
2. 在下载的示例中找到 docs 文件夹。
3. 在 docs 文件夹中，按照有关如何准备 Azure 资源、帐户并向其注册 IoT 设备的步骤操作。
5. 接下来，按照 docs 中的步骤为评估板生成新的固件映像并导入清单。
6. 接下来，将固件映像和清单发布到 IoT 中心设备更新。
7. 最后，在设备上下载并运行项目。

详细了解 [Azure RTOS](/azure/rtos/)。  

## <a name="tag-your-device"></a>标记设备

1. 完成上一步骤后，让设备应用程序保持运行。
2. 登录到 [Azure 门户](https://portal.azure.com)并导航到 IoT 中心。
3. 在左侧导航窗格上的“IoT 设备”中，找到你的 IoT 设备并导航到“设备孪生”。
4. 在“设备孪生”中，通过将任何现有的 Device Update 标记值设置为 null 来将其删除。
5. 按如下所示添加新的 Device Update 标记值。

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="create-update-group"></a>创建更新组

1. 转到你之前连接到 Device Update 实例的 IoT 中心。
2. 从左侧导航栏中选择“自动设备管理”下的“设备更新”选项。
3. 选择页面顶部的“组”选项卡。 
4. 选择“添加”按钮以创建新组。
5. 从列表中选择在上一步中创建的 IoT 中心标记。 选择“创建更新组”。

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="显示标记选择的屏幕截图。" lightbox="media/create-update-group/select-tag.PNG":::

[详细了解](create-update-group.md)如何添加标记和创建更新组

## <a name="deploy-new-firmware"></a>部署新固件

1. 创建组后，你应该会看到新的更新可用于你的设备组，并在“挂起的更新”下显示该更新的链接。 可能需要刷新一次。 
2. 单击可用更新。
3. 确认选择了正确的组作为目标组。 计划你的部署，然后选择“部署更新”。

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="选择更新" lightbox="media/deploy-update/select-update.png":::

4. 查看符合性图表。 此时会看到更新正在进行。 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="正在进行更新" lightbox="media/deploy-update/update-in-progress.png":::

5. 成功更新设备后，应会看到符合性图表和部署详细信息更新，以反映相同的情况。 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="更新成功" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>监视更新部署

1. 选择页面顶部的“部署”选项卡。

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="“部署”选项卡" lightbox="media/deploy-update/deployments-tab.png":::

2. 选择你创建的部署以查看部署详细信息。

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="部署详细信息" lightbox="media/deploy-update/deployment-details.png":::

3. 选择“刷新”以查看最新状态详细信息。 继续此过程，直到状态更改为“成功”。

现在，你已经在 Raspberry Pi 3 B+ 设备上使用 Device Update for IoT Hub 成功完成了端到端映像更新。 

## <a name="cleanup-resources"></a>清理资源

如果不再需要设备更新帐户、实例、IoT 中心和 IoT 设备，请进行清理。 

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure RTOS 及其如何与 Azure IoT 配合工作，请查看 https://azure.com/rtos 。