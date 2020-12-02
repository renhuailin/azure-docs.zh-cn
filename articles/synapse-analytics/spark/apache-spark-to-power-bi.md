---
title: Azure Synapse Studio 笔记本
description: 本教程提供了如何使用 Apache Spark 和无服务器 SQL 池来创建 Power BI 仪表板的概述。
services: synapse-analytics
author: midesa
ms.author: midesa
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: ea8fcb602f49dec61187260e08d3ccd1b148cee8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95918906"
---
# <a name="tutorial-create-a-power-bi-report-using-apache-spark-and-azure-synapse-analytics"></a>教程：使用 Apache Spark 和 Azure Synapse Analytics 创建 Power BI 报表

在为关键业务利益干系人提供服务之前，组织通常需要处理大量数据。 本教程介绍如何利用 Azure Synapse Analytics 中的集成体验以使用 Apache Spark 来处理数据，并在之后通过 Power BI 和无服务器 SQL 向最终用户提供数据。

## <a name="before-you-begin"></a>开始之前
- [Azure Synapse Analytics 工作区](../quickstart-create-workspace.md)，其中 ADLS Gen2 存储帐户配置为默认存储。 
- 用于可视化数据的 Power BI 工作区和 Power BI Desktop。 有关详细信息，请参阅创建 [Power BI 工作区](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces)和[安装 Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- 用于连接 Azure Synapse Analytics 和 Power BI 工作区的链接服务。 有关详细信息，请参阅[链接到 Power BI 工作区](../quickstart-power-bi.md)
- Synapse Analytics 工作区中的无服务器 Apache Spark 池。 有关详细信息，请参阅[创建无服务器 Apache Spark 池](../quickstart-create-apache-spark-pool-studio.md)
  
## <a name="download-and-prepare-the-data"></a>下载并准备数据
在此示例中，使用 Apache Spark 对纽约的出租车小费数据执行一些分析。 数据通过 [Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)提供。 此数据集的子集包含有关黄色出租车行程的信息，其中包括有关每次行程、开始和结束时间、位置、成本和其他感兴趣属性的信息。

1. 通过将代码粘贴到新单元格，运行以下行来创建 Spark 数据帧。 该过程会通过开放数据集 API 检索数据。 拉取所有这些数据将生成约 15 亿行。 下面的代码示例使用 start_date 和 end_date 应用一个会返回单个月份数据的筛选器。
   
   ```python
    from azureml.opendatasets import NycTlcYellow
    from dateutil import parser
    from datetime import datetime

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
   ```
2. 通过使用 Apache Spark SQL，我们将创建一个名为 NycTlcTutorial 的数据库。 我们将使用此数据库来存储数据处理的结果。
   ```python
   %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS NycTlcTutorial")
   ```
3. 接下来，我们将使用 Spark 数据帧操作来处理数据。 在以下代码中，我们将执行以下转换：
   1. 删除不需要的列。
   2. 通过筛选删除离群值/错误值。
   3. 创建新功能（如 ```tripTimeSecs``` 和 ```tipped```）以进行其他分析。
    ```python
    from pyspark.sql.functions import unix_timestamp, date_format, col, when

    taxi_df = filtered_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                    , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                    , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                    , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                    , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                    , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                    )\
                            .filter((filtered_df.passengerCount > 0) & (filtered_df.passengerCount < 8)\
                                    & (filtered_df.tipAmount >= 0) & (filtered_df.tipAmount <= 25)\
                                    & (filtered_df.fareAmount >= 1) & (filtered_df.fareAmount <= 250)\
                                    & (filtered_df.tipAmount < filtered_df.fareAmount)\
                                    & (filtered_df.tripDistance > 0) & (filtered_df.tripDistance <= 100)\
                                    & (filtered_df.rateCodeId <= 5)
                                    & (filtered_df.paymentType.isin({"1", "2"})))
    ```
4. 最后，我们将使用 Apache Spark ```saveAsTable``` 方法保存数据帧。 这样，你之后就可以使用无服务器 SQL 池来查询并连接到同一个表。
   
## <a name="query-data-using-serverless-sql-pools"></a>使用无服务器 SQL 池查询数据
Azure Synapse Analytics 允许不同的工作区计算引擎在其无服务器 Apache Spark 池（预览版）与无服务器 SQL 池（预览版）之间共享数据库和表。 这是通过 Synapse [共享元数据管理](../metadata/overview.md)功能实现的。 因此，Spark 创建的数据库及其基于 Parquet 的表将显示在工作区无服务器 SQL 池。

使用无服务器 SQL 池查询 Apache Spark 表：
   1. 保存 Apache Spark 表后，切换到“数据”选项卡。
   
   2. 在“工作区”下，找到刚创建的 Apache Spark 表，依次选择“新建 SQL 脚本”和“选择前 100 行”  。 
      
      :::image type="content" source="../spark/media/apache-spark-power-bi/query-spark-table-with-sql.png" alt-text="SQL 查询。" border="true":::

   3. 你可以继续优化查询，甚至可以使用 SQL 图表选项将结果可视化。

## <a name="connect-to-power-bi"></a>连接到 Power BI
接下来，我们会将无服务器 SQL 池连接到 Power BI 工作区。 连接工作区后，你将能够直接从 Azure Synapse Analytics 和 Power BI Desktop 创建 Power BI 报表。

>[!Note]
> 在开始之前，需要设置一个指向 [Power BI 工作区](../quickstart-power-bi.md)的链接服务，并下载 [Power BI Desktop](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces)。  

将无服务器 SQL 池连接到 Power BI 工作区：

1.  导航到“Power BI 数据库”选项卡，然后选择“+ 新建数据库”选项 。 在提示符处，从要用作数据源的 SQL Analytics 数据库下载 .pbids 文件。 
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-datasets.png" alt-text="Power BI 数据集。" border="true":::

2.  使用 Power BI Desktop 打开该文件，创建数据集。 打开文件后，使用 Microsoft 帐户和“导入”选项连接到 SQL Server 数据库 。 
   

## <a name="create-a-power-bi-report"></a>创建 Power BI 报表
1. 在 Power BI Desktop 中，你现在可以将“关键影响因素”图表添加到报表中。
   
   1. 将平均值“tipAmount”列拖动到“分析轴”。
   
   2. 将“weekdayString”、平均值“tripDistance”和平均值“tripTimeSecs”列拖动到“解释依据”轴   。 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-desktop.png" alt-text="Power BI Desktop。" border="true":::

2. 在 Power BI Desktop“主页”选项卡上，选择“发布”和“保存”，以发布并保存更改 。 输入文件名，并将此报表保存到 NycTaxiTutorial 工作区。
   
3. 此外，你也可以从 Azure Synapse Analytics 工作区中创建 Power BI 可视化效果。 为此，请导航到 Azure Synapse 工作区中的“开发”选项卡，然后打开“Power BI”选项卡。在此处，你可以选择你的报表，然后继续生成其他可视化效果。 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-synapse.png" alt-text="Azure Synapse Analytics 工作区。" border="true":::

若要更详细地了解如何通过无服务器 SQL 创建数据集并连接到 Power BI，你可以访问这一关于[连接到 Power BI Desktop](../../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md) 的教程

## <a name="next-steps"></a>后续步骤
通过访问以下文档和教程，可以继续详细了解 Azure Synapse Analytics 中的数据可视化功能：
   - [通过用无服务器 Apache Spark 池可视化数据](../spark/apache-spark-data-visualization-tutorial.md)
   - [通过 Apache Spark 池进行数据可视化的概述](../spark/apache-spark-data-visualization.md)