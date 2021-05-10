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
ms.openlocfilehash: ea08c82ed5772be7e3a6094f5477c4450975c1fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775832"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>使用 Synapse Studio 监视 Apache Spark 应用程序

借助 Azure Synapse Analytics，可以使用 Apache Spark 在工作区中的 Apache Spark 池上运行笔记本、作业和其他类型的应用程序。

本文介绍如何监视 Apache Spark 应用程序，使你能够关注最新状态、问题和进度。

本教程涵盖以下任务：

* 监视运行中的 Apache Spark 应用程序
* 查看已完成的 Apache Spark 应用程序
* 查看已取消的 Apache Spark 应用程序
* 调试失败的 Apache Spark 应用程序

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前，请确保满足以下要求：

- Synapse Studio 工作区。 有关说明，请参阅[创建 Synapse Studio 工作区](../../machine-learning/how-to-manage-workspace.md#create-a-workspace)。

- Apache Spark 池。

## <a name="view-apache-spark-applications"></a>查看 Apache Spark 应用程序 
你可以从“监视” -> “Apache Spark 应用程序”查看所有 Apache Spark 应用程序 。
   ![Apache Spark 应用程序](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>查看已完成的 Apache Spark 应用程序

打开“监视”，然后选择“Apache Spark 应用程序” 。 若要查看已完成的 Apache Spark 应用程序的详细信息，请选择 Apache Spark 应用程序并查看详细信息。

  ![选择已完成的作业](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. 查看“已完成的任务”、“状态”和“总持续时间”  。

2. 刷新日志查询。

3. 单击“Spark History Server”，可打开 Apache Spark History Server 链接。

4. 查看“摘要”信息。

5. 查看“日志”。 你可以从下拉列表中选择不同类型的日志，可以单击“下载日志”来下载日志信息，还可选中“筛选错误和警告”复选框，筛选出需要的错误和警告 。

6. 你可以在生成的作业图中查看作业概况。 默认情况下，该图将显示所有作业。 可按作业 ID 筛选此视图。

7. 默认情况下，“进度”显示处于选中状态。 通过在“显示”下拉列表中选择“进度”/“读取”/“写入”/“持续时间”，可查看数据流    。

8. 若要播放作业，请单击“播放”按钮。 单击“停止”按钮即可随时停止。

9. 你可使用鼠标滚轮或滚动条放大和缩小作业图，还可以选择“缩放到合适大小”使图形适合屏幕大小。

10. 作业图节点将显示每个阶段的以下信息：

    * ID。

    * 名称或说明。

    * 总任务数。

    * 读取的数据：输入大小和随机读取大小之和。

    * 写入的数据：输出大小和随机写入大小之和。

    * 执行时间：从第一次尝试的开始时间到最后一次尝试的完成时间的时间和。

    * 行计数：输入记录、输出记录、随机读取记录和随机写入记录的总和。

    * 进度。

     ![查看已完成的作业](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. 单击图中的“查看详细信息”，随即将显示相应阶段的详细信息。

    ![阶段的详细信息](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>监视运行中的 Apache Spark 应用程序

打开“监视”，然后选择“Apache Spark 应用程序” 。 若要查看运行中的 Apache Spark 应用程序的详细信息，请选择提交 Apache Spark 应用程序并查看详细信息。 如果 Apache Spark 应用程序仍在运行，则可以监视进度。

   ![选择运行中的作业](./media/how-to-monitor-spark-applications/select-running-job.png)

1. 查看“已完成的任务”、“状态”和“总持续时间”  。

2. 取消 Apache Spark 应用程序。

3. 刷新日志查询。

4. 单击“Spark UI”按钮，切换到“Spark 作业”页面。

5. 查看图形。 你可以在生成的作业图中查看作业概况。 请参阅[查看已完成的 Apache Spark 应用程序](#view-completed-apache-spark-application)中的步骤 6、7、8、9、10。

6. 查看“摘要”信息。

7. 在“诊断”选项卡中可查看诊断信息。

8. 在此选项卡中可查看日志。你可以从下拉列表中选择不同类型的日志，可以单击“下载日志”来下载日志信息，还可选中“筛选错误和警告”复选框，筛选出需要的错误和警告 。

    ![查看运行中的作业](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>查看已取消的 Apache Spark 应用程序

打开“监视”，然后选择“Apache Spark 应用程序” 。 若要查看有关已取消的 Apache Spark 应用程序的详细信息，请选择 Apache Spark 应用程序并查看详细信息。

 ![选择已取消的作业](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. 查看“已完成的任务”、“状态”和“总持续时间”  。

2. 刷新日志查询。

3. 单击“Spark history server”，打开 Apache History Server 链接。

4. 查看图形。 你可以在生成的作业图中查看作业概况。 请参阅[查看已完成的 Apache Spark 应用程序](#view-completed-apache-spark-application)中的步骤 6、7、8、9、10。

5. 查看“摘要”信息。

6. 查看“日志”。 你可以从下拉列表中选择不同类型的日志，可以单击“下载日志”来下载日志信息，还可选中“筛选错误和警告”复选框，筛选出需要的错误和警告 。

7. 单击图中的“查看详细信息”，随即将显示相应阶段的详细信息。

   ![查看已取消的作业](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>调试失败的 Apache Spark 应用程序

打开“监视”，然后选择“Apache Spark 应用程序” 。 若要查看有关失败的 Apache Spark 应用程序的详细信息，请选择 Apache Spark 应用程序并查看详细信息。

![选择失败的作业](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. 查看“已完成的任务”、“状态”和“总持续时间”  。

2. 刷新日志查询。

3. 单击“Spark History Server”，可打开 Apache Spark History Server 链接。

4. 查看图形。 你可以在生成的作业图中查看作业概况。 请参阅[查看已完成的 Apache Spark 应用程序](#view-completed-apache-spark-application)中的步骤 6、7、8、9、10

5. 查看“摘要”信息。

6. 查看错误信息。

   ![失败的作业信息](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="compare-apache-spark-applications"></a>比较 Apache Spark 应用程序

可以通过两种方法来比较应用程序。 可以选择“比较应用程序”进行比较，也可以单击“在笔记本中比较”按钮在笔记本中进行查看。

### <a name="compare-by-choose-an-application"></a>通过选择应用程序进行比较

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
