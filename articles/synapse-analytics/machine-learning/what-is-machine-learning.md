---
title: Azure Synapse Analytics 中的机器学习
description: Azure Synapse Analytics 中的机器学习功能概述。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 10/01/2021
author: nelgson
ms.author: negust
ms.openlocfilehash: e092cf94e5126b5bfa7bd42cc2f362f6d7a9da96
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402086"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中的机器学习功能

Azure Synapse Analytics 提供了各种机器学习功能。 本文概述了如何在 Azure Synapse 的上下文中应用机器学习。

本概述从数据科学流程的角度介绍了 Synapse 中与机器学习相关的各种功能。

你可能很熟悉典型的数据科学流程。 这是一个众所周知的流程，大多数机器学习项目都遵循该流程。

概括而言，该流程包含以下步骤：
* 业务理解（本文不讨论）
* 数据获取和理解
* 建模
* 模型部署和评分

本文从数据科学流程角度介绍了各种分析引擎中的 Azure Synapse 机器学习功能。 针对数据科学流程中的每个步骤，汇总了有用的 Azure Synapse 功能。

## <a name="data-acquisition-and-understanding"></a>数据获取和理解

大多数机器学习项目都涉及完善的步骤，其中一个步骤是访问和了解数据。

### <a name="data-source-and-pipelines"></a>数据源和管道

归功于 [Azure 数据工厂](../../data-factory/introduction.md)（Azure Synapse 的一个原生集成部分），有一组功能强大的工具可用于数据引入和数据业务流程管道。 这使你可以轻松地构建数据管道，以便访问数据并将其转换为可用于机器学习的格式。 [详细了解 Synapse 中的数据管道](../../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)。 

### <a name="data-preparation-and-explorationvisualization"></a>数据准备和浏览/可视化

机器学习过程中的一个重要部分是通过浏览和可视化来了解数据。

根据数据的存储位置，Synapse 提供了一组不同的工具来浏览和准备用于分析和机器学习的数据。 若要开始使用数据浏览，最快速的方法之一是直接对数据湖中的数据使用 Apache Spark 或无服务器 SQL 池。

* [Apache Spark for Azure Synapse](../spark/apache-spark-overview.md) 提供了大规模转换、准备和浏览数据的功能。 这些 Spark 池提供了用于大规模数据处理的 PySpark/Python、Scala 和 .NET 等工具。 使用功能强大的可视化效果库，可以增强数据浏览体验，以便更好地了解数据。 [详细了解如何使用 Spark 浏览和可视化 Synapse 中的数据](../get-started-analyze-spark.md)。

* [无服务器 SQL 池](../sql/on-demand-workspace-overview.md)提供了一种直接通过数据湖使用 TSQL 浏览数据的方法。 无服务器 SQL 池还在 Synapse Studio 中提供了一些内置的可视化效果。 [详细了解如何使用无服务器 SQL 池来浏览数据](../get-started-analyze-sql-on-demand.md)。

## <a name="modeling"></a>建模

在 Azure Synapse 中，可以通过 PySpark/Python、Scala 或 .NET 等工具在 Apache Spark 池中执行训练机器学习模型的操作。

### <a name="train-models-on-spark-pools-with-mllib"></a>通过 MLlib 在 Spark 池中训练模型

可以借助各种算法和库来训练机器学习模型。 [Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html) 提供了可缩放的机器学习算法，这些算法有助于解决大多数经典的机器学习问题。 有关如何在 Synapse 中使用 MLlib 训练模型的教程，请参阅[使用 Apache Spark MLlib 和 Azure Synapse Analytics 构建机器学习应用](../spark/apache-spark-machine-learning-mllib-notebook.md)。

除了 MLlib 之外，还可以使用 [Scikit Learn](https://scikit-learn.org/stable/) 等常用库来开发模型。 请参阅[在 Azure Synapse Analytics 中管理 Apache Spark 的库](../spark/apache-spark-azure-portal-add-libraries.md)，详细了解如何在 Synapse Spark 池中安装库。

### <a name="train-models-with-azure-machine-learning-automated-ml"></a>使用 Azure 机器学习自动化 ML 来训练模型

训练机器学习模型的另一种方法是使用自动化 ML，这不需要花太多时间事先熟悉机器学习。 [自动化 ML](../../machine-learning/concept-automated-ml.md) 是一项可自动训练一组机器学习模型的功能，允许用户根据特定指标选择最佳模型。 借助从 Azure Synapse Notebooks 与 Azure 机器学习进行的无缝集成，用户可以轻松地在 Synapse 中通过 Azure Active Directory 传递身份验证来利用自动化 ML。  这意味着你只需指向 Azure 机器学习工作区，无需输入任何凭据。 教程[使用 Python 通过自动化机器学习训练模型](../spark/apache-spark-azure-machine-learning-tutorial.md)介绍了如何在 Synapse Spark Pools 上使用 Azure 机器学习自动化 ML 训练模型。

## <a name="model-deployment-and-scoring"></a>模型部署和评分

可以轻松使用已在 Azure Synapse 中或 Azure Synapse 外部训练的模型进行批量评分。 在 Synapse 中，目前有两种方法可用于运行批量评分。

* 可以使用 Synapse SQL 池中的 [TSQL PREDICT 函数](../sql-data-warehouse/sql-data-warehouse-predict.md)直接在数据所在的位置运行你的预测。 此强大的可缩放功能允许你在不将任何数据移出数据仓库的情况下扩充数据。 [Synapse Studio 中引入了一个新的引导式机器学习模型体验](./tutorial-sql-pool-model-scoring-wizard.md)。在其中，你可以从 Synapse SQL 池中的 Azure 机器学习模型注册表部署 ONNX 模型，以便使用 PREDICT 进行批量评分。

* Azure Synapse 中用于对机器学习模型进行批量评分的另一个选项是利用 Azure Synapse 的 Apache Spark 池。 根据用来训练模型的库，你可以使用代码体验来运行批量评分。

## <a name="next-steps"></a>后续步骤

* [Azure Synapse Analytics 入门](../get-started.md)
* [创建工作区](../get-started-create-workspace.md)
* [快速入门：在 Synapse 中创建新的 Azure 机器学习链接服务](quickstart-integrate-azure-machine-learning.md)
* [教程：机器学习模型评分向导 - 专用 SQL 池](tutorial-sql-pool-model-scoring-wizard.md)