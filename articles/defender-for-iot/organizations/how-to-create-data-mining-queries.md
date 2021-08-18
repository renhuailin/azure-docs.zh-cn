---
title: 创建数据挖掘报告
description: 在各个层（例如协议、固件版本或编程命令）上生成有关网络设备的全面详细信息。
ms.date: 01/20/2021
ms.topic: how-to
ms.openlocfilehash: 99754959e7a3a08b4d763b85b0b9315476969774
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015850"
---
# <a name="sensor-data-mining-queries"></a>传感器数据挖掘查询

## <a name="about-sensor-data-mining-queries"></a>关于传感器数据挖掘查询

通过数据挖掘工具可以在各个层上生成有关网络设备的全面详细信息。 例如，可基于以下内容创建查询：

- 时间段

- 与 Internet 的连接

- 端口

- 协议

- 固件版本

- 编程命令

- 设备非活动状态

可以基于筛选器微调报表。 例如，可以查询更新了固件的特定子网。

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="活动设备列表。":::

我们提供各种工具来管理查询。 例如，可以导出并保存。

> [!NOTE]
> 管理员和安全分析师有权访问数据挖掘选项。

### <a name="dynamic-updates"></a>动态更新

所创建的数据挖掘查询在每次打开时都会动态更新。 例如：

- 如果你在 6 月 1 日创建设备固件版本报表，并在 6 月 10 日再次打开该报表，则此报表将更新包含 6 月 10 日的准确信息。

- 如果在 6 月 1 日创建报表来检测过去 30 天内发现的新设备，并在 6 月 30 日打开该报表，则将显示过去 30 天内的结果。

### <a name="data-mining-use-cases"></a>数据挖掘用例

可以使用查询来处理各种安全团队的各种安全需求：

- **SOC 事件响应**：生成实时报表以帮助处理即时事件响应。 例如，数据挖掘可以生成可能需要修补的设备列表的报表。

- **取证**：生成基于调查报表的历史数据的报表。

- **IT 网络完整性**：生成有助于提高总体网络安全的报表。 例如，生成可列出身份验证凭据较弱的设备的报表。

- **可见性**：生成涵盖所有查询项目以查看网络所有基线参数的报表。

## <a name="data-mining-storage"></a>数据挖掘存储

除删除设备以外，数据挖掘信息会不断保存并存储。 可将数据挖掘结果导出并存储在外部安全服务器中。 此外，传感器还会每日执行自动备份，以确保系统连续性和数据的保留。


## <a name="predefined-data-mining-queries"></a>预定义数据挖掘查询

提供以下预定义查询。 这些查询实时生成。

- **CVE**：过去 24 小时内检测到的具有已知漏洞的设备的列表。

- **排除的 CVE**：已手动排除的所有 CVE 的列表。 若要获取更精确的 VA 报表和攻击途径结果，可以通过包含和排除 CVE 来手动自定义 CVE 列表。

- **Internet 活动**：连接到 Internet 的设备。

- **非活动设备**：过去 7天 未通信的设备。

- **活动设备**：过去 24 小时内活动的网络设备。

- **远程访问**：通过远程会话协议进行通信的设备。

- **编程命令**：发送工业编程的设备。

“报表”屏幕中的这些报表自动可访问，便于 RO 用户和其他用户查看。 RO 用户无权访问数据挖掘报表。

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="数据挖掘屏幕。":::

## <a name="create-a-data-mining-query"></a>创建数据挖掘查询

要创建数据挖掘报表：

1. 在侧菜单中，选择“数据挖掘”。 此时将自动显示预定义的建议报表。

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="从侧窗格中选择数据挖掘。":::

2. 选择 :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::。

3. 选择“新建报表”并对其进行定义。

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="通过填写此屏幕创建新报表。":::

   提供下列参数：

   - 提供报表名称和说明。

   - 对于类别，请选择以下任一一个：

      - **类别(全部)** ，用于查看网络中所有设备的所有报表结果。

      - **泛型**，用于选择标准类别。

   - 选择感兴趣的特定报表参数。

   - 选择排序顺序（“排序依据”）。 基于活动或类别对结果进行排序。

   - 选择“保存到报表页”，可将报表结果保存为可从“报表”页访问的报表。 此操作支持 RO 用户运行你创建的报表。

   - 选择“筛选器(添加)”，可添加更多筛选器。 支持通配符请求。

   - 指定设备组（在设备映射中定义）。

   - 指定 IP 地址。

   - 指定端口。

   - 指定 MAC 地址。

4. 选择“保存”。 报表结果会在“数据挖掘”页中打开。

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="“数据挖掘”页中看到的报表结果。":::

### <a name="manage-data-mining-reports"></a>管理数据挖掘报表

下表介绍用于数据挖掘的管理选项：

| 图标图像 | 说明 |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | 编辑报表参数。 |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | 导出为 PDF。 |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |导出为 CSV。 |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | 显示其他信息，例如上次修改日期。 使用此功能可创建查询结果快照。 例如，你可能需要执行此操作以便与团队负责人或 SOC 分析师一起进行进一步调查。 |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | 在“报表”页中显示或隐藏。 :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="隐藏或显示报表。"::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | 删除报表。 |

#### <a name="create-customized-directories"></a>创建自定义目录 

通过为类别创建目录来组织数据挖掘查询的详细信息。 例如，可以为协议或位置创建目录。

要创建新目录：

1. 选择 :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: 添加新规则。

2. 选择“新建目录”，将显示“新建目录”窗体。

3. 为新目录命名。

4. 将所需的报表拖入新目录中。 可以随时将报表拖回主视图。

5. 右键单击新目录以打开、编辑或删除它。

#### <a name="create-snapshots-of-report-results"></a>创建报表结果的快照

你可能需要保存某些查询结果，以便进一步调查。 例如，你可能需要与各种安全团队共享结果。

快照保存在报表结果内，不会生成动态查询。

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="快照。":::

要创建快照：

1. 打开所需报表。

2. 选择信息图标 :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false":::。

3. 选择“新建”。

4. 输入快照的名称，并选择“保存”。

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="生成快照。":::

#### <a name="customize-the-cve-list"></a>自定义 CVE 列表

可以按如下所示手动自定义 CVE 列表：

  - 包括/排除 CVE

  - 更改 CVE 分数

要在 CVE 报表中手动执行更改：

1.  在侧菜单中，选择“数据挖掘”。

2. 在“数据挖掘”窗口的左上角选择 :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::。 然后，选择“新建报表”。

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="创建新报表。":::

3. 在左侧窗格中，选择以下选项之一：

   - **已知漏洞**：选择两个选项，并在报表的两个表中显示结果，一个表显示 CVE，另一个表显示排除的 CVE。

   - **CVE**：选择此选项以显示所有 CVE 的列表。

   - **排除的 CVE**：选择此选项以显示所有已排除的 CVE 的列表。

4. 在“名称”和“说明”中填写信息，然后选择“保存”。 新报表将显示在“数据挖掘”窗口中。

5. 若要排除 CVE，请打开 CVE 的数据挖掘报表。 此时将显示所有 CVE 的列表。

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="CVE 报表。":::

6. 若要选择列表中的项，请选择 :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: 并选择要自定义的 CVE。 底部将显示“操作”栏。

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="数据挖掘“操作”栏的屏幕截图。":::

7. 选择要排除的 CVE，然后选择“删除记录”。 所选的 CVE 不会显示在 CVE 列表中，它将显示在已排除的 CVE 的列表（生成此列表时）中。

8. 若要在 CVE 列表中包含已排除的 CVE，请生成已排除的 CVE 的报表，然后从该列表中删除要在 CVE 列表中再次包含的项。

9. 选择要在其中更改分数的 CVE，然后选择“更新 CVE 分数”。

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="更新 CVE 分数。":::

10. 输入新的分数，然后选择“确定”。 更新的分数将显示在所选的 CVE 中。



## <a name="sensor-reports-based-on-data-mining"></a>基于数据挖掘的传感器报表

可从“报表”选项访问的常规报表是预定义的数据挖掘报表。 它们不是数据挖掘中可用的动态查询，而是数据挖掘查询结果的静态表示形式。

数据挖掘查询结果对只读用户不可用。 希望只读用户有权访问数据挖掘查询生成的信息的管理员和安全分析师应将此信息保存为报表。

报表反映了数据挖掘查询结果生成的信息。 这包括默认数据挖掘报表，这些报表可在“报表”视图中找到。 管理员和安全分析师还可以生成自定义数据挖掘查询，并将它们保存为报表。 这些报表也对 RO 用户可用。

生成报表：

1. 在侧菜单中，选择“报表”。

2. 选择要显示的所需报表。 可以选择“自定义”或“自动生成”的报表，例如“编程命令”和“远程访问”。

3. 通过选择屏幕右上角的图标之一来导出报表：

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: 导出到 PDF 文件。

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: 导出到 CSV 文件。

> [!NOTE] 
> RO 用户只能查看为其创建的报表。

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="选择要生成的报表。":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="已生成的远程访问报表。":::

## <a name="on-premises-management-console-reports-based-on-data-mining-reports"></a>基于数据挖掘报表的本地管理控制台报表

通过本地管理控制台可以为连接到它的每个传感器生成报表。 报表基于执行的传感器数据挖掘查询。

你可以生成以下报表：

- **活动设备(过去 24 小时)** ：显示 24 小时内存在网络活动的设备的列表。

- **非活动设备(过去 7 天)** ：显示最近七天内没有任何网络活动的设备的列表。

- **编程命令**：显示最近 24 小时内发送了编程命令的设备的列表。

- **远程访问**：显示最近 24 小时内访问了远程源的设备的列表。

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="报表视图的屏幕截图。":::

从本地管理控制台选择该传感器时，该传感器上配置的所有自定义报表都会显示在报表列表中。 对于每个传感器，可以生成在该传感器上配置的默认报表或自定义报表。

生成报表：

1. 在左侧窗格中选择“报表”。 此时将显示“报表”窗口。

2. 从“传感器”下拉列表中，选择要为其生成报告的传感器。

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="传感器视图的屏幕截图。":::

3. 从右侧下拉列表中，选择要生成的报表。

4. 若要创建 PDF 格式的报表结果，请选择 :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false":::。
