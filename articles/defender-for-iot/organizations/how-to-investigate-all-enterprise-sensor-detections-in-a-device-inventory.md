---
title: 了解所有企业传感器发现的设备
description: 使用本地管理控制台中的设备清单全面了解来自连接的传感器的设备信息。 使用导入、导出和筛选工具管理此信息。
ms.date: 12/02/2020
ms.topic: how-to
ms.openlocfilehash: 0ae59123b59cfb54cba2a2ee9bdeefb411c8793b
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015717"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>调查设备清单中的所有企业传感器检测结果

你可以通过使用本地管理控制台中的设备清单查看来自连接传感器的设备信息。 借助此功能可全面了解所有网络信息。 使用导入、导出和筛选工具管理此信息。 此外，还显示有关连接的传感器版本的状态信息。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="设备清单数据表的屏幕截图。":::

下表对设备清单中的表列进行了说明。

| 参数 | 说明 |
|--|--|
| **Unacknowledged Alerts** | 与此设备关联的未处理的警报数。 |
| **Business Unit** | 包含此设备的业务部门。 |
| **区域** | 包含此设备的区域。 |
| **站点** | 包含此设备的站点。 |
| **区域** | 包含此设备的区域。 |
| **设备** | 保护此设备的 Azure Defender for IoT 传感器。 |
| **名称** | 由 Defender for IoT 发现的此设备的名称。 |
| 类型 | 设备类型，如 PLC 或 HMI。 |
| **供应商** | MAC 地址中定义的设备供应商的名称。 |
| **操作系统** | 设备的 OS。 |
| **固件** | 设备的固件。 |
| **IP 地址** | 设备的 IP 地址。 |
| **VLAN** | 设备的 VLAN。 |
| **MAC Address** | 设备的 MAC 地址。 |
| **协议** | 设备使用的协议。 |
| **Unacknowledged Alerts** | 与此设备关联的未处理的警报数。 |
| **Is Authorized** | 设备的授权状态：<br />- True：设备已获得授权。<br />- False：设备未获得授权。 |
| **Is Known as Scanner** | 此设备是否在网络中执行类似于扫描的活动。 |
| **Is Programming Device** | 这是否为编程设备：<br />- True：设备为与工程站相关的 PLC、RTU 和控制器执行编程活动。<br />- False：设备不是编程设备。 |
| **组** | 此设备参与的组。 |
| **Last Activity** | 设备执行的上一个活动。 |
| **Discovered** | 首次在网络中看到此设备时的状态。 |

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>将数据集成到企业设备清单

数据集成功能使你能够利用来自其他企业资源的信息增强设备清单中的数据。 这些源包括 CMDB、DNS、防火墙和 Web API。

你可以使用此信息了解以下内容。 例如：

- 设备购买日期和保修结束日期

- 负责每个设备的用户

- 已打开的设备票证

- 上次升级固件的日期

- 允许访问 Internet 的设备

- 运行主动防病毒应用程序的设备

- 登录到设备的用户

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="设备清单屏幕上的数据表。":::

可通过以下两种方法集成数据：

- 手动添加

- 运行 Defender for IoT 提供的自定义脚本

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="企业数据集成器图。":::

你可以使用 Defender for IoT 的技术支持将系统设置为接收 Web API 查询。

若要手动添加数据：

1. 在侧边菜单中，选择“设备清单”，然后选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false":::。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="编辑设备的清单设置。":::

2. 在“设备清单设置”对话框中，选择“添加自定义列” 。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="将自定义列添加到清单。":::

3. 在“添加自定义列”对话框中，添加新列的名称（最多 250 个字符 UTF），选择“手动”，然后选择“保存”  。 新项目会显示在“设备清单设置”对话框中。

4. 在“设备清单”窗口的右上角，选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::，然后选择“导出所有设备清单” 。 生成 CSV 文件。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="导出的 CSV 文件。":::

5. 手动将信息添加到新列并保存文件。

6. 在“设备清单”窗口的右上角，选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::，选择“导入手动输入列”，然后浏览到该 CSV 文件 。 新数据会显示在“设备清单”表中。

若要集成来自其他企业实体的数据：

1. 在“设备清单”窗口的右上角，选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::，然后选择“导出所有设备清单” 。

2. 在“设备清单设置”对话框中，选择“添加自定义列” 。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="将自定义列添加到清单。":::

3. 在“添加自定义列”对话框中，添加新列的名称（最多 250 个字符 UTF），然后选择“自动” 。 随即显示“上传脚本”和“测试脚本”选项 。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="自动添加自定义列。":::

4. 上传并测试从 [Microsoft 支持部门](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)接收的脚本。

## <a name="retrieve-information-from-the-device-inventory"></a>检索设备清单中的信息

你可以检索由托管传感器检测到的大量设备信息，并将这些信息与合作伙伴系统集成。 例如，你可以检索传感器、区域、站点 ID、IP 地址、MAC 地址、固件、协议和供应商信息。 基于以下设备对检索到的信息进行筛选：

- 授权和未经授权的设备。

- 与特定站点相关联的设备。

- 与特定区域相关联的设备。

- 与特定传感器相关联的设备。

使用 Defender for IoT 的 API 命令检索和集成此信息。 有关详细信息，请参阅 [Defender for IoT API 传感器和管理控制台 API](references-work-with-defender-for-iot-apis.md)。

## <a name="filter-the-device-inventory"></a>筛选设备清单

你可以筛选设备清单，以显示感兴趣的列。 例如，可以查看 PLC 设备信息。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="设备清单的屏幕截图。":::

退出窗口时，将清空筛选器。

要多次使用同一筛选器，则可以保存所需的筛选器或筛选器组合。 你可以打开左侧窗格并查看保存的筛选器：

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="设备清单屏幕。":::

若要筛选设备清单：

1. 在要筛选的列中，选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false":::。

2. 在“筛选器”对话框中，选择筛选器类型：

   - 等于：要根据其筛选列的确切值。 例如，如果根据“等于”和 `value=ICMP` 筛选协议列，则该列将显示仅使用 ICMP 协议的设备。

   - 包含：列中其他值包含的值。 例如，如果根据“包含”和 `value=ICMP` 筛选协议列，则该列将显示使用 ICMP 协议的设备作为设备使用的协议列表的一部分。

3. 若要根据字母顺序整理列信息，请选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false":::。 通过选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: 和 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: 箭头排列顺序。

4. 若要保存新的筛选器，请定义筛选器并选择“另存为”。

5. 若要更改筛选器定义，请更改定义，然后选择“保存更改”。

## <a name="next-steps"></a>后续步骤

[调查设备清单中的传感器检测](how-to-investigate-sensor-detections-in-a-device-inventory.md)
