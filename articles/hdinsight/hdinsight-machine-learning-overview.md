---
title: 机器学习概述 - Azure HDInsight
description: Azure HDInsight 中群集的大数据机器学习选项概述。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: a911e468443fe49bb1edc18af3d3412edc8eb8cc
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112678209"
---
# <a name="machine-learning-on-hdinsight"></a>HDInsight 中的机器学习

可以使用 HDInsight 通过大数据进行机器学习，以便从大量（千万亿字节甚至百亿亿字节）结构化、非结构化和快速移动的数据中获得有价值的见解。 HDInsight 中有多个机器学习选项：SparkML 和 Apache Spark MLlib、Apache Hive 以及 Microsoft Cognitive Toolkit。

## <a name="sparkml-and-mllib"></a>SparkML 和 MLlib

[HDInsight Spark](spark/apache-spark-overview.md) 是 Azure 托管的 [Apache Spark](https://spark.apache.org/) 产品/服务，它是统一的开源并行数据处理框架，支持使用内存中处理来大幅提升大数据分析性能。 Spark 处理引擎是专为速度、易用性和复杂分析打造的产品。 Spark 的内存中分布式计算功能使其成为机器学习和图形计算中使用的迭代算法的最佳选择。 有两个可缩放的机器学习库为此分布式环境带来了算法建模功能，这两个库是：MLlib 和 SparkML。 MLlib 包含构建在 RDD 基础之上的原始 API。 SparkML 是一个较新的包，提供构建在 DataFrames 基础之上的更高级 API，用于构造 ML 管道。 SparkML 目前尚不支持 MLlib 的所有功能，但正在替换 MLlib 的角色，即充当 Spark 的标准机器学习库。

[MMLSpark](https://github.com/Azure/mmlspark) 是适用于 Apache Spark 的 Microsoft 机器学习库。 该库旨在提升数据科学家在 Spark 上的生产力，它不仅可以提高试验成功率，而且还能在极大型数据集上利用前沿的机器学习技术，包括深度学习。 MMLSpark 在生成可缩放 ML 模型（例如编制字符串的索引、强制数据进入机器学习算法预期的布局中、组合特征矢量）时，可以在 SparkML 的低级别 API 基础上提供一个层。 MMLSpark 库简化了可在 PySpark 中生成模型的这些任务以及其他常见任务。

## <a name="azure-machine-learning-and-apache-hive"></a>Azure 机器学习和 Apache Hive

Azure 机器学习不仅提供预测分析建模工具，还提供完全托管的服务，可以通过此服务将预测模型部署为随时可用的 Web 服务。 Azure 机器学习是云中的完整预测分析解决方案，可以用来创建、测试、操作和管理预测模型。 可以从大型算法库中进行选择、使用基于 Web 的工作室来构建模型，然后将模型轻松部署为 Web 服务。

:::image type="content" source="./media/hdinsight-machine-learning-overview/azure-machine-learning.png" alt-text="Microsoft Azure 机器学习概述" border="false":::

使用 [Hive 查询](/azure/architecture/data-science-process/create-features-hive)，在 HDInsight Hadoop 群集中创建数据特征。 *特征工程* 尝试通过从原始数据创建特征，简化学习过程，从而增加学习算法的预测能力。 可以使用[“导入数据”模块](../machine-learning/classic/import-data.md)从 Azure 机器学习工作室（经典）运行 HiveQL 查询，以及访问在 Hive 中处理和在 Blob 存储中存储的数据。

## <a name="microsoft-cognitive-toolkit"></a>Microsoft 认知工具包

[深度学习](https://www.microsoft.com/en-us/research/group/dltc/)是机器学习的一个分支，使用神经网络是受人类大脑的生物学过程启发。 许多研究人员将深度学习视为有前景的可增强人工智能的方法。 深度学习的例子包括口译工具、图像识别系统和计算机推理。

为了推进自身在深度学习方面的工作，Microsoft 开发了免费、易用的开源 [Microsoft 认知工具包](https://www.microsoft.com/en-us/cognitive-toolkit/)。 各种 Microsoft 产品、世界各地需要大规模部署深度学习的公司，以及对最新算法和技术感兴趣的学生都在使用该工具包。

## <a name="see-also"></a>另请参阅

### <a name="scenarios"></a>方案

* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 结合 HVAC 数据分析建筑物温度](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](spark/apache-spark-machine-learning-mllib-ipython.md)
* [使用 Apache Mahout 生成影片推荐](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive 和 Azure 机器学习](/azure/architecture/data-science-process/create-features-hive)
* [Apache Hive 和 Azure 机器学习端到端教程](/azure/architecture/data-science-process/hive-walkthrough)
* [在 Apache Spark on HDInsight 中使用机器学习](/azure/architecture/data-science-process/spark-overview)

### <a name="deep-learning-resources"></a>深度学习资源

* [将 Microsoft 认知工具包深入学习模型与 Azure HDInsight Spark 群集配合使用](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Data Science Virtual Machine (DSVM) 上的深度学习和人工智能框架](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
