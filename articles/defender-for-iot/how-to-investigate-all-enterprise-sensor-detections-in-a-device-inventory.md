---
title: 了解所有企业传感器发现的设备
description: 使用本地管理控制台中的设备清单从连接的传感器获取设备信息的综合视图。 使用导入、导出和筛选工具来管理此信息。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9da5c8c89ee124e527584164b21b096ac815e5ca
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625458"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>调查设备清单中的所有企业传感器检测

可以通过在本地管理控制台中使用 *设备清单* 来查看连接的传感器的设备信息。 此功能提供所有网络信息的综合视图。 使用导入、导出和筛选工具来管理此信息。 还显示有关连接的传感器版本的状态信息。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="设备清单数据表的屏幕截图。":::

下表描述了设备清单中的表列。

| 参数 | 说明 |
|--|--|
| **未确认警报** | 与此设备关联的未处理警报数。 |
| **业务部门** | 包含此设备的业务部门。 |
| **区域** | 包含此设备的区域。 |
| **站点** | 包含此设备的站点。 |
| **区域** | 包含此设备的区域。 |
| **设备** | 保护此设备的 Azure Defender for IoT 传感器。 |
| **名称** | 此设备的名称，因为 IoT 已发现它。 |
| **类型** | 设备的类型，例如 PLC 或 HMI。 |
| **供应商** | 在 MAC 地址中定义的设备供应商的名称。 |
| **操作系统** | 设备的操作系统。 |
| **固件** | 设备的固件。 |
| **IP 地址** | 设备的 IP 地址。 |
| **VLAN** | 设备的 VLAN。 |
| **MAC 地址** | 设备的 MAC 地址。 |
| **协议** | 设备使用的协议。 |
| **未确认警报** | 与此设备关联的未处理警报数。 |
| **已授权** | 设备的授权状态：<br />- **True**：设备已获得授权。<br />- **False**：设备未获得授权。 |
| **称为扫描器** | 此设备是否在网络中执行类似于扫描的活动。 |
| **是编程设备** | 这是否是编程设备：<br />- **True**：设备执行 Plc、RTUs 和控制器的编程活动，这些活动与工程工作站相关。<br />- **False**：设备不是编程设备。 |
| **组** | 此设备参与的组。 |
| **上次活动** | 设备执行的最后一个活动。 |
| **Discovered** | 第一次在网络中出现此设备时。 |

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>将数据集成到企业设备清单

数据集成功能使你可以通过其他企业资源的信息来增强设备清单中的数据。 这些源包括 CMDBs、DNS、防火墙和 Web Api。

您可以使用此信息来了解。 例如：

- 设备购买日期和保修期结束日期

- 负责每个设备的用户

- 设备的已打开票证

- 固件升级的最后日期

- 允许访问 internet 的设备

- 运行活动防病毒应用程序的设备

- 登录到设备的用户

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="设备清单屏幕上的数据表。":::

可以通过以下任一方法来集成数据：

- 手动添加

- 运行用于 IoT 的自定义脚本提供

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="企业数据集成器关系图。":::

你可以使用适用于 IoT 技术支持的 Defender 将系统设置为接收 Web API 查询。

手动添加数据：

1. 在侧边菜单中，选择 " **设备清单** "，然后选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false"::: 。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="编辑设备的清单设置。":::

2. 在 " **设备清单设置** " 对话框中，选择 " **添加自定义列**"。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="向库存添加自定义列。":::

3. 在 " **添加自定义列** " 对话框中，将新列名称添加 (最多250个字符 UTF) ，选择 " **手动**"，然后选择 " **保存**"。 新项目将出现在 " **设备清单设置** " 对话框中。

4. 在 " **设备清单** " 窗口的右上角，选择 " :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: **导出所有设备清单**" 并选择 "导出"。 生成 CSV 文件。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="导出的 CSV 文件。":::

5. 手动将信息添加到新列并保存文件。

6. 在 " **设备清单** " 窗口的右上角，选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: ，选择 " **导入手动输入列**"，然后浏览到 CSV 文件。 新数据将显示在 " **设备清单** " 表中。

集成其他企业实体的数据：

1. 在 " **设备清单** " 窗口的右上角，选择 " :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: **导出所有设备清单**" 并选择 "导出"。

2. 在 " **设备清单设置** " 对话框中，选择 " **添加自定义列**"。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="向库存添加自定义列。":::

3. 在 " **添加自定义列** " 对话框中，将新列名称添加 (最多250个字符的 UTF) ，然后选择 " **自动**"。 随即显示 " **上传脚本** " 和 " **测试脚本** " 选项。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="自动添加自定义列。":::

4. 上载并测试从 [Microsoft 支持部门](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)收到的脚本。

## <a name="retrieve-information-from-the-device-inventory"></a>从设备清单中检索信息

可以检索由托管传感器检测到的范围广泛的设备信息，并将该信息与合作伙伴系统集成。 例如，你可以检索传感器、区域、站点 ID、IP 地址、MAC 地址、固件、协议和供应商信息。 根据以下条件筛选要检索的信息：

- 授权的和未经授权的设备。

- 与特定站点关联的设备。

- 与特定区域关联的设备。

- 与特定传感器关联的设备。

使用用于 IoT API 命令的 Defender 来检索和集成此信息。 有关详细信息，请参阅 [IOT API 传感器和管理控制台 api 的 Defender](references-work-with-defender-for-iot-apis.md)。

## <a name="filter-the-device-inventory"></a>筛选设备清单

可以筛选设备清单以显示相关列。 例如，可以查看 PLC 设备信息。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="设备清单的屏幕截图。":::

退出该窗口时，将清除该筛选器。

若要多次使用同一筛选器，可以保存所需筛选器或筛选器的组合。 可以打开左窗格并查看已保存的筛选器：

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="设备清单屏幕。":::

筛选设备清单：

1. 在要筛选的列中，选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false"::: 。

2. 在 " **筛选器** " 对话框中，选择筛选器类型：

   - **等于**：要根据其筛选列的确切值。 例如，如果根据 **等于** 和筛选 "协议" 列 `value=ICMP` ，则该列将显示仅使用 ICMP 协议的设备。

   - **Contains**：列中的其他值中包含的值。 例如，如果根据 **包含** 和筛选协议列 `value=ICMP` ，列将显示使用 ICMP 协议的设备作为设备使用的协议列表的一部分。

3. 若要根据字母顺序组织列信息，请选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: 。 通过选择和箭头排列顺序 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: 。

4. 若要保存新的筛选器，请定义筛选器并选择 " **另存为**"。

5. 若要更改筛选器定义，请更改定义，然后选择 " **保存更改**"。

## <a name="view-device-information-per-zone"></a>查看每个区域的设备信息

你可以了解有关区域中设备的以下信息。

### <a name="view-a-device-map"></a>查看设备映射

查看所选区域的传感器设备映射：

- 在 " **站点管理** " 窗口中，选择包含区域名称的栏中的 " **查看区域映射** "。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="默认区域为默认的业务部门。":::

此时将显示 " **设备映射** " 窗口。 其中显示了与所选区域相关的所有网络元素，包括传感器、连接到它们的设备以及其他信息。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="区域图的屏幕截图。":::

以下工具可用于从地图查看设备和设备信息。 有关上述每项功能的详细信息，请参阅 *用于 IoT 平台用户指南的 Defender*。

- **地图缩放视图**：简化视图、连接视图和详细信息视图。 显示的地图视图因地图的缩放级别而异。 可以通过调整缩放级别在地图视图之间切换。

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **地图搜索和布局工具**：用于显示各种网段、设备、设备组或层的工具。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="搜索和布局工具视图的屏幕截图。":::

- **设备上的标签和指示器：** 例如，在 IT 网络的子网中分组的设备数。 在此示例中，为8。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="标签和指示器的屏幕截图。":::

- **查看设备属性**：例如，监视设备和基本设备属性的传感器。 右键单击该设备可查看设备属性。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="设备属性视图的屏幕截图。":::

- **与设备相关联的警报：** 右键单击该设备可查看相关警报。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="显示警报视图的屏幕截图。":::

### <a name="view-alerts-associated-with-a-zone"></a>查看与区域关联的警报

查看与特定区域关联的警报：

- 选择 " **区域** " 窗口中的警报图标。 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="示例的默认业务部门视图。":::

有关详细信息，请参阅 [概述：使用警报](how-to-work-with-alerts-on-premises-management-console.md)。

### <a name="view-the-device-inventory-of-a-zone"></a>查看区域的设备清单

查看与特定区域关联的设备清单：

- 从 "**区域**" 窗口中选择 "**查看设备清单**"。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="将显示 &quot;设备清单&quot; 屏幕。":::

有关详细信息，请参阅 [调查设备清单中的所有企业传感器检测](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)。

### <a name="view-additional-zone-information"></a>查看其他区域信息

以下附加区域信息可用：

- **区域详细信息**：查看与区域关联的设备、警报和传感器的数量。

- **传感器详细信息**：查看分配给该区域的每个传感器的名称、IP 地址和版本。

- **连接状态**：如果传感器已断开连接，则从传感器连接。 请参阅 [将传感器连接到本地管理控制台](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)。 

- **更新进度**：如果正在升级连接的传感器，将出现升级状态。 在升级过程中，本地管理控制台不会从传感器接收设备信息。

## <a name="see-also"></a>另请参阅

[调查设备清单中的传感器检测](how-to-investigate-sensor-detections-in-a-device-inventory.md)
