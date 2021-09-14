---
title: 如何在 Synapse Studio 中监视 Apache Spark 应用程序
description: 了解如何使用 Synapse Studio 监视 Apache Spark 应用程序。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 9666b8c9424b03c34b1ac332ef7ceb2f617c96a3
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542289"
---
# <a name="monitor-your-apache-spark-applications-in-synapse-studio"></a>在 Synapse Studio 中监视 Apache Spark 应用程序

借助 Azure Synapse Analytics，可以使用 Apache Spark 在工作区中的 Apache Spark 池上运行笔记本、作业和其他类型的应用程序。

本文介绍如何监视 Apache Spark 应用程序，使你能够关注最新状态、问题和进度。

## <a name="access-apache-spark-applications-list"></a>访问 Apache Spark 应用程序列表

若要查看工作区中的 Apache Spark 应用程序列表，请先[打开 Synapse Studio](https://web.azuresynapse.net/)，然后选择工作区。

![登录到工作区](./media/common/login-workspace.png)

打开工作区后，选择左侧的“监视”部分。

![选择“监视”中心](./media/common/left-nav.png)

选择“Apache Spark 应用程序”以查看 Apache Spark 应用程序列表。

 ![选择 Spark 应用程序](./media/how-to-monitor-spark-applications/monitor-hub-nav-spark-applications.png)

## <a name="filter-your-apache-spark-applications"></a>筛选 Apache Spark 应用程序

可以筛选 Apache Spark 应用程序列表，仅查看你感兴趣的应用程序。 使用屏幕顶部的筛选器，可以指定要筛选的字段。

例如，可以筛选视图以仅查看包含名称“销售”的 Apache Spark 应用程序：

![示例筛选器](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>查看有关特定 Apache Spark 应用程序的详细信息

若要查看有关其中一个 Apache Spark 应用程序的详细信息，请选择 Apache Spark 应用程序并查看详细信息。 如果 Apache Spark 应用程序仍在运行，则可以监视进度。 [了解详细信息](apache-spark-applications.md)。

## <a name="next-steps"></a>后续步骤

有关监视管道运行的详细信息，请参阅[在 Synapse Studio 中监视管道运行](how-to-monitor-pipeline-runs.md)一文。 

有关调试 Apache Spark 应用程序的详细信息，请参阅[在 Synapse Studio 中监视 Apache Spark 应用程序](apache-spark-applications.md)一文。