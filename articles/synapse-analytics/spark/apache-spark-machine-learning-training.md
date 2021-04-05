---
title: 使用 Apache Spark 训练机器学习模型
description: 使用 Azure Synapse Analytics 中的 Apache Spark 训练机器学习模型
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 09/13/2020
ms.openlocfilehash: 56b9a98eb72b375aacfeb7cb147997028d3d9ba7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98116800"
---
# <a name="train-machine-learning-models"></a>训练机器学习模型
你可以使用 Azure Synapse Analytics 中的 Apache Spark 通过大数据进行机器学习，以便从大量结构化、非结构化和快速移动的数据中获得有价值的见解。 使用 Azure Synapse Analytics 中的 Apache Spark 训练机器学习模型时，有以下几个选项可供选择：Apache Spark MLlib、Azure 机器学习和其他各种开源库。 

## <a name="apache-sparkml-and-mllib"></a>Apache SparkML 和 MLlib
Azure Synapse Analytics 中的 Apache Spark 是 Apache Spark 在云中的一种 Microsoft 实现。 它提供一个统一的开源并行数据处理框架，支持使用内存中处理来大幅提升大数据分析性能。 Spark 处理引擎是专为速度、易用性和复杂分析打造的产品。 Spark 的内存中分布式计算功能使其成为机器学习和图形计算中使用的迭代算法的最佳选择。 

有两个可缩放的机器学习库为此分布式环境带来了算法建模功能，这两个库是：MLlib 和 SparkML。 MLlib 包含构建在 RDD 基础之上的原始 API。 SparkML 是一个较新的包，提供构建在 DataFrames 基础之上的更高级 API，用于构造 ML 管道。 SparkML 目前尚不支持 MLlib 的所有功能，但正在取代 MLlib 成为 Spark 的标准机器学习库。

> [!NOTE]
> 
> 你可以阅读此[教程](../spark/apache-spark-azure-machine-learning-tutorial.md)，详细了解如何创建 SparkML 模型。

## <a name="popular-libraries"></a>常用库
Azure Synapse Analytics 中的每个 Apache Spark 池都附带了一组预加载的常用机器学习库。 这些库提供了可重用的代码，你可能想要在程序或项目中包含这些代码。 默认附带的一些相关机器学习库包括：
- [Scikit-learn](https://scikit-learn.org/stable/index.html) 是用于经典 ML 算法的最受欢迎的单节点机器学习库之一。 Scikit-learn 支持大多数监督式和非监督式学习算法，也可用于数据挖掘和数据分析。
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) 是一个常用的机器学习库，其中包含用于训练决策树和随机林的优化算法。 
  
- [PyTorch](https://pytorch.org/) & [Tensorflow](https://www.tensorflow.org/) 是功能强大的 Python 深度学习库。 在 Azure Synapse Analytics 的 Apache Spark 池中，可以通过将池中的执行程序数设置为零来使用这些库构建单机模型。 尽管 Apache Spark 在此配置下不起作用，但这是创建单机模型的一种简单且经济高效的方法。

有关可用库和相关版本的详细信息，请查看已发布的 [Azure Synapse Analytics 运行时](../spark/apache-spark-version-support.md)。

## <a name="mmlspark"></a>MMLSpark
[MMLSpark](https://github.com/Azure/mmlspark) 是适用于 Apache Spark 的 Microsoft 机器学习库。 该库旨在让数据科学家更高效地使用 Spark，提高试验成功率，以及将包括深度学习在内的尖端机器学习技术应用于大型数据集。 

MMLSpark 在生成可缩放 ML 模型（例如编制字符串的索引、强制数据进入机器学习算法预期的布局中、组合特征矢量）时，可以在 SparkML 的低级别 API 基础上提供一个层。 MMLSpark 库简化了可在 PySpark 中生成模型的这些任务以及其他常见任务。

## <a name="automated-ml-in-azure-machine-learning"></a>Azure 机器学习中的自动化 ML 
Azure 机器学习是一种基于云的环境，可以对机器学习模型进行训练、部署、自动化、管理和跟踪。 Azure 机器学习中的自动化 ML 接受训练数据和配置设置，并自动循环访问不同特征规范化/标准化方法、模型和超参数设置的组合，以实现最佳模型。 

在 Azure Synapse Analytics 中使用自动化 ML 时，可以利用不同服务之间的深度集成来简化身份验证和模型训练。 

> [!NOTE]
> 
> 若要详细了解如何创建 Azure 机器学习自动化 ML 试验，请参阅此[教程](./spark/../apache-spark-azure-machine-learning-tutorial.md)。

## <a name="azure-cognitive-services"></a>Azure 认知服务
[Azure 认知服务](../../cognitive-services/what-are-cognitive-services.md)提供机器学习功能以解决常见问题，例如分析文本以获取其情感情绪，或分析图像以识别物体或人脸。 无需机器学习或数据科学方面的专业知识就能使用这些服务。 认知服务提供了机器学习解决方案中的部分或全部组件：数据、算法和训练模型。 这些服务旨在要求你具备有关数据的一般知识，而无需具备机器学习或数据科学方面的经验。 你可以在 Azure Synapse Analytics 中自动利用这些预先训练的认知服务。

## <a name="next-steps"></a>后续步骤
本文概述了用于在 Azure Synapse Analytics 的 Apache Spark 池中训练机器学习模型的各种选项。 若要详细了解模型训练，请参阅以下教程：

- 使用 Azure 机器学习和 Azure Synapse Analytics 运行自动化 ML 试验：[自动化 ML 教程](../spark/apache-spark-azure-machine-learning-tutorial.md) 
- 运行 SparkML 试验：[Apache SparkML 教程](../spark/apache-spark-machine-learning-mllib-notebook.md)
- 查看默认库：[Azure Synapse Analytics 运行时](../spark/apache-spark-version-support.md)