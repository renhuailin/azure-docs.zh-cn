---
title: 可视化效果
description: 使用 Azure Synapse 笔记本直观显示你的数据
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: 523356947d6d5f93fa8ef2202ad6e7d235c6afdd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919319"
---
# <a name="visualize-data"></a>可视化数据
Azure Synapse 是一种集成的分析服务，可在数据仓库和大数据分析系统之间加快洞察的时间。 数据可视化是一项关键组件，能够深入了解你的数据。 这有助于人们更轻松地理解大数据和小数据。 它还可以更轻松地检测数据组中的模式、趋势和离群值。 

使用 Azure Synapse Analytics 中的 Apache Spark 时，有多种内置选项可帮助你可视化数据，其中包括 Synapse 笔记本图表选项、对常用开源库的访问以及与 Synapse SQL 和 Power BI 的集成。

## <a name="notebook-chart-options"></a>笔记本图表选项
使用 Azure Synapse 笔记本时，可以使用图表选项将表格结果视图转变为自定义图表。 在这里，无需编写任何代码即可直观显示数据。 

### <a name="displaydf-function"></a>显示 (df) 函数
```display```函数使你能够将 SQL 查询和 Apache Spark dataframes 和 rdd 转换为丰富的数据可视化效果。```display```函数可用于在 PySpark、Scala、Java 和 .net 中创建的 dataframes 或 rdd。

访问图表选项：
1. ```%%sql```默认情况下，幻命令的输出显示在呈现的表视图中。 你还可以 ```display(df)``` 在 Spark DataFrames 或弹性分布式数据集上调用 (RDD) 函数以生成呈现的表视图。 
   
2. 获得呈现的表视图后，切换到 "图表" 视图。
   ![内置图表](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. 现在可以通过指定以下值自定义可视化效果：
   | 配置 | 说明 |
   |--|--| 
   | 图表类型 | ```display```函数支持多种图表类型，包括条形图、散点图、折线图等 |
   | 键 | 指定 x 轴的值范围|
   | 值 | 指定 y 轴值的值范围 |
   | 序列组 | 用于确定聚合的组 | 
   | 聚合 | 用于聚合可视化对象中的数据的方法| 
   
   
    > [!NOTE]
    > 默认情况下，该 ```display(df)``` 函数将只接受数据的前1000行来呈现图表。 检查 **所有结果的聚合** ，并单击 " **应用** " 按钮，将从整个数据集中应用图表生成。 当图表设置发生更改时，将触发 Spark 作业。 请注意，完成计算并呈现图表可能需要几分钟时间。
   
4. 完成后，可以查看最终的可视化效果并与其交互！

### <a name="displaydf-statistic-details"></a>显示 (df) 统计信息详细信息
您可以使用 <code>display(df, summary = true)</code> 来检查给定 Apache Spark 数据帧的统计信息摘要，其中包括列名、列类型、唯一值和每列的缺失值。 您还可以选择特定列以查看其最小值、最大值、平均值和标准偏差。
    ![内置图表-摘要](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtmldf-option"></a>displayHTML (df) 选项
Azure Synapse Analytics 笔记本使用函数支持 HTML 图形 ```displayHTML``` 。

下图是使用 [D3.js](https://d3js.org/)创建可视化效果的示例。

   ![d3-js-示例](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

运行以下代码来创建上面的可视化效果。

```python
displayHTML("""<!DOCTYPE html>
<meta charset="utf-8">

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>常用库
当涉及到数据可视化时，Python 提供了多个图形库，这些库已打包为许多不同的功能。 默认情况下，Azure Synapse Analytics 中的每个 Apache Spark 池包含一组特选和常用的开源库。 还可以使用 Azure Synapse Analytics 库管理功能添加或管理其他库 & 版本。 

### <a name="bokeh"></a>Bokeh
可以使用呈现 HTML 或交互式库，如 **bokeh** ```displayHTML(df)``` 。 

下图是使用 bokeh 在地图上绘制字形的示例。

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

运行下面的示例代码，绘制上面的图像。

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```

### <a name="matplotlib"></a>Matplotlib
可以使用每个库的内置呈现功能，来呈现标准绘图库，如 Matplotlib。

下图是使用 **Matplotlib** 创建条形图的示例。
   ![折线图：](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

运行下面的示例代码，绘制上面的图像。

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```

### <a name="additional-libraries"></a>其他库 
除了这些库以外，Azure Synapse Analytics 运行时还包括以下一组库，这些库通常用于数据可视化：
- [Matplotlib](https://matplotlib.org/)
- [Bokeh](https://bokeh.org/)
- [Seaborn](https://seaborn.pydata.org/) 

有关可用库和版本的最新信息，请访问 Azure Synapse Analytics 运行时 [文档](./spark/../apache-spark-version-support.md) 。

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>使用 Apache Spark & SQL 点播连接到 Power BI
Azure Synapse Analytics 与 Power BI 紧密集成，使数据工程师能够构建分析解决方案。

Azure Synapse Analytics 允许不同的工作区计算引擎在其 Spark 池（预览版）和 SQL 按需版本引擎（预览版）之间共享数据库和表。 使用 [共享的元数据模型](https://docs.microsoft.com/azure/synapse-analytics/metadata/overview)，可以按需使用 SQL 查询 Apache Spark 表。 完成后，可以将 SQL 点播终结点连接到 Power BI，以便轻松地查询已同步的 Spark 表。


## <a name="next-steps"></a>后续步骤
- 有关如何设置 Spark SQL DW 连接器的详细信息，请： [SYNAPSE sql Connector](./spark/../synapse-spark-sql-pool-import-export.md)
- 查看默认库： [Azure Synapse Analytics 运行时](../spark/apache-spark-version-support.md)