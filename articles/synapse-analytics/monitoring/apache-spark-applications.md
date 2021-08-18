---
title: 使用 Synapse Studio 监视 Apache Spark 应用程序
description: 使用 Synapse Studio 监视 Apache Spark 应用程序。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.custom: contperf-fy21q4
ms.openlocfilehash: f83e84e3aa747c41d0e45a5650c704c796c62447
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968260"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>使用 Synapse Studio 监视 Apache Spark 应用程序

借助 Azure Synapse Analytics，可以使用 Apache Spark 在工作区中的 Apache Spark 池上运行笔记本、作业和其他类型的应用程序。

本文介绍如何监视 Apache Spark 应用程序，使你能够关注最新状态、问题和进度。

## <a name="view-apache-spark-applications"></a>查看 Apache Spark 应用程序 
你可以从“监视” -> “Apache Spark 应用程序”查看所有 Apache Spark 应用程序 。
   ![Apache Spark 应用程序](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>查看已完成的 Apache Spark 应用程序

打开“监视”，然后选择“Apache Spark 应用程序” 。 若要查看已完成的 Apache Spark 应用程序的详细信息，请选择 Apache Spark 应用程序并查看详细信息。

  ![选择已完成的作业](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. 查看“已完成的任务”、“状态”和“总持续时间”  。

2. 刷新该作业。

3. 单击“比较应用程序”以使用比较功能，有关此功能详细信息，请参阅[比较 Apache Spark 应用程序](#compare-apache-spark-applications) 。

4. 单击“Spark 历史记录服务器”，以打开“历史记录服务器”页面。

5. 查看“摘要”信息。

6. 在“诊断”选项卡中可查看诊断信息。

7. 查看“日志”。 可以通过在下拉列表中选择不同的选项来查看“Livy”、“预启动”和“驱动程序”等日志的完整日志  。 并且可以通过搜索关键字来直接检索所需日志信息。 单击“下载日志”，以将日志信息下载到本地，然后选中“筛选错误和警告”复选框，以筛选所需错误和警告 。

8. 你可以在生成的作业图中查看作业概况。 默认情况下，该图将显示所有作业。 可按作业 ID 筛选此视图。

9. 默认情况下，“进度”显示处于选中状态。 可以通过在“视图”下拉列表中选择“进度”/“读取”/“写入”/“持续时间”来检查数据流    。

10. 若要播放该作业，请单击“播放”按钮。 单击“停止”按钮即可随时停止。

11. 请使用滚动条来放大和缩小作业图，还可以选择“缩放到合适大小”来使其适合屏幕。

    [![查看已完成的作业](./media/how-to-monitor-spark-applications/view-completed-job.png)](./media/how-to-monitor-spark-applications/view-completed-job.png#lightbox)


12. 作业图节点将显示每个阶段的以下信息：

    - 作业 ID
    - 任务数目
    - 持续时间
    - 行计数
    - 读取的数据：输入大小和随机读取大小之和
    - 写入的数据：输出大小和随机写入大小之和
    - 阶段编号

       ![作业图节点](./media/how-to-monitor-spark-applications/job-graph-node.png)

13. 将鼠标悬停在某个作业上，在工具提示中将会显示作业详细信息：
    
    - 作业状态的图标：如果该作业状态为成功，它将会显示绿色的“√”；如果该作业检测到问题，则会显示黄色的“!”。
    - 作业 ID。
    - 常规部分：
      - 进度
      - 持续时间
      - 任务总数
    - 数据部分：
      - 任务总数
      - 读取大小
      - 写入大小
    - 倾斜部分：
      - 数据倾斜
      - 时间倾斜
    - 阶段编号

      ![将鼠标悬停在作业上](./media/how-to-monitor-spark-applications/hover-a-job.png)

14. 单击“阶段编号”，以展开该作业中包含的所有阶段。 单击作业 ID 旁边的“折叠”，以折叠该作业中的所有阶段。

15. 单击阶段图中的“查看详细信息”，阶段的详细信息将会显示。

    [![展开所有阶段](./media/how-to-monitor-spark-applications/expand-all-the-stages.png)](./media/how-to-monitor-spark-applications/expand-all-the-stages.png#lightbox)
    
## <a name="monitor-running-apache-spark-application"></a>监视运行中的 Apache Spark 应用程序

打开“监视”，然后选择“Apache Spark 应用程序” 。 若要查看运行中的 Apache Spark 应用程序的详细信息，请选择提交 Apache Spark 应用程序并查看详细信息。 如果 Apache Spark 应用程序仍在运行，则可以监视进度。

   ![选择运行中的作业](./media/how-to-monitor-spark-applications/select-running-job.png)

1. 查看“已完成的任务”、“状态”和“总持续时间”  。

2. 取消 Apache Spark 应用程序。

3. 刷新该作业。

4. 单击“Spark UI”按钮，切换到“Spark 作业”页面。

5. 对于“作业图”、“摘要”、“诊断”和“日志”   。 你可以在生成的作业图中查看作业概况。 请参阅[查看已完成的 Apache Spark 应用程序](#view-completed-apache-spark-application)中的第 5 至 15 步。 

    [![查看运行中的作业](./media/how-to-monitor-spark-applications/view-running-job.png)](./media/how-to-monitor-spark-applications/view-running-job.png#lightbox)

## <a name="view-canceled-apache-spark-application"></a>查看已取消的 Apache Spark 应用程序

打开“监视”，然后选择“Apache Spark 应用程序” 。 若要查看有关已取消的 Apache Spark 应用程序的详细信息，请选择 Apache Spark 应用程序并查看详细信息。

 ![选择已取消的作业](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. 查看“已完成的任务”、“状态”和“总持续时间”  。

2. 刷新该作业。

3. 单击“比较应用程序”以使用比较功能，有关此功能详细信息，请参阅[比较 Apache Spark 应用程序](#compare-apache-spark-applications) 。

4. 单击“Spark history server”，打开 Apache History Server 链接。

5. 查看图形。 你可以在生成的作业图中查看作业概况。 请参阅[查看已完成的 Apache Spark 应用程序](#view-completed-apache-spark-application)中的第 5 至 15 步。

  [![查看已取消的作业](./media/how-to-monitor-spark-applications/view-cancelled-job.png)](./media/how-to-monitor-spark-applications/view-cancelled-job.png#lightbox)

## <a name="debug-failed-apache-spark-application"></a>调试失败的 Apache Spark 应用程序

打开“监视”，然后选择“Apache Spark 应用程序” 。 若要查看有关失败的 Apache Spark 应用程序的详细信息，请选择 Apache Spark 应用程序并查看详细信息。

![选择失败的作业](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. 查看“已完成的任务”、“状态”和“总持续时间”  。

2. 刷新该作业。

3. 单击“比较应用程序”以使用比较功能，有关此功能详细信息，请参阅[比较 Apache Spark 应用程序](#compare-apache-spark-applications) 。

4. 单击“Spark history server”，打开 Apache History Server 链接。

5. 查看图形。 你可以在生成的作业图中查看作业概况。 请参阅[查看已完成的 Apache Spark 应用程序](#view-completed-apache-spark-application)中的第 5 至 15 步。

   [![失败的作业信息](./media/how-to-monitor-spark-applications/failed-job-info.png)](./media/how-to-monitor-spark-applications/failed-job-info.png#lightbox)


## <a name="view-input-dataoutput-data-for-apache-spark-application"></a>查看 Apache Spark 应用程序的输入数据/输出数据

选择 Apache Spark 应用程序，然后单击“输入数据/输出数据”选项卡，以查看 Apache Spark 应用程序的输入和输出的日期。 此函数可以更好地帮助调试 Spark 作业。 数据源支持三种存储方法：gen1、gen2 和 Blob。
    
“输入数据”选项卡
     
1. 单击“复制输入”按钮，以将输入文件粘贴到本地。

2. 单击“导出到 CSV”按钮，以 CSV 格式导出输入文件。

3. 您可以按输入关键字在“搜索框”中搜索文件（关键字包括文件名、读取格式和路径）。

4. 可以通过单击“名称”、“读取格式”和“路径”来对输入文件进行排序  。

5. 在将鼠标悬停在某个输入文件上时，将会出现“下载/复制路径/更多”按钮的图标。

   ![输入选项卡](./media/how-to-monitor-spark-applications/input-tab.png)

6. 单击“更多”按钮，“复制路径/在资源管理器中显示/属性”会显示上下文菜单 。
      
    ![输入更多内容](./media/how-to-monitor-spark-applications/input-more.png)

   * 复制路径：可以复制完整路径和相对路径 。
   * 在资源管理器中显示：可以跳转到链接的存储帐户（数据 -> 已链接）。
   * 属性：显示文件的基本属性（文件名/文件路径/读取格式/大小/修改时间）。

     ![属性图像](./media/how-to-monitor-spark-applications/properties.png)

“输出数据”选项卡

   具有与输入相同的功能。

   ![输出图像](./media/how-to-monitor-spark-applications/output.png)

## <a name="compare-apache-spark-applications"></a>比较 Apache Spark 应用程序

可以通过两种方法来比较应用程序。 可以选择“比较应用程序”进行比较，也可以单击“在笔记本中比较”按钮在笔记本中进行查看。

### <a name="compare-by-choosing-an-application"></a>通过选择应用程序进行比较

单击“比较应用程序”按钮并选择一个应用程序来比较性能，你可以直观地了解两个应用程序之间的差异。

![比较应用程序](./media/how-to-monitor-spark-applications/compare-applications.png)

![比较应用程序详细信息](./media/how-to-monitor-spark-applications/details-compare-applications.png)

1. 将鼠标悬停在应用程序上，随即将显示“比较应用程序”图标。

2. 单击“比较应用程序”图标，随即将弹出“比较应用程序”页面。

3. 单击“选择应用程序”按钮，打开“选择比较应用程序”页面 。

4. 选择比较应用程序时，你需要输入应用程序 URL，或从重复列表中进行选择。 然后单击“确定”按钮。 

   ![选择比较应用程序](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. 比较结果将显示在“比较应用程序”页上。

   ![比较结果](./media/how-to-monitor-spark-applications/comparison-result.png)

### <a name="compare-by-compare-in-notebook"></a>通过“在笔记本中比较”进行比较

单击“比较应用程序”页上的“在笔记本中比较”按钮以打开笔记本。 .ipynb 文件的默认名称为“Recurrent Application Analytics”。

![在笔记本中比较](./media/how-to-monitor-spark-applications/compare-in-notebook.png)

在“Notebook: Recurrent Application Analytics”文件中，你可以在设置“Spark pool”和“Language”后直接运行它。

![反复的应用程序分析](./media/how-to-monitor-spark-applications/recurrent-application-analytics.png)

## <a name="next-steps"></a>后续步骤

有关监视管道运行的详细信息，请参阅[使用 Synapse Studio 监视管道运行](how-to-monitor-pipeline-runs.md)一文。
