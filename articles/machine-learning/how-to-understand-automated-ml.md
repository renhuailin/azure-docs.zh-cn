---
title: 评估 AutoML 试验结果
titleSuffix: Azure Machine Learning
description: 了解如何查看和评估每个自动化机器学习试验运行的图表与指标。
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: automl
ms.date: 12/09/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, automl
ms.openlocfilehash: 91c620a68d375084f8a4be6c5a8bf30b92d016c1
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812194"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>评估自动化机器学习试验结果

本文介绍如何评估和比较自动机器学习（自动化 ML）试验训练的模型。 在自动化 ML 试验过程中，创建了许多运行，每次运行都会创建一个模型。 对于每个模型，自动化 ML 生成评估指标和图表，帮助你衡量模型的性能。 

例如，自动化 ML 根据试验类型生成以下图表。

| 分类| 回归/预测 |
| ----------------------------------------------------------- | ---------------------------------------- |
| [混淆矩阵](#confusion-matrix)                       | [残差直方图](#residuals)        |
| [接收方操作特征 (ROC) 曲线](#roc-curve) | [预测值与实际值](#predicted-vs-true) |
| [精度-召回率 (PR) 曲线](#precision-recall-curve)      |                                          |
| [提升曲线](#lift-curve)                                   |                                          |
| [累积增益曲线](#cumulative-gains-curve)           |                                          |
| [校准曲线](#calibration-curve)                     |                     


## <a name="prerequisites"></a>先决条件

- Azure 订阅。 （如果没有 Azure 订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)）
- 使用以下之一创建 Azure 机器学习试验：
  - [Azure 机器学习工作室](how-to-use-automated-ml-for-ml-models.md)（无需代码）
  - [Azure 机器学习 Python SDK](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>查看运行结果

自动 ML 试验完成后，可以通过以下方式找到运行历史记录：
  - 包含 [Azure 机器学习工作室](overview-what-is-machine-learning-studio.md)的浏览器
  - 使用 [RunDetails Jupyter 小组件](/python/api/azureml-widgets/azureml.widgets.rundetails)的 Jupyter 笔记本

以下步骤和视频演示了如何在工作室中查看运行历史记录和模型评估指标及图表：

1. [登录到工作室](https://ml.azure.com/)并导航到你的工作区。
1. 在左侧菜单中选择“试验”。
1. 从试验列表中选择你的试验。
1. 在页面底部的表中，选择自动化 ML 运行。
1. 在“模型”选项卡中，选择要评估的模型的“算法名称” 。
1. 在“指标”选项卡中，使用左侧的复选框查看指标和图表。

![在工作室中查看指标的步骤](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>分类指标

自动化 ML 为试验生成的每个分类模型计算性能指标。 这些指标基于 scikit learn 实现。 

许多分类指标定义为对两个类进行二元分类，并要求对多个类进行平均以产生一个多类分类得分。 Scikit-learn 提供了几种平均方法，其中三种是自动化 ML 公开：宏、Micro 和加权  。

- **宏** - 计算每个类的指标并取未加权平均值
- **Micro** - 通过统计真正、假负和假正总值（独立于类）来全局计算指标。
- **加权** - 计算每个类的指标，并根据每个类的样本数取加权平均值。

虽然每种平均值方法都有其优点，但在选择合适的方法时，一个常见的考虑因素是类不平衡。 如果类具有不同的样本数，则使用宏平均值（向少数类赋予与多数类相等的权重）可能会提供更多信息。 进一步了解[自动化 ML 中的二进制与多类指标](#binary-vs-multiclass-classification-metrics)。 

下表汇总了模型性能指标，这些指标是自动化 ML 针对每个为试验生成的分类模型计算的。 有关更多详细信息，请参阅每个指标的“计算”字段中链接的 scikit-learn 文档。 

|指标|说明|计算|
|--|--|---|
|AUC | AUC 是[接收方操作特性曲线](#roc-curve)下面的区域。<br><br> **目的：** 越接近 1 越好 <br> **范围：** [0, 1]<br> <br>支持的指标名称包括， <li>`AUC_macro`，每个类的 AUC 算术平均值。<li> `AUC_micro`，以多标签方式计算。 每个示例的每一个不同的类都将被视为独立的 `0/1` 预测。 正确的类将变为 `true` 类，其余将为 `false` 类。 然后，系统将为新的二元分类任务计算 AUC，并将所有示例组合在一起。 <li> `AUC_weighted`，每个类的评分算术平均值，按每个类中的真实实例数加权。 <li> `AUC_binary`，将一个特定类视为 `true` 类并将所有其他类合并为 `false` 类的 AUC 值。<br><br>|[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| Accuracy 是与真实类标签完全匹配的预测比率。 <br> <br>**目的：** 越接近 1 越好 <br> **范围：** [0, 1]|[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|平均精度以每个阈值实现的加权精度汇总精度-召回率曲线，使用前一阈值中的召回率增量作为权重。 <br><br> **目的：** 越接近 1 越好 <br> **范围：** [0, 1]<br> <br>支持的指标名称包括，<li>`average_precision_score_macro`，每个类的平均精度评分算术平均值。<li> `average_precision_score_micro`，以多标签方式计算。 每个示例的每一个不同的类都将被视为独立的 `0/1` 预测。 正确的类将变为 `true` 类，其余将为 `false` 类。 然后，系统将为新的二元分类任务计算平均精度，并将所有示例组合在一起。<li>`average_precision_score_weighted`，每个类的平均精度评分算术平均值，按每个类中的真实实例数加权。 <li> `average_precision_score_binary`，将一个特定类视为 `true` 类并将所有其他类合并为 `false` 类的平均精度值。|[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|平衡准确度是每个类的召回率算术平均值。<br> <br>**目的：** 越接近 1 越好 <br> **范围：** [0, 1]|[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|F1 评分是精度和召回率的调和平均值。 这是一个很好的衡量假正和假负的平衡。 然而，它没有考虑到真负。 <br> <br>**目的：** 越接近 1 越好 <br> **范围：** [0, 1]<br> <br>支持的指标名称包括，<li>  `f1_score_macro`：每个类的 F1 评分算术平均值。 <li> `f1_score_micro`：通过统计真正、假负和假正总值来计算得出。 <li> `f1_score_weighted`：按每个类的 F1 评分类频率计算的加权平均值。 <li> `f1_score_binary`，将一个特定类视为 `true` 类并将所有其他类合并为 `false` 类的 f1 值。|[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|这是（多项式） 逻辑回归及其扩展（例如神经网络）中使用的损失函数，在给定概率分类器的预测的情况下，定义为真实标签的负对数可能性。 <br><br> **目的：** 越接近 0 越好 <br> **范围：** [0, inf)|[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| 规范化宏召回率是对宏召回率进行规范化和平均化，因此，随机性能的评分为 0，完美性能的评分为 1。 <br> <br>**目的：** 越接近 1 越好 <br> **范围：** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>其中，`R` 是随机预测的 `recall_score_macro` 期望值。<br><br>`R = 0.5`&nbsp; 表示&nbsp;二进制&nbsp;分类。 <br>`R = (1 / C)` 表示 C 类分类问题。|
matthews_correlation | Matthews 关联系数是一种平衡准确性度量值，即使一个类比另一个类有更多的样本，也可以使用它。 系数 1 表示完美预测、0 表示随机预测以及 -1 表示反向预测。<br><br> **目的：** 越接近 1 越好 <br> **范围：** [-1, 1]|[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
精准率|精准率是模型避免将负样本标记为正样本的能力。 <br><br> **目的：** 越接近 1 越好 <br> **范围：** [0, 1]<br> <br>支持的指标名称包括， <li> `precision_score_macro`，每个类的精度算术平均值。 <li> `precision_score_micro`，通过统计真正和假正总值来全局计算 Micro。 <li> `precision_score_weighted`，每个类的精度算术平均值，按每个类中的真实实例数加权。 <li> `precision_score_binary`，将一个特定类视为 `true` 类并将所有其他类合并为 `false` 类的精度值。|[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.precision_score.html)|
召回率| 召回率是模型检测所有正的样本。 <br><br> **目的：** 越接近 1 越好 <br> **范围：** [0, 1]<br> <br>支持的指标名称包括， <li>`recall_score_macro`，每个类的召回率算术平均值。 <li> `recall_score_micro`：通过统计真正、假负和假正总值来全局计算。<li> `recall_score_weighted`：每个类的召回率算术平均值，按每个类中的真实实例数加权。 <li> `recall_score_binary`，将一个特定类视为 `true` 类并将所有其他类合并为 `false` 类的召回值。|[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|加权准确性是指每个样本按属于同一类别的样本总数加权的准确性。 <br><br>**目的：** 越接近 1 越好 <br>**范围：** [0, 1]|[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>二进制与多类分类指标

自动 ML 会自动检测数据是否为二进制数据，还允许用户通过指定 `true` 类来激活二元分类指标（即使数据属于多类）。 无论数据集有两个类或两个以上类，系统都会报告多类分类指标。 只有在数据为二进制或用户激活选项时，才报告二元分类指标。 

请注意，多类分类指标适用于多类分类。 正如你所期望的那样，这些指标在应用于二元分类数据集时不会将任何类视为 `true` 类。 明确用于多类的指标以 `micro`、`macro` 或 `weighted` 为后缀。 示例包括 `average_precision_score`、`f1_score`、`precision_score`、`recall_score`、`AUC`。 例如，多类平均召回率（`micro`、`macro` 或 `weighted`）不按 `tp / (tp + fn)` 计算召回率，而是对二进制分类数据集的两个类进行平均。 这相当于分别计算 `true` 类和 `false` 类的召回率，然后取二者的平均值。

此外，尽管支持自动检测二元分类，但仍建议一律手动指定 `true` 类，以确保为正确的类计算二元分类指标。

若要在数据集本身为多类时激活二元分类数据集的指标，用户只需指定要视为 `true` 类的类，即可计算这些指标。

## <a name="confusion-matrix"></a>混淆矩阵

混淆矩阵提供了机器学习模型如何在分类模型的预测中产生系统性误差的直观信息。 名称中的“混淆”一词来自模型“混淆”或错误标记样本。 混淆矩阵中第`i` 行和第 `j` 列的单元格包含评估数据集中属于类 `C_i` 且由模型分类为类 `C_j` 的样本数。

在工作室中，较暗的单元格表示样本数较多。 在下拉列表中选择规范化的视图将对每个矩阵行进行规范化，以显示预测为类 `C_j` 的类 `C_i` 的百分比。 默认 Raw 视图的好处是，可以看到实际类分布的不平衡是否导致模型从少数类中错分类样本，这是不平衡数据集的常见问题。

良好模型的混淆矩阵沿对角线方向的样本最多。

### <a name="confusion-matrix-for-a-good-model"></a>良好模型的混淆矩阵 
![良好模型的混淆矩阵 ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>不良模型的混淆矩阵
![不良模型的混淆矩阵](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC 曲线

接收方操作特征 (ROC) 曲线描绘了真正率 (TPR) 和假正率 (FPR) 随决策阈值变化的关系。 在类失衡严重的情况下基于数据集训练模型时，ROC 曲线提供的信息可能较少，因为多数类可能会掩盖少数类的贡献。

曲线下区域 (AUC) 可以解释为正确分类样本的比例。 更准确地说，AUC 是分类器对随机选择的正样本的排名高于随机选择的负样本的概率。 曲线的形状直观地表明 TPR 和 FPR 之间的关系是分类阈值或决策边界的函数。

接近图表左上角的曲线接近 100% TPR 和 0% FPR，这是最佳模型。 随机模型会沿着 `y = x` 线从左下角到右上角生成一条 ROC 曲线。 比随机模型更糟糕的模型，其 ROC 曲线会下降到 `y = x` 线以下。
> [!TIP]
> 对于分类实验，为自动化 ML 模型生成的每个折线图都可以用于评估每个类的模型或所有类的平均值。 通过单击图表右侧图例中的类标签，可以在这些不同的视图之间切换。

### <a name="roc-curve-for-a-good-model"></a>良好模型的 ROC 曲线
![良好模型的 ROC 曲线](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>不良模型的 ROC 曲线
![不良模型的 ROC 曲线](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>精度-召回率曲线

精准率-召回率曲线描绘了精准率与召回率之间随决策阈值变化的关系。 召回率是模型检测所有正样本的能力，精准率是模型避免将负样本标记为正样本的能力。 某些业务问题可能需要更高的召回率和更高的精准率，这取决于避免假负和假正的相对重要性。
> [!TIP]
> 对于分类实验，为自动化 ML 模型生成的每个折线图都可以用于评估每个类的模型或所有类的平均值。 通过单击图表右侧图例中的类标签，可以在这些不同的视图之间切换。
### <a name="precision-recall-curve-for-a-good-model"></a>良好模型的精准率-召回率曲线
![良好模型的精准率-召回率曲线](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>不良模型的精准率-召回率曲线
![不良模型的精准率-召回率曲线](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>累积增益曲线

累积增益曲线绘制了正确分类的正样本百分比，作为我们按预测概率顺序考虑样本所占百分比的函数。

若要计算增益，首先从模型预测的最高概率到最低概率对所有样本进行排序。 然后取 `x%` 的最高置信度预测值。 将在该 `x%` 中检测到的正样本数除以正样本总数得到增益。 累积增益是我们在考虑最有可能属于正类别的某些数据百分比时检测到的正样本百分比。

理想的模型将所有正样本的排名高于所有负样本，给出由两个直线段组成的累积增益曲线。 第一条是斜率为 `1 / x` 的线，从 `(0, 0)` 到 `(x, 1)`，其中 `x` 是属于正类的样本分数（如果类是平衡的，则为 `1 / num_classes`）。 第二条是从 `(x, 1)` 到 `(1, 1)` 的水平线。 在第一段中，所有正样本都已正确分类，累积增益在考虑的样本的第一个 `x%` 内到达 `100%`。

基线随机模型在 `y = x` 之后有一个累积增益曲线，其中在所考虑的 `x%` 的样本中仅检测到 `x%` 的总正样本。 均衡数据集的完美模型包含一条微平均曲线和一条斜率 `num_classes` 的宏平均线，直到累积增益达到 100%，然后水平延伸，直到数据百分比达到 100。
> [!TIP]
> 对于分类实验，为自动化 ML 模型生成的每个折线图都可以用于评估每个类的模型或所有类的平均值。 通过单击图表右侧图例中的类标签，可以在这些不同的视图之间切换。
### <a name="cumulative-gains-curve-for-a-good-model"></a>良好模型的累积增益曲线
![良好模型的累积增益曲线](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>不良模型的累积增益曲线
![不良模型的累积增益曲线](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>提升曲线

提升曲线显示某个模型的表现优于随机模型的次数。 提升定义为随机模型的累积增益与累积增益之比（应始终为 `1`）。

这种相对表现考虑到类的数量越多，分类越困难。 （与具有两个类的数据集相比，随机模型对具有 10 个类的数据集中的样本进行预测时，错误率更高）

基线提升曲线是模型性能与随机模型性能一致的 `y = 1` 线。 通常，良好模型的提升曲线在图表上会更高，离 x 轴更远，这表明当模型对其预测最有信心时，它的表现会比随机猜测好很多倍。

> [!TIP]
> 对于分类实验，为自动化 ML 模型生成的每个折线图都可以用于评估每个类的模型或所有类的平均值。 通过单击图表右侧图例中的类标签，可以在这些不同的视图之间切换。
### <a name="lift-curve-for-a-good-model"></a>良好模型的提升曲线
![良好模型的提升曲线](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>不良模型的提升曲线
![不良模型的提升曲线](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>校准曲线

校准曲线根据每个置信度水平的正样本比例绘制模型预测的置信度。 经过良好校准的模型将正确地对 100% 的预测进行分类，其中 100% 的预测将赋予 100% 的置信度，50% 的预测降赋予 50% 的置信度，20% 的预测将赋予 20% 的置信度，依此类推。 完全校准的模型将有一条校准曲线，沿着 `y = x` 线，模型完美地预测样本属于每个类别的概率。

置信度过高的模型在预测接近零和一的概率时会出现高估的情况，但很少出现无法确定每个样本的类的情况，并且校准曲线会类似于倒着的“S”。 置信度过低的模型将为其预测的类别平均分配较低的概率，相关的校准曲线将类似于“S”。 校准曲线并不能说明一个模型是否能正确分类，而是说明它是否能正确向预测分配置信度。 如果模型正确地分配了低置信度和高不确定性，则不良模型仍然可具有好的校准曲线。

> [!NOTE]
> 校准曲线对样本数量非常敏感，因此小型验证集可能会产生难以解释的干扰结果。 这并不一定意味着模型没有进行正确校准。

### <a name="calibration-curve-for-a-good-model"></a>良好模型的校准曲线
![良好模型的校准曲线](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>不良模型的校准曲线
![不良模型的校准曲线](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>回归/预测指标

自动化 ML 为生成的每个模型计算相同的性能指标，不管它是回归试验还是预测试验。 这些指标也经过规范化处理，以便在不同范围的数据上训练的模型之间进行比较。 若要了解详细信息，请参阅[指标规范化](#metric-normalization)。  

下表总结了为回归和预测试验生成的模型性能指标。 与分类指标一样，这些指标也基于 scikit learn 实现。 相应的 scikit learn 文档在“计算”字段中链接。

|指标|说明|计算|
--|--|--|
explained_variance|解释的方差衡量模型对目标变量变化的解释程度。 它是原始数据方差与误差方差之间的递减百分比。 当误差的平均值为 0 时，它等于确定系数（请参见下面的 r2_score）。 <br> <br> **目的：** 越接近 1 越好 <br> **范围：** (-inf, 1]|[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|平均绝对误差是目标与预测之间的差的预期绝对值。<br><br> **目的：** 越接近 0 越好 <br> **范围：** [0, inf) <br><br> 类型： <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`，mean_absolute_error 除以数据范围。 | [计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|平均绝对百分比误差 (MAPE) 是预测值和实际值之间平均差值的度量值。<br><br> **目的：** 越接近 0 越好 <br> **范围：** [0, inf) ||
median_absolute_error|平均绝对误差是目标与预测之间的所有绝对差的中间值。 此损失值可靠地反映离群值。<br><br> **目的：** 越接近 0 越好 <br> **范围：** [0, inf)<br><br>类型： <br> `median_absolute_error`<br> `normalized_median_absolute_error`median_absolute_error 除以数据范围。 |[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R<sup>2</sup>（决定系数）衡量均方误差 (MSE) 相对于观察到的数据的总方差的按比例的降低程度。 <br> <br> **目的：** 越接近 1 越好 <br> **范围：** [-1, 1]<br><br>注意：R<sup>2</sup> 的范围通常为 (-inf, 1]。 MSE 可以大于观察到的方差，因此 R<sup>2</sup> 可以有任意大的负值，具体取决于数据和模型预测。 自动化 ML 剪辑报告的 R<sup>2</sup> 分数为 -1，因此 R<sup>2</sup> 的值为 -1 可能表示实际的 R<sup>2</sup> 分数小于 -1。 在解释负 R<sup>2</sup> 分数时，请考虑其他指标值和数据的属性。|[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |均方根误差 (RMSE) 是目标与预测之间的预期平方差的平方根。 对于无偏差估算器，RMSE 等于标准偏差。<br> <br> **目的：** 越接近 0 越好 <br> **范围：** [0, inf)<br><br>类型：<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`：root_mean_squared_error 除以数据范围。 |[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|均方根对数误差是预期平方对数误差的平方根。<br><br>**目的：** 越接近 0 越好 <br> **范围：** [0, inf) <br> <br>类型： <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`，root_mean_squared_log_error 除以数据范围。  |[计算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| 斯皮尔曼相关是两个数据集之间的关系单一性的非参数测量法。 与皮尔逊相关不同，斯皮尔曼相关不假设两个数据集呈正态分布。 与其他相关系数一样，斯皮尔曼在 -1 和 +1 之间变化，0 表示不相关。 -1 或 1 相关表示确切的单一关系。 <br><br> 斯皮尔曼是一个秩相关指标，这意味着，如果预测值或实际值的变化不改变预测值或实际值的秩序，则不会改变斯皮尔曼结果。<br> <br> **目的：** 越接近 1 越好 <br> **范围：** [-1, 1]|[计算](https://docs.scipy.org/doc/scipy-1.5.2/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>指标规范化

自动化 ML 规范化回归和预测指标，使得在不同范围的数据上训练的模型之间能够进行比较。 在较大范围的数据上训练的模型比在较小范围的数据上训练的模型具有更高的误差，除非该误差已规范化。

虽然没有规范化误差指标的方法，但是自动化 ML 采用的是将误差除以数据范围的通用方法：`normalized_error = error / (y_max - y_min)`

在对时间序列数据的预测模型进行评估时，自动化 ML 会采取额外的步骤来确保按照时序 ID（粒度）进行规范化，因为每个时序可能具有不同的目标值分布。
## <a name="residuals"></a>残差

残差图是为回归和预测试验生成的预测误差（残差）的直方图。 所有样本的残差计算为 `y_predicted - y_true`，然后显示为直方图以显示模型偏差。

在本例中，请注意，两个模型的预测值都略低于实际值。 对于实际目标分布扭曲的数据集来说，这并不罕见，但这表明模型性能较差。 良好的模型会有一个残差分布，在零处达到峰值，而在极值处残差很少。 更糟的模型会有一个分散的残差分布，在零附近有更少的样本。

### <a name="residuals-chart-for-a-good-model"></a>良好模型的残差图
![良好模型的残差图](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>不良模型的残差图
![不良模型的残差图](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>预测值与实际值

对于回归和预测试验，预测与实际图表绘制了目标特征（实际值）与模型预测之间的关系。 实际值沿 x 轴分格，每个分格的平均预测值用误差线绘制。 这允许你查看模型是否偏向于预测某些值。 该行显示平均预测，阴影区域表示围绕该平均值的预测方差。

通常，最常见的实际值将具有最准确的预测和最小的方差。 趋势线与理想 `y = x` 线之间的距离是一个很好的测量模型在异常值上的表现的方法。 可以使用图表底部的直方图来推断实际的数据分布。 包括更多分布稀疏的数据样本可以提高模型对不可见数据的性能。

在此示例中，请注意，更好的模型有一条更接近理想 `y = x` 线的预测与实际线。

### <a name="predicted-vs-true-chart-for-a-good-model"></a>良好模型的预测与实际图表
![良好模型的预测与实际图表](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>不良模型的预测与实际图表
![不良模型的预测与实际图表](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>模型解释和特征重要性

虽然模型评估指标和图表有助于衡量模型的总体质量，但在实践负责任的 AI 时，检查用于进行预测的模型的数据集特征是至关重要的。 这就是自动化 ML 提供模型说明仪表板来测量和报告数据集特征的相对贡献的原因。 请参阅如何[在 Azure 机器学习工作室中查看说明仪表板](how-to-use-automated-ml-for-ml-models.md#model-explanations-preview)。

有关代码优先的体验，请参阅如何[使用 Azure 机器学习 Python SDK 为自动 ML 试验设置模型说明](how-to-machine-learning-interpretability-automl.md)。

> [!NOTE]
> 可解释性（最佳模型解释）不适用于将以下算法推荐为最佳模型或系综的自动化 ML 预测试验： 
> * TCNForecaster
> * AutoArima
> * ExponentialSmoothing
> * Prophet
> * 平均值 
> * Naive
> * Seasonal Average 
> * Seasonal Naive

## <a name="next-steps"></a>后续步骤
* 请参阅[自动化机器学习模型解释](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)示例笔记本。
* 对于自动化 ML 特定问题，请联系 askautomatedml@microsoft.com。
