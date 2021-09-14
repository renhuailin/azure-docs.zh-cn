---
title: 如何在 Synapse Studio 中监视 Apache Spark 池
description: 了解如何使用 Synapse Studio 监视 Apache Spark 池。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: c6148f2bd5d3b1555ae61d2da3e922c9cfe632cb
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539652"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>使用 Synapse Studio 监视 Apache Spark 池

借助 Azure Synapse Analytics，可以使用 Apache Spark 在工作区中的 Apache Spark 池上运行笔记本、作业和其他类型的应用程序。

本文介绍如何监视 Apache Spark 池，使你能够时刻关注池的状态，包括不同工作区的用户正在使用多少个 Vcore。

## <a name="access-apache-spark-pools-list"></a>访问 Apache Spark 池列表

若要查看工作区中的 Apache Spark 池列表，请先[打开 Synapse Studio](https://web.azuresynapse.net/)，然后选择工作区。

![登录到工作区](./media/common/login-workspace.png)

打开工作区后，选择左侧的“监视”部分。

![选择“监视”选项卡](./media/common/left-nav.png)

选择 Apache Spark 池以查看 Apache Spark 池的列表。

 ![选择 Apache Spark 池](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-apache-spark-pools"></a>筛选 Apache Spark 池

可以筛选 Apache Spark 池列表，以便仅查看你感兴趣的池。 使用屏幕顶部的筛选器，可以指定要筛选的字段。

例如，可以对视图进行筛选，以便仅查看包含名称“dataprep”的 Apache Spark 池：

![示例筛选器](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-pool"></a>查看有关特定 Apache Spark 池的详细信息

若要查看有关某个 Apache Spark 池的详细信息，请选择该 Apache Spark 池以查看详细信息。

![Apache Spark 池详细信息](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>后续步骤

若要详细了解如何监视管道运行，请参阅[在 Synapse Studio 中监视管道运行](how-to-monitor-pipeline-runs.md)一文。 

有关监视 Apache Spark 应用程序的详细信息，请参阅[在 Synapse Studio 中监视 Apache Spark 应用程序](how-to-monitor-spark-applications.md)一文。
