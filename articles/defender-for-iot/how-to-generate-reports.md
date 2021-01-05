---
title: 生成报告
description: 使用用于 IoT 报表工具的 Defender 深入了解网络活动、风险、攻击和趋势。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d0c3f531ede0a590a6ba7bb21c6edbd768c08069
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838294"
---
# <a name="generate-reports"></a>生成报告

使用用于 IoT 报表工具的 Azure Defender 深入了解网络活动、风险、攻击和趋势。 工具可用于生成报告：

- 基于单个传感器检测到的活动。
- 基于所有传感器检测到的活动。 

这些报表是从本地管理控制台生成的。

## <a name="reports-for-sensor-risk-assessment"></a>传感器风险评估报告

利用风险评估报告，你可以为每个网络设备生成一个安全分数，同时为整个网络安全评分。 整体分数表示100% 安全的百分比。

此分数基于数据包检查、行为建模引擎和 SCADA 特定的状态机设计的结果。 提供了范围广泛的其他信息，例如：

- 配置问题

- 按安全级别划分的设备漏洞

- 网络安全问题

- 网络操作问题

- 攻击途径

- 与 ICS 网络的连接

- Internet 连接

- 工业恶意软件指示器

- 协议问题

  - 安全设备：安全分数高于90% 的设备。

- **安全设备**：安全分数高于90% 的设备。

- **易受攻击的设备**：安全分数低于70% 的设备。

- **需要改进的设备**：安全分数介于70% 和89% 之间的设备。

创建报表：

1. 选择侧栏菜单上的 " **风险评估** "。
2. 从 " **选择传感器** " 下拉列表中选择一个传感器。
3. 选择 " **生成报告**"。
4. 从 "存档的报表" 部分中选择 " **下载** "。

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="风险评估的视图。":::

导入公司徽标：

导入公司徽标：

- 选择 " **导入徽标**"。

## <a name="reports-for-sensor-attack-vector"></a>传感器攻击向量的报告

攻击向量报告提供可利用设备的漏洞链的图形表示。 这些漏洞会使攻击者能够访问关键网络设备。 攻击向量模拟器会实时计算攻击媒介，并分析特定目标的所有攻击向量。

使用攻击向量，可以评估攻击序列中缓解活动的影响。 例如，你可以通过中断攻击链来确定系统升级是否破坏了攻击者的路径，或者如果保留了备用攻击路径，则可以确定。 此信息可帮助你确定修正和缓解活动的优先级。

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="在控制中心查看警报。":::

> [!NOTE]
> 管理员和安全分析师可以执行本部分中所述的过程。

创建攻击向量模拟：

1. 选择侧边菜单上的 :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="加号":::以添加模拟。

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="攻击向量模拟。":::

2. 输入模拟属性：

   - **名称**：模拟名称。

   - **最大向量**：单个模拟中的最大向量数。

   - **在设备映射中显示**：在设备映射上以筛选器的形式显示攻击向量。

   - **所有源设备**：攻击向量会将所有设备视为攻击源。

   - **攻击源**：攻击向量仅将指定设备视为攻击源。

   - **所有目标设备**：攻击向量会将所有设备视为攻击目标。

   - **攻击目标**：攻击向量仅将指定设备视为攻击目标。

   - **排除设备**：指定的设备将从攻击向量模拟中排除。

   - **排除子网**：将从攻击向量模拟中排除指定的子网。

3. 选择 " **添加模拟**"。 模拟将添加到模拟列表。

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="添加新的模拟。":::

4. :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false":::如果要编辑模拟，请选择。

   :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false":::如果要删除模拟，请选择。

   :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false":::如果要将模拟标记为收藏项，请选择此项。

5. 此时会显示攻击向量列表，其中包括 100) 、攻击源设备和攻击目标设备 (的矢量分数。 选择特定攻击以对攻击向量的图形化表示形式。

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="攻击向量。":::

## <a name="sensor-data-mining-queries"></a>传感器数据挖掘查询

利用数据挖掘工具，你可以在不同的层上生成有关网络设备的全面的详细信息。 例如，你可以基于以下内容创建查询：

- 时间段

- 连接到 internet

- 端口

- 协议

- 固件版本

- 编程命令

- 设备处于非活动状态

您可以基于筛选器精细调整报表。 例如，你可以查询更新了固件的特定子网。

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="活动设备的列表。":::

可以使用各种工具来管理查询。 例如，可以导出并保存。

> [!NOTE]
> 管理员和安全分析师有权访问数据挖掘选项。

### <a name="dynamic-updates"></a>动态更新

创建的数据挖掘查询在每次打开时都将动态更新。 例如：

- 如果你在6月1日在设备上创建固件版本报表，并在6月10日再次打开该报表，则此报表将更新为在6月10日准确的信息。

- 如果创建了一个报表来检测6月1日过去30天内发现的新设备，并在6月30日打开该报表，则将显示过去30天内的结果。

### <a name="data-mining-use-cases"></a>数据挖掘用例

您可以使用查询来处理各种安全团队的各种安全需求：

- **SOC 事件响应**：实时生成报告，以帮助处理即时事件响应。 例如，为可能需要修补的设备列表生成报告。

- **辩论**：基于调查报表的历史数据生成报表。

- **IT 网络完整性**：生成有助于提高总体网络安全的报告。 例如，生成一个报表，其中列出了具有弱身份验证凭据的设备。

- **可见性**：生成包含所有查询项的报表，以查看网络的所有基线参数。

### <a name="data-mining-storage"></a>数据挖掘存储

数据挖掘信息会不断保存并存储，但在删除设备时除外。 数据挖掘结果可以导出并存储在外部到安全服务器。 此外，该传感器还会执行每日自动备份，以确保系统连续性和数据保留。

### <a name="data-mining-and-reports"></a>数据挖掘和报告 

从 " **报告** " 选项中访问的常规报告是预定义的数据挖掘报告。 它们不是数据挖掘中可用的动态查询，而是数据挖掘查询结果的静态表示形式。

数据挖掘查询结果对于 RO 用户不可用。 希望 RO 用户有权访问数据挖掘查询生成的信息的管理员和安全分析师应将信息保存为报表。

### <a name="predefined-queries"></a>预定义查询

以下预定义的查询可用。 这些查询是实时生成的。

- **标识符**：在过去24小时内检测到的具有已知漏洞的设备的列表。

- **排除的标识符**：已手动排除的所有标识符的列表。 若要在 VA 报表和攻击向量中获得更准确的结果，可以通过包含和排除标识符来手动自定义 CVE 列表。

- **Internet 活动**：连接到 internet 的设备。

- 非活动 **设备**：过去7天未通信的设备。

- **活动设备**：在过去24小时内活动的网络设备。

- **远程访问**：通过远程会话协议进行通信的设备。

- **编程命令**：发送工业编程的设备。

这些报表可从 " **报表** " 屏幕自动访问，用户和其他用户可以在其中查看这些报表。 RO 用户无法访问数据挖掘报表。

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="数据挖掘屏幕。":::

### <a name="create-a-data-mining-report"></a>创建数据挖掘报表

创建数据挖掘报表：

1. 从侧边菜单中选择 " **数据挖掘** "。 预定义的建议报表将自动显示。

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="从侧窗格中选择数据挖掘。":::

2. 选择 :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::。

3. 选择 " **新建报表** " 并定义报表。

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="通过填写此屏幕创建新报表。":::

   以下参数可用：

   - 提供报表名称和说明。

   - 对于 "类别"，请选择以下任一操作：

      - **类别 (所有)** 查看网络中所有设备的所有报告结果。

      - 用于选择标准类别的 **泛型**。

   - 选择你感兴趣的特定报表参数。

   - 按)  (**顺序** 选择排序顺序。 基于活动或类别对结果进行排序。

   - 选择 " **保存到报表页** " 可将报表结果保存为可从 **报表** 页访问的报表。 这将允许 RO 用户运行您创建的报表。

   - 选择 " **筛选器" (添加)** 以添加更多筛选器。 支持通配符请求。

   - 指定设备组) 中定义 (设备组。

   - 指定一个 IP 地址。

   - 指定端口。

   - 指定 MAC 地址。

4. 选择“保存”。 报表结果在 " **数据挖掘** " 页上打开。

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="报告在数据挖掘页上显示的结果。":::

### <a name="manage-data-mining-reports"></a>管理数据挖掘报表

下表描述了用于数据挖掘的管理选项：

| 图标图像 | 说明 |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | 编辑报表参数。 |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | 导出为 PDF。 |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |导出为 CSV。 |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | 显示其他信息，如上次修改日期。 使用此功能可以创建查询结果快照。 例如，你可能需要执行此操作以便进一步调查团队负责人或 SOC 分析师。 |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | 显示 **在 "报表" 页** 上，或在 " **报表** " 页上隐藏。 :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="隐藏或显示报表。"::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | 删除报表。 |

#### <a name="create-customized-directories"></a>创建自定义目录 

您可以通过创建类别的目录来组织数据挖掘查询的详细信息。 例如，你可以为协议或位置创建目录。

若要创建新目录：

1. 选择此 :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: 添加新目录。

2. 选择 " **新目录** " 以显示 "新目录" 窗体。

3. 将新目录命名为。

4. 将所需的报表拖到新目录中。 随时可以将报表拖回主视图。

5. 右键单击新目录以打开、编辑或删除它。

#### <a name="create-snapshots-of-report-results"></a>创建报表结果的快照

你可能需要保存某些查询结果，以便进一步调查。 例如，你可能需要与各种安全团队共享结果。

快照保存在报表结果内，不生成动态查询。

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="快照.":::

若要创建快照：

1. 打开所需的报表。

2. 选择 "信息" 图标 :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: 。

3. 选择 " **新建**"。

4. 输入快照的名称，然后选择 " **保存**"。

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="拍摄快照。":::

#### <a name="customize-the-cve-list"></a>自定义 CVE 列表

您可以按如下所示手动自定义 CVE 列表：

  - 包含/排除标识符

  - 更改 CVE 评分

在 CVE 报表中执行手动更改：

1.  从侧边菜单中，选择 " **数据挖掘**"。

2. 选择 :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: **数据挖掘** 窗口左上角的。 然后选择 " **新建报表**"。

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="创建新报表。":::

3. 从左窗格中，选择下列选项之一：

   - **已知漏洞**：选择两个选项，并在报表的两个表中显示结果，一个表具有标识符，另一个包含排除的标识符。

   - **标识符**：选择此选项可以显示所有标识符的列表。

   - **排除的标识符**：选择此选项以显示所有已排除标识符的列表。

4. 填写 " **名称** " 和 " **说明** " 信息，然后选择 " **保存**"。 新报表将显示在 " **数据挖掘** " 窗口中。

5. 若要排除标识符，请打开标识符的数据挖掘报表。 将显示所有标识符的列表。

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="C V E 报表。":::

6. 若要启用列表中的项目选择，请选择 :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: 并选择要自定义的标识符。 **操作** 栏将显示在底部。

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="数据挖掘操作栏的屏幕截图。":::

7. 选择要排除的标识符，然后选择 " **删除记录**"。 您选择的标识符不会显示在标识符列表中，并且会在您生成它时出现在排除的标识符列表中。

8. 若要在标识符列表中包含已排除的标识符，请为 "已排除的标识符" 和 "从列表中删除" 生成报告，其中列出了要在标识符列表中包含的项。

9. 选择要在其中更改分数的标识符，然后选择 " **更新 CVE 评分**"。

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="更新 CVE 分数。":::

10. 输入新分数，然后选择 **"确定"**。 已更新的分数将显示在所选的标识符中。

### <a name="reports-based-on-data-mining"></a>基于数据挖掘的报表

报表反映数据挖掘查询结果生成的信息。 这包括默认的数据挖掘报表，这些报表在 "报表" 视图中可用。 管理员和安全分析师还可以生成自定义数据挖掘查询，并将其另存为报表。 这些报告也适用于 RO 用户。

生成报表：

1. 选择侧栏菜单上的 " **报告** "。

2. 选择要显示的所需报表。 选项可以是 **自定义** 或 **自动生成** 的报表，如 **编程命令** 和 **远程访问**。

3. 您可以通过选择屏幕右上角的一个图标来导出报表：

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: 导出到 PDF 文件。

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: 导出到 CSV 文件。

> [!NOTE] 
> RO 用户只能查看为其创建的报表。

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="选择要生成的报表。":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="已生成远程访问报告。":::

## <a name="reports-for-sensor-trends-and-statistics"></a>传感器趋势和统计信息的报告

你可以创建小组件图和饼图来深入了解网络趋势和统计信息。 小组件可以在用户定义的仪表板下进行分组。

> [!NOTE]
> 只有管理员和安全分析师才能执行本部分中的过程。

若要查看仪表板和小组件，请选择侧菜单上的 " **趋势 & 统计信息** "。

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="调查的屏幕截图。":::

该仪表板包含小组件，这些小组件以图形方式描述以下类型的信息：

- 按端口排列的流量
- 通道带宽
- 总带宽
- 活动 TCP 连接
- 设备：
  - 新建设备
  - 繁忙设备
  - 按供应商的设备
  - 按 OS 的设备
  - 断开连接的设备
- 连接丢弃时间
- 事件的警报（按类型）
- 数据库表访问
- 协议剖析小组件
- 以太网和 IP 地址：
  - CIP 服务的以太网和 IP 地址流量
  - 通过 CIP 类的以太网和 IP 地址流量
  - 以太网和 IP 地址流量（按命令）
- OPC
  - OPC 顶层管理操作
  - OPC top i/o 操作
- Siemens S7：
  - 按 control 函数的 S7 流量
  - 按 subfunction S7 流量
- SRTP:
  - 按服务代码 SRTP 流量
  - 按天列出的 SRTP 错误
- SuiteLink:
  - SuiteLink 查询排在最前面的标记
  - SuiteLink 数值标记行为
- IEC-60870 流量 by ASDU
- 按功能的 DNP3 流量
- 按服务的彩信流量
- 按功能 Modbus 流量
- OPC-按服务的 UA 流量

> [!NOTE]
>  根据传感器时间设置小组件中的时间。

## <a name="reports-for-the-on-premises-management-console"></a>本地管理控制台的报表

本地管理控制台使你可以为连接到它的每个传感器生成报告。 报表基于执行的传感器数据挖掘查询。

您可以生成以下报表：

- **(过去24小时内的活动设备)**：显示在24小时内显示网络活动的设备的列表。

- **非活动设备 (过去7天)**：显示最近七天内没有任何网络活动的设备的列表。

- **编程命令**：显示最近24小时内发送编程命令的设备列表。

- **远程访问**：显示最近24小时内访问远程源的设备的列表。

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="&quot;报表&quot; 视图的屏幕截图。":::

当你从本地管理控制台中选择该传感器时，该传感器上配置的所有自定义报表都会显示在报表列表中。 对于每个传感器，可以生成在该传感器上配置的默认报表或自定义报表。

生成报表：

1. 在左侧窗格中，选择 " **报表**"。 此时将显示 " **报表** " 窗口。

2. 从 " **传感器** " 下拉列表中，选择要为其生成报告的传感器。

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="传感器视图的屏幕截图。":::

3. 从右侧下拉列表中，选择要生成的报表。

4. 若要创建报表结果的 PDF，请选择 :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false"::: 。

## <a name="risk-assessment-reports-for-the-on-premises-management-console"></a>本地管理控制台的风险评估报表

为连接到本地管理控制台的每个传感器生成风险评估报告。 此报告提供对传感器监视的每个网络的深入了解。

利用风险评估报表，您可以为每个网络设备生成一个安全评分，同时为整个网络安全评分。 整体分数基于深层数据包检查、行为建模引擎和 SCADA 特定的状态机设计。 提供了范围广泛的其他信息。 例如：

- 配置问题

- 按安全级别划分的设备漏洞

- 网络安全问题

- 网络操作问题

- 攻击途径

- 与 ICS 网络的连接

- Internet 连接

- 工业恶意软件指示器

- 协议问题

该报表提供缓解建议，可帮助您提高安全分数。

- 安全设备：安全分数高于90% 的设备。

- **易受攻击的设备**：安全分数低于70% 的设备。

- **需要改进的设备**：安全分数介于70% 和89% 之间的设备。

创建报表：

1. 选择侧栏菜单上的 " **风险评估** "。

2. 从 " **选择传感器** " 下拉列表中选择一个传感器。

3. 选择 " **生成报告**"。

4. 从 "存档的 **报表**" 部分中选择 "**下载**"。

导入公司徽标：

- 选择 " **导入徽标**"。

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="通过风险评估视图导入徽标。":::

## <a name="see-also"></a>另请参阅
[使用站点地图视图](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
