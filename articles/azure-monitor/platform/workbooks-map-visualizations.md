---
title: Azure Monitor 工作簿地图可视化效果
description: 了解 Azure Monitor 工作簿地图可视化效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: 9b79efeeb90627bee6096c9142d8180896150295
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439641"
---
# <a name="map-visualization"></a>地图可视化效果

地图可视化通过提供聚合映射到每个位置/国家/地区的所有数据的功能，来帮助特定区域中的固定问题，并显示监视数据的高级聚合视图。

下面的屏幕截图显示了不同存储帐户的总事务数和端到端延迟。 此处的大小由事务总数确定，地图下面的颜色指标显示了端到端延迟。 第一次观察时，" **美国西部** " 区域中的事务数比 " **美国东部** " 区域小，但 " **美国西部** " 区域的端到端延迟高于 " **美国东部** " 区域。 这就为 **美国西部** 似乎的内容提供了初步见解。

![Azure 定位图的屏幕截图](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>添加映射

如果基础数据/指标包含纬度/经度信息、Azure 资源信息、Azure 位置信息或国家/地区、名称或国家/地区代码，则可以对地图进行可视化。

### <a name="using-azure-location"></a>使用 Azure location

1. 通过选择 " **编辑** " 工具栏项，将工作簿切换到编辑模式。
2. 选择“添加”，然后选择“添加查询” 。
3. 将 *数据源* 更改为， `Azure Resource Graph` 然后选择包含存储帐户的任何订阅。
4. 为分析输入以下查询，并选择 " **运行查询**"。

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. 将 *大小* 设置为 `Large` 。
6. 将 *可视化效果* 设置为 `Map` 。
7. 将会自动填充所有设置。 对于 "自定义设置"，请选择 " **映射设置** " 按钮以打开 "设置" 窗格。
8. 下面是地图可视化对象的屏幕截图，显示所选订阅的每个 Azure 区域的存储帐户。

![带有上述查询的 Azure 定位图的屏幕截图](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>使用 Azure 资源

1. 通过选择 " **编辑** " 工具栏项，将工作簿切换到编辑模式。
2. 选择 " **添加** "，然后 *添加度量值*。
3. 使用包含存储帐户的订阅。
4. 在资源中更改 *资源类型* `storage account` 选择多个存储帐户。 *Resource*
5. 选择 " **添加度量** 值" 并添加事务指标。
    1. 命名空间：`Account`
    2. 跃 `Transactions`
    3. 聚合 `Sum`
    
    ![事务指标的屏幕截图](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. 选择 " **添加度量值** "，然后添加 "成功 E2E 延迟指标"。
    1. 命名空间：`Account`
    1. 跃 `Success E2E Latency`
    1. 聚合 `Average`
    
    ![成功的端到端延迟指标的屏幕截图](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. 将 *大小* 设置为 `Large` 。
1. 将 *可视化效果* 大小设置为 `Map` 。
1. 在 " **地图设置** " 中，设置下列设置：
    1. 位置信息使用： `Azure Resource`
    1. Azure 资源字段： `Name`
    1. 大小依据： `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. 位置聚合： `Sum of values`
    1. 着色类型：`Heatmap`
    1. 颜色依据： `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. 颜色聚合： `Sum of values`
    1. 调色板：`Green to Red`
    1. 最小值： `0`
    1. 指标值： `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. 聚合其他指标按： `Sum of values`
    1. 选择 **自定义格式** 框
    1. 单位 `Milliseconds`
    1. 方式 `Decimal`
    1. 最大小数位： `2`

### <a name="using-countryregion"></a>使用国家/地区

1. 通过选择 " **编辑** " 工具栏项，将工作簿切换到编辑模式。
2. 选择 "*添加*"，然后选择 " *添加查询*"。
3. 将 *数据源* 更改为 `Log` 。
4. 选择 " *资源类型* `Application Insights` "，然后选择包含 pageViews 数据的任何 Application Insights 资源。
5. 使用查询编辑器为分析输入 KQL，并选择 " **运行查询**"。

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. 将大小值设置为 `Large` 。
7. 将可视化效果设置为 `Map` 。
8. 将会自动填充所有设置。 对于自定义设置，请选择 " **地图设置**"。

### <a name="using-latitudelocation"></a>使用纬度/位置

1. 通过选择 " **编辑** " 工具栏项，将工作簿切换到编辑模式。
2. 选择 "*添加*"，然后选择 " *添加查询*"。
3. 将 *数据源* 更改为 `JSON` 。
1. 在查询编辑器的下面输入 JSON 数据，并选择 " **运行查询**"。
1. 将 *大小* 值设置为 `Large` 。
1. 将 *可视化效果* 设置为 `Map` 。
1. 在 "度量值设置" 下的 **映射设置** 中，将 " *指标标签* " 设置为， `displayName` 然后选择 " **保存并关闭**"。

下面的地图可视化效果显示了具有选定度量值标签的每个纬度和经度位置的用户。

![地图可视化对象的屏幕截图，显示具有所选度量值标签的每个纬度和经度位置的用户](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>地图设置

### <a name="layout-settings"></a>布局设置

| 设置 | 说明 |
|:-------------|:-------------|
| `Location info using` | 选择一种方法来获取地图上显示的项的位置。 <br> **纬度/经度**：如果有包含纬度和经度信息的列，请选择此选项。 带有纬度和经度数据的每一行都将显示为地图上的不同项。 <br> **Azure 位置**：如果列中有 Azure location (eastus、westeurope、centralindia 等 ) 信息，请选择此选项。 指定该列后，它将提取每个 Azure 位置对应的纬度和经度，根据指定的聚合)  (分组同一位置行，以显示地图上的位置。 <br> **Azure 资源**：如果有列包含 Azure 资源信息 (存储帐户、cosmosdb 帐户等 ) ，请选择此选项。 指定该列并将为每个 Azure 资源提取相应的纬度和经度，将同一位置 (Azure location) 行 (基于指定的聚合) 一起显示地图上的位置。 <br> **国家/地区**：如果有一列的国家/地区名称/代码 (US、美国、IN、印度、CN、中国) 信息，请选择此选项。 指定该列并将为每个国家/地区/代码提取相应的纬度和经度，并将这些行与相同的 Country-Region 代码/国家/地区名称一起显示，以显示地图上的位置。 国家/地区名称和国家/地区代码不会作为地图上的单个实体组合在一起。
| `Latitude/Longitude` | 如果位置信息字段值为：纬度/经度，则这两个选项将可见。 在 "纬度" 字段中选择具有纬度的列，并分别选择 "经度" 字段中的纬度。 |
| `Azure location field` | 如果位置信息字段值为： Azure location，则会显示此选项。 选择 Azure 位置信息所在的列。 |
| `Azure resource field` | 如果位置信息字段值为： Azure 资源，则此选项可见。 选择 Azure 资源信息所在的列。 |
| `Country/Region field` | 如果 "位置信息" 字段值为 "国家或地区"，则会显示此选项。 选择国家/地区信息所在的列。 |
| `Size by` | 此选项控制在地图上显示的项的大小。 大小取决于用户指定的列中的值。 当前，圆的半径与列的值的平方根直接成比例。 如果 "None ..."选中时，所有圆圈将显示默认区域大小。|
| `Aggregation for location` | 此字段指定如何按具有相同 Azure 位置/Azure 资源/国家/地区的列聚合 **大小** 。 |
| `Minimum region size` | 此字段指定地图上显示的项的最小半径。 当 "大小" 列的值有很大差异时，将使用此值，因此，较小的项在地图上几乎不可见。 |
| `Maximum region size` | 此字段指定地图上显示的项的最大半径。 当 "大小" 列的值非常大，并且它们覆盖了地图的大区域时，将使用此方法。|
| `Default region size` | 此字段指定地图上显示的项的默认半径。 当 Size By 列为 "None ..." 时，将使用默认半径或值为0。|
| `Minimum value` | 用于计算区域大小的最小值。 如果未指定，最小值将是聚合后的最小值。 |
| `Maximum value` | 用于计算区域大小的最大值。 如果未指定此值，则最大值将是聚合后的最大值。|
| `Opacity of items on Map` | 此字段指定在地图上显示的项的透明度。 不透明度为1表示，无透明度，其中0表示不透明度，项在地图上不可见。 如果地图上的项过多，则不能将不透明度设置为 low 值，以便所有重叠项可见。|

### <a name="color-settings"></a>颜色设置

| 着色类型 | 说明 |
|:------------- |:-------------|
| `None` | 所有节点都具有相同的颜色。 |
| `Thresholds` | 在此类型中，单元颜色由阈值规则设置 (例如， _CPU > 90% => 红色，60% > cpu > 90% => 黄色，cpu < 60% => 绿色_) 。 <ul><li> **颜色依据**：此列的值将由阈值/热度地图逻辑使用。</li> <li>**颜色聚合**：此字段指定如何按具有相同 azure 位置/azure 资源/国家/地区的列聚合 **颜色** 。 </li> <ul> |
| `Heatmap` | 在此类型中，单元将基于调色板和 "按字段颜色" 进行着色。 这也会为颜色选项提供与阈值相同的 **颜色** 和 **聚合** 。 |

### <a name="metric-settings"></a>度量值设置
| 设置 | 说明 |
|:------------- |:-------------|
| `Metric Label` | 如果位置信息字段值为：纬度/经度，则可以看到此选项。 使用此功能，用户可以选取要为地图下显示的指标显示的标签。 |
| `Metric Value` | 此字段指定要在地图下方显示的指标值。 |
| `Create 'Others' group after` | 此字段指定创建 "其他" 组之前的限制。 |
| `Aggregate 'Others' metrics by` | 此字段指定在显示 "其他" 组时使用的聚合。 |
| `Custom formatting` | 使用此字段可为数字值设置单位、样式和格式设置选项。 这与 [网格的自定义格式](workbooks-grid-visualizations.md#custom-formatting)相同。|

## <a name="next-steps"></a>后续步骤

- 了解如何 [在工作簿中创建蜜标梳理数可视化](workbooks-honey-comb.md)对象。