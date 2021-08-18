---
title: 了解特定区域的设备
description: 使用本地管理控制台获取每个特定区域的综合视图信息
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 685d7b1df4389c356ee7b531d179919025d298d0
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015460"
---
# <a name="view-information-per-zone"></a>查看每个区域的信息


## <a name="view-a-device-map-for-a-zone"></a>查看区域的设备映射

查看传感器上所选区域的设备映射。 此视图显示与所选区域相关的所有网络元素，包括传感器、连接到它们的设备以及其他信息。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="区域映射的屏幕截图。":::


- 在“站点管理”窗口中，从包含区域名称的栏中选择“查看区域映射”。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="默认业务单元的默认区域。":::

此时将显示“设备映射”窗口。 以下工具可用于查看映射中的设备和设备信息。 有关上述每项功能的详细信息，请参阅《*Defender for IoT 平台用户指南*》。

- **映射缩放视图**：简化的视图、连接视图和详细信息视图。 显示的映射视图因映射的缩放级别而异。 可以通过调整缩放级别在映射视图之间切换。

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **映射搜索和布局工具**：用于显示各种网段、设备、设备组或层的工具。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="搜索和布局工具视图的屏幕截图。":::

- **设备上的标签和指示器：** 例如，在 IT 网络的子网中分组的设备数。 在此示例中，它是 8。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="标签和指示器的屏幕截图。":::

- **查看设备属性**：例如，监视设备和基本设备属性的传感器。 右键单击该设备可查看设备属性。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="设备属性视图的屏幕截图。":::

- **与设备关联的警报：** 右键单击该设备可查看相关警报。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="显示警报视图的屏幕截图。":::

## <a name="view-alerts-associated-with-a-zone"></a>查看与区域关联的警报

查看与特定区域关联的警报：

- 从“区域”窗口中选择警报图标。 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="含示例的默认业务单元视图。":::

有关详细信息，请参阅[概述：使用警报](how-to-work-with-alerts-on-premises-management-console.md)。

### <a name="view-the-device-inventory-of-a-zone"></a>查看区域的设备库存

查看与特定区域关联的设备库存：

- 从“区域”窗口中选择“查看设备库存”。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="将显示设备库存屏幕。":::

有关详细信息，请参阅[调查设备库存中的所有企业传感器检测](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)。

## <a name="view-additional-zone-information"></a>查看其他区域信息

以下附加区域信息可用：

- **区域详细信息**：查看与该区域关联的设备、警报和传感器的数量。

- **传感器详细信息**：查看分配给该区域的每个传感器的名称、IP 地址和版本。

- **连接状态**：如果传感器已断开连接，则从传感器连接。 请参阅[将传感器连接到本地管理控制台](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)。 

- **更新进度**：如果正在升级连接的传感器，将出现升级状态。 在升级过程中，本地管理控制台不会从传感器接收设备信息。

## <a name="next-steps"></a>后续步骤

[深入了解全球、区域和本地威胁](how-to-gain-insight-into-global-regional-and-local-threats.md)
