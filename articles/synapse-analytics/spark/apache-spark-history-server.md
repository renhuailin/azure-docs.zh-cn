---
title: 使用扩展的 Spark History Server 调试应用
description: 在 Azure Synapse Analytics 中使用扩展的 Spark History Server 来调试和诊断 Spark 应用程序。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 2cdf4277c1177f0a963e7b7bb05813d47d2c1f57
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109814327"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>使用扩展的 Apache Spark History Server 调试和诊断 Apache Spark 应用程序

本文指导如何使用扩展的 Apache Spark History Server 来调试和诊断已完成的和正在运行的 Spark 应用程序。

扩展包括“数据”选项卡、“图形”选项卡和“诊断”选项卡。使用“数据”选项卡检查 Spark 作业的输入和输出数据。 “图形”选项卡显示作业图的数据流和重播。 “诊断”选项卡显示“数据倾斜”、“时间倾斜”和“执行程序使用情况分析”  。

## <a name="access-the-apache-spark-history-server"></a>访问 Apache Spark History Server

Apache Spark History Server 是已完成的和正在运行的 Spark 应用程序的 Web 用户界面。 你可以从 Azure Synapse Analytics 打开 Apache Spark History Server Web 界面。

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>从 Apache Spark 应用程序节点打开 Spark History Server Web UI

1. 打开 [Azure Synapse Analytics](https://web.azuresynapse.net/)。

2. 选择“监视”，然后选择“Apache Spark 应用程序” 。

    ![选择“监视”，然后选择 Spark 应用程序。](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. 选择一个应用程序，然后选择“日志查询”将其打开。

    ![打开日志查询窗口。](./media/apache-spark-history-server/open-application-window.png)

4. 选择“Spark History Server”，随即显示 Spark History Server Web UI。

    ![打开 Spark History Server。](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node&quot;></a>从数据节点打开 Spark History Server Web UI

1. 在 Synapse Studio 笔记本中，从作业执行输出单元或从笔记本文档底部的状态面板中选择“Spark History Server”。 选择“会话详细信息”。

   ![启动 Spark History Server 1](./media/apache-spark-history-server/launch-history-server2.png &quot;启动 Spark History Server")

2. 从滑出面板中选择“Spark History Server”。

   ![启动 Spark History Server 2](./media/apache-spark-history-server/launch-history-server.png "启动 Spark History Server")

## <a name="explore-the-data-tab-in-spark-history-server"></a>浏览 Spark History Server 中的“数据”选项卡

选择要查看的作业的作业 ID。 然后选择工具菜单中的“数据”以获取数据视图。 本部分说明如何在“数据”选项卡中执行各种任务。

* 分别选择“输入”、“输出”和“表操作”，以选中这些选项卡  。

    ![Spark 应用程序选项卡的数据](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* 选择“复制”可复制所有行。

    ![Spark 应用程序复制操作的数据](./media/apache-spark-history-server/apache-spark-data-copy.png)

* 选择“csv”可将所有数据另存为 CSV 文件。

    ![Spark 应用程序保存操作的数据](./media/apache-spark-history-server/apache-spark-data-save.png)

* 通过在“搜索”字段中输入关键字，可进行搜索。 搜索结果会立即显示。

    ![Spark 应用程序搜索操作的数据](./media/apache-spark-history-server/apache-spark-data-search.png)

* 选择列标题将表排序，选择加号展开某行以显示更多详细信息，或选择减号折叠某行。

    ![Spark 应用程序表的数据](./media/apache-spark-history-server/apache-spark-data-table.png)

* 选择“部分下载”可下载单个文件。 所选文件将下载到本地。 如果该文件不再存在，则会显示一个新选项卡，并显示一条错误消息。

    ![Spark 应用程序下载行的数据](./media/apache-spark-history-server/sparkui-data-download-row.png)

* 若要复制完整路径或相对路径，请选择下拉菜单中展开的“复制完整路径”或“复制相对路径”选项。 对于 Azure Data Lake Storage 文件，“在 Azure 存储资源管理器中打开”会启动 Azure 存储资源管理器并定位到登录时的文件夹。

    ![Spark 应用程序复制路径的数据](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* 如果一个页面中显示的行数过多，选择表下面的页码可以浏览不同的页面。

    ![Spark 应用程序页面的数据](./media/apache-spark-history-server/apache-spark-data-page.png)

* 将鼠标悬停在“数据”旁边的问号上可显示工具提示，或选择问号获取更多信息。

    ![Spark 应用程序更多信息的数据](./media/apache-spark-history-server/sparkui-data-more-info.png)

* 选择“向我们提供反馈”发送问题反馈。

    ![Spark 图形 -“向我们提供反馈”](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark History Server 中的“图形”选项卡

选择要查看的作业的作业 ID。 然后选择工具菜单中的“图形”以获取作业图视图。

### <a name="overview"></a>概述

你可以在生成的作业图中查看作业概况。 默认情况下，该图将显示所有作业。 可按作业 ID 筛选此视图。

![Spark 应用程序和作业图作业 ID](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>显示

默认情况下，“进度”显示处于选中状态。 通过在“显示”下拉列表中选择“已读取”或“已写入”，可查看数据流  。

![Spark 应用程序和作业图显示](./media/apache-spark-history-server/sparkui-graph-display.png)

图形节点显示热度地图图例中所示的颜色。

![Spark 应用程序和作业图热度地图](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>播放

若要播放作业，请选择“播放”。 你可以随时选择“停止”以停止播放。 任务颜色显示播放时的不同状态：

|颜色|含义|
|-|-|
|绿色|成功：作业已成功完成。|
|橙色|已重试：失败但不影响作业最终结果的任务实例。 这些任务包括可能稍后会成功的重复或重试实例。|
|蓝色|正在运行：任务正在运行。|
|White|正在等待或已跳过：任务正在等待运行，或已跳过该阶段。|
|Red|失败：任务失败。|

下图显示了绿色、橙色和蓝色状态颜色。

![Spark 应用程序和作业图颜色示例，正在运行](./media/apache-spark-history-server/sparkui-graph-color-running.png)

下图显示了绿色和白色状态颜色。

![Spark 应用程序和作业图颜色示例，跳过](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

下图显示了红色和绿色状态颜色。

![Spark 应用程序和作业图颜色示例，失败](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> 每个作业都可以播放。 未完成的作业不支持播放。

### <a name="zoom"></a>Zoom

使用鼠标滚轮放大和缩小作业图，或者选择“缩放到合适大小”使图形适合屏幕大小。

![Spark 应用程序和作业图缩放到合适大小](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>工具提示

如果存在失败的任务，将鼠标悬停在图形节点上可查看工具提示；选择阶段可打开阶段页。

![Spark 应用程序和作业图工具提示](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

在作业图选项卡上，如果阶段具有满足以下条件的任务，则会显示工具提示和小图标：

|条件|说明|
|-|-|
|数据倾斜|数据读取大小 > 此阶段内所有任务的平均数据读取大小 * 2 且数据读取大小 > 10 MB|
|时间倾斜|执行时间 > 此阶段内所有任务的平均执行时间 * 2 且执行时间 > 2 分钟|
   
![Spark 应用程序和作业图倾斜图标](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>图形节点说明

作业图节点显示每个阶段的以下信息：

  * ID。
  * 名称或说明。
  * 总任务数。
  * 读取的数据：输入大小和随机读取大小之和。
  * 写入的数据：输出大小和随机写入大小之和。
  * 执行时间：从第一次尝试的开始时间到最后一次尝试的完成时间的时间和。
  * 行计数：输入记录、输出记录、随机读取记录和随机写入记录的总和。
  * 进度。

    > [!NOTE]  
    > 默认情况下，作业图节点将显示每个阶段最后一次尝试的相关信息（阶段执行时间除外）。 但是，在播放期间，图形节点将显示每次尝试的相关信息。
    >  
    > 读取和写入的数据大小为 1MB = 1000 KB = 1000 * 1000 字节。

### <a name="provide-feedback"></a>提供反馈

选择“向我们提供反馈”发送问题反馈。

![Spark 应用程序和作业图形反馈](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>浏览 Apache Spark History Server 中的“诊断”选项卡

若要访问“诊断”选项卡，请选择作业 ID。 然后选择工具菜单中的“诊断”以获取作业的“诊断”视图。 诊断选项卡包括“数据倾斜”、“时间偏差”和“执行程序使用情况分析”  。

分别选择“数据倾斜”、“时间偏差”和“执行程序使用情况分析”，以选中这些选项卡  。

![SparkUI - 诊断数据偏斜选项卡](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>数据倾斜

选择“数据倾斜”选项卡时，系统会根据指定的参数显示相应的倾斜任务。

* **指定参数** - 第一部分显示用于检测数据倾斜的参数。 默认规则如下：读取的任务数据比读取的平均任务数据多三倍，读取的任务数据超过 10 MB。 若要为倾斜任务定义自己的规则，可以选择参数。 “倾斜阶段”和“倾斜图表”部分会相应地刷新 。

* **倾斜阶段** - 第二部分显示其任务满足上面指定的条件的阶段。 如果一个阶段中存在多个倾斜任务，则倾斜阶段表仅显示倾斜程度最高的任务（例如，数据倾斜的最大数据）。

    ![sparkui - 诊断数据偏斜选项卡](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **倾斜图表** - 选中倾斜阶段表中的行时，倾斜图表会根据数据读取和执行时间显示更多任务分布详细信息。 倾斜任务标记为红色，普通任务标记为蓝色。 该图表最多显示 100 个示例任务，并且任务详细信息显示在右下方面板中。

    ![sparkui - 阶段 10 的偏斜图表](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>时间倾斜

“时间倾斜”选项卡根据任务执行时间显示倾斜任务。

* **指定参数** - 第一部分显示用于检测时间倾斜的参数。 用于检测时间倾斜的默认条件是：任务执行时间是平均执行时间的三倍，任务执行时间大于 30 秒。 可以按需更改相关参数。 与上面的“时间倾斜”选项卡一样，倾斜阶段和倾斜图表显示相应的阶段和任务信息  。

* 选择“时间偏斜”，然后筛选的结果就会根据在“指定参数”部分设置的参数显示在“偏斜的阶段”部分。 选择“偏斜的阶段”部分的一个项目，然后相应的图表就会在第 3 部分绘制，任务详细信息显示在右下面板中。

    ![sparkui - 诊断时间偏斜部分](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>执行程序使用情况分析

执行程序使用情况图直观显示 Spark 作业执行程序的分配和运行状态。  

1. 选择“执行程序使用情况分析”，然后系统就会绘制四种类型的有关执行程序使用情况的曲线，包括已分配的执行程序、正在运行的执行程序、空闲执行程序，以及最大执行程序实例数。 对于已分配的执行程序，每个“已添加执行程序”或“已删除执行程序”事件都会增加或减少已分配执行程序数。 可以在“作业”选项卡中选中“事件时间线”以进行更多比较。

   ![sparkui 诊断执行程序选项卡](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. 选择彩色图标即可选中或取消选中所有草稿中的相应内容。

    ![sparkui 诊断选择图表](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>已知问题

使用可复原分布式数据集 (RDD) 的输入/输出数据不显示在“数据”选项卡中。

## <a name="next-steps"></a>后续步骤

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET for Apache Spark 文档](/dotnet/spark)

