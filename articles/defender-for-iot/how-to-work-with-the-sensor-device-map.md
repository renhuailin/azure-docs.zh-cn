---
title: 使用传感器设备映射
description: 设备映射提供检测到的网络设备的图形表示。 使用 map 分析和管理设备信息、网络切片和生成报告。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/7/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 5b7059129c45149c64bc7fc145c68d9e09a7c046
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523832"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>在设备映射中调查传感器检测

设备映射提供检测到的网络设备的图形表示。 使用以下映射：

  - 检索、分析和管理设备信息。

  - 分析网络切片，例如特定的感兴趣组或 Purdue 层。

  - 生成报告，例如导出设备详细信息和摘要。

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="设备映射的屏幕截图。":::

访问映射：

  - 从控制台主屏幕中选择 " **设备映射** "。

## <a name="map-search-and-layout-tools"></a>地图搜索和布局工具

以下工具用于在映射中工作。

你的用户角色确定哪些工具可用于 "设备映射" 窗口。 有关用户角色的详细信息，请参阅 [创建和管理用户](how-to-create-and-manage-users.md) 。

| 符号 | 说明 |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| 搜索特定设备的 IP 地址或 MAC 地址。 在文本框中输入 IP 地址或 MAC 地址。 该地图将显示您在连接到该设备的设备上搜索的设备。 |
| 组突出显示和筛选器 <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="组突出显示和筛选器的屏幕截图。"::: | 基于默认和自定义设备组筛选或突出显示地图。 |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | 它折叠视图，在设备上启用集中视图，并将 IT 设备分组。  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | 在映射中维护当前设备排列。 例如，如果将设备拖到地图上的新位置，则在退出地图时，设备将保留在这些位置。 |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | 适应屏幕 |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | -查看为此设备标识的 Purdue 层，包括自动、进程控制、监督和企业 <br /> -查看设备之间的连接。|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | 显示或隐藏广播和多播。 |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | 根据上一次与其他设备通信的时间，筛选地图上的设备。 |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="报警" border="false"::: | 查看有关设备的通知。 例如，如果使用现有 MAC 地址为设备检测到新的 IP |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="导出" border="false"::: | 导出/导入设备信息。 |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="properties" border="false"::: | 查看所选设备的基本设备属性。 |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="放大" border="false"::: 或 :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="缩小" border="false"::: | 放大或缩小地图中的设备。 |

## <a name="view-ot-elements-only"></a>仅查看 OT 元素

默认情况下，系统会按子网自动聚合 IT 设备，以便将地图视图集中于 OT 和 ICS 网络。 IT 网络元素的表示形式折叠为最小值，这会减少地图上显示的设备总数，并清楚地说明了 OT 和 ICS 网络元素。

每个子网都作为设备映射上的单个实体提供，其中包括一个交互式折叠和扩展功能，可查看 IT 子网和背面的详细信息。

下图显示了具有27个 IT 网络元素的折叠的 IT 子网。

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="已折叠具有 27 IT 网络元素的 IT 子网":::

启用 IT 网络折叠功能：

- 在 " **系统设置** " 窗口中，确保 "切换 IT 网络分组功能" 已启用。

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="系统设置窗口":::

展开 IT 子网：

1. 若要区分 IT 与网络网络，请在 "系统设置" 屏幕中选择 " **子网**"。

   > [!NOTE]
   > 建议为每个具有有意义的名称的子网命名，用户可以轻松地识别这些子网，以便区分其网络。

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="子网配置":::

2. 在 " **编辑子网配置** " 窗口中，清除要定义为 IT 子网的每个子网的 " **ICS 子网** " 复选框。 IT 子网在设备映射中显示为折叠，其中包含针对 ICS 设备（如控制器或 PLC）的通知。

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="编辑子网配置":::

3. 若要展开地图上的 IT 网络，请在 "设备" 窗口中右键单击该网络，然后选择 " **展开网络**"。

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="展开你的网络视图。":::

4. 此时将显示一个确认框，通知你无法重做布局更改。

5. 选择“确定”。 子网元素显示在地图上。

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="确定":::

折叠 IT 子网：

1.  在左侧窗格中，选择 " **设备**"。

2. 在 "设备" 窗口中，选择 "折叠" 图标。 红色数字表示当前在地图上显示的已扩展的 IT 子网数。

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="设备窗口":::

3. 选择要折叠的子网 () ，或选择 " **全部折叠**"。 所选子网在地图上显示为折叠。

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="全部折叠":::

将更新折叠图标，其中包含已更新的扩展 IT 子网数。

## <a name="view-or-highlight-device-groups"></a>查看或突出显示设备组

你可以基于设备组自定义地图显示。 例如，与特定 OT 协议、VLAN 或子网关联的设备组。 预定义组可用，可以创建自定义组。

查看组的方式：

  - **突出显示：** 以蓝色突出显示属于特定组的设备。

  - **筛选：** 仅显示属于特定组的设备。

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="端口的标准视图":::

以下预定义组可用：

| 组名称 | 说明 |
|--|--|
| **已知应用程序** | 使用保留端口的设备，如 TCP。  |
| **默认)  (非标准端口** | 使用非标准端口的设备或未分配别名的端口。 |
| **(默认) 的协议** | 处理已知的通信的设备。 |
| **授权 (默认值)** | 在学习过程中或在网络上正式授权时在网络中发现的设备。 |
| **设备清单筛选器** | 根据筛选器保存在设备清单表中的设备进行分组。 |
| **轮询间隔** | 按轮询间隔分组的设备。 轮询间隔根据循环通道或期间自动生成。 例如，15.0 秒、3.0 秒、1.5 秒或任意间隔。 查看此信息可帮助您了解系统轮询速度是否太快或速度缓慢。 |
| **编程** | 工程工作站和编程计算机。 |
| **子网** | 属于特定子网的设备。 |
| **VLAN** | 与特定 VLAN ID 相关联的设备。 |
| **跨子网连接** | 从一个子网到另一个子网的通信。 |
| **固定警报** | 用户已将警报固定到其中的设备。 |
| **攻击向量模拟** | 攻击向量报告中检测到易受攻击的设备。 若要在地图上查看这些设备，请在生成攻击向量时选择 " **在设备上显示** " 复选框。 :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="添加攻击向量模拟":::。 |
| **上次出现时间** | 按上次查看的时间范围分组的设备，例如：1小时、6小时、一天、七天。 |
| **不在 Active Directory** | 所有未与 Active Directory 通信的非 PLC 设备。 |

突出显示或筛选设备：

1. 选择侧边菜单上的 " **设备映射** "。

2. 选择 "筛选器" 图标。 :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="菜单":::

3. 从 "组" 窗格中，选择要突出显示或筛选设备的组。

4. 选择 " **突出显示** 或 **筛选**"。 切换相同的选择项以删除突出显示或筛选器。

## <a name="define-custom-groups"></a>定义自定义组

除了查看预定义的组外，还可以定义自定义组。 组显示在设备映射、设备清单和数据挖掘报表中。

> [!NOTE]
> 你还可以从设备清单创建组。

若要创建组，请执行以下操作：

1. 选择侧边菜单上的 " **设备** "。 随即显示设备映射。

1. 选择 :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="组设置"::: 以显示组设置。

1. 选择 " :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="组"::: " 以创建新的自定义组。

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="创建自定义组屏幕":::

1. 添加组的名称，使用最多30个字符。

1. 选择相关设备，如下所示：

   - 从该菜单中选择设备，方法是从下拉列表中选择该设备 (选择 ") " 按钮。<br /> 或者， <br /> 
   - 通过从选定的组中复制设备，从此菜单添加设备， (在箭头按钮上选择) 

1. 选择 " **添加组** "，将现有组添加到自定义组。

### <a name="add-devices-to-a-custom-group"></a>向自定义组添加设备

可以将设备添加到自定义组，也可以创建新的自定义组和设备。

1. 右键单击地图上) 的设备 (。

1. 选择 " **添加到组**"。

1. 在 "组" 字段中输入组名称，并选择 "+"。 此时将显示新组。 如果该组已存在，则会将其添加到现有的自定义组。

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="组名":::

1. 重复步骤1-3，将设备添加到组。

## <a name="map-zoom-views"></a>地图缩放视图

使用地图视图有助于在分析大型网络时加快鉴证。

可以显示三个设备详细信息视图：

  - [鸟瞰视图](#birds-eye-view)

  - [设备类型和连接视图](#device-type-and-connection-view)

  - [详细视图](#detailed-view)

### <a name="birds-eye-view"></a>鸟瞰视图

此视图提供按如下方式表示的设备的一览式视图：

  - 红点表示具有警报 (s 的设备) 

  - 带有星标点指示标记为重要的设备

  - 黑色点表示没有警报的设备

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="鸟瞰视图":::

### <a name="device-type-and-connection-view"></a>设备类型和连接视图 

此视图提供在地图上表示为图标的设备，以突出显示带有警报、设备类型和连接设备的设备。

  - 带有警报的设备将显示红色圆圈

  - 不带警报的设备将以灰色环显示

  - 显示为星号的设备被标记为重要

设备类型图标显示为 "已连接设备"。

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="连接视图":::

### <a name="detailed-view"></a>详细视图 

详细视图显示设备和设备标签，并提供以下信息：

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="详细视图":::

### <a name="control-the-zoom-view"></a>控制缩放视图

显示的地图视图取决于地图缩放级别。 可以通过更改缩放级别来在地图视图之间切换。

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="控制缩放视图":::

### <a name="enable-simplified-zoom-views"></a>启用简化的缩放视图

如果管理员想要安全分析师和 RO 用户访问鸟瞰和设备和类型连接视图，则应启用简化的视图选项。

启用简化地图视图：

  - 选择 " **系统设置** "，并切换 " **简化地图视图** " 选项。

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="简化地图视图":::

## <a name="learn-more-about-devices"></a>了解有关设备的详细信息

可以使用范围广泛的工具来了解有关设备的详细信息，请参阅设备映射：

- [设备标签和指示器](#device-labels-and-indicators)

- [设备快速视图](#device-quick-views)

- [查看和管理设备属性](#view-and-manage-device-properties)

- [查看设备类型](#view-device-types)

- [接触](#backplane-properties)

- [查看设备事件的时间线](#view-a-timeline-of-events-for-the-device)

- [分析编程详细信息和更改](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>设备标签和指示器

地图上的设备上可能会出现以下标签和指示器：

| 设备标签 | 说明 |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="IP 主机名"::: | IP 地址主机名和 IP 地址，或子网地址 |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="警报数"::: | 与设备关联的警报数 |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | 设备类型图标，例如存储、PLC 或 historian。 |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="已分组的设备"::: | 在 IT 网络的子网中分组的设备数。 在此示例中，为8。 |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="设备学习周期"::: | 在学习期间检测到并且未作为网络设备授权的设备。 |
| 实线 | 设备间的逻辑连接 |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="新设备"::: | 学习完成后发现新设备。 |

### <a name="device-quick-views"></a>设备快速视图

从映射访问设备属性和连接。

打开 "快速属性" 菜单：

  - 选择 "快速属性" 菜单的 " :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="快速属性&quot; 菜单":::。

#### <a name="quick-device-properties"></a>快速设备属性

选择一台或多台设备，同时打开 "快速属性" 屏幕来查看这些设备的亮点：

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="快速设备属性":::

#### <a name="quick-connection-properties"></a>快速连接属性

"快速属性" 屏幕打开时，请选择一个连接，以查看在此连接中使用的协议以及最后看到的协议：

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="快速连接属性":::

## <a name="view-and-manage-device-properties"></a>查看和管理设备属性

可以查看地图上显示的每个设备的设备 microsoft.support。 例如，设备名称、类型或 OS，或者固件或供应商。

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="查看和管理设备属性":::

可以手动更新以下信息。 手动输入的信息会替代用于 IoT 的 Defender 发现的信息。

  - 名称

  - 类型

  - OS

  - Purdue 层

  - 说明

| Item | 说明 |
|--|--|
| 基本信息 | 所需的基本信息。 |
| 名称 | 设备名称。 <br /> 默认情况下，传感器会发现网络中定义的设备名称。 例如，在 DNS 服务器中定义的名称。 <br /> 如果未定义此类名称，则设备 IP 地址将显示在此字段中。 <br /> 你可以手动更改设备名称。 为设备指定有意义的名称，这些名称反映了其功能。 |
| 类型 | 传感器检测到的设备类型。 <br /> 有关详细信息，请参阅 [查看设备类型](#view-device-types)。 |
| Vendor | 设备供应商。 这取决于设备 MAC 地址的前导字符。 此字段为只读。 |
| 操作系统 | 传感器检测到的设备操作系统。 |
| Purdue 层 | 此设备的传感器标识的 Purdue 层，包括： <br /> -自动 <br /> -进程控制 <br /> -监督 <br /> - Enterprise |
| 说明 | 一个自由文本字段。 <br /> 添加有关设备的详细信息。 |
| 特性 | 在学习期间发现的有关设备的任何其他信息，如果不属于其他类别，则会出现在 "属性" 部分中。 <br /> 此信息为 RO。 |
| 设置 | 可以手动更改设备设置以防止误报： <br /> - **授权设备**：在学习期间，网络中发现的所有设备被标识为授权设备。 在学习期间发现设备后，默认情况下，该设备显示为未经授权的设备。 可以手动更改此定义。 <br /> - **称为扫描器**：如果知道此设备称为扫描器，则启用此选项，无需对其发出警报。 <br /> - **编程设备**：如果知道此设备称为编程设备，并用于进行编程更改，请启用此选项。 将其标识为编程设备会阻止来自此资产的编程更改的警报。 |
| 自定义组 | 此设备参与的设备映射中的自定义组。 |
| 状态 | 设备的安全和授权状态： <br /> -如果没有警报，状态为 `Secured` <br /> -当存在有关设备的警报时，将显示警报的数目 <br /> -显示在 `Unauthorized` 学习时间段之后添加到网络的设备的状态。 你可以 `Authorized Device` 在 "设置" 中手动定义设备 <br /> -如果将此设备的地址定义为动态地址， `DHCP` 则会将添加到状态。 |


| 网络 | 说明 |
|--|--|
| 接口 | 设备接口。 只读字段。 |
| 协议 | 设备使用的协议。 只读字段。 |
| 固件 | 如果底板信息可用，则不会显示固件信息。 |
| 地址 | 设备 IP 地址。 |
| 串行 | 设备序列号。 |
| 模块地址 | 设备型号和槽号或 ID。 |
| 建模 | 设备型号。 |
| 固件版本 | 固件版本号。 |

查看设备信息：

1. 选择侧边菜单上的 " **设备** "。

2. 右键单击某个设备，然后选择 " **查看属性**"。 将显示设备属性窗口。

3. 选择此窗口底部的 "所需警报"，查看有关此设备的警报的详细信息。

### <a name="view-device-types"></a>查看设备类型

设备类型在设备发现过程中自动由传感器标识。 可以手动更改类型。

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="查看设备类型":::

下表显示了系统中的所有类型：

| 类别 | 设备类型 |
|--|--|
| 保证 | 工程工作站 <br /> 股票 <br />Historian <br />HMI <br />I <br />DCS 控制器 <br />RTU <br />工业打包系统 <br />工业规模 <br />工业机器人 <br />槽位 <br />计量 <br />可变频率驱动器  <br />机器人控制器 <br />伺服驱动器 <br />气压设备 <br />Marquee |
| IT | 域控制器 <br />DB 服务器 <br />工作站 <br />服务器 <br />终端工作站 <br />存储 <br />智慧型手機 <br />平板电脑 <br />备份服务器 |
| IoT | IP 照相机 <br />打印机  <br />冲孔打卡 <br />ATM <br />智能电视 <br />游戏控制台 <br />DVR <br />门控制面板 <br />HVAC <br />恒温调节器 <br />触发警报 <br />智能光线 <br />智能交换机 <br />触发探测器 <br />IP 电话 <br />警报系统 <br />警报警笛 <br />动作检测器 <br />升降机 <br />湿度传感器 <br />条形码扫描仪 <br />不间断电源 <br />人员计数器系统 <br />Intercom <br />转 |
| 网络 | 无线接入点 <br />路由器 <br />开关 <br />防火墙 <br />VPN 网关 <br />NTP 服务器 <br />Wifi Pineapple <br />物理位置 <br />I/o 适配器 <br /> 协议转换器 |

查看设备信息：

1.  选择侧边菜单上的 " **设备** "。

2. 右键单击某个设备，然后选择 " **查看属性**"。 将显示设备属性窗口。

3. 选择 "所需警报"，查看有关此设备的警报的详细信息。

### <a name="backplane-properties"></a>底板属性

如果 PLC 包含多个模块，这些模块分为多个机架和槽，则这些特征在模块卡之间可能会有所不同。 例如，如果 IP 地址和 MAC 地址相同，则固件可能会有所不同。

可以使用底板选项查看多个控制器/卡及其嵌套设备，作为一个具有各种定义的实体。 底板视图中的每个槽都代表基础设备-在其后面发现的设备。

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="底板属性":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="底板设备属性":::

一个底板最多可包含30个控制器卡和最多30个机架单元。 多个级别中包含的设备总数最多可达200个设备。

检测到底板详细信息时，底板窗格会显示在设备属性窗口中。

每个槽与基础设备的数量以及显示模块类型的图标一起显示。

| 图标 | 模块类型 |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="电源"::: | 电源 |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="模拟 i/o"::: | 模拟 i/o |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="通信适配器"::: | 通信适配器 |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="数字 i/o"::: | 数字 i/o |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="CPU"::: | CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="常规"::: | 泛型 |

选择槽时，将显示槽详细信息：

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="选择槽":::

若要查看槽后面的基础设备，请选择 **"在地图上查看**"。 该槽在设备映射中显示，所有基础模块和设备都连接到它。

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="在地图上查看":::

## <a name="view-a-timeline-of-events-for-the-device"></a>查看设备事件的时间线

查看与设备关联的事件的时间线。

查看时间线：

1. 右键单击映射中的某个设备。

2. 选择 " **显示事件**"。 此时将打开 "事件时间线" 窗口，其中包含有关为选定设备检测到的事件的信息。

有关详细信息，请参阅 [事件时间线](#event-timeline) 。

## <a name="analyze-programming-details-and-changes"></a>分析编程详细信息和更改

通过显示在网络设备上执行的编程事件和对代码更改进行分析来增强鉴证。 此信息可帮助你发现可疑编程活动，例如：

  - 人为错误：工程师正在编程错误的设备。

  - 损坏的编程自动化：由于自动化故障，编程错误地执行。

  - 攻击的系统：未经授权的用户登录到编程设备。

可以显示程控设备，并滚动浏览其他设备对其执行的各种编程更改。

查看由编程设备添加、更改、删除或重新加载的代码。 根据文件类型、日期或关注的时间搜索编程更改。

### <a name="when-to-review-programming-activity"></a>何时查看编程活动 

你可能需要查看编程活动：

  - 查看有关未经授权编程的警报后

  - 在对控制器进行了计划的更新后

  - 如果进程或计算机未正常工作 (查看执行最后一次更新的人员和) 

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="编程更改日志":::

其他选项可让你：

  - 使用星形标记感兴趣的事件。

  - 下载包含当前代码的 * .txt 文件。

### <a name="about-authorized-vs-unauthorized-programming-events"></a>关于授权与未经授权的编程事件 

未经授权的编程事件由尚未学习或手动定义为编程设备的设备执行。 授权的编程事件由已解析或手动定义为编程设备的设备执行。

"编程分析" 窗口显示已授权和未授权的编程事件。

### <a name="accessing-programming-details-and-changes"></a>访问编程详细信息和更改

从中访问 "编程分析" 窗口：

- [事件时间线](#event-timeline)

- [未经授权的编程警报](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>事件时间线

使用事件时间线显示检测到编程更改的事件的时间线。

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="事件时间线视图。":::

### <a name="unauthorized-programming-alerts"></a>未经授权的编程警报

未经授权的编程设备执行编程活动时，会触发警报。

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="未经授权的编程警报":::

> [!NOTE]
> 你还可以在设备属性窗口和设备清单中查看基本的编程信息。

### <a name="working-in-the-programming-timeline-window"></a>在编程时间线窗口中工作

本部分介绍如何查看编程文件和比较版本。 搜索发送到程控设备的特定文件。 基于以下内容搜索文件：

  - Date

  - 文件类型

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="编程时间线窗口":::

|编程时间线类型 | 说明 |
|--|--|
| 程控设备 | 提供有关已编程设备的详细信息，包括主机名和文件。 |
| 最近的事件 | 显示传感器检测到的最新事件50。 <br />若要突出显示某个事件，请将鼠标悬停在该事件上，并单击星形。 :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> 可以查看最后的50事件。 |
| 文件 | 显示在程控设备上为所选日期和文件大小检测到的文件。 <br /> 默认情况下，每个设备可显示的最大文件数为300。 <br /> 默认情况下，每个文件的最大文件大小为 15 MB。 |
| 文件状态 :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | 文件标签表示设备上文件的状态，包括： <br /> **已添加**：该文件已添加到所选日期或时间的端点。 <br /> **已更新**：该文件已在选定的日期或时间更新。 <br /> **已删除**：已删除此文件。 <br /> **无标签**：未更改文件。   |
| 编程设备 | 进行编程更改的设备。 多个设备可能在一台程控设备上执行了编程更改。 显示更改和已登录用户的主机名、日期或时间。 |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | 显示在程控设备上安装的当前文件。 |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | 下载显示的代码的文本文件。 |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | 将当前文件与在选定日期检测到的文件进行比较。 |

### <a name="choose-a-file-to-review"></a>选择要审阅的文件

本部分介绍如何选择要查看的文件。

选择要查看的文件：

1. 从 " **最近的事件** " 窗格中选择一个事件

2. 选择 "文件" 窗格中的文件。 文件将显示在 "当前" 窗格中。

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="选择要使用的文件。":::

### <a name="compare-files"></a>比较文件

本部分介绍如何比较编程文件。

要比较：

1. 从 "最近的事件" 窗格中选择一个事件。

2. 从 "文件" 窗格中选择文件。 文件将显示在 "当前" 窗格中。 可以将此文件与其他文件进行比较。

3. 选择比较指示器。

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="比较指标":::

   此窗口将显示在程控设备上检测到所选文件的所有日期。 此文件可能已由多个编程设备在程控设备上更新。

   检测到的差异数将显示在窗口的右上角。 你可能需要向下滚动以查看不同之处。

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="向下滚动到所选内容":::

   该数字由更改文本的相邻行进行计算。 例如，如果更改了八个连续的代码行 (删除、更新或添加) 该值将计算为一个差异。

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="编程时间线视图。":::

4. 选择一个日期。 在选定日期上检测到的文件将显示在窗口中。

5. 从 "最近的事件/文件" 窗格中选择的文件始终显示在右侧。

### <a name="device-programming-information-other-locations"></a>设备编程信息：其他位置

除了在编程时间线中查看详细信息外，还可以访问设备属性窗口和设备清单中的编程信息。

| 设备类型 | 说明 |
|--|--|
| 设备属性 | "设备属性" 窗口提供有关在 device\. 上检测到的最后一个编程事件的信息 :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="设备的属性"::: |
| 设备清单 | 设备清单指示设备是否是编程 device\。 :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="设备清单"::: |

## <a name="manage-device-information-from-the-map"></a>从地图管理设备信息

传感器不会直接更新或影响网络上的设备。 此处所做的更改只会影响分析设备的方式。

### <a name="delete-devices"></a>删除设备

如果认识到的信息不相关，你可能需要删除设备。 例如，

  - 工程工作站的合作伙伴承包商暂时连接到执行配置更新。 任务完成后，设备将被删除。

  - 由于网络发生了更改，某些设备将不再连接。

如果不删除设备，传感器将继续对其进行监视。 60天后会出现一个通知，建议你删除。

如果其他设备尝试访问设备，则可能会收到一条警报，指出设备无响应。 在这种情况下，您的网络可能配置错误。

设备将从设备映射、设备清单和数据挖掘报表中删除。 其他信息，例如：存储在小组件中的信息将会保留。

设备必须处于非活动状态至少10分钟才能将其删除。

从设备映射中删除设备：

1. 选择侧边菜单上的 " **设备** "。

2. 右键单击某个设备，然后选择 " **删除**"。

### <a name="merge-devices"></a>合并设备

在某些情况下，可能需要合并设备。 如果传感器发现与一个唯一设备关联的单独网络实体，则可能需要执行此项。 例如，

  - 使用四个网卡的 PLC。

  - 带有 WIFI 和物理卡的便携式计算机。
  
  - 具有两个或多个网卡的工作站。

合并时，指示传感器将两个设备的设备属性合并为一个设备的属性。 执行此操作时，将用新的设备属性详细信息更新设备属性窗口和传感器报告。

例如，如果合并两个设备，每个设备都有一个 IP 地址，则这两个 IP 地址将显示为设备属性窗口中的单独接口。 只能合并授权的设备。

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="设备属性窗口":::

事件时间线显示合并事件。

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="带有合并事件的事件时间线。":::

不能撤消设备合并。 如果错误地合并了两个设备，请删除设备，并等待传感器重新发现两者。

合并设备：

1. 选择两个设备 (按住 shift 键单击) ，然后右键单击其中一个设备。

2. 选择 " **合并** " 以合并设备。 完成合并最多可能需要2分钟。

3. 在 "设置合并设备属性" 对话框中，选择设备名称。

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="&quot;属性&quot; 对话框":::

4. 选择“保存” 。

### <a name="authorize-and-unauthorize-devices"></a>授权和 unauthorize 设备

在学习期间，网络中发现的所有设备被标识为授权设备。 **授权** 标签不会显示在设备映射的设备上。

在学习期间发现设备后，它将显示为未经授权的设备。 除了查看地图中的未授权设备之外，还可以在设备清单中看到它们。

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="设备清单":::

**新设备与未授权的设备**

学习期间检测到的新设备将显示 `New` 和 `Unauthorized` 标签。

如果移动地图上的设备或手动更改设备属性，则 `New` 会从设备图标中删除标签。

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>未经授权的设备-攻击向量和风险评估报表

未经授权的设备包括在风险评估报表和攻击向量报表中。

- **攻击向量报告：** 被标记为 "未授权" 的设备在攻击媒介中被解析为可疑的恶意设备，这可能是网络的威胁。

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="视图攻击向量报告":::

- **风险评估报表：** 标记为未授权的设备是：

  - 在风险评估报表中标识

手动授权或 unauthorize 设备：

1. 右键单击地图上的设备，然后选择 " **Unauthorize** "。

### <a name="mark-devices-as-important"></a>将设备标记为重要

你可以将重要的网络设备标记为重要，例如关键业务服务器。 这些设备在地图上用星形标记。 星号根据地图的缩放级别而有所不同。

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>重要设备-攻击向量和风险评估报表

重要设备是在生成风险评估报表和攻击向量报表时计算的。

  - 攻击向量报告标记为重要的设备在攻击媒介中被解析为攻击目标。 

  - 风险评估报表：在风险评估报表中提供安全分数时，将计算标记为重要的设备。

## <a name="generate-activity-reports-from-the-map"></a>从地图生成活动报告

为选定的设备生成1、6、12或24小时的活动报表。 该边栏选项卡中提供以下信息：

  - 类别：基于流量方案的基本检测信息。

  - 源和目标设备

  - 数据：附加信息脱离。

  - 上次查看的时间和日期。

您可以将报表保存为 Microsoft Excel 或 Word 文件。

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="“最近活动”":::

为设备生成活动报告：

1. 右键单击映射中的某个设备。

2. 选择活动报告。

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="查看活动报告。":::

## <a name="generate-attack-vector-reports-from-the-map"></a>从地图生成攻击向量报告

模拟攻击向量报告，以了解所选地图上的设备是否是易受攻击的目标。

攻击向量报告提供可利用设备的漏洞链的图形表示。 这些漏洞会使攻击者能够访问关键网络设备。 攻击向量模拟器会实时计算攻击媒介，并分析每个特定目标的所有攻击向量。

查看攻击向量报告中的设备：

1. 右键单击映射中的某个设备。

2. 选择 " **模拟攻击向量**"。 此时会打开 "攻击向量" 对话框，其中包含所选的作为攻击目标的设备。

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="添加攻击向量模拟":::

3. 将其余参数添加到对话框，然后选择 " **添加模拟**"。

## <a name="export-device-information-from-the-map"></a>从地图中导出设备信息

从映射中导出以下设备信息。

  - Microsoft Excel)  (设备详细信息

  - Microsoft Excel (的设备摘要) 

  - 带有组 (Microsoft Word) 的 word 文件

导出：

1. 从映射中选择 "导出" 图标。

1. 选择导出选项。

## <a name="next-steps"></a>后续步骤

[调查设备清单中的传感器检测](how-to-investigate-sensor-detections-in-a-device-inventory.md)
