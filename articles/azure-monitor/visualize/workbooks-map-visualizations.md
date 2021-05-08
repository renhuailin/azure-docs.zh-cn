---
title: Azure Monitor 工作簿地图可视化效果
description: 了解有关 Azure Monitor 工作簿地图可视化效果的信息。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: dd9bafab982923785898b7f135e5c0408aa19a14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100607640"
---
# <a name="map-visualization"></a>地图可视化效果

通过提供聚合映射到每个位置/国家/地区/区域的所有数据的功能，地图可视化效果可帮助查明特定区域中的问题，并显示监视数据的大致聚合视图。

下面的屏幕截图显示了不同存储帐户的总事务数和端到端延迟。 这里的大小由事务总数确定，地图下方的颜色指标显示了端到端延迟。 在第一次观察时，“美国西部”区域中的事务数比“美国东部”区域少，但“美国西部”区域的端到端延迟高于“美国东部”区域   。 这样就形成了 **美国西部** 有异常状况的初步见解。

![Azure 位置图的屏幕截图](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>添加地图

如果基础数据/指标具有纬度/经度信息、Azure 资源信息、Azure 位置信息或国家/地区/区域、名称或国家/地区/区域代码，则可让地图呈现可视化效果。

### <a name="using-azure-location"></a>使用 Azure 位置

1. 选择“编辑”工具栏项，将工作簿切换到编辑模式。
2. 选择“添加”，然后选择“添加查询” 。
3. 将“数据源”更改为 `Azure Resource Graph`，然后选择具有存储帐户的任何订阅。
4. 为分析输入以下查询，然后选择“运行查询”。

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. 将“大小”设置为 `Large`。
6. 将“可视化效果”设置为 `Map`。
7. 所有设置将自动填充。 对于自定义设置，选择“地图设置”按钮以打开“设置”窗格。
8. 下面是地图可视化效果的屏幕截图，该屏幕截图显示了所选订阅的每个 Azure 区域的存储帐户。

![带有上述查询的 Azure 位置地图的屏幕截图](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>使用 Azure 资源

1. 选择“编辑”工具栏项，将工作簿切换到编辑模式。
2. 选择“添加”，然后选择“添加指标”。
3. 使用具有存储帐户的订阅。
4. 将“资源类型”更改为 `storage account`，然后在“资源”中选择多个存储帐户 。
5. 选择“添加指标”并添加事务指标。
    1. 命名空间：`Account`
    2. 指标：`Transactions`
    3. 聚合：`Sum`
    
    ![事务指标的屏幕截图](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. 选择“添加指标”，然后添加成功的 E2E 延迟指标。
    1. 命名空间：`Account`
    1. 指标：`Success E2E Latency`
    1. 聚合：`Average`
    
    ![成功的端到端延迟指标的屏幕截图](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. 将“大小”设置为 `Large`。
1. 将“可视化效果”的大小设置为 `Map`。
1. 在“地图设置”中指定以下设置：
    1. 使用的位置信息：`Azure Resource`
    1. Azure 资源字段：`Name`
    1. 设置大小的依据：`microsoft.storage/storageaccounts-Transaction-Transactions`
    1. 位置聚合：`Sum of values`
    1. 着色类型：`Heatmap`
    1. 着色依据：`microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. 颜色聚合：`Sum of values`
    1. 调色板：`Green to Red`
    1. 最小值：`0`
    1. 指标值：`microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. 聚合其他指标的依据：`Sum of values`
    1. 选择“自定义格式设置”框
    1. 单位：`Milliseconds`
    1. 样式：`Decimal`
    1. 最大小数位数：`2`

### <a name="using-countryregion"></a>使用国家/地区

1. 选择“编辑”工具栏项，将工作簿切换到编辑模式。
2. 选择“添加”，然后选择“添加查询” 。
3. 将“数据源”更改为 `Log`。
4. 选择“资源类型”作为 `Application Insights`，然后选择包含 pageViews 数据的任何 Application Insights 资源。
5. 使用查询编辑器输入用于分析的 KQL，然后选择“运行查询”。

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. 将“大小”值设置为 `Large`。
7. 将“可视化效果”设置为 `Map`。
8. 所有设置将自动填充。 对于自定义设置，请选择“地图设置”。

### <a name="using-latitudelocation"></a>使用纬度/位置

1. 选择“编辑”工具栏项，将工作簿切换到编辑模式。
2. 选择“添加”，然后选择“添加查询” 。
3. 将“数据源”更改为 `JSON`。
1. 在下面的查询编辑器中输入 JSON 数据，然后选择“运行查询”。
1. 将“大小”值设置为 `Large`。
1. 将“可视化效果”设置为 `Map`。
1. 在“指标设置”下的“地图设置”中，将“指标标签”设置为 `displayName`，然后选择“保存并关闭”。

下面的地图可视化效果显示了每个经度和纬度位置的用户，并带有选定的指标标签。

![地图可视化效果的屏幕截图，其中显示了每个经度和纬度位置的用户，并带有选定的指标标签](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>地图设置

### <a name="layout-settings"></a>布局设置

| 设置 | 说明 |
|:-------------|:-------------|
| `Location info using` | 选择一种方法来获取地图上显示的项的位置。 <br> **纬度/经度**：如果有包含纬度和经度信息的列，请选择此选项。 包含纬度和经度数据的每一行将在地图上显示为不同的项。 <br> Azure 位置：如果有包含 Azure 位置（eastus、westeurope、centralindia 等）信息的列，则请选择此选项。 指定该列，它将为每个 Azure 位置获取相应的纬度和经度，将相同的位置行（基于指定的聚合）分组到一起，以在地图上显示位置。 <br> **Azure 资源**：如果存在包含 Azure 资源信息（存储帐户、cosmosdb 帐户等）的列，请选择此选项。 指定该列，它将为每个 Azure 资源获取相应的纬度和经度，将相同的位置（Azure 资源）行（基于指定的聚合）分组到一起，以在地图上显示位置。 <br> 国家/地区：如果有包含国家/地区名称/代码（US、美国、IN、印度、CN、中国）信息的列，则请选择此选项。 指定该列，它将为每个国家/地区/区域/代码提取相应的纬度和经度，并将行与相同的国家/地区代码或国家/地区名称分组到一起，以在地图上显示位置。 国家/地区名称和国家/地区代码不会在地图上作为单个实体分组到一起。
| `Latitude/Longitude` | 如果“位置信息”字段值为以下值，则可以看到这两个选项：纬度/经度。 选择在纬度字段中和在经度字段中分别具有纬度和经度的列。 |
| `Azure location field` | 如果“位置信息”字段值为以下值，则可以看到此选项：Azure 位置。 选择 Azure 位置信息所在的列。 |
| `Azure resource field` | 如果“位置信息”字段值为以下值，则可以看到此选项：Azure 资源。 选择 Azure 资源信息所在的列。 |
| `Country/Region field` | 如果“位置信息”字段值为以下值，则可以看到此选项：国家或地区。 选择国家/地区信息所在的列。 |
| `Size by` | 此选项控制在地图上显示的项的大小。 大小取决于用户指定列中的值。 目前，圆的半径与列值的平方根成正比。 如果选择“无…”，则所有圆都将显示默认区域大小。|
| `Aggregation for location` | 此字段指定如何聚合具有相同 Azure 位置/Azure 资源或国家/地区的“设置大小的依据”列。 |
| `Minimum region size` | 此字段指定地图上显示的项的最小半径。 当“设置大小的依据”列的值之间存在很大差异时使用此设置，因此较小的项在地图上几乎不可见。 |
| `Maximum region size` | 此字段指定地图上显示的项的最大半径。 当“设置大小的依据”列的值非常大且覆盖地图上的大片区域时，将使用此设置。|
| `Default region size` | 此字段指定地图上显示的项的默认半径。 当“设置大小的依据”列为“无…”或值为 0 时，将使用默认半径。|
| `Minimum value` | 用于计算区域大小的最小值。 如果未指定，则最小值将是聚合后的最小值。 |
| `Maximum value` | 用于计算区域大小的最大值。 如果未指定，则最大值将是聚合后的最大值。|
| `Opacity of items on Map` | 此字段指定地图上显示的项的透明度。 不透明度 1 表示没有透明度，而不透明度 0 表示项在地图上不可见。 如果地图上的项太多，可以将不透明度设置为较低的值，以便所有重叠的项都可见。|

### <a name="color-settings"></a>颜色设置

| 着色类型 | 说明 |
|:------------- |:-------------|
| `None` | 所有节点都具有相同的颜色。 |
| `Thresholds` | 在此类型中，单元颜色由阈值规则设置（例如 CPU > 90% => 红色，60% > CPU > 90% => 黄色，CPU < 60% => 绿色）。 <ul><li> **着色依据**：阈值/热度地图逻辑将使用此列的值。</li> <li>**颜色聚合**：此字段指定如何聚合具有相同 Azure 位置/Azure 资源或国家/地区的“着色依据”列。 </li> <ul> |
| `Heatmap` | 在此类型中，单元根据调色板和“着色依据”字段进行着色。 这还将具有与阈值相同的“着色依据”和“颜色聚合”选项 。 |

### <a name="metric-settings"></a>指标设置
| 设置 | 说明 |
|:------------- |:-------------|
| `Metric Label` | 如果“位置信息”字段值为以下值，则可以看到此选项：纬度/经度。 使用此功能，用户可以选取要为地图下方显示的指标显示的标签。 |
| `Metric Value` | 此字段指定要在地图下方显示的指标值。 |
| `Create 'Others' group after` | 此字段指定创建“其他”组之前的限制。 |
| `Aggregate 'Others' metrics by` | 此字段指定用于“其他”组的聚合（如果显示的话）。 |
| `Custom formatting` | 使用此字段可以设置数字值的单位、样式和格式设置选项。 这与[网格的自定义格式设置](../visualize/workbooks-grid-visualizations.md#custom-formatting)相同。|

## <a name="next-steps"></a>后续步骤

- 了解如何[在工作簿中创建蜂巢可视化效果](../visualize/workbooks-honey-comb.md)。