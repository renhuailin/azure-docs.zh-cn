---
title: 创建数据挖掘报告
description: 在不同的层（例如协议、固件版本或编程命令）生成有关网络设备的全面的详细信息。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/20/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 0738dc7f2b7012002fc6ddf5cc68dd0c8e42b04d
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522473"
---
# <a name="sensor-data-mining-queries"></a>传感器数据挖掘查询

## <a name="about-sensor-data-mining-queries"></a>关于传感器数据挖掘查询

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

- **SOC 事件响应**：实时生成报告，以帮助处理即时事件响应。 例如，数据挖掘可以生成可能需要修补的设备列表。

- **辩论**：基于调查报表的历史数据生成报表。

- **IT 网络完整性**：生成有助于提高总体网络安全的报告。 例如，可以生成一个报告，其中列出了具有弱身份验证凭据的设备。

- **可见性**：生成包含所有查询项的报表，以查看网络的所有基线参数。

## <a name="data-mining-storage"></a>数据挖掘存储

数据挖掘信息会不断保存并存储，但在删除设备时除外。 数据挖掘结果可以导出并存储在外部到安全服务器。 此外，该传感器还会执行每日自动备份，以确保系统连续性和数据保留。


## <a name="predefined-data-mining-queries"></a>预定义数据挖掘查询

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

## <a name="create-a-data-mining-query"></a>创建数据挖掘查询

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

4. 选择“保存” 。 报表结果在 " **数据挖掘** " 页上打开。

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



## <a name="sensor-reports-based-on-data-mining"></a>基于数据挖掘的传感器报表

从 " **报告** " 选项中访问的常规报告是预定义的数据挖掘报告。 它们不是数据挖掘中可用的动态查询，而是数据挖掘查询结果的静态表示形式。

数据挖掘查询结果不能用于只读用户。 希望只读用户有权访问数据挖掘查询生成的信息的管理员和安全分析师应将信息保存为报表。

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

## <a name="on-premises-management-console-reports-based-on-data-mining-reports"></a>基于数据挖掘报表的本地管理控制台报表

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

## <a name="next-steps"></a>后续步骤

[传感器趋势和统计信息报告](how-to-create-trends-and-statistics-reports.md)。