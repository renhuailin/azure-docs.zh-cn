---
title: 使用 Apache Spark 进行机器学习
description: 本文以概念的方式概述了通过 Azure Synapse Analytics 上的 Apache Spark 提供的机器学习和数据科学功能。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 0485f697b9360b0f2dfe94fdf07629978b5127c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98116919"
---
# <a name="machine-learning-with-apache-spark"></a>使用 Apache Spark 进行机器学习

你可以使用 Azure Synapse Analytics 中的 Apache Spark 通过大数据进行机器学习，以便从大量结构化、非结构化和快速移动的数据中获得有价值的见解。 

本部分提供机器学习工作流的概述和教程，其中包括探索性数据分析、特征工程、模型训练、模型评分和部署。  

## <a name="synapse-runtime"></a>Synapse 运行时 
Synapse 运行时是针对数据科学和机器学习进行优化的策展环境。 默认情况下，Synapse 运行时提供一系列常用的开源库，并在 Azure 机器学习 SDK 中进行生成。 Synapse 运行时还包括许多外部库，例如 PyTorch、Scikit-Learn、XGBoost 等。

有关可用库和相关版本的详细信息，请查看已发布的 [Azure Synapse Analytics 运行时](../spark/apache-spark-version-support.md)。

## <a name="exploratory-data-analysis"></a>探索性数据分析
使用 Azure Synapse Analytics 中的 Apache Spark 时，有多种内置选项可帮助你可视化数据，其中包括 Synapse 笔记本图表选项、访问常用开源库（例如 Seaborn 和 Matplotlib）的选项以及与 Synapse SQL 和 Power BI 集成的选项。

若要详细了解数据可视化和数据分析选项，请参阅介绍如何[使用 Azure Synapse Notebooks 来可视化数据](../spark/apache-spark-data-visualization.md)的文章。

## <a name="feature-engineering"></a>特性工程
默认情况下，Synapse 运行时包含一组常用于特征工程的库。 对于大型数据集，可使用 Spark SQL、MLlib 和 Koalas 进行特征工程处理。 对于较小的数据集，可使用第三方库（如 Numpy、Pandas 和 Scikit-learn），这些库也提供适用于这些方案的方法。

## <a name="train-models"></a>训练模型
使用 Azure Synapse Analytics 中的 Apache Spark 训练机器学习模型时，有以下几个选项可供选择：Apache Spark MLlib、Azure 机器学习和各种其他的开源库。 

若要详细了解机器学习功能，请查看介绍如何[在 Azure Synapse Analytics 中训练模型](../spark/apache-spark-machine-learning-training.md)的文章。

### <a name="sparkml-and-mllib"></a>SparkML 和 MLlib
Spark 的内存中分布式计算功能使其成为机器学习和图形计算中使用的迭代算法的最佳选择。 ```spark.ml``` 提供了一组统一的高级 API，可帮助用户创建和优化机器学习管道。若要详细了解 ```spark.ml```，可以访问 [Apache Spark ML 编程指南](https://spark.apache.org/docs/1.2.2/ml-guide.html)。

### <a name="azure-machine-learning-automated-ml"></a>Azure 机器学习自动化 ML
[Azure 机器学习自动化 ML](../../machine-learning/concept-automated-ml.md)（自动化机器学习）有助于自动完成计算机学习模型的开发过程。 数据科学家、分析师和开发人员可以使用它来生成高度可缩放、高效且高产能的 ML 模型，同时保证模型的质量。 运行 Azure 机器学习自动化 ML SDK 的组件会直接内置到 Synapse 运行时中。

### <a name="open-source-libraries"></a>开放源代码库
Azure Synapse Analytics 中的每个 Apache Spark 池都附带了一组预加载的常用机器学习库。  默认附带的一些相关的机器学习库包括：

- [Scikit-learn](https://scikit-learn.org/stable/index.html) 是用于经典 ML 算法的最受欢迎的单节点机器学习库之一。 Scikit-learn 支持大多数监督式和非监督式学习算法，也可用于数据挖掘和数据分析。
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) 是一个常用的机器学习库，其中包含用于训练决策树和随机林的优化算法。 
  
- [PyTorch](https://pytorch.org/) & [Tensorflow](https://www.tensorflow.org/) 是功能强大的 Python 深度学习库。 在 Azure Synapse Analytics 的 Apache Spark 池中，可以通过将池中的执行程序数设置为零来使用这些库构建单机模型。 尽管 Apache Spark 在此配置下不起作用，但这是创建单机模型的一种简单且经济高效的方法。

## <a name="track-model-development"></a>跟踪模型开发
[MLFlow](https://www.mlflow.org/) 是一个开放源代码库，用于管理机器学习试验的生命周期。 MLflow 跟踪是 MLflow 的一个组件，用于记录和跟踪训练运行指标和模型项目。 若要详细了解如何通过 Azure Synapse Analytics 和 Azure 机器学习使用 MLFlow 跟踪，请访问此教程：[如何使用 MLFlow](../../machine-learning/how-to-use-mlflow.md)。

## <a name="model-scoring"></a>模型评分
模型评分或推理是将模型用于进行预测的阶段。 对于使用 SparkML 或 MLLib 进行的模型评分，可以使用原生 Spark 方法直接在 Spark 数据帧上进行推理。 对于其他开放源代码库和模型类型，也可创建一个 Spark UDF 来横向扩展针对大型数据集的推理。 对于较小的数据集，也可使用库提供的原生模型推理方法。

## <a name="register-and-serve-models"></a>注册和提供模型
通过注册模型可以让你在工作区中存储、版本和跟踪有关模型的元数据。 训练完模型后，可以将模型注册到 [Azure 机器学习模型注册表](../../machine-learning/concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere)。 注册后，还可以使用 ONNX 模型来[扩充存储在专用 SQL 池中的数据](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md)。

## <a name="next-steps"></a>后续步骤
若要开始在 Azure Synapse Analytics 中进行机器学习，请务必查看以下教程：
- [使用 Azure Synapse Notebooks 分析数据](../spark/apache-spark-data-visualization-tutorial.md)

- [使用自动化 ML 训练机器学习模型](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [使用 Apache Spark MLlib 训练机器学习模型](../spark/apache-spark-machine-learning-mllib-notebook.md)
