---
title: 评估 AutoML 试验结果
titleSuffix: Azure Machine Learning
description: 了解如何查看和评估每个自动化机器学习试验运行的图表与指标。
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 11/30/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2, automl
ms.openlocfilehash: 43ce1c4865b3458ccd9c0ac17589f8ca5d77d92f
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922073"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>评估自动化机器学习试验结果

在本文中，了解如何评估和比较通过自动化机器学习 (自动 ML) 试验进行定型的模型。 在自动 ML 试验过程中，会创建多个运行，并且每次运行都会创建一个模型。 对于每个模型，自动 ML 会生成评估指标和图表，以帮助你衡量模型的性能。 

例如，自动 ML 会根据试验类型生成以下图表。

| 分类| 回归/预测 |
| ----------------------------------------------------------- | ---------------------------------------- |
| [混淆矩阵](#confusion-matrix)                       | [残差直方图](#residuals)        |
| [接收方操作特征 (ROC) 曲线](#roc-curve) | [预测与 true](#predicted-vs-true) |
| [精度-召回 (PR) 曲线](#precision-recall-curve)      |                                          |
| [提升曲线](#lift-curve)                                   |                                          |
| [累积收益曲线](#cumulative-gains-curve)           |                                          |
| [校准曲线](#calibration-curve)                     |                     


## <a name="prerequisites"></a>先决条件

- Azure 订阅。  (如果你没有 Azure 订阅，请在开始前 [创建一个免费帐户](https://aka.ms/AMLFree)) 
- 使用以下方法之一创建的 Azure 机器学习试验：
  - [Azure 机器学习 studio](how-to-use-automated-ml-for-ml-models.md) (无需任何代码) 
  - [Azure 机器学习 PYTHON SDK](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>查看运行结果

自动 ML 试验完成后，可以通过以下方式找到运行历史记录：
  - 使用[Azure 机器学习 studio](overview-what-is-machine-learning-studio.md)的浏览器
  - 使用[RunDetails Jupyter 小组件](/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py&preserve-view=true)的 Jupyter 笔记本

以下步骤和视频演示如何在工作室中查看运行历史记录和模型评估指标和图表：

1. [登录到工作室](https://ml.azure.com/)并导航到你的工作区。
1. 在左侧菜单中，选择 " **试验**"。
1. 从实验列表中选择试验。
1. 在页面底部的表中，选择 "自动 ML 运行"。
1. 在 " **模型** " 选项卡中，选择要计算的模型的 **算法名称** 。
1. 在 " **度量值** " 选项卡中，使用左侧的复选框来查看度量值和图表。

![在工作室中查看指标的步骤](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>分类指标

自动 ML 会计算为试验生成的每个分类模型的性能指标。 这些指标基于 scikit-learn 学习实现。 

许多分类度量值是针对两个类上的二元分类定义的，需要对类进行求平均值，以便为多类分类生成一个分数。 Scikit-learn 提供了几个平均方法，其中三个方法自动执行 ML： **宏**、 **微** 和 **加权**。

- **宏** -计算每个类的指标并取加权滑动 average
- **微** 计算指标，方法是计算与) 类无关的总真实正值、假负和误报 (。
- **加权** -计算每个类的指标，并根据每个类的样本数采用加权平均值。

虽然每个平均值方法都有其优点，但选择适当的方法时要考虑的一个常见因素是类不平衡。 如果类具有不同的样本数，则使用宏 average 可能更有意义，其中少数类向多数类赋予了相等的权重。 详细了解 [自动 ML 中的二进制与多类指标](#binary-vs-multiclass-classification-metrics)。 

下表汇总了模型性能指标，这些指标是自动化 ML 针对每个为试验生成的分类模型计算的。 有关更多详细信息，请参阅每个指标的 **计算** 字段中链接的 scikit-learn-了解的文档。 

|指标|说明|计算|
|--|--|---|
|AUC | AUC 是 [接收方操作特征曲线](#roc-curve)下的区域。<br><br> **目标：** 越接近1越好 <br> **范围：** [0，1]<br> <br>支持的指标名称包括、 <li>`AUC_macro`，每个类的 AUC 的算术平均值。<li> `AUC_micro`，通过将每个类中的真正误报和误报进行组合计算得出。 <li> `AUC_weighted`，每个类的分数的算术平均值，由每个类中的真实实例的数目加权。   |[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| 准确性是与真正的类标签完全匹配的预测的比率。 <br> <br>**目标：** 越接近1越好 <br> **范围：** [0，1]|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|平均精度以每个阈值实现的加权精度汇总精度-召回率曲线，使用前一阈值中的召回率增量作为权重。 <br><br> **目标：** 越接近1越好 <br> **范围：** [0，1]<br> <br>支持的指标名称包括、<li>`average_precision_score_macro`，每个类的平均精度分数的算术平均值。<li> `average_precision_score_micro`，它通过在每个截止时合并真正的正值和假正值进行计算。<li>`average_precision_score_weighted`，每个类的平均精度分数的算术平均值，由每个类中的真实实例的数目加权。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|平衡准确度是每个类的召回率算术平均值。<br> <br>**目标：** 越接近1越好 <br> **范围：** [0，1]|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|F1 评分是精度和召回率的调和平均值。 这是对误报和漏报的合理平衡。 但是，它不会考虑真正的否定。 <br> <br>**目标：** 越接近1越好 <br> **范围：** [0，1]<br> <br>支持的指标名称包括、<li>  `f1_score_macro`：每个类的 F1 分数的算术平均值。 <li> `f1_score_micro`：通过计算总真实正值、假负值和假正值计算得出。 <li> `f1_score_weighted`：每个类的每类 F1 分数的加权平均值。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|这是（多项式） 逻辑回归及其扩展（例如神经网络）中使用的损失函数，在给定概率分类器的预测的情况下，定义为真实标签的负对数可能性。 <br><br> **目标：** 越接近0越好 <br> **范围：** [0，inf) |[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| 规范化的宏召回是回调宏的平均和规范化，因此，随机性能的分数为0，理想的性能为1的分数。 <br> <br>**目标：** 越接近1越好 <br> **范围：** [0，1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>其中， `R` 是用于随机预测的的预期值 `recall_score_macro` 。<br><br>`R = 0.5`&nbsp;对于 &nbsp; 二元 &nbsp; 分类。 <br>`R = (1 / C)` 对于 C 类分类问题。|
Matthews 相关系数 | Matthews 相关系数是精确度的均衡度量值，即使一个类有比另一个类更多的示例，也可以使用该度量值。 系数1表示完美预测，0随机预测，-1 反转预测。<br><br> **目标：** 越接近1越好 <br> **范围：** [-1，1]|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
精准率|精度是指模型避免将负样本标记为正值的能力。 <br><br> **目标：** 越接近1越好 <br> **范围：** [0，1]<br> <br>支持的指标名称包括、 <li> `precision_score_macro`，每个类的精度算术平均值。 <li> `precision_score_micro`，通过计算总的实际正值和假正值来全局计算。 <li> `precision_score_weighted`，每个类的精度的算术平均值，按每个类中的真实实例数为加权。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
召回率| 回想一下，模型能够检测所有正的样本。 <br><br> **目标：** 越接近1越好 <br> **范围：** [0，1]<br> <br>支持的指标名称包括、 <li>`recall_score_macro`：每个类的算术平均值。 <li> `recall_score_micro`：通过计算总真实正值、假负值和假正值来全局计算。<li> `recall_score_weighted`：对每个类的回调算术平均值，按每个类中的真实实例的数目加权。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|加权准确性是精确的，其中每个样本都按属于同一类的样本总数加权。 <br><br>**目标：** 越接近1越好 <br>**范围：** [0，1]|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>二元和多类分类指标

自动化 ML 不区分二元分类指标与多类指标。 不管数据集有两个类还是两个以上的类，都会报告相同的验证指标。 但是，某些指标旨在用于多类分类。 正如你所期望的那样，这些指标在应用于二元分类数据集时不会将任何类视为 `true` 类。 明确用于多类的指标以 `micro`、`macro` 或 `weighted` 为后缀。 示例包括 `average_precision_score`、`f1_score`、`precision_score`、`recall_score`、`AUC`。

例如，多类平均召回率（`micro`、`macro` 或 `weighted`）不按 `tp / (tp + fn)` 计算召回率，而是对二进制分类数据集的两个类进行平均。 这相当于分别计算 `true` 类和 `false` 类的召回率，然后取二者的平均值。

## <a name="confusion-matrix"></a>混淆矩阵

混淆矩阵为机器学习模型在分类模型的预测中做出系统错误的方式提供了视觉对象。 名称中的 "混乱" 一词来自模型 "混乱" 或 mislabeling 示例。 混淆矩阵的行 `i` 和列 `j` 中的单元包含属于类 `C_i` 并由模型分类为类的计算数据集中的样本数 `C_j` 。

在工作室中，较暗的单元格表示更多的示例。 选择下拉列表中的 " **规范化** 视图" 将对每个矩阵行进行规范化，以显示 `C_i` 预测为类的类的百分比 `C_j` 。 默认的 **原始** 视图的优点是，您可以查看实际类的分布中的不平衡是否导致模型从少数类中对样本，这是不均衡数据集中的一个常见问题。

好模型的混淆矩阵将会沿着对角线方向获得大多数示例。

### <a name="confusion-matrix-for-a-good-model"></a>良好模型的混淆矩阵 
![良好模型的混淆矩阵 ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>错误模型的混淆矩阵
![错误模型的混淆矩阵](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC 曲线

当决策阈值变化时，接收方操作特征 (ROC) 曲线绘制 (TPR) 和误报 (以及) 之间的关系。 如果在具有高类不平衡的数据集上定型模型，则该 ROC 曲线可能会不太丰富，因为多数类可以 drown 少数类的贡献。

曲线下的区域 (AUC) 可以解释为正确分类样本的比例。 更准确地说，AUC 是分类器排名随机选择的正样本比随机选择的负样本大的概率。 曲线的形状提供 TPR 和以及之间的关系的直觉作为分类阈值或决策边界的函数。

接近图表左上角的曲线接近 100% TPR，0% 以及，这是可能的最佳模型。 随机模型将沿 `y = x` 从左下角到右上角的行生成一个 ROC 曲线。 比随机模型更糟糕的是，它的 `y = x`
> [!TIP]
> 对于分类试验，为自动 ML 模型生成的每个折线图都可用于评估每类的模型或所有类的平均值。 您可以通过单击图表右侧图例中的类标签，在这些不同视图之间切换。
### <a name="roc-curve-for-a-good-model"></a>良好模型的 ROC 曲线
![良好模型的 ROC 曲线](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>错误模型的 ROC 曲线
![错误模型的 ROC 曲线](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>精度-召回曲线

当决策阈值发生变化时，精度召回曲线会绘制精度和召回之间的关系。 回想一下，模型能够检测所有正的样本和精度，这是模型避免将负样本标记为正值的能力。 某些业务问题可能需要更高的召回度和更高的精度，具体取决于避免误报和误报的相对重要性。
> [!TIP]
> 对于分类试验，为自动 ML 模型生成的每个折线图都可用于评估每类的模型或所有类的平均值。 您可以通过单击图表右侧图例中的类标签，在这些不同视图之间切换。
### <a name="precision-recall-curve-for-a-good-model"></a>精确模型的精度召回曲线
![精确模型的精度召回曲线](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>错误模型的精度召回曲线
![错误模型的精度召回曲线](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>累积收益曲线

累积的增益曲线会将正样本的百分比正确地分类为所考虑的样本百分比的函数，在这种情况下，我们将示例视为预测概率的顺序。

若要计算增益，请首先对模型预测的最高和最低概率进行排序。 然后 `x%` ，采用最高的置信度预测。 将其中检测到的正样本数除以 `x%` 正样本总数以获取收益。 累积增益是指在考虑最有可能属于正类的数据的一些百分比时检测到的正样本的百分比。

理想的模型将对所有负样本的所有正抽样进行排名，使其具有两个直段组成的累积收益曲线。 第一条是一条从到斜率的线条， `1 / x` `(0, 0)` `(x, 1)` 其中 `x` 是 `1 / num_classes`) 类平衡的情况下，属于正类 (样本的小数部分。 第二个是从到的水平线条 `(x, 1)` `(1, 1)` 。 在第一段中，所有正则样本均已正确分类，累积收益将在 `100%` 考虑的第一个样本中进行 `x%` 。

基线随机模型将产生一条累积的增益曲线，该曲线后面 `y = x` `x%` 仅考虑 `x%` 到已检测到的样本总数。 理想的模型将有一个接近左上角的微平均曲线和一个具有斜度的宏平均线条， `1 / num_classes` 直到累积增益为100%，然后水平滚动，直到数据百分比为100。
> [!TIP]
> 对于分类试验，为自动 ML 模型生成的每个折线图都可用于评估每类的模型或所有类的平均值。 您可以通过单击图表右侧图例中的类标签，在这些不同视图之间切换。
### <a name="cumulative-gains-curve-for-a-good-model"></a>良好模型的累积收益曲线
![良好模型的累积收益曲线](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>错误模型的累积收益曲线
![错误模型的累积收益曲线](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>提升曲线

提升曲线显示了模型比随机模型更好地执行多少次。 升降定义为随机模型的累计收益的比率。

这种相对性能考虑到在增加类的数量时，分类变得困难。  (随机模型从包含10个类的数据集中错误预测较高部分的样本，其中包含两个类) 

基线提升曲线是 `y = 1` 模型性能与随机模型的性能一致的行。 通常，好模型的提升曲线在该图上和 x 轴上的位置将会更高，这表明当模型最有把握其预测时，它所执行的时间比随机推测更好。

> [!TIP]
> 对于分类试验，为自动 ML 模型生成的每个折线图都可用于评估每类的模型或所有类的平均值。 您可以通过单击图表右侧图例中的类标签，在这些不同视图之间切换。
### <a name="lift-curve-for-a-good-model"></a>好模型的提起曲线
![好模型的提起曲线](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>错误模型的提起曲线
![错误模型的提起曲线](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>校准曲线

校准曲线根据每个置信度的正样本比例，在其预测中绘制模型的置信度。 校准良好的模型将正确地分类100% 的预测，以将其分配100% 置信度，50% 的预测分配了50% 置信度，20% 的预测分配了20% 的置信度，依此类推。 完全校准的模型会在行的后面有一个校准曲线 `y = x` ，其中的模型可用于预测样本所属的每个类的概率。

过度自信的模型将过度预测接近零和一的概率，很少无法确定每个样本的类，并且校准曲线看起来将类似于向后 "S"。 置信度较低的模型将对它所预测的类平均分配一个较低的概率，而关联的校准曲线将类似于 "S"。 校准曲线并不说明模型能否正确地分类，而是使其能够正确地将置信度分配给其预测。 如果模型正确地分配了置信度和高不确定性，则不正确的模型仍可以具有良好的校准曲线。

> [!NOTE]
> 校准曲线对样本的数量很敏感，因此小型验证集可能产生难以解释的干扰结果。 这并不一定意味着模型没有正确校准。

### <a name="calibration-curve-for-a-good-model"></a>好模型的校准曲线
![好模型的校准曲线](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>错误模型的校准曲线
![错误模型的校准曲线](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>回归/预测指标

对于每个生成的模型，自动 ML 会计算相同的性能指标，而不考虑它是回归试验还是预测试验。 这些度量值还会经过规范化，以便在使用不同范围的数据进行定型的模型之间进行比较。 若要了解详细信息，请参阅 [指标规范化](#metric-normalization)  

下表总结了为回归和预测试验而生成的模型性能指标。 与分类指标一样，这些指标也基于 scikit-learn 学习实现。 相应的 scikit-learn 学习文档在 **计算** 字段中进行了相应的链接。

|指标|说明|计算|
--|--|--|
explained_variance|解释的方差用于测量目标变量中的一个模型帐户的变化程度。 它是原始数据方差与误差方差之间的递减百分比。 当误差的平均值为 0 时，它等于确定系数（请参见下面的 r2_score）。 <br> <br> **目标：** 越接近1越好 <br> **范围：** (-inf，1]|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|平均绝对误差是目标和预测的绝对值的绝对值值。<br><br> **目标：** 越接近0越好 <br> **范围：** [0，inf)  <br><br> 各种 <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`，mean_absolute_error 除以数据的范围。 | [计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
median_absolute_error|平均绝对误差是目标与预测之间的所有绝对差的中间值。 此损失值可靠地反映离群值。<br><br> **目标：** 越接近0越好 <br> **范围：** [0，inf) <br><br>各种 <br> `median_absolute_error`<br> `normalized_median_absolute_error`： median_absolute_error 除以数据范围。 |[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R^2 是确定系数或平方误差与输出平均值的基线模型相比减少的百分比。 <br> <br> **目标：** 越接近1越好 <br> **范围：** (-inf，1]|[计算](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |根平均方形错误 (RMSE) 是目标与预测的预期平方差的平方根。 对于无偏差估计器，RMSE 等于标准偏差。<br> <br> **目标：** 越接近0越好 <br> **范围：** [0，inf) <br><br>各种<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`： root_mean_squared_error 除以数据范围。 |[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|根平均值方形日志错误是预期的方形对数误差的平方根。<br><br>**目标：** 越接近0越好 <br> **范围：** [0，inf)  <br> <br>各种 <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`： root_mean_squared_log_error 除以数据范围。  |[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| 斯皮尔曼相关是两个数据集之间的关系单一性的非参数测量法。 与皮尔逊相关不同，斯皮尔曼相关不假设两个数据集呈正态分布。 与其他相关系数一样，斯皮尔曼在-1 和1之间有所不同，0意味着无关联。 -1 或1的相关性表示完全单调的关系。 <br><br> 斯皮尔曼是一种排名顺序相关性指标，如果预测值或实际值的更改不更改预测值或实际值的排名顺序，则不会更改其结果。<br> <br> **目标：** 越接近1越好 <br> **范围：** [-1，1]|[计算](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>指标标准化

自动 ML 标准化了回归和预测指标，实现了对具有不同范围的数据进行定型的模型之间的比较。 使用较大范围的数据训练的模型的错误比使用较小范围的数据训练的模型更高，除非该错误已规范化。

尽管没有标准化错误度量标准的标准方法，但自动 ML 采用了一种通用的方法将错误划分为数据范围： `normalized_error = error / (y_max - y_min)`

评估时序数据的预测模型时，自动 ML 会执行额外的步骤，以确保每个时序 ID (粒度) 进行标准化，因为每个时序可能会有不同的目标值分布。
## <a name="residuals"></a>残差

"残差" 图表是预测错误的直方图， (为回归和预测试验生成的残差) 。 为所有样本计算残差 `y_predicted - y_true` ，然后显示为直方图以显示模型偏差。

在此示例中，请注意，这两个模型的预测低于实际值。 这对于具有实际目标的歪斜分布的数据集并不常见，但表明模型性能更差。 良好的模型将有一个残差分布，其峰值为零，最极端的残差。 更糟的模型将有一个分散的残差分布，其中的样本数更少。

### <a name="residuals-chart-for-a-good-model"></a>良好模型的残差图
![良好模型的残差图](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>错误模型的残差图
![错误模型的残差图](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>预测与 true

对于回归和预测试验，预测与真实的图表会绘制目标特征 (true/实际值) 和模型预测之间的关系。 True 值沿 x 轴装箱，对于每个箱，将用误差线绘制平均值预测值。 这使您可以查看模型是否偏离预测某些值。 该行显示平均预测，灰色区域指示围绕该平均值的预测的方差。

通常，最常见的 true 值将具有具有最小方差的最准确预测。 趋势线距理想线条的距离， `y = x` 其中有很少的真实值，是对离群值的模型性能的良好度量。 您可以使用图表底部的直方图来了解实际的数据分布。 包括更多数据样本，其中分布是稀疏的，可以提高对不可见数据的模型性能。

在此示例中，请注意，更好的模型具有更接近理想线条的预测与真行 `y = x` 。

### <a name="predicted-vs-true-chart-for-a-good-model"></a>优秀模型的预测与真实图表
![优秀模型的预测与真实图表](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>错误模型的预测与真实图表
![错误模型的预测与真实图表](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>模型说明和功能 importances

虽然模型评估度量值和图表非常适合用于测量模型的总体质量，但检查哪些数据集的特征是在练习负责的 AI 时，用来进行预测的模型是至关重要的。 这就是自动化 ML 提供模型 interpretability 仪表板来度量和报告数据集功能的相对贡献的原因。

![功能 importances](./media/how-to-understand-automated-ml/how-to-feature-importance.gif)

若要查看工作室中的 interpretability 仪表板：

1. [登录到工作室](https://ml.azure.com/) 并导航到工作区
2. 在左侧菜单中，选择 "**试验**"
3. 从试验列表中选择实验
4. 在页面底部的表中，选择 "AutoML" 运行
5. 在 "**模型**" 选项卡中，选择要说明的模型的 **算法名称**
6. 在 " **说明** " 选项卡中，如果模型是最佳模式，可能会看到已创建的说明
7. 若要创建新的说明，请选择 " **说明模型** "，然后选择用于计算说明的远程计算

> [!NOTE]
> ForecastTCN 模型目前不受自动 ML 说明和其他预测模型的支持。

## <a name="next-steps"></a>后续步骤
* 尝试 [自动机器学习模型解释示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)。
* [在自动 ML 中了解有关责任 AI 产品/服务的](how-to-machine-learning-interpretability-automl.md)详细信息。
* 对于自动 ML 特定问题，请联系 askautomatedml@microsoft.com 。
