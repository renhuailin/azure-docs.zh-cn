---
title: 机器学习工作室（经典）可以用于执行哪些操作 - Azure
description: 机器学习工作室（经典）是一个拖放式工具，可以通过现成可用的算法库和模块快速构建模型。
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: overview
ms.date: 08/19/2020
ms.openlocfilehash: 8b9c03395f77bc33fd03cf2b14b2fb1d27bfc0f0
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112579479"
---
# <a name="what-can-i-do-with-machine-learning-studio-classic"></a>机器学习工作室（经典）可以用于执行哪些操作？

适用范围：![这是复选标记，意味着本文适用于机器学习工作室（经典）。](../../../includes/media/aml-applies-to-skus/yes.png)机器学习工作室（经典）   ![这是 X，意味着本文不适用于 Azure 机器学习。](../../../includes/media/aml-applies-to-skus/no.png)[Azure 机器学习](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

机器学习工作室（经典）是一个拖放式工具，可用于生成、测试和部署机器学习模型。 工作室（经典）将模型发布为 Web 服务，可轻松由自定义应用或 BI 工具（如 Excel）使用。


## <a name="studio-classic--interactive-workspace"></a>工作室（经典）交互式工作区

若要开发预测分析模型，通常使用一个或多个源中的数据，然后通过各种数据操作和统计函数对该数据进行转换和分析，生成一组结果。 开发此类模型是一个迭代过程。 在修改各种函数及其参数时，结果会不断趋于一致，直到已训练的有效模型令人满意。

机器学习工作室（经典）提供交互式的可视工作区，可在其中轻松构建、测试和迭代预测分析模型。 可以将数据集和分析模块拖放到交互式画布，将它们连接在一起构成试验，然后在机器学习工作室（经典版）中运行 。 若要在模型设计上迭代，则需要编辑试验，可根据需要保存一个副本，并重新运行该试验。 准备就绪后，可以将训练实验转换为预测实验，然后将其发布为 Web 服务，以便其他人可以访问模型 。

不需要编程，只需以可视方式连接数据集和模块即可构建预测分析模型。

![机器学习工作室（经典）示意图：创建试验、读取多个源的数据、编写评分的数据、编写模型。](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-ml-studio-classic-overview-diagram"></a>下载机器学习工作室（经典）概述示意图
下载“Microsoft 机器学习工作室（经典）功能概述”示意图，并获取机器学习工作室（经典）功能的高级视图。 若要随时随地查看，可以打印卡片大小（11 x 17 英寸）的示意图。

**此处下载关系图：[Microsoft Azure 机器学习工作室（经典版）功能概述](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
![Microsoft Azure Machine Learning Studio (classic) Capabilities Overview](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>工作室（经典版）试验的组成部分
试验由数据集组成，数据集将数据提供给分析模块，将这些模块连接起来即可构成预测分析模型。 具体而言，有效的试验有以下特征：

* 试验至少包含一个数据集和一个模块
* 数据集只能连接到模块
* 模块可以连接到数据集或其他模块
* 模块的所有输入端口必须与数据流建立某种连接
* 必须设置每个模块的所有必需参数

可以从头开始创建试验，或者使用现有的示例试验作为模板。 有关详细信息，请参阅[复制示例试验以创建新的机器学习试验](sample-experiments.md)。

有关创建试验的示例，请参阅[在机器学习工作室（经典）中创建试验](create-experiment.md)。

有关创建预测分析解决方案的更完整演练，请参阅[使用机器学习工作室（经典）开发预测解决方案](tutorial-part1-credit-risk.md)。

### <a name="datasets"></a>数据集
数据集是指已上传到机器学习工作室（经典版），可在建模过程中使用的数据。 机器学习工作室（经典版）提供了许多示例数据集供试验，你可根据需要上传更多的数据集。 下面是随附数据集的一些例子：

* **各种汽车的 MPG 数据** - 汽车的每加仑燃油英里数 (MPG) 值，按缸数、马力等参数列出。
* **乳腺症数据** - 乳腺癌诊断数据。
* **森林火灾数据** - 葡萄牙东北部森林火灾的规模。

构建试验时，可以从画布左侧提供的数据集列表中进行选择。

有关机器学习工作室（经典版）随附的示例数据集列表，请参阅[使用机器学习工作室（经典）中的示例数据集](use-sample-datasets.md)。

### <a name="modules"></a>模块
模块是可对数据执行的算法。 机器学习工作室（经典）有许多模块，包括数据引入函数、训练、评分和验证过程。 下面是随附模块的一些例子：

* [转换为 ARFF][convert-to-arff] - 将 .NET 序列化数据集转换为属性关系文件格式 (ARFF)。
* [计算基本统计信息][elementary-statistics] - 计算基本统计信息，例如平均值、标准偏差等。
* [线性回归][linear-regression] - 创建基于在线梯度下降的线性回归模型。
* [评分模型][score-model] - 为训练的分类或回归模型评分。

构建试验时，可以从画布左侧提供的模块列表中选择。

模块可能提供一组参数用于配置模块的内部算法。 在画布上选择模块时，模块的参数会显示在画布右侧的“属性”  窗格中。 可以在该窗格中修改参数来调整模型。

在浏览可用的机器学习算法大型库时如需帮助，请参阅[如何选择 Microsoft 机器学习工作室（经典）的算法](../how-to-select-algorithms.md)。

## <a name="deploying-a-predictive-analytics-web-service"></a>部署预测分析 Web 服务
准备好预测分析模型后，可以从机器学习工作室（经典版）将它部署为 Web 服务。 有关此过程的信息，请参阅[部署机器学习 Web 服务](deploy-a-machine-learning-web-service.md)。

## <a name="next-steps"></a>后续步骤
可以使用[分步快速入门](create-experiment.md)和[基于样本的构建](sample-experiments.md)了解预测分析和机器学习的基础知识。

<!-- Module References -->
[convert-to-arff]: /azure/machine-learning/studio-module-reference/convert-to-arff
[elementary-statistics]: /azure/machine-learning/studio-module-reference/compute-elementary-statistics
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[score-model]: /azure/machine-learning/studio-module-reference/score-model