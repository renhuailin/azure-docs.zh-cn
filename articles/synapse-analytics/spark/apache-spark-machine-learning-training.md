---
title: 用 Apache Spark 训练机器学习模型
description: 使用 Azure Synapse Analytics 中的 Apache Spark 训练机器学习模型
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 09/13/2020
ms.openlocfilehash: 805bc2aecda37599234c81dd928868d8c14aea4e
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97091983"
---
# <a name="train-machine-learning-models"></a>训练机器学习模型
Azure Synapse Analytics 中的 Apache Spark 支持具有大数据的机器学习，从而能够从大量结构化、非结构化和快速移动的数据中获取有价值的见解。 使用 azure Synapse 分析中的 Azure Spark 训练机器学习模型时，有几个选项可供使用： Apache Spark MLlib、Azure 机器学习和其他各种开源库。 

## <a name="apache-sparkml-and-mllib"></a>Apache SparkML 和 MLlib
Azure Synapse Analytics 中的 Apache Spark 是 Apache Spark 在云中的一种 Microsoft 实现。 它提供了一个统一的开源并行数据处理框架，支持内存中处理，以提升大数据分析能力。 Spark 处理引擎是专为速度、易用性和复杂分析打造的产品。 Spark 的内存中分布式计算功能使其成为机器学习和图形计算中使用的迭代算法的最佳选择。 

有两个可缩放的机器学习库为此分布式环境带来了算法建模功能，这两个库是：MLlib 和 SparkML。 MLlib 包含构建在 RDD 基础之上的原始 API。 SparkML 是一个较新的包，提供构建在 DataFrames 基础之上的更高级 API，用于构造 ML 管道。 SparkML 尚不支持 MLlib 的所有功能，而是将 MLlib 替换为 Spark 的标准机器学习库。

> [!NOTE]
> 
> 可以按照本 [教程](../spark/apache-spark-azure-machine-learning-tutorial.md)了解有关创建 SparkML 模型的详细信息。

## <a name="popular-libraries"></a>常用库
Azure Synapse Analytics 中的每个 Apache Spark 池附带一组预加载的和热门的机器学习库。 这些库提供了可重用的代码，你可能想要在程序或项目中包含这些代码。 默认情况下包含的一些相关机器学习库包括：
- [Scikit-learn](https://scikit-learn.org/stable/index.html) 是适用于传统 ML 算法的最受欢迎单节点机器学习库之一。 Scikit-learn-了解支持大多数监督和无人监督学习算法，还可用于数据挖掘和数据分析。
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) 是一个受欢迎的机器学习库，其中包含用于定型决策树和随机林的优化算法。 
  
- [PyTorch](https://pytorch.org/)  & [Tensorflow](https://www.tensorflow.org/)是功能强大的 Python 深度学习库。 在 Azure Synapse Analytics 的 Apache Spark 池中，可以通过将池上的执行器数设置为零，使用这些库来构建单机模型。 尽管 Apache Spark 在此配置下不起作用，但它是创建单机模型的一种简单且经济高效的方式。

可以通过查看已发布的 [Azure Synapse Analytics 运行时](../spark/apache-spark-version-support.md)了解有关可用库和相关版本的详细信息。

## <a name="mmlspark"></a>MMLSpark
[MMLSpark](https://github.com/Azure/mmlspark) 是适用于 Apache Spark 的 Microsoft 机器学习库。 此库旨在使数据科学家在 Spark 上更高效，提高试验速度，并在大型数据集上利用先进的机器学习技术，包括深度学习。 

MMLSpark 在生成可缩放 ML 模型（例如编制字符串的索引、强制数据进入机器学习算法预期的布局中、组合特征矢量）时，可以在 SparkML 的低级别 API 基础上提供一个层。 MMLSpark 库简化了可在 PySpark 中生成模型的这些任务以及其他常见任务。

## <a name="automated-ml-in-azure-machine-learning"></a>Azure 机器学习中的自动 ML 
Azure 机器学习是一种基于云的环境，可以对机器学习模型进行训练、部署、自动化、管理和跟踪。 Azure 机器学习中的自动 ML 接受定型数据和配置设置，并自动循环访问不同功能标准化/标准化方法、模型和超参数设置，以获得最佳模型。 

在 Azure Synapse Analytics 中使用自动 ML 时，可以利用不同服务之间的深度集成来简化身份验证 & 模型定型。 

> [!NOTE]
> 
> 若要详细了解如何创建 Azure 机器学习自动 ML 试验，请遵循本 [教程](./spark/../apache-spark-azure-machine-learning-tutorial.md)。

## <a name="azure-cognitive-services"></a>Azure 认知服务
[Azure 认知服务](https://docs.microsoft.com/azure/cognitive-services/what-are-cognitive-services) 提供了机器学习功能，可解决一般问题，例如分析激动人心的情绪的文本或分析图像以识别对象或面部。 无需机器学习或数据科学方面的专业知识就能使用这些服务。 认知服务提供了机器学习解决方案中的部分或全部组件：数据、算法和训练模型。 这些服务旨在要求你具备有关数据的一般知识，而无需具备机器学习或数据科学方面的经验。 可以在 Azure Synapse 分析中自动利用这些预先训练的认知服务。

## <a name="next-steps"></a>后续步骤
本文概述了用于在 Azure Synapse 分析中的 Apache Spark 池内训练机器学习模型的各种选项。 可以按照以下教程了解有关模型定型的详细信息：

- 使用 Azure 机器学习和 Azure Synapse Analytics 运行自动 ML 试验： [自动 Ml 教程](./spark/../apache-spark-autoscale.md) 
- 运行 SparkML 试验： [Apache SparkML 教程](../spark/apache-spark-azure-machine-learning-tutorial.md)
- 查看默认库： [Azure Synapse Analytics 运行时](../spark/apache-spark-version-support.md)