---
title: 将数据加载到 Azure 存储环境中 - Team Data Science Process
description: 了解如何将数据引入到存储和处理数据的各种目标环境中。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 46e911ad6bdf5a478d46e425f8700740ece56c6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "96452672"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>将数据载入存储环境以进行分析

Team Data Science Process 要求在每个阶段将数据引入或加载到最合适的方式。 数据目标可以包括 Azure Blob 存储、SQL Azure 数据库、Azure VM 上的 SQL Server、HDInsight (Hadoop)、Azure Synapse Analytics 和 Azure 机器学习。 

以下文章介绍如何将数据引入到存储和处理数据的各种目标环境中。

* 到/从 [Azure Blob 存储](move-azure-blob.md)
* 到 [Azure VM 上的 SQL Server](move-sql-server-virtual-machine.md)
* 到 [Azure SQL 数据库](move-sql-azure.md)
* 到 [Hive 表](move-hive-tables.md)
* 到 [SQL 分区表](parallel-load-sql-partitioned-tables.md)
* 从[本地 SQL Server](move-sql-azure-adf.md)

技术和业务需求以及数据的初始位置、格式和大小将决定最佳的数据引入计划。 最佳计划有几个步骤，这并不少见。 例如，此系列任务可能包括数据浏览、预处理、清理、向下采样和模型定型。  建议使用 Azure 数据工厂来协调数据移动和转换。
