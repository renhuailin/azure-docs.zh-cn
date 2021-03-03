---
title: 使用 Azure IoT 中心的设备更新部署更新 |Microsoft Docs
description: 使用 Azure IoT 中心的设备更新部署更新。
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 0a11c8e8946229941c1fe60f7f2ce84d9fadb2ed
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679102"
---
# <a name="deploy-an-update-using-device-update-for-iot-hub"></a>使用 IoT 中心的设备更新部署更新

了解如何使用 IoT 中心的设备更新将更新部署到 IoT 设备。

## <a name="prerequisites"></a>先决条件

* [使用启用 Iot 中心的设备更新访问 Iot 中心](create-device-update-account.md)。 建议为 IoT 中心使用 S1 (标准) 层或更高版本。 
* [至少已成功为预配的设备导入一个更新。](import-update.md) 
* 为 IoT 中心内的设备更新设置的 IoT 设备 (或模拟器) 。
* [已为尝试更新的 IoT 设备分配了标记。设备至少是一个更新组的一部分。](create-update-group.md)
* 支持的浏览器：
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="deploy-an-update"></a>部署更新

1. 转到 [Azure 门户](https://portal.azure.com)

2. 导航到 IoT 中心的 "设备更新" 边栏选项卡。

  :::image type="content" source="media/deploy-update/device-update-iot-hub.png" alt-text="IoT 中心" lightbox="media/deploy-update/device-update-iot-hub.png":::

3. 选择页面顶部的 "组" 选项卡。 [了解](device-update-groups.md) 有关设备组的详细信息。 

  :::image type="content" source="media/deploy-update/updated-view.png" alt-text="“组”选项卡" lightbox="media/deploy-update/updated-view.png":::

4. 查看更新符合性图表和组列表。 你应该会看到新的更新可用于你的设备组，并在 "挂起的更新" 下显示该更新的链接 (你可能需要在) 时刷新一次。 [了解有关更新符合性的详细信息。](device-update-compliance.md) 

5. 选择可用的更新。

6. 确认选择了正确的组作为目标组。 计划你的部署，然后选择 "部署更新"。

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="选择更新" lightbox="media/deploy-update/select-update.png":::

7. 查看符合性图表。 此时会看到更新正在进行。 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="正在进行更新" lightbox="media/deploy-update/update-in-progress.png":::

8. 成功更新设备后，应会看到符合性图表和部署详细信息更新，以反映相同的情况。 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="更新成功" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>监视更新部署

1. 选择页面顶部的 "部署" 选项卡。

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="部署选项卡" lightbox="media/deploy-update/deployments-tab.png":::

2. 选择您创建的部署以查看部署详细信息。

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="部署详细信息" lightbox="media/deploy-update/deployment-details.png":::

3. 选择 "刷新" 以查看最新状态详细信息。 继续此过程，直到状态更改为 "成功"。


## <a name="retry-an-update-deployment"></a>重试更新部署

如果部署由于某些原因而失败，则可以对失败的设备重试部署。 

1. 请在 "部署" 选项卡上选择失败的部署。 

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="部署详细信息" lightbox="media/deploy-update/deployment-details.png":::

2. 单击 "详细部署信息" 窗格中的 "失败" 设备状态。

3. 单击 "重试失败的设备" 并确认确认通知。 

## <a name="next-steps"></a>后续步骤

[排查常见问题](troubleshoot-device-update.md)
