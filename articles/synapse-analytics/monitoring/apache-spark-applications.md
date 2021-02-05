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
ms.openlocfilehash: 00407b4a55b3c074fa77d4c3664f6e218c23fe21
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584496"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>使用 Synapse Studio 监视 Apache Spark 应用程序

使用 Azure Synapse Analytics，可以使用 Apache Spark 在工作区中的 Apache Spark 池上运行笔记本、作业和其他类型的应用程序。

本文介绍如何监视 Apache Spark 应用程序，使你能够关注最新的状态、问题和进度。

本教程涵盖以下任务：

* 运行 Apache Spark 应用程序的监视器
* 查看已完成的 Apache Spark 应用程序
* 查看已取消 Apache Spark 应用程序
* 调试失败的 Apache Spark 应用程序

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前，请确保满足以下要求：

- Synapse Studio 工作区。 有关说明，请参阅 [创建 Synapse Studio 工作区](../../machine-learning/how-to-manage-workspace.md#create-a-workspace)。

- Apache Spark 池。

## <a name="view-apache-spark-applications"></a>查看 Apache Spark 应用程序 
你可以从 "**监视**"  ->  **Apache Spark 应用程序** 中查看所有 Apache Spark 应用程序。
   ![apache spark 应用程序](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>查看已完成的 Apache Spark 应用程序

打开 " **监视器**"，然后选择 " **Apache Spark 应用程序**"。 若要查看有关已完成的 Apache Spark 应用程序的详细信息，请选择 Apache Spark 应用程序并查看详细信息。

  ![选择已完成的作业](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. 检查 " **已完成的任务**"、" **状态**" 和 " **总持续时间**"。

2. 刷新日志查询。

3. 单击 " **Spark history server**" 打开 Apache Spark history server 链接 "。

4. 检查 **摘要** 信息。

5. 检查 **日志**。 可以从下拉列表中选择不同类型的日志，还可以通过单击 " **下载日志**" 下载日志信息，然后选中 " **筛选错误和警告** " 复选框以筛选所需的错误和警告。

6. 可以在生成的作业关系图中查看作业的概述。 默认情况下，该图显示所有作业。 可按 **作业 ID** 筛选此视图。

7. 默认情况下， **进度** 显示处于选中状态。 可以通过 /  /  / 在 "**显示**" 下拉列表中选择 "进程读写 **持续时间**" 来检查数据流。

8. 若要播放作业，请单击 " **播放** " 按钮。 随时可以单击 " **停止** " 按钮停止。

9. 使用鼠标滚动或滚动条放大和缩小作业图，还可以选择 " **缩放到合适大小** " 以使其适合屏幕。

10. 作业图形节点显示每个阶段的下列信息：

    * ID。

    * 名称或说明。

    * 总任务数。

    * 读取的数据：输入大小和随机读取大小之和。

    * 数据写入：输出大小和无序写入大小的总和。

    * 执行时间：从第一次尝试的开始时间到最后一次尝试的完成时间的时间和。

    * 行计数：输入记录、输出记录、随机读取记录和随机写入记录的总和。

    * 进度。

     ![查看已完成作业](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. 单击关系图中的 " **查看详细信息** "，将显示 "阶段的详细信息"。

    ![阶段的详细信息](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>运行 Apache Spark 应用程序的监视器

打开 " **监视器**"，然后选择 " **Apache Spark 应用程序**"。 若要查看有关正在运行的 Apache Spark 应用程序的详细信息，请选择 "提交 Apache Spark 应用程序并查看详细信息。 如果 Apache Spark 的应用程序仍在运行，则可以监视进度。

   ![选择正在运行的作业](./media/how-to-monitor-spark-applications/select-running-job.png)

1. 检查 " **已完成的任务**"、" **状态**" 和 " **总持续时间**"。

2. **取消** Apache Spark 应用程序。

3. **刷新** 日志查询。

4. 单击 " **SPARK UI** " 按钮以切换到 spark 作业页面。

5. 查看图形。 可以在生成的作业关系图中查看作业的概述。 请参阅步骤6、7、8、9、完成的 [视图 Apache Spark 应用程序](#view-completed-apache-spark-application)。

6. 检查 **摘要** 信息。

7. 在 " **诊断** " 选项卡中查看诊断。

8. 检查此选项卡中的 **日志** 。可以从下拉列表中选择不同类型的日志，还可以通过单击 " **下载日志**" 下载日志信息，然后选中 " **筛选错误和警告** " 复选框以筛选所需的错误和警告。

    ![查看正在运行的作业](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>查看已取消 Apache Spark 应用程序

打开 " **监视器**"，然后选择 " **Apache Spark 应用程序**"。 若要查看有关已取消的 Apache Spark 应用程序的详细信息，请选择 Apache Spark 应用程序并查看详细信息。

 ![选择已取消作业](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. 检查 " **已完成的任务**"、" **状态**" 和 " **总持续时间**"。

2. 刷新日志查询。

3. 通过单击 " **Spark 历史记录服务器**" 打开 Apache history 服务器链接。

4. 查看图形。 可以在生成的作业关系图中查看作业的概述。 请参阅步骤6、7、8、9、完成的 [视图 Apache Spark 应用程序](#view-completed-apache-spark-application)。

5. 检查 **摘要** 信息。

6. 检查 **日志**。 可以从下拉列表中选择不同类型的日志，还可以通过单击 " **下载** 日志" 下载日志信息，然后选中 " **筛选错误和警告** " 复选框以筛选所需的错误和警告。

7. 单击关系图中的 " **查看详细信息** "，将显示 "阶段的详细信息"。

   ![查看已取消作业](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>调试失败的 Apache Spark 应用程序

打开 " **监视器**"，然后选择 " **Apache Spark 应用程序**"。 若要查看有关失败的 Apache Spark 应用程序的详细信息，请选择 Apache Spark 应用程序并查看详细信息。

![选择失败的作业](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. 检查 " **已完成的任务**"、" **状态**" 和 " **总持续时间**"。

2. 刷新日志查询。

3. 单击 " **Spark history server**" 打开 Apache Spark history server 链接 "。

4. 查看图形。 可以在生成的作业关系图中查看作业的概述。 请参阅步骤6、7、8、9、完成的 [视图 Apache Spark 应用程序](#view-completed-apache-spark-application)

5. 检查 **摘要** 信息。

6. 检查错误信息。

   ![失败的作业信息](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="compare-apache-spark-applications"></a>比较 Apache Spark 应用程序
单击 " **比较应用程序** " 按钮并选择用于比较性能的应用程序，用户可以直观地了解两个应用程序之间的差异。

![比较应用程序](./media/how-to-monitor-spark-applications/compare-applications.png)

1. 使用鼠标将鼠标悬停在应用程序上，然后显示 " **比较应用程序** " 图标。

2. 单击 " **比较应用程序** " 图标，随即弹出 "比较应用程序" 页。

3. 单击 " **选择应用程序** " 按钮打开 " **选择比较应用程序** " 页。

4. 选择比较应用程序时，用户需要输入应用程序 URL，或从定期列表中进行选择。 然后单击 **"确定"** 按钮。 

   ![选择比较应用程序](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. 比较结果将显示在 "比较应用程序" 页上。

   ![比较结果](./media/how-to-monitor-spark-applications/comparison-result.png)


## <a name="next-steps"></a>后续步骤

有关监视管道运行的详细信息，请参阅 [使用 Synapse Studio 运行监视管道](how-to-monitor-pipeline-runs.md) 一文。
