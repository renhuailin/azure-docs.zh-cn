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
ms.openlocfilehash: 8d95897e0c2d58b2a3955918be945800eed9ba56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466086"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>使用 Synapse Studio 监视 Apache Spark 池

使用 Azure Synapse Analytics，可以使用 Spark 在工作区中的 Spark 池上运行笔记本、作业和其他类型的应用程序。

本文介绍如何监视 Apache Spark 池，使你能够关注池的状态，包括不同的工作区用户使用了多少个 Vcore。

## <a name="access-spark-pools-list"></a>访问 Spark 池列表

若要查看工作区中的 Apache Spark 池列表，请先 [打开 Synapse Studio](https://web.azuresynapse.net/) ，然后选择工作区。

![登录到工作区](./media/common/login-workspace.png)

打开工作区后，请选择左侧的 " **监视** " 部分。

![选择监视中心](./media/common/left-nav.png)

选择 **Apache Spark 池** 以查看 Apache Spark 池的列表。

 ![选择 Apache Spark 池](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-spark-pools"></a>筛选 Spark 池

你可以将 Spark 池的列表筛选为你感兴趣的池。 使用屏幕顶部的筛选器，可以指定要筛选的字段。

例如，你可以筛选视图以便仅查看包含名称 "dataprep" 的 Spark 池：

![示例筛选器](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-spark-pool"></a>查看有关特定 Spark 池的详细信息

若要查看有关某个 Spark 池的详细信息，请选择 Spark 池以查看详细信息。

![Apache Spark 池详细信息](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>后续步骤

有关监视管道运行的详细信息，请参阅 [在 Synapse Studio 中运行的监视管道](how-to-monitor-pipeline-runs.md) 一文。 

有关监视 Apache Spark 应用程序的详细信息，请参阅在 [Synapse Studio 中监视 Apache Spark 应用程序](how-to-monitor-spark-applications.md) 一文。
