---
title: 通过 Python 设置自动化机器学习
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习自动化机器学习将自动化机器学习训练设置为通过 Azure 机器学习 Python SDK 来运行。
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 07/01/2021
ms.topic: how-to
ms.custom: devx-track-python,contperf-fy21q1, automl, contperf-fy21q4, FY21Q4-aml-seo-hack
ms.openlocfilehash: 2da9b19bb0d2bcdf09cb478898590d55398b2cc9
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122180067"
---
# <a name="set-up-automl-training-with-python"></a>使用 Python 设置自动化机器学习训练

在本指南中，了解如何使用 Azure 机器学习自动化机器学习将自动化机器学习训练设置为通过 [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/intro) 来运行。 自动化 ML 将自动选择算法和超参数，并生成随时可用于部署的模型。 可以使用多个选项来配置这些类型的试验。

有关端到端示例，请参阅[教程：自动化机器学习 - 训练回归模型](tutorial-auto-train-models.md)。

自动化机器学习提供的配置选项：

* 选择试验类型：分类、回归或时序预测
* 数据源、格式和提取数据
* 选择计算目标：本地或远程
* 自动化机器学习试验设置
* 运行自动化机器学习试验
* 探索模型指标
* 注册和部署模型

如果你更喜欢无代码体验，还可以 [在 Azure 机器学习工作室中设置无代码自动化机器学习训练](how-to-use-automated-ml-for-ml-models.md)。

## <a name="prerequisites"></a>先决条件

在本文中，你需要： 
* Azure 机器学习工作区。 若要创建工作区，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* 已安装 Azure 机器学习 Python SDK。
    若要安装该 SDK，你可以： 
    * 创建一个计算实例，该实例将自动安装 SDK 并针对 ML 工作流进行预先配置。 有关详细信息，请参阅[创建和管理 Azure 机器学习计算实例](how-to-create-manage-compute-instance.md)。 

    * [自行安装 `automl` 包](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)，其中包括 SDK [默认安装](/python/api/overview/azure/ml/install#default-install)。

    [!INCLUDE [automl-sdk-version](../../includes/machine-learning-automl-sdk-version.md)]
    
    > [!WARNING]
    > Python 3.8 与 `automl` 不兼容。 

## <a name="select-your-experiment-type"></a>选择试验类型

在开始试验之前，应确定要解决的机器学习问题类型。 自动化机器学习支持 `classification`、`regression` 和 `forecasting` 任务类型。 详细了解[任务类型](concept-automated-ml.md#when-to-use-automl-classification-regression--forecasting)。

下面的代码使用 `AutoMLConfig` 构造函数中的 `task` 参数将试验类型指定为 `classification`。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>数据源和格式

自动化机器学习支持驻留在本地桌面上或云中（例如 Azure Blob 存储）的数据。 数据可以读入 Pandas 数据帧或 Azure 机器学习 TabularDataset 中 。 [了解有关数据集的详细信息](how-to-create-register-datasets.md)。

机器学习中对训练数据的要求：
- 数据必须为表格格式。
- 要预测的值（目标列）必须位于数据中。

**对于远程试验**，必须能够从远程计算访问训练数据。 自动化 ML 仅在处理远程计算时才接受 [Azure 机器学习 TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset)。 

Azure 机器学习数据集公开的功能可以：

* 轻松地将数据从静态文件或 URL 源传输到工作区。
* 在云计算资源上运行时，使数据可用于训练脚本。 有关使用 `Dataset` 类将数据装载到远程计算目标的示例，请参阅[如何使用数据集进行训练](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets)。

下面的代码从一个 Web URL 创建 TabularDataset。 有关如何从其他源（例如本地文件和数据存储）创建数据集的代码示例，请参阅[创建 TabularDataset](how-to-create-register-datasets.md#create-a-tabulardataset)。

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**对于本地计算试验**，我们建议使用 pandas 数据帧以提高处理速度。

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>训练、验证和测试数据

可以直接在 `AutoMLConfig` 构造函数中指定单独的训练数据和验证数据集。 详细了解[如何配置数据拆分和交叉验证](how-to-configure-cross-validation-data-splits.md)（针对 AutoML 试验）。 

如果未显式指定 `validation_data` 或 `n_cross_validation` 参数，则自动化 ML 将应用默认技术来决定如何执行验证。 此决定依赖于分配给 `training_data` 参数的数据集中的行数。 

|训练数据大小| 验证技术 |
|---|-----|
|**大于 20,000 行**| 将应用训练/验证数据拆分。 默认行为是将初始训练数据集的 10% 用作验证集。 然后，该验证集将用于指标计算。
|**小于 20,000 行**| 将应用交叉验证方法。 默认折数取决于行数。 <br> **如果数据集小于 1,000 行**，则使用 10 折。 <br> **如果行数在 1,000 到 20,000 之间**，则使用 3 折。

此时，你需要提供自己的 **测试数据** 来进行模型评估。 如果需要通过代码示例来演示如何引入你自己的测试数据进行模型评估，请参阅 [此 Jupyter 笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)的 **Test** 节。

### <a name="large-data"></a>大型数据 

自动化 ML 支持使用有限数量的算法针对大型数据进行训练，这样可以在小型虚拟机上成功生成大数据的模型。 自动化 ML 启发式方法取决于数据大小、虚拟机内存大小、试验超时和特征化设置等属性，以确定是否应该应用这些大型数据算法。 [详细了解自动化 ML 支持哪些模型](#supported-models)。 

* 对于回归，[联机梯度下降回归量](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest)和[快速线性回归量](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)

* 对于分类，[平均感知器分类器](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)和[线性 SVM 分类器](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)；其中，线性 SVM 分类器同时具有大型数据和小型数据版本。

如果要替代这些启发式方法，请应用以下设置： 

任务 | 设置 | 说明
|---|---|---
阻止数据流式处理算法&nbsp; | `AutoMLConfig` 对象中的 `blocked_models`，并列出不需要使用的模型。 | 导致运行失败或长时间运行
使用数据流式处理算法&nbsp;&nbsp;&nbsp;| `AutoMLConfig` 对象中的 `allowed_models`，并列出需要使用的模型。| 
使用数据流式处理算法&nbsp;&nbsp;&nbsp; <br> [（工作室 UI 试验）](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)|阻止除要使用的大数据算法之外的所有模型。 |

## <a name="compute-to-run-experiment"></a>用于运行试验的计算环境

接下来，确定要在何处训练模型。 自动化机器学习训练试验可在以下计算选项中运行。 了解[本地和远程计算选项的优缺点](concept-automated-ml.md#local-remote)。 

* **本地** 台式机或便携式计算机等本地计算机 – 如果数据集较小，并且你仍然处于探索阶段，则通常使用此选项。 有关本地计算示例，请参阅[此笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb)。 
 
* 云中的 **远程** 计算机 – [Azure 机器学习托管计算](concept-compute-target.md#amlcompute)是一个托管服务，可用于在 Azure 虚拟机的群集上训练机器学习模型。 

    有关使用 Azure 机器学习托管计算的远程示例，请参阅[此笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)。 

* Azure 订阅中的 **Azure Databricks 群集**。 有关更多详细信息，可参阅[为自动化 ML 设置 Azure Databricks 群集](how-to-configure-databricks-automl-environment.md)。 有关包含 Azure Databricks 的示例 Notebook，请参阅此 [GitHub 站点](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)。

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>配置试验设置

可以使用多个选项来配置自动化机器学习试验。 通过实例化 `AutoMLConfig` 对象来设置这些参数。 有关参数的完整列表，请参阅 [AutoMLConfig 类](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)。

示例包括：

1. 使用 AUC 作为主要指标加权的分类实验，其中实验超时分钟数设置为 30 分钟，且包含 2 折交叉验证。

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. 下面是设置为 60 分钟后结束的回归试验示例，其中包含 5 折交叉验证。

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. 预测任务需要额外设置，有关更多详细信息，请参阅[为时序预测设置自动化机器学习](how-to-auto-train-forecast.md)一文。 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>支持的模型

在自动化和优化过程中，自动化机器学习会尝试各种模型和算法。 用户不需要指定算法。 

这三个不同的 `task` 参数值确定要应用的算法或模型的列表。 使用 `allowed_models` 或 `blocked_models` 参数通过要包含或排除的可用模型来进一步修改迭代。 

下表按任务类型汇总了支持的模型。 

> [!NOTE]
> 如果你计划将自动化 ML 创建的模型导出为 [ONNX 模型](concept-onnx.md)，则只有标有 *（星号）的算法才能被转换为 ONNX 格式。 详细了解[如何将模型转换为 ONNX](concept-automated-ml.md#use-with-onnx)。 <br> <br> 另请注意，ONNX 目前只支持分类和回归任务。 

分类 | 回归 | 时序预测
|-- |-- |--
[逻辑回归](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [弹性网络](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [AutoARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* | [Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* | [Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[梯度提升](https://scikit-learn.org/stable/modules/ensemble.html#classification)* | [梯度提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)* | [弹性网络](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[决策树](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[决策树](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[K 最近的邻域](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K 最近的邻域](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* | [渐进提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[线性 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* | [决策树](https://scikit-learn.org/stable/modules/tree.html#regression)
[支持矢量分类 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* | [决策树](https://scikit-learn.org/stable/modules/tree.html#regression)
[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* | [随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests) | [LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* | [极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* | [随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[平均感知器分类器](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)| [在线梯度下降回归量](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[快速线性回归量](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)| ForecastTCN
[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* || Naive
[线性 SVM 分类器](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)* || SeasonalNaive
||| 平均值
||| SeasonalAverage
||| [ExponentialSmoothing](https://www.statsmodels.org/v0.10.2/generated/statsmodels.tsa.holtwinters.ExponentialSmoothing.html)

### <a name="primary-metric"></a>主要指标
`primary metric` 参数决定了将在模型训练期间用于优化的指标。 你可选择的可用指标取决于所选择的任务类型，下表显示了每种任务类型的有效主要指标。

选择自动化机器学习要优化的主要指标取决于许多因素。 建议主要考虑选择最能体现业务需求的指标。 然后考虑指标是否适用于数据集配置文件（数据大小、范围、类分布等）。

如需了解上述指标的具体定义，请参阅[了解自动化机器学习结果集](how-to-understand-automated-ml.md)。

|分类 | 回归 | 时序预测
|--|--|--
|`accuracy`| `spearman_correlation` | `normalized_root_mean_squared_error`
|`AUC_weighted` | `normalized_root_mean_squared_error` | `r2_score`
|`average_precision_score_weighted` | `r2_score` | `normalized_mean_absolute_error`
|`norm_macro_recall` | `normalized_mean_absolute_error` | 
|`precision_score_weighted` |

#### <a name="metrics-for-classification-scenarios"></a>分类方案的指标 
如果数据集小、类倾斜非常大（类不均衡），或者预期的指标值非常接近 0.0 或 1.0，则阈值后指标（如 `accuracy`、`average_precision_score_weighted`、`norm_macro_recall` 和 `precision_score_weighted`）可能也不是最优的。 在这些情况下，对于主要指标，`AUC_weighted` 可能是更好的选择。 自动机器学习完成后，可以根据最能满足你业务需求的指标选择所需模型。

| 指标 | 示例用例 |
| ------ | ------- |
| `accuracy` | 图像分类、情绪分析、流失预测 |
| `AUC_weighted` | 欺诈检测、图像分类、异常检测/垃圾邮件检测 |
| `average_precision_score_weighted` | 情绪分析 |
| `norm_macro_recall` | 流失预测 |
| `precision_score_weighted` |  |

#### <a name="metrics-for-regression-scenarios"></a>回归方案的指标
 
当要预测的值的范围涵盖多个数量级时，`r2_score` 和 `spearman_correlation` 等指标可以更好地体现模型的质量。 例如，在薪水估算中，许多人的薪水在 2 万至 10 万美元之间，但某些人的薪水在 1 亿美元的范围中，这极大地拉大了总量级范围。 

在这种情况下，`normalized_mean_absolute_error` 和 `normalized_root_mean_squared_error` 会认为 2 万美元的预测误差对于薪水为 3 万美元的工人和薪水为 2000 万美元的工人是没有区别的。 但实际上，在 2000 万美元薪水的基础上增减 2 万美元和增减前的预测应该非常接近（相对差值很小，增减占比 0.1%），而在 3 万美元薪水的基础上增减 2 万美元和增减前的预测应该非常大（相对差值很大，增减占比 67%）。 当要预测的值在相似的量级范围内时，`normalized_mean_absolute_error` 和 `normalized_root_mean_squared_error` 会比较有用。

| 指标 | 示例用例 |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | 价格预测（房屋/产品/小费），查看分数预测 |
| `r2_score` | 航空延迟、薪金估算、Bug 解决时间 |
| `normalized_mean_absolute_error` |  |

#### <a name="metrics-for-time-series-forecasting-scenarios"></a>时序预测方案的指标
这些建议与针对回归方案提供的建议类似。 

| 指标 | 示例用例 |
| ------ | ------- |
| `normalized_root_mean_squared_error` | 价格预测（预测）、库存优化、需求预测 | 
| `r2_score` | 价格预测（预测）、库存优化、需求预测 |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>数据特征化

在每个自动化机器学习实验中，数据都是自动缩放和规范化，以帮助对不同规模上的特征敏感的 *某些* 算法。 此缩放和规范化称为特征化。 有关更多详细信息和代码示例，请参阅 [AutoML 中的特征化](how-to-configure-auto-features.md#)。 

在 `AutoMLConfig` 对象中配置试验时，可以启用/禁用设置 `featurization`。 下表列出了 [AutoMLConfig 对象](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)中的特征化的已接受设置。 

|特征化配置 | 说明 |
| ------------- | ------------- |
|`"featurization": 'auto'`| 指示在处理过程中自动执行[数据护栏和特征化步骤](how-to-configure-auto-features.md#featurization)。 默认设置。|
|`"featurization": 'off'`| 表示不应自动执行特征化步骤。|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 指示应当使用自定义特征化步骤。 [了解如何自定义特征化](how-to-configure-auto-features.md#customize-featurization)。|

> [!NOTE]
> 自动化机器学习特征化步骤（特征规范化、处理缺失数据，将文本转换为数字等）成为了基础模型的一部分。 使用模型进行预测时，将自动向输入数据应用在训练期间应用的相同特征化步骤。

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> 集成配置

集成模型默认启用，在 AutoML 运行中显示为最终的运行迭代次数。 目前支持 **VotingEnsemble** 和 **StackEnsemble**。 

投票采用使用加权平均值的软投票。 堆栈实现使用一个两层实现，其中的第一层具有与投票集成相同的模型，第二层模型用于从第一层中查找模型的最佳组合。 

如果使用 ONNX 模型，或启用了模型可解释性，则会禁用堆栈，仅使用投票。

可以通过使用 `enable_voting_ensemble` 和 `enable_stack_ensemble` 布尔参数来禁用集成训练。

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

若要更改默认集成行为，可以将多个默认参数作为 `kwargs` 在 `AutoMLConfig` 对象中提供。

> [!IMPORTANT]
>  以下参数不是 AutoMLConfig 类的显式参数。 

* `ensemble_download_models_timeout_sec`：在 VotingEnsemble 和 StackEnsemble 模型生成期间，会下载来自先前子运行的多个拟合模型。 如果遇到此错误 `AutoMLEnsembleException: Could not find any models for running ensembling`，则可能需要为要下载的模型提供更多时间。 默认值为 300 秒并行下载这些模型，且没有最大超时限制。 如果需要更多时间，请将此参数配置为大于 300 秒的值。 

  > [!NOTE]
  >  如果已超时且下载了模型，则融合会使用它下载的多个模型继续执行。 并不需要下载所有模型才能在超时内完成。

以下参数只应用于 StackEnsemble 模型： 

* `stack_meta_learner_type`：元学习器是针对单个异类模型的输出而训练出来的模型。 默认的元学习器是用于分类任务的 `LogisticRegression`（或为 `LogisticRegressionCV`，如果启用了交叉验证的话），以及用于回归/预测任务的 `ElasticNet`（或为 `ElasticNetCV`，如果启用了交叉验证的话）。 此参数可以是下列字符串之一：`LogisticRegression`、`LogisticRegressionCV`、`LightGBMClassifier`、`ElasticNet`、`ElasticNetCV`、`LightGBMRegressor` 或 `LinearRegression`。

* `stack_meta_learner_train_percentage`：指定为训练元学习器而保留的训练集的比例（选择训练的训练和验证类型时）。 默认值为 `0.2`。 

* `stack_meta_learner_kwargs`：要传递给元学习器的初始值设定项的可选参数。 这些参数和参数类型对来自相应模型构造函数的参数和参数类型进行镜像，然后再转发到模型构造函数。

下面的代码示例展示了如何在 `AutoMLConfig` 对象中指定自定义融合行为。

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

<a name="exit"></a> 

### <a name="exit-criteria"></a>退出条件

可以在 AutoMLConfig 中定义几个选项来结束试验。

|条件| description
|----|----
无条件 | 如果未定义任何退出参数，则试验将继续，直到主要指标不再需要执行其他步骤。
在一段时间后| 在设置中使用 `experiment_timeout_minutes` 来定义试验应继续运行多长时间（以分钟为单位）。 <br><br> 若要避免试验超时失败，最少需要 15 分钟，如果行数乘以列数的大小超过 10,000,000，则最少需要 60 分钟。
达到某个分数| 使用 `experiment_exit_score` 将在达到指定的主要指标分数后完成试验。

## <a name="run-experiment"></a>运行试验

> [!WARNING]
> 如果多次使用相同的配置设置和主要指标运行一个试验，你可能会发现每个试验最终指标分数与生成的模型之间的差异。 自动化 ML 使用的算法本身具有随机性，这可能会导致试验输出的模型与建议的模型最终指标分数（如准确度）之间出现细微差异。 你也可能会看到模型名称相同，但使用的超参数不同的结果。 

对于自动化 ML，可以创建 `Experiment` 对象，这是 `Workspace` 中用于运行实验的命名对象。

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

提交试验以运行和生成模型。 将 `AutoMLConfig` 传递给 `submit` 方法以生成模型。

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>首先在新的计算机上安装依赖项。  最长可能需要在 10 分钟后才会显示输出。
>将 `show_output` 设置为 `True` 可在控制台上显示输出。

### <a name="multiple-child-runs-on-clusters"></a>群集上的多个子运行

自动化 ML 试验子运行可以在已经运行另一个试验的群集上执行。 但是，计时取决于群集具有的节点数，以及这些节点是否可用于运行不同的试验。

群集中的每个节点充当单个可以完成一次训练运行的虚拟机 (VM)；对于自动化 ML，这意味着一个子运行。 如果所有节点都处于忙状态，则新的试验将排队。 但是，如果有空闲节点，新的试验将在可用节点/VM 中并行运行自动化 ML 子运行。

为了管理子运行及其执行时间，建议你为每个试验创建一个专用群集，使试验的 `max_concurrent_iterations` 数与群集中的节点数匹配。 这样就可以同时使用群集的所有节点以及所需数量的并发子运行/迭代。

在 `AutoMLConfig` 对象中配置 `max_concurrent_iterations`。 如果未进行配置，则默认情况下每个试验仅允许一个并发子运行/迭代。  

## <a name="explore-models-and-metrics"></a>探索模型和指标

自动化 ML 提供用于监视和评估训练结果的选项。 

* 如果在笔记本中操作，可以在小组件或内联单元中查看训练结果。 请参阅[监视自动化机器学习运行](#monitor)，了解更多详细信息。

* 有关为每次运行提供的性能图表和指标的定义和示例，请参阅[评估自动化机器学习试验结果](how-to-understand-automated-ml.md)。 

* 若要获取特征化摘要并了解哪些功能已添加到特定模型，请参阅[特征化透明度](how-to-configure-auto-features.md#featurization-transparency)。 

你可以使用以下自定义代码解决方案查看应用于特定自动化 ML 运行的超参数、缩放和规范化技术以及算法。 

下面定义了自定义方法 `print_model()`，该方法打印自动化 ML 训练管道的每个步骤的超参数。
 
```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        elif hasattr(step[1], '_base_learners') and hasattr(step[1], '_meta_learner'):
            print("\nMeta Learner")
            pprint(step[1]._meta_learner)
            print()
            for estimator in step[1]._base_learners:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()   
```

对于刚刚在同一个试验笔记本中提交和训练的本地或远程运行，可以使用 `get_output()` 方法传入最佳模型。 

```python
best_run, fitted_model = run.get_output()
print(best_run)
         
print_model(fitted_model)
```

以下输出表明：
 
* StandardScalerWrapper 技术用于在训练之前缩放和规范化数据。

* XGBoostClassifier 算法被识别为最佳运行，并显示了超参数值。 

```python
StandardScalerWrapper
{'class_name': 'StandardScaler',
 'copy': True,
 'module_name': 'sklearn.preprocessing.data',
 'with_mean': False,
 'with_std': False}

XGBoostClassifier
{'base_score': 0.5,
 'booster': 'gbtree',
 'colsample_bylevel': 1,
 'colsample_bynode': 1,
 'colsample_bytree': 0.6,
 'eta': 0.4,
 'gamma': 0,
 'learning_rate': 0.1,
 'max_delta_step': 0,
 'max_depth': 8,
 'max_leaves': 0,
 'min_child_weight': 1,
 'missing': nan,
 'n_estimators': 400,
 'n_jobs': 1,
 'nthread': None,
 'objective': 'multi:softprob',
 'random_state': 0,
 'reg_alpha': 0,
 'reg_lambda': 1.6666666666666667,
 'scale_pos_weight': 1,
 'seed': None,
 'silent': None,
 'subsample': 0.8,
 'tree_method': 'auto',
 'verbose': -10,
 'verbosity': 1}
```

如果想了解工作区中其他试验的现有运行，请获取要浏览的特定运行 ID，并将其传递到 `print_model()` 方法中。 

```python
from azureml.train.automl.run import AutoMLRun

ws = Workspace.from_config()
experiment = ws.experiments['automl-classification']
automl_run = AutoMLRun(experiment, run_id = 'AutoML_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')

automl_run
best_run, model_from_aml = automl_run.get_output()

print_model(model_from_aml)

```

## <a name="monitor-automated-machine-learning-runs"></a><a name="monitor"></a> 监视自动化机器学习运行

对于自动化机器学习运行，若要访问根据以前的运行生成的图表，请将 `<<experiment_name>>` 替换为相应的试验名称：

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![自动化机器学习的 Jupyter Notebook 小组件](./media/how-to-configure-auto-train/azure-machine-learning-auto-ml-widget.png)

## <a name="register-and-deploy-models"></a>注册和部署模型

可以注册模型，以便以后使用。 

若要从自动化 ML 运行注册模型，请使用 [`register_model()`](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) 方法。 

```Python

best_run = run.get_best_child()
print(fitted_model.steps)

model_name = best_run.properties['model_name']
description = 'AutoML forecast example'
tags = None

model = run.register_model(model_name = model_name, 
                                  description = description, 
                                  tags = tags)
```


有关如何创建部署配置以及将注册模型部署到 Web 服务的详细信息，请参阅[如何部署模型以及在何处部署模型](how-to-deploy-and-where.md?tabs=python#define-a-deployment-configuration)。

> [!TIP]
> 对于已注册的模型，可通过 [Azure 机器学习工作室](https://ml.azure.com)获取一键式部署。 请参阅[如何从工作室部署已注册的模型](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)。 
<a name="explain"></a>

## <a name="model-interpretability"></a>模型可解释性

模型可解释性让你可以了解模型进行预测的原因，以及基础特征重要性值。 SDK 包括各种包，这些包用于在训练和推理时间为本地和已部署的模型启用模型可解释性功能。

有关如何在自动化机器学习实验中专门启用可解释性特征的代码示例，请参阅[操作指南](how-to-machine-learning-interpretability-automl.md)。

有关如何在自动化机器学习之外的其他 SDK 区域中启用模型解释和特征重要性的基本信息，请参阅可解释性方面的[概念](how-to-machine-learning-interpretability.md)文章。

> [!NOTE]
> 解释客户端目前不支持 ForecastTCN 模型。 如果此模型作为最佳模型返回，则不会返回解释仪表板，并且不支持按需解释运行。

## <a name="next-steps"></a>后续步骤

+ 详细了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。

+ 详细了解[如何使用自动化机器学习训练回归模型](tutorial-auto-train-models.md)。

+ [对自动化 ML 试验进行故障排除](how-to-troubleshoot-auto-ml.md)。 
