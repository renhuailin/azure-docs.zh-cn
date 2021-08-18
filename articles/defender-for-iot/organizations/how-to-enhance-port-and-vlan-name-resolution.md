---
title: 优化端口和 VLAN 名称解析
description: 在传感器上自定义端口和 VLAN 名称，以扩充设备的分辨率。
ms.date: 12/13/2020
ms.topic: how-to
ms.openlocfilehash: de6fbe70d5a5359ad4e4c276642b9b9ed0cef00f
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015806"
---
# <a name="enhance-port-vlan-and-os-resolution"></a>优化端口、VLAN 和操作系统的分辨率

可以在传感器上自定义端口和 VLAN 名称，从而扩充设备的分辨率。

## <a name="customize-port-names"></a>自定义端口名称

Azure Defender for IoT 会自动向最通用的保留端口（例如 DHCP 或 HTTP）分配名称。 可以为 Defender for IoT 检测到的其他端口自定义端口名称。 例如，为非保留端口分配一个名称，因为该端口显示活动异常多。

这些名称将在以下情况中显示：

  - 从设备映射中选择“设备组”。

  - 创建提供端口信息的小组件。

### <a name="view-custom-port-names-in-the-device-map"></a>在设备映射中查看自定义端口名称

包含用户定义的名称的端口显示在“已知应用程序”组的设备映射中。

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="显示已知应用程序组的设备映射的屏幕截图。":::

### <a name="view-custom-port-names-in-widgets"></a>查看小组件中的自定义端口名称

你定义的端口名称将显示在涵盖按端口覆盖流量的小组件中。

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="覆盖流量。":::

要定义自定义端口名称：

1. 依次选择“系统设置”和“标准别名”。

2. 选择“添加端口别名”。

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="添加端口别名。":::

3. 输入端口号，选择“TCP/UDP”或选择“两者”并添加名称。

4. 选择“保存”。

## <a name="configure-vlan-names"></a>配置 VLAN 名称

可以通过设备 VLAN 编号和标记扩充设备清单数据。 除了数据扩充，还可以查看每个 VLAN 的设备数，并按 VLAN 小组件查看带宽。

Vlan 支持基于 802.1q（最多为 VLAN ID 4094）。

可使用两种方法来检索 VLAN 信息：

- 自动发现：默认情况下，传感器会自动发现 VLAN。 检测到带有流量的 VLAN 显示在 VLAN 配置屏幕、数据挖掘报表和包含 VLAN 信息的其他报表中。 不会显示未使用的 VLAN。 无法编辑或删除这些 VLAN。 

  应为每个 VLAN 添加唯一名称。 如果未添加名称，VLAN 编号将显示在报告 VLAN 的所有位置中。

- 手动添加：可以手动添加 VLAN。 必须为手动添加的每个 VLAN 添加唯一名称，并且可以编辑或删除这些 VLAN。

VLAN 名称最多可包含 50 个 ASCII 字符。

> [!NOTE]
> VLAN 名称在传感器和管理控制台之间未同步。 还需要在管理控制台上定义名称。  
对于 Cisco 交换机，请将以下行添加到跨度配置：`monitor session 1 destination interface XX/XX encapsulation dot1q`。 在该命令中，XX/XX 是端口的名称和编号。

要配置 VLAN 名称：

1. 在侧边菜单中，选择“系统设置”。

2. 在“系统设置”窗口中，选择“VLAN” 。

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="使用系统设置编辑 VLAN。":::

3. 在每个 VLAN ID 旁边添加唯一名称。

## <a name="improve-device-operating-system-classification-data-enhancement"></a>改进设备操作系统分类：数据优化

传感器连续自动发现新设备，并更改以前发现的设备，包括操作系统类型。

在某些情况下，可能会在发现的操作系统中检测到冲突。 例如，如果你有引用桌面或服务器系统的操作系统版本，则可能会发生这种情况。 如果发生这种情况，你将收到包含可选操作系统分类的通知。

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="优化数据。":::

调查建议，以便扩充操作系统分类。 此分类显示在设备清单、数据挖掘报表和其他显示中。 确保此信息是最新的，可以提高警报、威胁和风险分析报表的准确性。

要访问操作系统建议：

1. 选择“系统设置”。
1. 选择“数据优化”。

## <a name="next-steps"></a>后续步骤

在各种报表中查看扩充的设备信息：

- [调查设备清单中的传感器检测](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [传感器趋势和统计信息报告](how-to-create-trends-and-statistics-reports.md)
- [传感器数据挖掘查询](how-to-create-data-mining-queries.md)
