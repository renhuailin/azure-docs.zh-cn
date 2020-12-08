---
title: 教程：Azure Synapse Analytics 入门 - 监视 Synapse 工作区
description: 在本教程中，你将了解如何监视 Synapse 工作区中的活动。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 10/15/2020
ms.openlocfilehash: c2260cc0d2c6b889b44c738576e33abceebe23f1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444409"
---
# <a name="monitor-your-synapse-workspace"></a>监视 Synapse 工作区

在本教程中，你将了解如何监视 Synapse 工作区中的活动。 你可以监视 SQL、Apache Spark 和 Pipelines 的当前和历史活动。 

## <a name="introduction-to-the-monitor-hub"></a>监视中心简介

打开 Synapse Studio 并导航到“监视”中心。 在此处，你可以查看工作区中所有活动的历史记录，以及目前处于有效状态的活动。 

* 在“集成”下，可监视管道、触发器和集成运行时
* 在“活动”下，可监视 Spark 和 SQL 活动。 

## <a name="integration"></a>集成

1. 导航到“集成”>“管道”。 在此视图中，你可以查看每次管道在工作区中运行时的状况。 
1. 找到在先前步骤中运行的管道，然后单击其管道名称以查看详细信息。
1. 单击 Synapse Studio 顶部附近的“痕迹导航栏”，再单击“所有管道运行”以返回到先前视图 。

## <a name="apache-spark-activities"></a>Apache Spark 活动

1. 导航到“集成”>“活动”>“Apache Spark 应用程序”。 现在，你可以查看正在工作区中运行或已经在工作区中运行的所有 Spark 应用程序。
1. 找到一个已不再运行的应用程序，然后单击其应用程序名称。 现在，你可以查看 Spark 应用程序的详细信息。
1. 如果你熟悉 Apache Spark，则可以通过单击 Spark 历史记录服务器来找到标准 Apache Spark 历史记录服务器 UI。

## <a name="sql-activities"></a>SQL 活动

1. 导航到“集成”>“活动”>“SQL 请求”。
1. 在此视图中，你可以查看 SQL 请求。
1. 选择要监视的池。 现在，你可以查看正在池的工作区中运行或已经在池的工作区中运行的所有 SQL 请求。
1. 查找特定的 SQL 请求，并将鼠标悬停在该项上。 悬停后，将看到“SQL 脚本”图标出现。
1. 单击“SQL 脚本”图标以查看 SQL 请求全文。

    > [!NOTE] 
    > 可以在监视器中心中查看通过 Synapse Studio 在启用工作区的专用 SQL 池（以前的 SQL DW）中提交的 SQL 请求。 对于所有其他监视活动，你可以转到 Azure 门户专用 SQL 池（以前的 SQL DW）监视。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [探索知识中心](get-started-knowledge-center.md)
