---
title: 机器学习算法备忘单 - 设计器
titleSuffix: Azure Machine Learning
description: 可打印的机器学习算法备忘单可帮助在 Azure 机器学习设计器中为预测模型选择正确的算法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 07/20/2021
adobe-target: true
ms.openlocfilehash: ebe02e264a9f3faea2812253eeced6d56319627d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742168"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Azure 机器学习设计器机器学习算法备忘单

“Azure 机器学习算法备忘单”可帮助你从设计器为预测分析模型选择正确的算法。

Azure 机器学习有一个大型算法库，包括“分类”、“推荐系统”、“聚类分析”、“异常检测”、“回归” 和“文本分析” 系列。 每一类算法都可用于解决一种类型的机器学习问题。

有关详细信息，请参阅[如何选择算法](how-to-select-algorithms.md)。

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>下载：机器学习算法备忘单

**在此下载备忘单：[机器学习算法备忘单(11 x 17 英寸)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![机器学习算法备忘单：了解如何选择机器学习算法。](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.png)

下载该机器学习算法备忘单，并将其打印为 Tabloid 大小，既方便携带又可帮助你选择算法。

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>如何使用机器学习算法备忘单

此算法备忘单中提供的建议近似于经验法则。 一些可以不完全照做，一些可以大胆地违反。 此备忘单旨在提供一个起点。 不要担心几种算法之间对数据运行正面竞争。 每种算法的原理和生成数据的系统都需要了解，此外别无选择。

每种机器学习算法都有自己的风格或归纳偏差。 对于特定问题，有多种算法可能都合适，但会有一种算法可能比其他算法更合适。 但并非总是可以预先知道哪种是最合适的。 在这些情况下，会在备忘单中列出几种算法。 适当的策略是尝试一种算法，如果结果尚不令人满意，则尝试其他算法。 

若要详细了解 Azure 机器学习设计器中的算法，请参阅[算法和模块参考](algorithm-module-reference/module-reference.md)。

## <a name="kinds-of-machine-learning"></a>机器学习的种类

有三种主要类别的机器学习：*监督式学习*、*非监督式学习* 和 *强化学习*。

### <a name="supervised-learning"></a>监督式学习

在监督式学习中，将标记每个数据点或将其与某个类别或相关值相关联。 分类标签的示例是将图像分配为“猫”或“狗”。 值标签的示例是与二手车关联的销售价格。 监督式学习的目的是研究大量类似这样的标记示例，并能够对未来的数据点进行预测。 例如，识别包含正确动物的新照片或为其他二手车指定准确的销售价格。 这是一种常用且有用的机器学习类型。

### <a name="unsupervised-learning"></a>非监督式学习

在非监督式学习中，数据点没有与其关联的标签。 相反，非监督式学习算法的目的是以某种方式组织数据或者说明其结构。 像 K-means 一样，非监督式学习将数据分组到群集中，或者找到不同的方法来查看复杂数据，使其看起来更简单。

### <a name="reinforcement-learning"></a>强化学习

在强化学习中，算法需选择响应每个数据点的操作。 它是机器人学中的常见方法，在此技术中一个时间点的传感器读数集是数据点，并且算法必须选择机器人的下一个动作。 它也是物联网应用程序的理想选择。 学习算法还会在短时间后收到奖励信号，指示决策的优秀程度。 基于该信号，该算法会修改其策略以获得最高奖励。 

## <a name="next-steps"></a>后续步骤

* 请参阅有关[如何选择算法](how-to-select-algorithms.md)的更多信息。

* [了解 Azure 机器学习中的工作室和 Azure 门户](overview-what-is-azure-machine-learning.md)。

* [教程：在 Azure 机器学习设计器中构建预测模型](tutorial-designer-automobile-price-train-score.md)。

* [了解深度学习与机器学习](concept-deep-learning-vs-machine-learning.md)。
