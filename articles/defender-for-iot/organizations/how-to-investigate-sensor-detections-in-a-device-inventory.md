---
title: 深入了解特定传感器发现的设备
description: 设备清单显示服务器检测到的广泛设备属性。
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: 10df10a64059e5f5673a9e4ae6f18a09e4c41838
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515260"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>调查设备清单中的传感器检测

设备清单显示服务器检测到的广泛设备属性。 可用选项如下：

 - 轻松筛选信息。

 - 将信息导出到 CSV 文件。

 - 导入 Windows 注册表详细信息。

 - 创建要在设备映射中显示的组。

## <a name="view-device-attributes-in-the-device-inventory"></a>查看设备清单中的设备属性

以下属性将显示在设备清单表中。

| 参数 | 说明 |
|--|--|
| 名称 | 传感器发现的设备名称，或用户输入的设备名称。 |
| 类型 | 由传感器确定的设备类型，或由用户输入的设备类型。 |
| Vendor | MAC 地址中定义的设备供应商的名称。 |
| 操作系统 | 设备的操作系统（如果检测到）。 |
| 固件版本 | 设备的固件（如果检测到）。 |
| IP 地址 | 定义的设备的 IP 地址。 |
| VLAN | 设备的 VLAN。 有关指示传感器发现 VLAN 的详细信息，请参阅[定义 VLAN 名称](how-to-manage-the-on-premises-management-console.md#define-vlan-names).(how-to-define-management-console-network-settings.md#define-vlan-names)。 |
| MAC 地址 | 设备的 MAC 地址。 |
| 协议 | 设备使用的协议。 |
| 未确认的警报 | 与此设备关联的未确认的警报数。 |
| 已授权 | 用户定义的授权状态：<br />- True：设备已获得授权。<br />- False：设备未获得授权。 |
| 称为扫描器 | 由用户定义的网络扫描设备。 |
| 是编程设备 | 由用户定义的授权编程设备。 <br />- True：设备执行与工程站相关的 PLC、RTU 和控制器的编程活动。 <br />- False：设备不是编程设备。 |
| 组 | 此设备参与的组。 |
| 上一个活动 | 设备执行的上一个活动。 |
| Discovered | 首次在网络中看到此设备时的状态。 |

查看设备清单：

1. 在左侧窗格中，选择“设备”。 “设备”窗格将在右侧打开。

2. 在“设备”窗格中，选择 **所有设备** :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false":::。

若要隐藏和显示列，请自定义设备清单表：

1. 在设备清单的右上方菜单中，选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false":::。

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="设备清单设置屏幕。":::

2. 在“设备清单设置”窗口中，选择要在设备清单表中显示的列。

3. 使用箭头更改表中各列的位置。

4. 选择“保存”。 “设备清单设置”窗口将关闭，表中将显示新的设置。

### <a name="create-temporary-device-inventory-filters"></a>创建临时设备清单筛选器

可以设置一个筛选器，用于定义表显示的信息。 例如，你可以决定只查看 PLC 设备的信息。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="设备学习。":::

离开清单时，不会保存筛选器。

### <a name="save-device-inventory-filters"></a>保存设备清单筛选器

你可以保存所需的筛选器或筛选器的组合，并在设备清单中重新应用这些筛选器。 根据特定设备类型创建更广泛的筛选器，或根据特定类型和特定协议创建范围更窄筛选器。

保存的筛选器也会另存为设备映射组。 此功能为在映射中查看网络设备提供了额外的粒度级别。

创建筛选器：

1. 在要筛选的列中，选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false":::。

2. 在“筛选器”对话框中，选择筛选器类型：

   - 等于：要根据其筛选列的确切值。 例如，如果根据“等于”和 `value=ICMP` 筛选协议列，则该列将显示仅使用 ICMP 协议的设备。

   - 包含：列中其他值包含的值。 例如，如果根据“包含”和 `value=ICMP` 筛选协议列，则该列将显示使用 ICMP 协议的设备作为设备使用的协议列表的一部分。

3. 若要根据字母顺序整理列信息，请选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false":::。 通过选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: 和 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: 箭头排列顺序。

4. 若要保存新的筛选器，请定义筛选器并选择“另存为”。

5. 若要更改筛选器定义，请更改定义，然后选择“保存更改”。

查看筛选器：

- 打开左侧窗格并查看保存的筛选器：

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="在左侧窗格中查看筛选器。":::

### <a name="view-filtered-information-as-a-map-group"></a>以映射组形式查看筛选的信息

切换到“映射”视图时，将突出显示并筛选出已筛选的设备。 保存的筛选器组显示在“设备清单筛选器”组下的侧面菜单中。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="在映射视图中查看筛选器。":::

## <a name="learn-windows-registry-details"></a>了解 Windows 注册表详细信息

除了了解 OT 设备外，还可以发现 Microsoft Windows 工作站和服务器。 这些设备也会显示在设备清单中。 了解设备后，你可以通过详细的 Windows 信息扩充设备清单，例如：

- 安装的 Windows 版本

- 安装的应用程序

- 修补程序级别信息

- 打开端口

- 更可靠的操作系统版本信息

有两个选项可用于检索此信息：

- 使用计划的 WMI 扫描进行活动轮询。 

- 通过在设备上分发和运行脚本来进行本地调查。 使用本地脚本可绕过在终结点上运行 WMI 轮询的风险。 它还适用于具有瀑布图和单向元素的受管控网络。

本文介绍如何使用脚本在本地调查 Windows 终结点注册表。 此信息将用于生成警报、通知、数据挖掘报告、风险评估和攻击途径报告。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="数据挖掘屏幕。":::

可以调查以下 Windows 操作系统：

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>开始之前

若要使用该脚本，需要满足以下要求：

- 需要管理员权限才能在设备上运行脚本。

- 传感器应该已经了解 Windows 设备。 这意味着，如果设备已存在，脚本将检索其信息。

- 传感器正在监视 Windows 电脑连接的网络。

### <a name="acquire-the-script"></a>获取脚本

若要接收该脚本，请[联系客户支持](mailto:support.microsoft.com)。

### <a name="deploy-the-script"></a>部署脚本

可以通过使用标准的自动部署方法和工具，部署脚本一次或计划正在进行的查询。

### <a name="about-the-script"></a>关于脚本

- 脚本将作为实用工具（而不是已安装的程序）运行。 运行脚本不会影响终结点。

- 脚本生成的文件将保留在本地驱动器上，直到你删除这些文件。

- 脚本生成的文件会放置在彼此相邻的位置。 不要分隔它们。

- 如果在同一位置再次运行脚本，则将覆盖这些文件。

若要运行该脚本，请执行以下操作：  

1. 将脚本复制到本地驱动器并进行解压缩。 将显示以下文件：

    - start.bat

    - settings.json

    - data.bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="文件资源管理器中文件的视图。":::

2. 运行 `run.bat` 文件。

3. 探测到注册表后，会显示带有注册表信息的 CX 快照文件。

4. 文件名指示快照的系统名称和日期以及时间。 示例文件名为 `CX-snaphot_SystemName_Month_Year_Time`。

### <a name="import-device-details"></a>导入设备详细信息

应将每个终结点上了解的信息导入传感器。

从查询生成的文件可以放置在可从传感器访问的文件夹中。 使用标准的自动方法和工具将文件从每个 Windows 终结点移到要将它们导入传感器的位置。

不要更新文件名。

进行导入操作：

1. 从“导入 Windows 配置”对话框中选择“导入设置” 。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="导入 Windows 配置。":::

2. 选择“添加”，然后选择所有文件 (Ctrl+A)。

3. 选择“关闭”。 设备注册表信息已导入。 如果上传某个文件时出现问题，系统会通知你哪些文件上传失败。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="已成功上传添加的文件。":::

## <a name="view-and-delete-inactive-devices-from-the-inventory"></a>查看并删除清单中的非活动设备

建议查看网络中处于非活动状态的设备并将其删除。
由于以下原因，设备可能变为非活动状态：
- SPAN 端口错误配置
- 网络覆盖范围发生更改
- 与网络断开连接

删除非活动设备有助于：

- Defender for IoT 创建当前网络活动的更准确表示形式
- 管理订阅时更好地评估已提交的设备
- 减少屏幕上的混乱

### <a name="view-inactive-devices"></a>查看非活动设备

你可以筛选清单以显示处于非活动状态的设备：

- 7 天或以上
- 14 天或以上
- 30 天或以上
- 90 天或以上

**若要筛选清单，请执行以下操作：**
  
1. 选择清单中的“上次查看时间”筛选器图标。
1. 选择筛选器选项。
1. 选择“应用”。

### <a name="delete-inactive-devices"></a>删除非活动设备

从清单中删除的设备将从映射中删除，并且不会在生成 Defender for IoT 报告（例如数据挖掘、风险评估和攻击途径报告）时进行计算。

系统将提示你记录删除设备的原因。 此信息以及时间/日期和删除的设备数将显示在“事件时间线”中。

**若要从清单中删除设备，请执行以下操作：**
  
1. 选择清单中的“上次查看时间”筛选器图标。
1. 选择筛选器选项。
1. 选择“应用”。
1. 选择“删除非活动设备”。
1. 在打开的确认对话框中，输入删除的原因，然后选择“删除”。 在筛选范围内检测到的所有设备将被删除。 如果删除大量设备，删除过程可能需要几分钟的时间。

## <a name="export-device-inventory-information"></a>导出设备清单信息

可以将设备清单信息导出到 Excel 文件中。

导出 CSV 文件：

- 在设备清单的右上方菜单中，选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false":::。 将生成并下载 CSV 报表。

## <a name="see-also"></a>另请参阅

[调查设备清单中的所有企业传感器检测](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[使用站点地图视图](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
