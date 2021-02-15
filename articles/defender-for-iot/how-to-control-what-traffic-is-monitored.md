---
title: 控制要监视的流量
description: 传感器会自动对其和通信执行深度数据包检测，并解析网络设备的相关信息，例如设备属性和网络行为。 有几种工具可用于控制每个传感器检测到的流量类型。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: a7af817074400bedd8f07adaefc35309566dc931
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522387"
---
# <a name="control-what-traffic-is-monitored"></a>控制要监视的流量

传感器会自动对其和通信执行深度数据包检测，并解析网络设备的相关信息，例如设备属性和行为。 有几种工具可用于控制每个传感器检测到的流量类型。

## <a name="learning-and-smart-it-learning-modes"></a>学习和智能 IT 学习模式

学习模式指示传感器了解网络的常规活动。 例如，网络中发现的设备、网络中检测到的协议、特定设备之间的文件传输等。 此活动将成为你的网络基线。

学习模式在安装后会自动启用，并将一直保持启用状态，直到关闭为止。 大约的学习模式期限介于两到六周之间，具体取决于网络大小和复杂性。 在这段时间之后，当学习模式处于禁用状态时，检测到的任何新活动将触发警报。 当策略引擎发现与已知基线的偏差时，会触发警报。

在学习期间完成并且学习模式处于禁用状态后，传感器可能会检测到正常 IT 活动（如 DNS 和 HTTP 请求）的结果，这是一种异常的高级基准更改。 该活动称为非确定性 IT 行为。 此行为可能还会触发不必要的策略冲突警报和系统通知。 若要减少这些警报和通知的数量，可以启用 **智能 IT 学习** 功能。

启用智能 IT 学习时，传感器会根据特定的警报方案跟踪网络流量，这些网络流量会生成不确定性的 IT 行为。

传感器监视此流量7天。 如果它在七天内检测到相同的非确定性 IT 流量，它将继续监视另外七天的流量。 如果未检测到超过7天的流量，则会为该方案禁用智能 IT 学习。 为该方案检测到的新流量仅会生成警报和通知。

使用智能 IT 学习可帮助减少不必要的警报数以及干扰 IT 方案引起的通知。

如果你的传感器由本地管理控制台控制，则无法禁用学习模式。 在这种情况下，只能从管理控制台中禁用学习模式。

默认情况下， (学习和智能 IT 学习) 启用了学习功能。

启用或禁用学习：

- 选择 " **系统设置** "，并切换 **学习** 和 **智能 IT 学习** 选项。

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="系统设置切换屏幕。":::

## <a name="configure-subnets"></a>配置子网

子网配置影响你在设备映射中看到设备的方式。

默认情况下，传感器会发现子网设置，并使用此信息填充 " **子网配置** " 对话框。

若要在 OT 设备上启用焦点，IT 设备会按子网在设备映射中自动聚合。 每个子网都显示为地图上的单个实体，包括交互式折叠/展开功能，可 "向下钻取" 到 IT 子网并返回。

使用子网时，请选择 ICS 子网来识别 OT 子网。 然后，你可以将地图视图集中于 OT 和 ICS 网络上，并折叠到 IT 网络元素的最小表示形式。 这一努力减少了地图上显示的设备总数，并清楚地描述了 OT 和 ICS 网络元素。

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="显示筛选到网络的屏幕截图。":::

你可以通过导出发现的数据、手动更改它，然后导入手动定义的子网列表，来更改配置或手动更改子网信息。 有关导出和导入的详细信息，请参阅 [导入设备信息](how-to-import-device-information.md)。

在某些情况下（例如使用公共范围作为内部范围的环境），可以通过选择 " **不检测 Internet 活动** " 选项指示传感器将所有子网解析为内部子网。 选择该选项时：

- 公共 IP 地址将被视为本地地址。

- 不会发送有关未经授权的 internet 活动的警报，从而减少了在外部地址收到的通知和警报。

若要配置子网：

1. 在侧边菜单中，选择 " **系统设置**"。

2. 在 " **系统设置** " 窗口中，选择 " **子网**"。

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="屏幕截图，显示子网配置屏幕。"::: 

3. 要在发现新设备时自动添加子网，请选择 " **自动子网学习** "。

4. 若要将所有子网解析为内部子网，请选择 " **不检测 Internet 活动**"。

5. 选择 " **添加网络** "，并为每个子网定义以下参数：

    - 子网 IP 地址。
    - 子网掩码地址。
    - 子网名称。 建议你将每个子网名称命名为有意义的名称，你可以轻松地识别这些子网，以便区分其网络。 名称最多可包含60个字符。

6. 若要将此子网标记为 OT 子网，请选择 " **ICS 子** 网"。

7. 若要根据 Purdue 级别排列地图，请选择 " **分离**"。

8. 若要删除子网，请选择 :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false"::: 。

9. 若要删除所有子网，请选择 " **全部清除**"。

10. 若要导出配置的子网，请选择 " **导出**"。 子网表将下载到工作站。

11. 选择“保存” 。

### <a name="importing-information"></a>导入信息 

若要导入子网信息，请选择 " **导入** " 并选择要导入的 CSV 文件。 将用导入的信息更新子网信息。 如果你重要的是空字段，你会丢失数据。

## <a name="detection-engines"></a>检测引擎

自学习分析引擎无需更新签名或定义规则。 这些引擎使用特定于 ICS 的行为分析和数据科学连续分析网络流量，以应对异常、恶意软件、操作问题、协议冲突和基线网络活动偏差。

> [!NOTE]
> 建议启用所有安全引擎。

当引擎检测到偏差时，会触发警报。 你可以从警报屏幕或合作伙伴系统中查看和管理警报。

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="显示偏差检测的屏幕截图。":::

触发警报的引擎的名称出现在警报标题下。

### <a name="protocol-violation-engine"></a>协议冲突引擎 

当数据包结构或字段值不符合协议规范时，将发生协议冲突。

示例方案： *" (函数代码零) " 的非法 MODBUS 操作* 。 此警报表明主要设备向辅助设备发送了具有函数代码0的请求。 根据协议规范，不允许执行此操作，辅助设备可能无法正确处理输入。

### <a name="policy-violation-engine"></a>策略冲突引擎

发生策略冲突时，与已学习或配置的设置中定义的基线行为发生偏差。

示例方案： *"未经授权的 HTTP 用户代理"* 警报。 此警报表示策略未学习或批准的应用程序在设备上用作 HTTP 客户端。 这可能是该设备上的新 web 浏览器或应用程序。

### <a name="malware-engine"></a>恶意软件引擎

恶意软件引擎会检测恶意网络活动。

示例方案： *"怀疑恶意活动 (Stuxnet) "* 警报。 此警报表明传感器检测到与 Stuxnet 恶意软件相关的可疑网络活动。 此恶意软件是一种针对工业控制和 SCADA 网络的高级持久威胁。

### <a name="anomaly-engine"></a>异常引擎

异常引擎检测网络行为中的异常。

示例方案： *"通信通道中的定期行为"* 警报。 组件检查网络连接，并查找数据传输的周期性和循环行为。 此行为在工业网络中很常见。

### <a name="operational-engine"></a>操作引擎

操作引擎检测到操作事件或发生故障的实体。

示例方案： *"怀疑设备断开连接 (无响应) "* 警报。 当设备不响应预定义时间段的任何类型的请求时，将引发此警报。 此警报可能表示设备关闭、断开连接或出现故障。

### <a name="enable-and-disable-engines"></a>启用和禁用引擎

禁用策略引擎时，该引擎生成的信息将无法供传感器使用。 例如，如果禁用异常引擎，则不会收到有关网络异常的警报。 如果创建了转发规则，则不会发送引擎所学习的异常。 若要启用或禁用策略引擎，请为特定引擎选择 " **已启用** " 或 " **已禁用** "。

整体分数显示在 " **系统设置** " 屏幕的右下角。 分数指示通过威胁防护引擎启用的可用保护的百分比。 每个引擎都是可用保护的20%。

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="显示分数的屏幕截图。":::

## <a name="configure-dhcp-address-ranges"></a>配置 DHCP 地址范围

网络可能由静态和动态 IP 地址组成。 通常，通过历史学家、控制器和网络基础结构设备（如交换机和路由器）在网络上找到静态地址。 动态 IP 分配通常在具有便携式计算机、电脑、智能手机和其他便携式设备的来宾网络上实现， (在不同位置) 使用 Wi-Fi 或 LAN 物理连接。

如果你使用的是动态网络，则会处理分配新 IP 地址时所发生的 IP 地址更改。 可以通过定义 DHCP 地址范围来完成此操作。

例如，当 DHCP 服务器分配 IP 地址时，可能会发生更改。

在每个传感器上定义动态 IP 地址可在 IP 地址更改实例中实现全面的透明支持。 这可确保为每个唯一设备提供全面的报告。

传感器控制台显示与设备关联的最新 IP 地址，并指示哪些设备是动态的。 例如：

- 数据挖掘报表和设备清单报表将从设备中获知的所有活动合并为一个实体，而不考虑 IP 地址的更改。 这些报告表明哪些地址定义为 DHCP 地址。

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="显示设备清单的屏幕截图。":::

- " **设备属性** " 窗口指示设备是否已定义为 DHCP 设备。

设置 DHCP 地址范围：

1.  在侧菜单中，从 "**系统设置**" 窗口中选择 " **DHCP 范围**"。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="显示 DHCP 范围选择的屏幕截图。":::

2.  通过设置 " **从** " 和 "到" 值 **来** 定义新范围。

3.  （可选）：定义范围名称，最多256个字符。

4.  若要将范围导出到 CSV 文件，请选择 " **导出**"。

5. 若要从 CSV 文件中手动添加多个范围，请选择 " **导入** "，然后选择该文件。

    > [!NOTE]
    > 从 CSV 文件导入的范围将覆盖现有范围设置。

6. 选择“保存” 。

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>为反向查找解析配置 DNS 服务器

若要增强设备扩充，可将多个 DNS 服务器配置为 carryout 反向查找。 可以解析与网络子网中检测到的 IP 地址相关联的主机名或 Fqdn。 例如，如果某个传感器发现 IP 地址，则它可能会查询多个 DNS 服务器来解析主机名。

支持所有的 CIDR 格式。

主机名显示在设备清单、设备映射和报表中。

您可以计划每小时特定时间间隔的反向查找解析计划，例如每隔12小时。 或者，您可以计划特定的时间。

定义 DNS 服务器：

1. 选择 " **系统设置** "，然后选择 " **DNS 设置**"。

2. 选择 " **添加 DNS 服务器**"。

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="显示 &quot;添加 DNS 服务器&quot; 选项的屏幕截图。":::

3. 在 " **计划反向 DNS 查找** " 字段中，选择以下任一项：

    - 每小时 () 的间隔。
  
    - 特定时间。 使用欧洲格式。 例如，使用 **14:30** ，而不是 **2:30 PM**。

4. 在 " **Dns 服务器地址** " 字段中，输入 dns IP 地址。

5. 在 " **Dns 服务器端口** " 字段中，输入 dns 端口。

6. 将网络 IP 地址解析为设备 Fqdn。 在 " **标签数** " 字段中，添加要显示的域标签的数目。 从左到右显示最多30个字符。

7. 在 " **子网** " 字段中，输入希望 DNS 服务器查询的子网。

8. 如果要启动反向查找，请选择 " **启用** " 切换。

### <a name="test-the-dns-configuration"></a>测试 DNS 配置 

通过使用测试设备，验证所定义的设置是否正常工作：

1. 启用 **DNS 查找** 切换。

2. 选择“测试”。

3. 在 " **DNS 反向查找服务器的服务器**" 对话框的 "**查找地址**" 中输入地址。

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="屏幕截图，显示查找地址区域。":::

4. 选择“测试”。

## <a name="configure-windows-endpoint-monitoring"></a>配置 Windows 终结点监视

借助 Windows 终结点监视功能，你可以将 Azure Defender for IoT 配置为有选择地探测 Windows 系统。 这为你提供了更专注且准确的设备信息，如 Service Pack 级别。

你可以配置对特定范围和主机的探测，并将其配置为仅在需要时才执行。 您可以使用 Windows Management Instrumentation (WMI) （这是 Microsoft 用于管理 Windows 系统的标准脚本语言）来完成选择性探测。

> [!NOTE]
> - 一次只能运行一个扫描。
> - 你可获得具有域或本地管理员权限的用户的最佳结果。
> - 在开始 WMI 配置之前，请配置防火墙规则，以便通过使用 UDP 端口135和1024以上的所有 TCP 端口，打开从传感器到扫描子网的传出流量。

探测完成后，可从用于导出日志的选项获取具有所有探测尝试的日志文件。 日志包含已探测的所有 IP 地址。 对于每个 IP 地址，日志显示成功和失败信息。 还有一个错误代码，它是派生自异常的可用字符串。 最后一个日志的扫描将保留在系统中。

您可以执行计划的扫描或手动扫描。 扫描完成后，可以在 CSV 文件中查看结果。

**先决条件**

配置防火墙规则，通过使用 UDP 端口135和1024以上的所有 TCP 端口，打开从传感器到扫描子网的传出流量。

若要配置自动扫描：

1. 在侧边菜单中，选择 " **系统设置**"。

2. 选择 " **Windows 终结点监视**" :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: 。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="显示选定的 Windows 终结点监视的屏幕截图。":::

3. 在 " **扫描计划** " 窗格上，按如下所示配置选项：

      - **按固定间隔 (小时)**：根据时间间隔（小时）设置扫描计划。

      - **按特定时间**：根据特定时间设置扫描计划，然后选择 " **保存扫描**"。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="显示 &quot;保存扫描&quot; 按钮的屏幕截图。":::

4. 若要定义扫描范围，请选择 " **设置扫描范围**"。

5. 设置 IP 地址范围并添加用户和密码。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="显示添加用户和密码的屏幕截图。":::

6. 若要从扫描中排除 IP 范围，请选择该范围旁边的 " **禁用** "。

7. 若要删除范围，请选择 :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: 该范围旁的。

8. 选择“保存” 。 " **编辑扫描范围配置** " 对话框将关闭，范围数将显示在 " **扫描范围** " 窗格中。

执行手动扫描：

1. 在侧边菜单中，选择 " **系统设置**"。

2. 选择 " **Windows 终结点监视**" :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: 。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="显示 Windows 终结点监视安装屏幕的屏幕截图。":::

3. 在 " **操作** " 窗格中，选择 " **开始扫描**"。 此时将在 " **操作** " 窗格中显示一个状态栏，并显示扫描进程的进度。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="显示 &quot;开始扫描&quot; 按钮的屏幕截图。":::

查看扫描结果：

1. 扫描完成后，在 " **操作** " 窗格中选择 " **查看扫描结果**"。 带有扫描结果的 CSV 文件将下载到您的计算机。

## <a name="next-steps"></a>后续步骤

[调查设备清单](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 中的传感器检测[在设备映射中调查传感器检测](how-to-work-with-the-sensor-device-map.md)
