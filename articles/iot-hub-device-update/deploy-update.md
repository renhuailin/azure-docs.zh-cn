---
title: 使用 Device Update for Azure IoT Hub 部署更新 | Microsoft Docs
description: 使用 Device Update for Azure IoT Hub 部署更新。
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 0a11c8e8946229941c1fe60f7f2ce84d9fadb2ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "101679102"
---
# <a name="deploy-an-update-using-device-update-for-iot-hub"></a>使用 Device Update for IoT Hub 部署更新

了解如何使用 Device Update for IoT Hub 将更新部署到 IoT 设备。

## <a name="prerequisites"></a>先决条件

* [能够访问启用了 Device Update for IoT Hub 的 IoT 中心](create-device-update-account.md)。 建议为 IoT 中心使用 S1（标准）层或更高层级。 
* [已为预配的设备成功导入了至少一个更新](import-update.md)。 
* 为 IoT 中心内的 Device Update 预配了 IoT 设备（或模拟器）。
* [已将标签分配给你尝试更新的 IoT 设备。该设备至少属于一个更新组。](create-update-group.md)
* 支持的浏览器：
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="deploy-an-update"></a>部署更新

1. 转到 [Azure 门户](https://portal.azure.com)

2. 导航到 IoT 中心的“Device Update”边栏选项卡。

  :::image type="content" source="media/deploy-update/device-update-iot-hub.png" alt-text="IoT 中心" lightbox="media/deploy-update/device-update-iot-hub.png":::

3. 选择页面顶部的“组”选项卡。 [详细了解](device-update-groups.md)设备组。 

  :::image type="content" source="media/deploy-update/updated-view.png" alt-text="“组”选项卡" lightbox="media/deploy-update/updated-view.png":::

4. 查看更新合规性图表和组列表。 你应该会看到新的更新可用于你的设备组，且“挂起的更新”下显示了该更新的链接。 [详细了解更新合规性。](device-update-compliance.md) 

5. 选择可用更新。

6. 确认选择了正确的组作为目标组。 计划你的部署，然后选择“部署更新”。

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="选择更新" lightbox="media/deploy-update/select-update.png":::

7. 查看符合性图表。 此时会看到更新正在进行。 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="正在进行更新" lightbox="media/deploy-update/update-in-progress.png":::

8. 成功更新设备后，应会看到符合性图表和部署详细信息更新，以反映相同的情况。 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="更新成功" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>监视更新部署

1. 选择页面顶部的“部署”选项卡。

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="“部署”选项卡" lightbox="media/deploy-update/deployments-tab.png":::

2. 选择你创建的部署以查看部署详细信息。

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="部署详细信息" lightbox="media/deploy-update/deployment-details.png":::

3. 选择“刷新”以查看最新状态详细信息。 继续此过程，直到状态更改为“成功”。


## <a name="retry-an-update-deployment"></a>重试更新部署

如果部署由于某些原因而失败，可对失败的设备重试部署。 

1. 转到“部署”选项卡，选择失败的部署。 

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="部署详细信息" lightbox="media/deploy-update/deployment-details.png":::

2. 单击“详细部署信息”窗格中的“失败”设备状态。

3. 单击“重试失败的设备”并对确认通知进行确认。 

## <a name="next-steps"></a>后续步骤

[排查常见问题](troubleshoot-device-update.md)
