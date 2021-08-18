---
title: 控制要监视的流量
description: 传感器会自动对 IT 和 OT 流量执行深度数据包检测，并解析有关网络设备的信息，例如设备属性和网络行为。 有几种工具可用于控制每个传感器检测到的流量类型。
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: b80ef4a4a1fca7383da80fda6fdf42831913f2bd
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015896"
---
# <a name="control-what-traffic-is-monitored"></a>控制要监视的流量

传感器会自动对 IT 和 OT 流量执行深度数据包检测，并解析有关网络设备的信息，例如设备属性和行为。 有几种工具可用于控制每个传感器检测到的流量类型。

## <a name="learning-and-smart-it-learning-modes"></a>学习和智能 IT 学习模式

学习模式指示传感器了解网络的常规活动。 例如，在网络中发现的设备、在网络中检测到的协议、特定设备之间的文件传输等。 此活动会成为网络基线。

学习模式在安装之后会自动启用，并将保持启用状态，直至关闭为止。 学习模式时间段大约为两周至六周，具体取决于网络大小和复杂性。 在这段时间之后，当学习模式处于禁用状态时，检测到的任何新活动都会触发警报。 在策略引擎发现与学习到的基线有偏差时，会触发警报。

在学习期满和学习模式被禁用后，传感器可能会检测到由于正常 IT 活动（如 DNS 和 HTTP 请求）而产生的异常高级基线变化。 该活动被称为“非确定性 IT 行为”。 该行为可能也会触发不必要的策略违规警报和系统通知。 若要减少这些警报和通知的数量，可以启用智能 IT 学习功能。

在启用了智能 IT 学习时，传感器会根据特定警报方案，对生成非确定性 IT 行为的网络流量进行跟踪。

传感器会对此流量监视 7 天。 如果它在 7 天内检测到相同的非确定性 IT 流量，则会对该流量再继续监视 7 天。 如果在完整 7 天内未检测到流量，则会为该方案禁用智能 IT 学习。 为该方案检测到的新流量将只生成警报和通知。

使用智能 IT 学习有助于减少由干扰性 IT 方案引起的不必要的警报和通知。

如果传感器由本地管理控制台控制，则无法禁用学习模式。 在这种情况下，只能从管理控制台禁用学习模式。

学习功能（学习和智能 IT 学习）在默认情况下处于启用状态。

若要启用或禁用学习，请执行以下操作：

- 选择“系统设置”，并切换“学习”和“智能 IT 学习”选项  。

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="系统设置切换屏幕。":::

## <a name="configure-subnets"></a>配置子网

子网配置会影响设备在设备映射中显示的方式。

在默认情况下，传感器会发现子网设置，并使用此信息填充“子网配置”对话框。

若要实现对 OT 设备的聚焦，IT 设备通过设备映射中的子网自动聚合。 每个子网在映射上显示为单个实体，包括交互式折叠/扩展功能，以“向下钻取”到 IT 子网中并返回。

在使用子网时，请选择 ICS 子网来识别 OT 子网。 然后，可以将映射视图集中于 OT 和 ICS 网络上，并折叠到 IT 网络元素的最小表示形式。 这项操作会减少映射上显示的设备总数，并清晰地呈现 OT 和 ICS 网络元素。

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="显示在网络上筛选的屏幕截图。":::

可以导出发现的数据，手动更改这些数据，然后再导回手动定义的子网列表，通过这种方式来手动更改配置或更改子网信息。 有关导出和导入的详细信息，请参阅[导入设备信息](how-to-import-device-information.md)。

在有些情况下（例如使用公共范围作为内部范围的环境），可通过选择“不检测 Internet 活动”选项来指示传感器将所有子网都解析为内部子网。 在选择该选项时：

- 公共 IP 地址将会被视为本地地址。

- 将不会发送有关未经授权的 Internet 活动的警报，从而减少外部地址收到的通知和警报。

若要配置子网，请执行以下操作：

1. 在侧边菜单中选择“系统设置”。

2. 在“系统设置”窗口中，选择“子网” 。

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="显示子网配置屏幕的屏幕截图。"::: 

3. 若要在发现新设备时自动添加子网，请保持“自动子网学习”处于选中状态。

4. 若要将所有子网都解析为内部子网，请选择“不检测 Internet 活动”。

5. 选择“添加网络”，并为每个子网定义以下参数：

    - 子网 IP 地址。
    - 子网掩码地址。
    - 子网名称。 建议使用可以轻松识别的有意义的名称来命名每个子网，以便能够区分 IT 网络和 OT 网络。 名称最多可以使用 60 个字符。

6. 若要将此子网标记为 OT 子网，请选择“ICS 子网”。

7. 若要在根据 Purdue 级别安排映射时分别显示子网，请选择“分隔”。

8. 若要删除子网，请选择 :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false":::。

9. 若要删除所有子网，请选择“全部清除”。

10. 若要导出已配置的子网，请选择“导出”。 子网表会下载到工作站。

11. 选择“保存”。

### <a name="importing-information"></a>导入信息 

若要导入子网信息，请选择“导入”并选择要导入的 CSV 文件。 子网信息会更新为导入的信息。 如果导入空字段，将会丢失数据。

## <a name="detection-engines"></a>检测引擎

自学分析引擎无需更新签名或定义规则。 这些引擎使用特定于 ICS 的行为分析和数据科学来持续分析 OT 网络流量的异常、恶意软件、操作问题、协议违规和基线网络活动偏差。

> [!NOTE]
> 建议启用所有安全引擎。

当引擎检测到偏差时，会触发警报。 可以从警报屏幕或从合作伙伴系统查看和管理警报。

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="显示偏差检测的屏幕截图。":::

触发了警报的引擎的名称出现在警报标题下方。

### <a name="protocol-violation-engine"></a>协议违反引擎 

在数据包结构或字段值不符合协议规范时，会发生违反协议的情况。

示例场景：“MODBUS 操作非法（函数代码零）”警报。 此警报表明主要设备向辅助设备发送了具有函数代码 0 的请求。 根据协议规范，不允许执行此操作，辅助设备可能不会正确处理输入。

### <a name="policy-violation-engine"></a>策略违反引擎

在已学习的或已配置的设置中定义的基线行为发生偏差时，会发生违反策略的情况。

示例场景：“HTTP 用户代理未经授权”警报。 此警报表示在设备上使用了未经策略学习或批准的应用程序作为 HTTP 客户端。 它可能是指该设备上的新 Web 浏览器或新应用程序。

### <a name="malware-engine"></a>恶意软件引擎

恶意软件引擎会检测恶意网络活动。

示例场景：“怀疑恶意活动 (Stuxnet)”警报。 此警报表明传感器检测到已知与 Stuxnet 恶意软件相关的可疑网络活动。 此恶意软件是针对工业控制和 SCADA 网络的高级持久威胁。

### <a name="anomaly-engine"></a>异常引擎

异常引擎检测网络行为中的异常情况。

示例场景：“通信通道中的定期行为”警报。 该组件会检查网络连接，并查找数据传输的周期行为和循环行为。 这种行为在工业网络中很常见。

### <a name="operational-engine"></a>操作引擎

操作引擎会检测到操作方面的事件或发生故障的实体。

示例场景：“怀疑设备已离线（无响应）”警报。 在设备对任何类型的请求均无响应达到预定时长时，引发此警报。 此警报可能表示设备关闭、断开连接或出现故障。

### <a name="enable-and-disable-engines"></a>启用和禁用引擎

如果禁用策略引擎，传感器将无法使用该引擎生成的信息。 例如，如果禁用异常引擎，则不会收到有关网络异常的警报。 如果创建了转发规则，则不会发送该引擎学习的异常。 若要启用或禁用策略引擎，请为特定引擎选择“已启用”或“已禁用” 。

整体分数会显示在“系统设置”屏幕的右下角。 该分数指示通过威胁防护引擎启用的可用保护的百分比。 每个引擎都是可用保护的 20%。

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="显示分数的屏幕截图。":::

## <a name="configure-dhcp-address-ranges"></a>配置 DHCP 地址范围

网络可能由静态和动态 IP 地址组成。 通常，可通过 historian、控制器和网络基础结构设备（如交换机和路由器）在 OT 网络上找到静态地址。 动态 IP 分配通常在具有便携式计算机、台式电脑、智能手机和其他便携式设备（在不同位置使用 Wi-Fi 或 LAN 物理连接）的来宾网络上实现。

如果使用动态网络，则会处理在分配新 IP 地址时发生的 IP 地址更改。 通过定义 DHCP 地址范围来完成此操作。

例如，在 DHCP 服务器分配 IP 地址时，可能会发生更改。

在每个传感器上定义动态 IP 地址，会在 IP 地址更改的实例中启用全面的、透明的支持。 这样就会确保为每个独一无二的设备提供全面的报告。

传感器控制台会显示与设备关联的最新 IP 地址，并指示哪些设备是动态的。 例如：

- 数据挖掘报表和设备清单报表会将从设备中获知的所有活动整合为一个实体，而不考虑 IP 地址的更改。 这些报表指示哪些地址定义为 DHCP 地址。

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="显示设备清单的屏幕截图。":::

- “设备属性”窗口指示设备是否已定义为 DHCP 设备。

若要设置 DHCP 地址范围，请执行以下操作：

1.  在侧边菜单中，从“系统设置”窗口中选择“DHCP 范围” 。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="显示选择 DHCP 范围的屏幕截图。":::

2.  通过设置“从”和“到”值来定义新范围 。

3.  （可选）：定义范围名称，最多 256 个字符。

4.  若要将范围导出到 CSV 文件，请选择“导出”。

5. 若要从 CSV 文件中手动添加多个范围，请选择“导入”，然后选择该文件。

    > [!NOTE]
    > 从 CSV 文件导入的范围会覆盖现有的范围设置。

6. 选择“保存”。

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>配置 DNS 服务器来进行反向查找解析

若要增强设备扩充，可配置多个 DNS 服务器来执行反向查找。 可以解析与网络子网中检测到的 IP 地址相关联的主机名或 FQDN。 例如，如果某个传感器发现了 IP 地址，它可能会查询多个 DNS 服务器来解析主机名。

支持所有的 CIDR 格式。

主机名会显示在设备清单、设备映射和报表中。

可以计划按特定的小时间隔（例如每 12 小时一次）进行反向查找解析。 或者，可以计划某个特定的时间。

若要定义 DNS 服务器，请执行以下操作：

1. 选择“系统设置”，然后选择“DNS 设置” 。

2. 选择“添加 DNS 服务器”。

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="显示选择“添加 DNS 服务器”的屏幕截图。":::

3. 在“计划反向 DNS 查找”字段中，选择以下任一项：

    - 间隔（按小时）。
  
    - 某个特定时间。 请使用欧洲格式。 例如，请使用 14:30 而不是 2:30 PM 。

4. 在“DNS 服务器地址”字段中，输入 DNS IP 地址。

5. 在“DNS 服务器端口”字段中，输入 DNS 端口。

6. 将网络 IP 地址解析为设备 FQDN。 在“标签数”字段中，添加要显示的域标签的数目。 从左到右最多显示 30 个字符。

7. 在“子网”字段中，输入希望 DNS 服务器查询的子网。

8. 如果要启动反向查找，请选择“启用”开关。

### <a name="test-the-dns-configuration"></a>测试 DNS 配置 

通过使用测试设备，验证定义的设置是否正常工作：

1. 启用“DNS 查找”开关。

2. 选择“测试”。

3. 在“服务器的 DNS 反向查找测试”对话框的“查找地址”中输入地址 。

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="显示“查找地址”区域的屏幕截图。":::

4. 选择“测试”。

## <a name="configure-windows-endpoint-monitoring"></a>配置 Windows 终结点监视

利用 Windows 终结点监视功能，可以配置 Azure Defender for IoT，以有选择地探测 Windows 系统。 这样可以为用户提供更明确更准确的设备信息，例如服务包级别。

用户可配置探测特定范围和主机，并可将此操作配置为仅按所需频次执行。 为实现选择性探测，可使用 Windows Management Instrumentation (WMI)，这是用于管理 Windows 系统的 Microsoft 标准脚本语言。

> [!NOTE]
> - 一次只能运行一个扫描。
> - 可以具有域或本地管理员权限的用户身份获取最佳结果。
> - 在开始 WMI 配置之前，请先配置防火墙规则，以使用 UDP 端口 135 和 1024 以上的所有 TCP 端口开启从传感器到已扫描子网的传出流量。

在探测完成时，使用导出日志的选项可提供包含所有探测尝试的日志文件。 该日志包含已探测的所有 IP 地址。 对于每个 IP 地址，该日志都会显示成功和失败信息。 其中还包含错误代码，此代码是一个根据异常派生的自由字符串。 系统中只保留最后一个扫描日志。

可以执行计划扫描或手动扫描。 在扫描完成时，可在 CSV 文件中查看结果。

**必备条件**

配置防火墙规则，以便通过使用 UDP 端口 135 和 1024 以上的所有 TCP 端口打开从传感器到扫描的子网的传出流量。

若要配置自动扫描，请执行以下操作：

1. 在侧边菜单中选择“系统设置”。

2. 选择“Windows 终结点监视”:::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false":::。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="显示选择“Windows 终结点监视”的屏幕截图。":::

3. 在“扫描计划”窗格上，配置选项，如下所示：

      - 按固定间隔（以小时为单位）：根据以小时为单位的间隔设置扫描计划。

      - 按特定时间：根据特定时间设置扫描计划，并选择“保存扫描”。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="显示“保存扫描”按钮的屏幕截图。":::

4. 若要定义扫描范围，请选择“设置扫描范围”。

5. 设置 IP 地址范围并添加用户和密码。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="显示添加用户和密码的屏幕截图。":::

6. 若要从扫描中排除某个 IP 范围，请选择该范围旁边的“禁用”。

7. 若要删除某个范围，请选择该范围旁边的 :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false":::。

8. 选择“保存”。 “编辑扫描范围配置”对话框会关闭，范围数会显示在“扫描范围”窗格中 。

若要执行手动扫描，请执行以下操作：

1. 在侧边菜单中选择“系统设置”。

2. 选择“Windows 终结点监视”:::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false":::。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="显示“Windows 终结点监视”设置屏幕的屏幕截图。":::

3. 在“操作”窗格中，选择“开始扫描”。 “操作”窗格中会显示一个状态栏，其中会显示扫描进程的进度。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="显示“开始扫描”按钮的屏幕截图。":::

若要查看扫描结果，请执行以下操作：

1. 在扫描完成时，在“操作”窗格中选择“查看扫描结果” 。 包含扫描结果的 CSV 文件会下载到计算机。

## <a name="see-also"></a>另请参阅

[在设备清单中调查传感器检测](how-to-investigate-sensor-detections-in-a-device-inventory.md)
[在设备映射中调查传感器检测](how-to-work-with-the-sensor-device-map.md)
