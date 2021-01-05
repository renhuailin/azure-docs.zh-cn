---
title: 增强端口和 VLAN 名称解析
description: 在传感器上自定义端口和 VLAN 名称，以丰富设备的分辨率。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 1747af77468ebe41f59892dea4bb49ce24757f37
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838267"
---
# <a name="enhance-port-and-vlan-name-resolution"></a>增强端口和 VLAN 名称解析

可以在传感器上自定义端口和 VLAN 名称，以丰富设备的分辨率。

## <a name="customize-port-names"></a>自定义端口名称

Azure Defender for IoT 会自动向最通用的保留端口（例如 DHCP 或 HTTP）分配名称。 可以为用于 IoT 检测的其他端口自定义端口名称。 例如，为非保留端口分配一个名称，因为该端口显示异常高的活动。

这些名称将在以下情况中显示：

  - 从设备映射中选择 **设备组** 。

  - 创建提供端口信息的小组件。

### <a name="view-custom-port-names-in-the-device-map"></a>在设备映射中查看自定义端口名称

包含用户定义的名称的端口显示在 " **已知应用程序** " 组中的 "设备映射" 中。

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="显示已知应用程序组的设备映射屏幕截图。":::

### <a name="view-custom-port-names-in-widgets"></a>查看小组件中的自定义端口名称

你定义的端口名称将显示在涵盖按端口显示流量的小组件中。

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="覆盖流量。":::

定义自定义端口名称：

1. 选择 " **系统设置** "，然后选择 " **标准别名**"。

2. 选择 " **添加端口别名**"。

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="添加端口别名。":::

3. 输入端口号，选择 **TCP/UDP**，或同时选择 **两者**，并添加名称。

4. 选择“保存”。

## <a name="configure-vlan-names"></a>配置 VLAN 名称

您可以通过设备 VLAN 编号和标记扩充设备清单数据。 除了数据扩充，还可以查看每个 VLAN 的设备数，并按 VLAN 小组件查看带宽。

Vlan 支持基于 802.1 q (最多 VLAN ID 4094) 。

可使用两种方法来检索 VLAN 信息：

- **自动发现**：默认情况下，传感器会自动发现 vlan。 检测到带有流量的 Vlan 显示在 VLAN 配置屏幕、数据挖掘报表和包含 VLAN 信息的其他报表中。 未使用的 Vlan 不会显示。 你无法编辑或删除这些 Vlan。 

  应为每个 VLAN 添加一个唯一名称。 如果未添加名称，VLAN 编号将显示在报告 VLAN 的所有位置。

- **手动添加**：可以手动添加 vlan。 必须为手动添加的每个 VLAN 添加唯一名称，并且可以编辑或删除这些 Vlan。

VLAN 名称最多可包含 50 ASCII 字符。

> [!NOTE]
> VLAN 名称在传感器和管理控制台之间不同步。 还需要在管理控制台上定义名称。  
对于 Cisco 交换机，请将以下行添加到 span 配置： `monitor session 1 destination interface XX/XX encapsulation dot1q` 。 在该命令中， *xx/xx* 是端口的名称和编号。

配置 Vlan：

1. 在侧边菜单中，选择 " **系统设置**"。

2. 在 " **系统设置** " 窗口中，选择 " **VLAN**"。

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="使用系统设置编辑 Vlan。":::

3. 在每个 VLAN ID 旁边添加一个唯一名称。

## <a name="next-steps"></a>后续步骤

在报告中查看设备清单和数据挖掘的已扩充设备信息：

- [调查设备清单中的传感器检测](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [生成报告](how-to-generate-reports.md)
