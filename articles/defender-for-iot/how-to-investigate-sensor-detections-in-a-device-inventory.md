---
title: 深入了解特定传感器发现的设备
description: 设备清单显示传感器检测到的范围广泛的设备属性。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9dc6849c1b2ad5daa6142e894e36f351663ff9bd
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523985"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>调查设备清单中的传感器检测

设备清单显示传感器检测到的范围广泛的设备属性。 可用选项如下：

 - 轻松筛选信息。

 - 将信息导出到 CSV 文件。

 - 导入 Windows 注册表详细信息。

 - 创建要在设备映射中显示的组。

## <a name="view-device-attributes-in-the-device-inventory"></a>查看设备清单中的设备属性

以下属性将显示在设备清单表中。

| 参数 | 说明 |
|--|--|
| 名称 | 设备发现的设备名称，或用户输入的设备名称。 |
| 类型 | 由传感器确定的设备类型，或由用户输入的设备类型。 |
| Vendor | 在 MAC 地址中定义的设备供应商的名称。 |
| 操作系统 | 如果检测到设备的操作系统，则为。 |
| 固件版本 | 如果检测到设备固件，则为。 |
| IP 地址 | 定义的设备的 IP 地址。 |
| VLAN | 设备的 VLAN。 有关指示传感器发现 Vlan 的详细信息，请参阅 [定义 vlan 名称](how-to-manage-the-on-premises-management-console.md#define-vlan-names)。 (操作方法 # define # define # 定义-VLAN 名称) 。 |
| MAC 地址 | 设备的 MAC 地址。 |
| 协议 | 设备使用的协议。 |
| 未确认警报 | 与此设备关联的未确认的警报数。 |
| 已授权 | 用户定义的授权状态：<br />- **True**：设备已获得授权。<br />- **False**：设备未获得授权。 |
| 称为扫描器 | 由用户定义为网络扫描设备。 |
| 是编程设备 | 由用户定义为授权的编程设备。 <br />- **True**：设备执行 Plc、RTUs 和控制器的编程活动，这些活动与工程工作站相关。 <br />- **False**：设备不是编程设备。 |
| 组 | 此设备参与的组。 |
| 上次活动 | 设备执行的最后一个活动。 |
| Discovered | 第一次在网络中出现此设备时。 |

查看设备清单：

1. 在左窗格中，选择 " **设备**"。 " **设备** " 窗格将在右侧打开。

2. 在 " **设备** " 窗格中，选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false"::: 。

若要隐藏和显示列，请自定义设备清单表：

1. 在设备清单的右上方菜单中，选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false"::: 。

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="设备清单设置屏幕。":::

2. 在 " **设备清单设置** " 窗口中，选择要在设备清单表中显示的列。

3. 使用箭头更改表中各列的位置。

4. 选择“保存” 。 " **设备清单设置** " 窗口将关闭，并在表中显示新的设置。

### <a name="create-temporary-device-inventory-filters"></a>创建临时设备清单筛选器

可以设置一个筛选器，用于定义表中显示的信息。 例如，你可以决定只查看 PLC 设备的信息。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="设备学习。":::

当您离开清单时，不会保存该筛选器。

### <a name="save-device-inventory-filters"></a>保存设备清单筛选器

您可以保存所需筛选器或筛选器的组合，并在设备清单中重新应用这些筛选器。 基于特定的设备类型创建更广泛的筛选器，或基于特定类型和特定协议的更窄筛选器。

保存的筛选器也会另存为设备映射组。 此功能为在地图上查看网络设备提供了额外的粒度级别。

创建筛选器：

1. 在要筛选的列中，选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: 。

2. 在 " **筛选器** " 对话框中，选择筛选器类型：

   - **等于**：要根据其筛选列的确切值。 例如，如果根据 **等于** 和筛选 "协议" 列 `value=ICMP` ，则该列将显示仅使用 ICMP 协议的设备。

   - **Contains**：列中的其他值中包含的值。 例如，如果根据 **包含** 和筛选协议列 `value=ICMP` ，列将显示使用 ICMP 协议的设备作为设备使用的协议列表的一部分。

3. 若要根据字母顺序组织列信息，请选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: 。 通过选择和箭头排列顺序 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: 。

4. 若要保存新的筛选器，请定义筛选器并选择 " **另存为**"。

5. 若要更改筛选器定义，请更改定义，然后选择 " **保存更改**"。

查看筛选器：

- 打开左窗格并查看已保存的筛选器：

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="在左侧窗格中查看筛选器。":::

### <a name="view-filtered-information-as-a-map-group"></a>以地图组形式查看筛选信息

切换到 "地图" 视图时，将突出显示并筛选已筛选的设备。 保存的筛选器组显示在 " **设备清单筛选器** " 组下的侧菜单中。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="在地图视图中查看筛选器。":::

## <a name="learn-windows-registry-details"></a>了解 Windows 注册表详细信息

除了学习设备外，还可以发现 Microsoft Windows 工作站和服务器。 这些设备也会显示在设备清单中。 了解设备后，你可以通过详细的 Windows 信息扩充设备清单，例如：

- Windows 版本已安装

- 安装的应用程序

- 修补程序级别信息

- 打开端口

- 更可靠的操作系统版本信息

有两个选项可用于检索此信息：

- 使用计划 WMI 扫描的活动轮询。 

- 通过在设备上分发和运行脚本来进行本地调查。 使用本地脚本会绕过在终结点上运行 WMI 轮询的风险。 它还适用于具有瀑布图和单向元素的管控网络。

本文介绍如何使用脚本在本地调查 Windows 终结点注册表。 此信息将用于生成警报、通知、数据挖掘报告、风险评估和攻击向量报告。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="数据挖掘屏幕截图。":::

你可以调查以下 Windows 操作系统：

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>准备阶段

若要使用该脚本，需要满足以下要求：

- 需要管理员权限才能在设备上运行该脚本。

- 传感器应该已经了解 Windows 设备。 这意味着，如果设备已存在，则脚本将检索其信息。

- 传感器正在监视 Windows 电脑连接到的网络。

### <a name="acquire-the-script"></a>获取脚本

若要接收该脚本，请 [与客户支持联系](mailto:support.microsoft.com)。

### <a name="deploy-the-script"></a>部署脚本

可以通过使用标准的自动部署方法和工具，部署脚本一次或计划正在进行的查询。

### <a name="about-the-script"></a>关于脚本

- 脚本将作为实用工具运行，而不是已安装的程序。 运行该脚本不会影响该终结点。

- 脚本生成的文件将保留在本地驱动器上，直到你将其删除。

- 脚本生成的文件会彼此相邻放置。 不要分隔它们。

- 如果在同一位置再次运行该脚本，则将覆盖这些文件。

若要运行该脚本，请执行以下操作：  

1. 将脚本复制到本地驱动器并将其解压缩。 将显示以下文件：

    - start.bat

    - settings.js

    - 数据 bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="文件资源管理器中的文件视图。":::

2. 运行该 `run.bat` 文件。

3. 探测到注册表后，会出现 CX 快照文件和注册表信息。

4. 文件名指示快照的系统名称和日期和时间。 例如，文件名为 `CX-snaphot_SystemName_Month_Year_Time` 。

### <a name="import-device-details"></a>导入设备详细信息

应将每个终结点上了解的信息导入到传感器。

从查询生成的文件可以放置在一个可从传感器访问的文件夹中。 使用标准的自动方法和工具将文件从每个 Windows 终结点移到将它们导入传感器的位置。

不要更新文件名。

导入：

1. 从 "**导入 Windows 配置**" 对话框中选择 "**导入设置**"。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="导入 Windows 配置。":::

2. 选择 " **添加**"，然后选择 "所有文件" (Ctrl + A) 。

3. 选择“关闭”。 导入设备注册表信息。 如果上传某个文件时出现问题，将会通知你哪些文件上传失败。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="已成功上载已添加的文件。":::

## <a name="export-device-inventory-information"></a>导出设备清单信息

可以将设备清单信息导出到 Excel 文件中。

导出 CSV 文件：

- 在设备清单的右上方菜单中，选择 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false"::: 。 将生成并下载 CSV 报表。

## <a name="next-steps"></a>后续步骤

[调查设备清单中的所有企业传感器检测](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[使用站点地图视图](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
