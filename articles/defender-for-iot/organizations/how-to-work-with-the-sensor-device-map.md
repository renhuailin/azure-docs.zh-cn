---
title: 使用传感器设备映射
description: 设备映射提供检测到的网络设备的图形表示。 使用映射分析和管理设备信息、网络切片并生成报告。
ms.date: 1/7/2021
ms.topic: how-to
ms.openlocfilehash: f7579cbca618baef404236556993c9831dd84bdf
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015301"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>调查设备映射中的传感器检测

设备映射提供检测到的网络设备的图形表示。 使用映射可以：

  - 检索、分析和管理设备信息。

  - 分析网络切片，例如特定兴趣组或 Purdue 层。

  - 生成报告，例如导出设备详细信息和摘要。

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="设备映射的屏幕截图。":::

若要访问映射，请执行以下操作：

  - 从控制台主屏幕中选择“设备映射”。

## <a name="map-search-and-layout-tools"></a>映射搜索和布局工具

以下工具可在映射中使用。

用户角色决定“设备映射”窗口中可用的工具。 有关用户角色的详细信息，请参阅[创建和管理用户](how-to-create-and-manage-users.md)。

| 符号 | 说明 |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| 按 IP 地址或 MAC 地址搜索特定设备。 在文本框中输入 IP 地址或 MAC 地址。 映射将显示搜索的设备以及与其连接的设备。 |
| 组突出显示和筛选器 <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="组突出显示和筛选器的屏幕截图。"::: | 根据默认和自定义的设备组筛选或突出显示映射。 |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | IT 折叠视图，用于启用 OT 设备的集中视图以及对 IT 设备分组。  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | 维持映射中的当前设备排列。 例如，如果将设备拖到映射上的新位置，则退出映射时，设备将保留在这些位置。 |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | 适应屏幕 |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | - 查看为此设备标识的 Purdue 层，其中包括 Automatic、Process Control、Supervisory 和 Enterprise <br /> - 查看设备之间的连接。|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | 在广播和多播之间显示或隐藏。 |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | 根据设备上次与其他设备通信的时间筛选映射上的设备。 |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="通知" border="false"::: | 查看有关设备的通知。 例如，如果检测到使用现有 MAC 地址的设备的新 IP |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="导出" border="false"::: | 导出/导入设备信息。 |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="properties" border="false"::: | 查看所选设备的基本设备属性。 |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="放大" border="false":::或:::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="缩小" border="false"::: | 放大或缩小映射中的设备。 |

## <a name="view-ot-elements-only"></a>仅查看 OT 元素

默认情况下，IT 设备按子网自动聚合，因此映射视图将重点放在 OT 和 ICS 网络上。 IT 网络元素的表示形式会折叠为最小状态，这减少了映射上显示的设备总数，并清晰显示了 OT 和 ICS 网络元素。

每个子网在设备映射上都表示为单个实体，其中包括交互式折叠和展开功能，可查看 IT 子网的详细信息并返回。

下图显示折叠的 IT 子网，其中包含 27 个 IT 网络元素。

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="包含 27 个 IT 网络元素的折叠 IT 子网":::

若要启用 IT 网络折叠功能，请执行以下操作：

- 在“系统设置”窗口中，确保已启用“切换 IT 网络分组”功能。

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="“系统设置”窗口":::

若要展开 IT 子网，请执行以下操作：

1. 若要区分 IT 网络和 OT 网络，请从“系统设置”屏幕中选择“子网”。

   > [!NOTE]
   > 建议使用有意义的名称为每个子网命名，以便用户能够轻松识别，从而区分 IT 网络和 OT 网络。

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="子网配置":::

2. 在“编辑子网配置”窗口中，清除要定义为 IT 子网的每个子网的“ICS 子网”复选框。 IT 子网在设备映射中显示为折叠状态，并包含 IT 网络中 ICS 设备（例如控制器或 PLC）的通知。

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="编辑子网配置":::

3. 若要在映射上展开 IT 网络，请在“设备”窗口中右键单击它，然后选择“展开网络”。

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="展开网络视图。":::

4. 系统将显示确认框，通知你无法重做布局更改。

5. 选择“确定”。 IT 子网元素将在映射上显示。

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="确定":::

若要折叠 IT 子网，请执行以下操作：

1.  在左侧窗格中，选择“设备”。

2. 在“设备”窗口中，选择折叠图标。 红色数字指示映射上当前显示的已展开 IT 子网的数量。

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="设备窗口":::

3. 选择要折叠的子网，或选择“全部折叠”。 所选子网在映射上以折叠状态显示。

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="全部折叠":::

折叠图标会使用更新后的已展开 IT 子网数进行更新。

## <a name="view-or-highlight-device-groups"></a>查看或突出显示设备组

可以基于设备组自定义映射的显示。 例如，与特定 OT 协议、VLAN 或子网关联的设备组。 可以使用预定义的组，也可以创建自定义组。

分组查看依据：

  - **突出显示**：使用蓝色突出显示属于特定组的设备。

  - **筛选**：仅显示属于特定组的设备。

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="端口的标准视图":::

可以使用以下预定义的组：

| 组名称 | 描述 |
|--|--|
| **已知应用程序** | 使用保留端口（例如 TCP）的设备。  |
| **非标准端口（默认）** | 使用非标准端口或未分配别名的端口的设备。 |
| **OT 协议（默认）** | 处理已知 OT 流量的设备。 |
| **授权（默认）** | 学习过程中在网络中发现或在网络上获得正式授权的设备。 |
| **设备清单筛选器** | 根据设备清单表中保存的筛选器分组的设备。 |
| **轮询间隔** | 按轮询间隔分组的设备。 轮询间隔根据循环通道或周期自动生成。 例如，15.0 秒、3.0 秒、1.5 秒或任何间隔。 查看此信息有助于了解系统是否轮询过快或过慢。 |
| **编程** | 工程站和编程计算机。 |
| **子网** | 属于特定子网的设备。 |
| **VLAN** | 与特定 VLAN ID 关联的设备。 |
| **跨子网连接** | 从一个子网通信到另一子网的设备。 |
| **已固定警报** | 用户为其固定了警报的设备。 |
| **攻击途径模拟** | 攻击途径报告中检测到的易受攻击设备。 若要在映射上查看这些设备，请在生成攻击途径时选中“在设备映射上显示”复选框。 :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="添加攻击途径模拟":::。 |
| **上次出现时间** | 按上次查看时间范围（例如：一小时、六小时、一天、七天）分组的设备。 |
| **不在 Active Directory 中** | 所有不与 Active Directory 通信的非 PLC 设备。 |

若要突出显示或筛选设备，请执行以下操作：

1. 在侧边菜单中选择“设备映射”。

2. 选择“筛选”图标。 :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="菜单":::

3. 在“组”窗格中，选择要突出显示或筛选设备的组。

4. 选择“突出显示”或“筛选”。 切换同一选项以删除突出显示或筛选器。

## <a name="define-custom-groups"></a>定义自定义组

除查看预定义的组外，还可以定义自定义组。 这些组将在设备映射、设备清单和数据挖掘报告中显示。

> [!NOTE]
> 还可以从设备清单中创建组。

若要创建组，请执行以下操作：

1. 在侧边菜单中选择“设备”。 此时将显示设备映射。

1. 选择 :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="组设置"::: 以显示组设置。

1. 选择 :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="组"::: 以创建新的自定义组。

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="创建自定义组屏幕":::

1. 添加组名，最多 30 个字符。

1. 选择相关设备，如下所示：

   - 通过从列表中选择设备（通过箭头按钮选择），从此菜单添加设备，<br /> 或者， <br /> 
   - 通过从选定的组复制设备（通过箭头按钮选择），从此菜单添加设备

1. 选择“添加组”以将现有组添加到自定义组。

### <a name="add-devices-to-a-custom-group"></a>将设备添加到自定义组

可以将设备添加到自定义组，也可以创建新的自定义组和设备。

1. 右键单击映射上的设备。

1. 选择“添加到组”。

1. 在“组”字段中输入组名，然后选择 +。 此时将显示新组。 如果组已经存在，则会添加到现有的自定义组中。

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="组名":::

1. 重复步骤 1-3，将设备添加到组中。

## <a name="map-zoom-views"></a>映射缩放视图

分析大型网络时，使用映射视图有助于加快取证。

可以显示三种设备详细信息视图：

  - [鸟瞰视图](#birds-eye-view)

  - [设备类型和连接视图](#device-type-and-connection-view)

  - [详细视图](#detailed-view)

### <a name="birds-eye-view"></a>鸟瞰视图

此视图提供按如下方式表示的设备概览视图：

  - 红点指示带有警报的设备

  - 星形点指示标记为重要的设备

  - 黑点指示没有警报的设备

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="鸟瞰视图":::

### <a name="device-type-and-connection-view"></a>设备类型和连接视图 

此视图在映射中显示以图标表示的设备，以突出显示带有警报的设备、设备类型和连接的设备。

  - 带有警报的设备以红色圆圈显示

  - 没有警报的设备以灰色圆圈显示

  - 以星形显示的设备标记为重要

设备类型图标随连接的设备一起显示。

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="连接视图":::

### <a name="detailed-view"></a>详细视图 

详细视图显示设备和设备标签，以及具有以下信息的指示器：

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="详细视图":::

### <a name="control-the-zoom-view"></a>控制缩放视图

显示的映射视图取决于映射的缩放级别。 通过更改缩放级别可以在映射视图之间切换。

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="控制缩放视图":::

### <a name="enable-simplified-zoom-views"></a>启用简化缩放视图

希望安全分析师和 RO 用户访问鸟瞰视图以及设备类型和连接视图的管理员应启用简化视图选项。

若要启用简化映射视图，请执行以下操作：

  - 选择“系统设置”，然后切换“简化映射视图”选项。

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="简化映射视图":::

## <a name="learn-more-about-devices"></a>详细了解设备

可以使用各种工具详细了解形成设备映射的设备：

- [设备标签和指示器](#device-labels-and-indicators)

- [设备快速视图](#device-quick-views)

- [查看和管理设备属性](#view-and-manage-device-properties)

- [查看设备类型](#view-device-types)

- [底板](#backplane-properties)

- [查看设备事件的时间线](#view-a-timeline-of-events-for-the-device)

- [分析编程详细信息和更改](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>设备标签和指示器

以下标签和指示器可能会在映射中的设备上出现：

| 设备标签 | 说明 |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="IP 主机名"::: | IP 地址主机名和 IP 地址，或子网地址 |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="警报数"::: | 与设备关联的警报数 |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | 设备类型图标，例如存储、PLC 或历史数据库。 |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="已分组的设备"::: | IT 网络的子网中的已分组设备数。 在此示例中为 8。 |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="设备学习期"::: | 在学习期过后检测到且未获得网络设备授权的设备。 |
| 实线 | 设备之间的逻辑连接 |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="新设备"::: | 学习完成后发现的新设备。 |

### <a name="device-quick-views"></a>设备快速视图

从映射访问设备属性和连接。

若要打开快速属性菜单，请执行以下操作：

  - 选择快速属性菜单 :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="快速属性菜单":::。

#### <a name="quick-device-properties"></a>快速设备属性

打开“快速属性”屏幕时，选择一个或多个设备以查看这些设备的突出显示：

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="快速设备属性":::

#### <a name="quick-connection-properties"></a>快速连接属性

打开“快速属性”屏幕时，选择一个连接以查看此连接中使用的协议以及上次查看它们的时间：

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="快速连接属性":::

## <a name="view-and-manage-device-properties"></a>查看和管理设备属性

可以查看映射上显示的每个设备的设备属性。 例如，设备名称，类型或 OS，固件或供应商。

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="查看和管理设备属性":::

可以手动更新以下信息。 手动输入的信息将替代 Defender for IoT 发现的信息。

  - 名称

  - 类型

  - (OS)

  - Purdue 层

  - 说明

| 项目 | 说明 |
|--|--|
| 基本信息 | 需要的基本信息。 |
| 名称 | 设备名称。 <br /> 默认情况下，传感器发现的设备名称是其在网络中定义的设备名称。 例如，在 DNS 服务器中定义的名称。 <br /> 如果未定义此类名称，则设备 IP 地址将在此字段中显示。 <br /> 可以手动更改设备名称。 为设备提供反映其功能的有意义的名称。 |
| 类型 | 传感器检测到的设备类型。 <br /> 有关详细信息，请参阅[查看设备类型](#view-device-types)。 |
| Vendor | 设备供应商。 由设备 MAC 地址的前导字符决定。 此字段为只读。 |
| 操作系统 | 传感器检测到的设备 OS。 |
| Purdue 层 | 传感器为此设备标识的 Purdue 层，其中包括： <br /> - Automatic <br /> - Process Control <br /> - Supervisory <br /> - Enterprise |
| 说明 | 自由文本字段。 <br /> 添加有关设备的详细信息。 |
| 属性 | 学习期间发现的关于设备且不属于其他类别的所有其他信息都将在属性部分中显示。 <br /> 该信息为 RO。 |
| 设置 | 可以手动更改设备设置以防止误报： <br /> -  **授权设备**：学习期间，网络中发现的所有设备均标识为授权设备。 学习期过后发现设备时，默认情况下显示为未经授权的设备。 可以手动更改此定义。 <br /> -  **已知是扫描器**：如果知道此设备已知为扫描器并且无需提醒你，请启用此选项。 <br /> -  **编程设备**：如果知道此设备已知为编程设备并用于进行编程更改，请启用此选项。 将其标识为编程设备将防止针对源自此资产的编程更改发出警报。 |
| 自定义组 | 设备映射中此设备参与的自定义组。 |
| 状态 | 设备的安全和授权状态： <br /> - 没有警报时，状态为 `Secured` <br /> - 存在关于设备的警报时，将显示警报数 <br /> - 将为学习期过后添加到网络中的设备显示状态 `Unauthorized`。 可以在设置中将设备手动定义为 `Authorized Device` <br /> - 如果此设备的地址定义为动态地址，则 `DHCP` 会添加到状态中。 |


| 网络 | 说明 |
|--|--|
| 接口 | 设备接口。 RO 字段。 |
| 协议 | 设备使用的协议。 RO 字段。 |
| 固件 | 如果有底板信息可用，则不会显示固件信息。 |
| 地址 | 设备 IP 地址。 |
| 串行 | 设备序列号。 |
| 模块地址 | 设备型号和插槽编号或 ID。 |
| 建模 | 设备型号。 |
| 固件版本 | 固件版本号。 |

若要查看设备信息，请执行以下操作：

1. 在侧边菜单中选择“设备”。

2. 右键单击设备，然后选择“查看属性”。 此时将显示“设备属性”窗口。

3. 在此窗口底部选择所需的警报，以查看有关此设备的警报的详细信息。

### <a name="view-device-types"></a>查看设备类型

设备类型在设备发现过程中由传感器自动标识。 可以手动更改类型。

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="查看设备类型":::

下表列出系统中的所有类型：

| 类别 | 设备类型 |
|--|--|
| ICS | 工程站 <br /> PLC <br />历史数据库 <br />HMI <br />IED <br />DCS 控制器 <br />RTU <br />工业包装系统 <br />工业规模 <br />工业机器人 <br />槽位 <br />计量 <br />变频驱动器  <br />机器人控制器 <br />伺服驱动器 <br />气动装置 <br />Marquee |
| IT | 域控制器 <br />数据库服务器 <br />工作站 <br />服务器 <br />终端站 <br />存储 <br />智慧型手機 <br />平板电脑 <br />备份服务器 |
| IoT | IP 摄像机 <br />打印机  <br />打卡机 <br />ATM <br />智能电视 <br />游戏机 <br />DVR <br />门控制面板 <br />HVAC <br />恒温调节器 <br />火警警报器 <br />智能灯 <br />智能开关 <br />火警检测器 <br />IP 电话 <br />警报系统 <br />警报器 <br />动作检测器 <br />升降机 <br />湿度传感器 <br />条形码扫描仪 <br />不间断电源 <br />人员计数器系统 <br />Intercom <br />十字转门 |
| 网络 | 无线接入点 <br />路由器 <br />开关 <br />防火墙 <br />VPN 网关 <br />NTP 服务器 <br />Wifi Pineapple <br />物理位置 <br />I/O 适配器 <br /> 协议转换器 |

若要查看设备信息，请执行以下操作：

1.  在侧边菜单中选择“设备”。

2. 右键单击设备，然后选择“查看属性”。 此时将显示“设备属性”窗口。

3. 选择所需的警报，以查看有关此设备的警报的详细信息。

### <a name="backplane-properties"></a>底板属性

如果 PLC 包含多个分为机架和插槽的模块，则各模块卡的特征可能会有所不同。 例如，如果 IP 地址和 MAC 地址相同，则固件可能会不同。

可以使用底板选项将多个控制器/卡及其嵌套设备作为一个具有各种定义的实体进行查看。 底板视图中的每个插槽都表示基础设备，即在其背后发现的设备。

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="底板属性":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="底板设备属性":::

一个底板可以包含最多 30 个控制卡和最多 30 个机架单元。 多个级别中包含的设备总数可以达到 200 个设备。

检测到底板详细信息后，“设备属性”窗口中将显示“底板”窗格。

每个插槽都会显示基础设备的数量以及显示模块类型的图标。

| 图标 | 模块类型 |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="电源"::: | 电源 |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="模拟 I/O"::: | 模拟 I/O |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="通信适配器"::: | 通信适配器 |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="数字 I/O"::: | 数字 I/O |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="CPU"::: | CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="常规"::: | 泛型 |

选择插槽后，将显示插槽详细信息：

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="选择插槽":::

若要查看插槽背后的基础设备，请选择“在映射上查看”。 插槽会在设备映射中显示，并包括所有连接到它的基础模块和设备。

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="在映射上查看":::

## <a name="view-a-timeline-of-events-for-the-device"></a>查看设备事件的时间线

查看与设备关联的事件的时间线。

若要查看时间线，请执行以下操作：

1. 右键单击映射中的设备。

2. 选择“显示事件”。 “事件时间线”窗口将打开，其中包含有关针对选定设备检测到的事件的信息。

有关详细信息，请参阅[事件时间线](#event-timeline)。

## <a name="analyze-programming-details-and-changes"></a>分析编程详细信息和更改

通过显示在网络设备上执行的编程事件并分析代码更改来增强取证。 此信息有助于发现可疑的编程活动，例如：

  - 人为错误：工程师正在编程错误的设备。

  - 编程自动化损坏：由于自动化故障而错误地执行了编程。

  - 遭到黑客攻击的系统：未经授权的用户登录了编程设备。

可以显示已编程的设备，并滚动浏览其他设备对其进行的各种编程更改。

查看由编程设备添加、更改、删除或重载的代码。 根据感兴趣的文件类型、日期或时间搜索编程更改。

### <a name="when-to-review-programming-activity"></a>查看编程活动的时间 

在以下情况下，你可能需要查看编程活动：

  - 查看有关未经授权编程的警报后

  - 控制器进行计划更新后

  - 进程或计算机未正常工作时（查看执行上次更新的人员以及时间）

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="编程更改日志":::

借助其他选项，你可以：

  - 使用星形标记感兴趣的事件。

  - 下载包含当前代码的 *.txt 文件。

### <a name="about-authorized-vs-unauthorized-programming-events"></a>关于授权与未经授权的编程事件 

未经授权的编程事件由尚未学习或尚未手动定义为编程设备的设备执行。 授权的编程事件由已解析或已手动定义为编程设备的设备执行。

“编程分析”窗口同时显示授权和未经授权的编程事件。

### <a name="accessing-programming-details-and-changes"></a>访问编程详细信息和更改

从以下位置访问“编程分析”窗口：

- [事件时间线](#event-timeline)

- [未经授权的编程警报](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>事件时间线

使用事件时间线来显示检测到编程更改的事件的时间线。

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="事件时间线视图。":::

### <a name="unauthorized-programming-alerts"></a>未经授权的编程警报

当未经授权的编程设备执行编程活动时，将触发警报。

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="未经授权的编程警报":::

> [!NOTE]
> 还可以在“设备属性”窗口和设备清单中查看基本编程信息。

### <a name="working-in-the-programming-timeline-window"></a>在“编程时间线”窗口中执行操作

本部分介绍如何查看编程文件以及比较版本。 搜索发送到已编程设备的特定文件。 根据以下信息搜索文件：

  - Date

  - 文件类型

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="“编程时间线”窗口":::

|编程时间线类型 | 说明 |
|--|--|
| 已编程设备 | 提供有关已编程设备的详细信息，包括主机名和文件。 |
| 最近的事件 | 显示传感器最近检测到的 50 个事件。 <br />若要突出显示某个事件，请将鼠标悬停在事件上，然后单击星形图标。 :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> 可以查看最后 50 个事件。 |
| 文件 | 显示已编程设备上在所选日期检测到的文件以及文件大小。 <br /> 默认情况下，每个设备可显示的最大文件数为 300 个。 <br /> 默认情况下，每个文件的最大文件大小为 15 MB。 |
| 文件状态 :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | 文件标签指示设备上文件的状态，其中包括： <br /> **已添加**：文件已在所选日期或时间添加到终结点。 <br /> **已更新**：文件已在所选日期或时间更新。 <br /> **已删除**：此文件已删除。 <br /> **无标签**：未更改文件。   |
| 编程设备 | 进行编程更改的设备。 多个设备可能已在一个已编程设备上进行了编程更改。 将显示主机名、更改的日期或时间以及登录的用户。 |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | 显示安装在已编程设备上的当前文件。 |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | 下载包含显示的代码的文本文件。 |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | 将当前文件与在选定日期检测到的文件进行比较。 |

### <a name="choose-a-file-to-review"></a>选择要查看的文件

本部分介绍如何选择要查看的文件。

若要选择要查看的文件，请执行以下操作：

1. 从“最近的事件”窗格中选择事件

2. 从“文件”窗格中选择文件。 文件将在“当前”窗格中显示。

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="选择要使用的文件。":::

### <a name="compare-files"></a>比较文件

本部分介绍如何比较编程文件。

若要进行比较，请执行以下操作：

1. 从“最近的事件”窗格中选择事件。

2. 从“文件”窗格中选择文件。 文件将在“当前”窗格中显示。 可以将此文件与其他文件进行比较。

3. 选择比较指示器。

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="比较指示器":::

   该窗口显示在已编程设备上检测到所选文件的所有日期。 文件可能已由多个编程设备在已编程设备上更新。

   检测到的差异数量将在窗口右上角显示。 可能需要向下滚动以查看差异。

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="向下滚动到你的选择":::

   该数字根据更改文本的相邻行计算得出。 例如，如果八行连续的代码被更改（删除、更新或添加），则将计为一个差异。

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="编程时间线视图。":::

4. 选择日期。 在所选日期检测到的文件将在窗口中显示。

5. 从“最近的事件/文件”窗格中选择的文件始终在右侧显示。

### <a name="device-programming-information-other-locations"></a>设备编程信息：其他位置

除了在“编程时间线”中查看详细信息之外，还可以在“设备属性”窗口和设备清单中访问编程信息。

| 设备类型 | 说明 |
|--|--|
| 设备属性 | “设备属性”窗口提供有关在设备上检测到的最后一个编程事件的信息。 :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="设备的属性"::: |
| 设备清单 | 设备清单指示设备是否为编程设备。 :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="设备清单"::: |

## <a name="manage-device-information-from-the-map"></a>从映射管理设备信息

传感器不会直接更新或影响网络上的设备。 此处所做的更改仅影响分析设备的方式。

### <a name="delete-devices"></a>删除设备

如果学习到的信息不相关，则可能要删除设备。 例如，

  - 工程工作站的合作伙伴承包商会临时连接以执行配置更新。 任务完成后，将删除设备。

  - 由于网络更改，一些设备不再连接。

如果不删除设备，传感器将继续对其进行监视。 60 天后，将显示一条通知，建议进行删除。

你可能会收到一条警报，指示设备在另一台设备尝试访问它时无响应。 在此例中，网络可能配置错误。

设备将从设备映射、设备清单和数据挖掘报告中删除。 将保留其他信息，例如存储在小组件中的信息。

设备必须处于非活动状态至少 10 分钟才能将其删除。

若要从设备映射中删除设备，请执行以下操作：

1. 在侧边菜单中选择“设备”。

2. 右键单击设备，然后选择“删除”。

### <a name="merge-devices"></a>合并设备

在某些情况下，可能需要合并设备。 如果传感器发现与一个唯一设备关联的单独网络实体，则可能需要执行此操作。 例如，

  - 具有四个网卡的 PLC。

  - 具有 WIFI 和物理卡的笔记本电脑。
  
  - 具有两个或更多个网卡的工作站。

合并时，指示传感器将两个设备的设备属性合而为一。 执行此操作时，“设备属性”窗口和传感器报告将使用新的设备属性详细信息进行更新。

例如，如果合并两个设备，每个设备都有一个 IP 地址，则两个 IP 地址将在“设备属性”窗口中显示为单独的接口。 只能合并授权的设备。

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="“设备属性”窗口":::

事件时间线显示合并事件。

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="具有合并事件的事件时间线。":::

无法撤消设备合并。 如果错误地合并了两个设备，请删除设备，然后等待传感器重新发现这两个设备。

若要合并设备，请执行以下操作：

1. 选择两个设备（按住 Shift 键并单击），然后右键单击其中一个设备。

2. 选择“合并”以合并设备。 完成合并最多可能需要 2 分钟。

3. 在“设置合并设备属性”对话框中，选择设备名称。

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="属性对话框":::

4. 选择“保存”。

### <a name="authorize-and-unauthorize-devices"></a>授权和取消授权设备

学习期间，在网络中发现的所有设备均标识为授权设备。 在设备映射中，“已授权”标签不会在这些设备上显示。

学习期过后发现设备时，它会显示为未经授权的设备。 除了在映射中查看未经授权的设备之外，还可以在设备清单中查看它们。

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="设备清单":::

**新设备与未经授权的设备**

学习期过后检测到的新设备将显示 `New` 和 `Unauthorized` 标签。

如果在映射上移动设备或手动更改设备属性，则会从设备图标中删除 `New` 标签。

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>未经授权的设备 - 攻击途径和风险评估报告

未经授权的设备包括在风险评估报告和攻击途径报告中。

- **攻击途径报告**：标记为未经授权的设备在攻击途径中解析为可能会对网络造成威胁的可疑恶意设备。

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="查看攻击途径报告":::

- **风险评估报告**：标记为未经授权的设备：

  - 在风险评估报告中标识

若要手动授权或取消授权设备，请执行以下操作：

1. 右键单击映射上的设备，然后选择“取消授权”

### <a name="mark-devices-as-important"></a>将设备标记为重要

可以将重要的网络设备标记为重要，例如，业务关键型服务器。 这些设备在映射上标有星形。 星形因映射的缩放级别而异。

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>重要设备 - 攻击途径和风险评估报告

生成风险评估报告和攻击途径报告时，将计算重要设备。

  - 攻击途径报告：标记为重要的设备在攻击途径中解析为攻击目标。 

  - 风险评估报告：在风险评估报告中提供安全分数时，将计算标记为重要的设备。

## <a name="generate-activity-reports-from-the-map"></a>从映射生成活动报告

为所选设备生成 1 小时、6 小时、12 小时或 24 小时的活动报告。 该边栏选项卡中提供以下信息：

  - 类别：基于流量方案的基本检测信息。

  - 源设备和目标设备

  - 数据：检测到的其他信息。

  - 上次查看的时间和日期。

可以将报告另存为 Microsoft Excel 或 Word 文件。

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="“最近活动”":::

若要生成设备的活动报告，请执行以下操作：

1. 右键单击映射中的设备。

2. 选择活动报告。

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="查看活动报告。":::

## <a name="generate-attack-vector-reports-from-the-map"></a>从映射生成攻击途径报告

模拟攻击途径报告，以了解所选映射上的设备是否为易受攻击的目标。

攻击途径报告提供可利用设备的漏洞链的图形表示。 这些漏洞可向攻击者授予访问关键网络设备的权限。 攻击途径模拟器实时计算攻击途径，并按特定目标分析所有攻击途径。

若要在攻击途径报告中查看设备，请执行以下操作：

1. 右键单击映射中的设备。

2. 选择“模拟攻击途径”。 此时将打开“攻击途径”对话框，其中包含选择作为攻击目标的设备。

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="添加攻击途径模拟":::

3. 将其余参数添加到对话框中，然后选择“添加模拟”。

## <a name="export-device-information-from-the-map"></a>从映射导出设备信息

从映射导出以下设备信息。

  - 设备详细信息 (Microsoft Excel)

  - 设备摘要 (Microsoft Excel)

  - 包含组的 Word 文件 (Microsoft Word)

若要导出，请执行以下操作：

1. 从映射中选择导出图标。

1. 选择导出选项。

## <a name="see-also"></a>另请参阅

[调查设备清单中的传感器检测](how-to-investigate-sensor-detections-in-a-device-inventory.md)
