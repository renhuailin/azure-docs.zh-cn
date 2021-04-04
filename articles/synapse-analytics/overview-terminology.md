---
title: 术语 - Azure Synapse Analytics
description: 引导用户了解 Azure Synapse Analytics 的参考指南
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 828f37030ae567cacbaad25849b7ba24c561c20c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98132760"
---
# <a name="azure-synapse-analytics-terminology"></a>Azure Synapse Analytics 术语

本文档介绍了 Azure Synapse Analytics 的基本概念。

## <a name="basics"></a>基础

Synapse 工作区是用于在 Azure 中执行基于云的企业分析的安全协作边界。 工作区部署在特定的区域中，并有关联的 ADLS Gen2 帐户和文件系统（用于存储临时数据）。 工作区位于资源组下。

通过工作区，可以使用 SQL 和 Apache spark 执行分析。 可用于 SQL 和 Spark 分析的资源将组织到 SQL 和 Spark 池中。 

## <a name="linked-services"></a>链接服务

工作区可包含任意数量的链接服务，它们实质上是定义工作区在连接到外部资源时所需的连接信息的连接字符串。

## <a name="synapse-sql"></a>Synapse SQL

Synapse SQL 用于在 Synapse 工作区中执行基于 T-SQL 的分析。 Synapse SQL 有两种使用模型：专用模型和无服务器模型。  对于专用模型，请使用专用 SQL 池。 一个工作区可以有任意数量的这些池。 若要使用无服务器模型，请使用无服务器 SQL 池。 每个工作区都有这些池之一。

在 Synapse Studio 中，可以通过创建和运行 SQL 脚本来使用 SQL 池。

## <a name="apache-spark-for-synapse"></a>Apache Spark for Synapse

若要使用 Spark 分析，请在 Synapse 工作区中创建并使用无服务器 Apache Spark 池。 开始使用 Spark 池时，工作区会创建一个“spark 会话”来处理与该会话相关的资源。 

Synapse 中有两种方法使用 Spark：
* **Spark 笔记本**，使用 Scala、PySpark、C# 和 SparkSQL 处理数据科学和工程的数据
* **Spark 作业定义** 使用 jar 文件运行批处理 Spark 作业。

## <a name="pipelines"></a>管道

管道是 Azure Synapse 提供数据集成的方式，通过它可在服务之间移动数据，并安排活动。

* 管道是共同执行某个任务的活动的逻辑分组。
* 活动定义要在管道中对数据执行的操作，例如复制数据、运行 Notebook 或 SQL 脚本。
* 数据流是特定类型的活动，可为使用隐藏式 Synapse Spark 进行数据转换提供无代码体验。
* **触发器** - 执行管道。 可以手动或自动（计划、翻转窗口或基于事件）运行触发器
* **集成数据集** - 数据的命名视图，它只指向或引用要在活动中作为输入和输出使用的数据。 它属于链接服务。

## <a name="next-steps"></a>后续步骤

* [Azure Synapse Analytics 入门](get-started.md)
* [创建工作区](quickstart-create-workspace.md)
* [使用无服务器 SQL 池](quickstart-sql-on-demand.md)

