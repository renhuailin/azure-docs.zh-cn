---
title: 机器学习与 Apache Spark
description: 本文提供通过 Azure Synapse Analytics Apache Spark 提供的机器学习和数据科学功能的概念性概述。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 0485f697b9360b0f2dfe94fdf07629978b5127c1
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116919"
---
# <a name="machine-learning-with-apache-spark"></a>计算机学习与 Apache Spark

Azure Synapse Analytics 中的 Apache Spark 支持具有大数据的机器学习，从而能够从大量结构化、非结构化和快速移动的数据中获取有价值的见解。 

本部分包括机器学习工作流的概述和教程，包括探索数据分析、特征工程、模型定型、模型评分和部署。  

## <a name="synapse-runtime"></a>Synapse 运行时 
Synapse 运行时是针对数据科学和机器学习进行优化的特选环境。 默认情况下，Synapse 运行时提供一系列流行的开源库，并在 Azure 机器学习 SDK 中生成。 Synapse 运行时还包括许多外部库，包括 PyTorch、Scikit-learn 和 XGBoost 等。

查看发布的 [Azure Synapse Analytics 运行时](../spark/apache-spark-version-support.md)，了解可用库和相关版本的详细信息。

## <a name="exploratory-data-analysis"></a>探索数据分析
使用 Azure Synapse Analytics 中的 Apache Spark 时，有多种内置选项可帮助你将数据可视化，其中包括 Synapse 笔记本图表选项、Seaborn 和 Matplotlib 等常用开源库的访问权限，以及与 Synapse SQL 和 Power BI 的集成。

查看有关数据可视化和数据分析选项的详细信息，详细了解如何 [使用 Azure Synapse 笔记本实现数据的可视化](../spark/apache-spark-data-visualization.md)。

## <a name="feature-engineering"></a>特性工程
默认情况下，Synapse 运行时包含一组通常用于功能设计的库。 对于大型数据集，可以使用 Spark SQL、MLlib 和 Koalas 进行特征工程。 对于较小的数据集，第三方库（如 Numpy、Pandas 和 Scikit-learn）还提供适用于这些方案的有用方法。

## <a name="train-models"></a>训练模型
使用 azure Synapse 分析中的 Azure Spark 训练机器学习模型时，有几个选项可供使用： Apache Spark MLlib、Azure 机器学习和其他各种开源库。 

若要详细了解机器学习功能，请查看如何 [在 Azure Synapse Analytics 中训练模型](../spark/apache-spark-machine-learning-training.md)一文。

### <a name="sparkml-and-mllib"></a>SparkML 和 MLlib
Spark 的内存中分布式计算功能使其成为机器学习和图形计算中使用的迭代算法的最佳选择。 ```spark.ml``` 提供了一组统一的高级 Api，可帮助用户创建和优化机器学习管道。若要了解有关 ```spark.ml``` 的详细信息，可以访问 [Apache Spark ML 编程指南](https://spark.apache.org/docs/1.2.2/ml-guide.html)。

### <a name="azure-machine-learning-automated-ml"></a>Azure 机器学习自动 ML
自动机器学习 ([Azure 机器学习自动 ML](../../machine-learning/concept-automated-ml.md)) 有助于自动执行计算机学习模型的开发过程。 数据科学家、分析师和开发人员可以使用它来生成高度可缩放、高效且高产能的 ML 模型，同时保证模型的质量。 运行 Azure 机器学习自动 ML SDK 的组件将直接内置到 Synapse 运行时中。

### <a name="open-source-libraries"></a>开源库
Azure Synapse Analytics 中的每个 Apache Spark 池附带一组预加载的和热门的机器学习库。  默认情况下包含的一些相关机器学习库包括：

- [Scikit-learn](https://scikit-learn.org/stable/index.html) 是适用于传统 ML 算法的最受欢迎单节点机器学习库之一。 Scikit-learn-了解支持大多数监督和无人监督学习算法，还可用于数据挖掘和数据分析。
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) 是一个受欢迎的机器学习库，其中包含用于定型决策树和随机林的优化算法。 
  
- [PyTorch](https://pytorch.org/)  & [Tensorflow](https://www.tensorflow.org/)是功能强大的 Python 深度学习库。 在 Azure Synapse Analytics 的 Apache Spark 池中，可以通过将池上的执行器数设置为零，使用这些库来构建单机模型。 尽管 Apache Spark 在此配置下不起作用，但它是创建单机模型的一种简单且经济高效的方式。

## <a name="track-model-development"></a>跟踪模型开发
[MLFlow](https://www.mlflow.org/) 是一个开源库，用于管理机器学习试验的生命周期。 MLflow 跟踪是 MLflow 的一个组件，用于记录和跟踪训练运行指标和模型项目。 若要详细了解如何通过 Azure Synapse Analytics 和 Azure 机器学习使用 MLFlow 跟踪，请访问本教程， [了解如何使用 MLFlow](../../machine-learning/how-to-use-mlflow.md)。

## <a name="model-scoring"></a>模型评分
模型计分或推断是模型用于进行预测的阶段。 对于使用 SparkML 或 MLLib 的模型评分，可以利用本机 Spark 方法直接在 Spark 数据帧上执行推断。 对于其他开源库和模型类型，您还可以创建一个 Spark UDF 来横向扩展大型数据集上的推理。 对于较小的数据集，还可以使用库提供的本机模型推理方法。

## <a name="register-and-serve-models"></a>注册和服务模型
通过注册模型可以让你在工作区中存储、版本和跟踪有关模型的元数据。 完成模型定型后，可以将模型注册到 [Azure 机器学习模型注册表](../../machine-learning/concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere)。 注册后，还可以使用 ONNX 模型来丰富存储在专用 SQL 池中 [的数据](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md) 。

## <a name="next-steps"></a>后续步骤
若要开始在 Azure Synapse Analytics 中进行机器学习，请务必查看以下教程：
- [用 Azure Synapse 笔记本分析数据](../spark/apache-spark-data-visualization-tutorial.md)

- [使用自动 ML 训练机器学习模型](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [使用 Apache Spark MLlib 训练机器学习模型](../spark/apache-spark-machine-learning-mllib-notebook.md)
