---
title: 为时序预测设置 AutoML
titleSuffix: Azure Machine Learning
description: 设置 Azure 机器学习自动化 ML，通过 Azure 机器学习 Python SDK 训练时序预测模型。
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.custom: contperf-fy21q1, automl, FY21Q4-aml-seo-hack
ms.date: 06/11/2021
ms.openlocfilehash: f8e036a77603c1e0833117a4562ad9dfd93c7ac9
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427184"
---
# <a name="set-up-automl-to-train-a-time-series-forecasting-model-with-python"></a>设置 AutoML，通过 Python 训练时序预测模型

本文将介绍如何使用 [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/) 中的 Azure 机器学习自动化 ML 为时序预测模型设置 AutoML 训练。

为此，需要： 

> [!div class="checklist"]
> * 准备用于时序建模的数据。
> * 在 [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 对象中配置特定的时序参数。
> * 使用时序数据运行预测。

要获得低代码体验，请参阅[教程：使用自动化机器学习预测需求](tutorial-automated-ml-forecast.md)，查看 [Azure 机器学习工作室](https://ml.azure.com/)中使用自动化 ML 的时序预测示例。

与经典的时序方法不同，在自动化 ML 中，将“透视”过去的时序值，使其成为回归器与其他预测器的附加维度。 此方法会在训练过程中，将多个上下文变量及其关系彼此整合。 影响预测的因素有很多，因此该方法将自身与真实的预测场景很好地协调起来。 例如，在预测销售额时，历史趋势、汇率和价格相互作用，共同推动着销售结果。 


## <a name="prerequisites"></a>先决条件

在本文中，你需要： 

* Azure 机器学习工作区。 若要创建工作区，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* 本文假设你对设置自动化机器学习试验有一定的了解。 遵循[教程](tutorial-auto-train-models.md)或[操作方法](how-to-configure-auto-train.md)，了解主要的自动化机器学习试验设计模式。

    [!INCLUDE [automl-sdk-version](../../includes/machine-learning-automl-sdk-version.md)]
## <a name="preparing-data"></a>准备数据

自动化 ML 中预测回归任务类型与回归任务类型之间最重要的区别是，前者包含数据中一个表示有效时序的特征。 常规时序具有明确定义的一致频率，并且在连续时间范围内的每个采样点上都有一个值。 

将以下快照看作 `sample.csv` 文件。
此数据集是有两个不同商店（A 和 B）的公司的每日销售数据。 

此外，还有一些功能适用于

 *  `week_of_year`：允许模型检测每周周期性。
* `day_datetime`：表示具有每日频率的干净时序。
* `sales_quantity`：用于运行预测的目标列。 

```output
day_datetime,store,sales_quantity,week_of_year
9/3/2018,A,2000,36
9/3/2018,B,600,36
9/4/2018,A,2300,36
9/4/2018,B,550,36
9/5/2018,A,2100,36
9/5/2018,B,650,36
9/6/2018,A,2400,36
9/6/2018,B,700,36
9/7/2018,A,2450,36
9/7/2018,B,650,36
```


将数据读取到 Pandas 数据帧中，然后使用 `to_datetime` 函数确保该时序的类型为 `datetime`。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

在这种情况下，数据已按时间字段 `day_datetime` 降序排序。 但在设置试验时，请确保所需的时间列按升序进行排序，从而生成有效的时序。 

以下代码 
* 假设数据包含 1,000 条记录，并在数据中进行确定性拆分以创建训练和测试数据集。 
* 将标签列标识为 `sales_quantity`。
* 从 `test_data` 中分离出标签字段，以形成 `test_target` 集。

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!IMPORTANT]
> 在训练用于预测未来值的模型时，请确保在针对预期范围运行预测时可使用训练中用到的所有特征。 <br> <br>例如，在创建需求预测时，包含当前股票价格的特征可能大幅提升训练准确度。 但是，如果你打算使用较长的时间范围进行预测，则可能没法准确预测与未来的时序点相对应的未来股价值，模型准确性也会受到影响。

<a name="config"></a>

## <a name="training-and-validation-data"></a>训练和验证数据

可以直接在 `AutoMLConfig` 对象中指定不同的训练集和验证集。   详细了解 [AutoMLConfig](#configure-experiment)。

对于时序预测，默认情况下仅使用滚动原点交叉验证 (ROCV) 进行验证。 将训练数据和验证数据一起传递，并在 `AutoMLConfig` 中使用 `n_cross_validations` 参数设置交叉验证折叠数。 ROCV 使用原始时间点将时序分成训练数据和验证数据。 在时间内滑动原点会生成交叉验证折叠。 此策略保留时序数据完整性并消除了数据泄露风险

![滚动原点交叉验证](./media/how-to-auto-train-forecast/ROCV.svg)

你还可以自带验证数据，详情请参阅[在 AutoML 中配置数据拆分和交叉验证](how-to-configure-cross-validation-data-splits.md#provide-validation-data)。


```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```

详细了解 AutoML 如何应用交叉验证来[防止过度拟合模型](concept-manage-ml-pitfalls.md#prevent-overfitting)。

## <a name="configure-experiment"></a>配置试验

[`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 对象定义自动化机器学习任务所需的设置和数据。 预测模型的配置与标准回归模型的设置相似，但存在专门针对时序数据的某些模型、配置选项和特征化步骤。 

### <a name="supported-models"></a>支持的模型
在模型创建和优化过程中，自动化机器学习会自动尝试各种模型和算法。 用户不需要指定算法。 对于预测试验，本机时序模型和深度学习模型都是推荐系统的一部分。 下表对此模型子集进行了汇总。 

>[!Tip]
> 传统回归模型也作为预测试验的推荐系统的一部分进行了测试。 有关模型的完整列表，请参阅[支持的模型表](how-to-configure-auto-train.md#supported-models)。 

模型| 说明 | 优点
----|----|---
Prophet（预览版）|Prophet 最适合用于受季节影响大且包含多个季节历史数据的时序。 若要利用此模型，请使用 `pip install fbprophet` 在本地安装它。 | 准确、快速、可靠地反应时序中的离群值、缺失数据和巨大变化。
Auto-ARIMA（预览版）|自动回归集成移动平均 (ARIMA) 在数据处于静态时性能最佳。 这意味着其统计属性（例如平均值和方差）在整个集中保持不变。 例如，如果你掷一枚硬币，那么无论是今天掷、明天掷还是明年掷，正面朝上的可能性都是 50%。| 适用于单变量系列，这是因为使用过去的值来预测未来的值。
ForecastTCN（预览版）| ForecastTCN 是一种神经网络模型，旨在处理最苛刻的预测任务。 它捕获数据中的非线性的本地和全局趋势以及时序之间的关系。|可利用数据中的复杂趋势并轻松扩展到最大型的数据集。

### <a name="configuration-settings"></a>配置设置

与回归问题类似，你要定义标准训练参数，例如任务类型、迭代次数、训练数据和交叉验证次数。 对于预测任务，还必须设置对试验有影响的其他参数。 

下表汇总了这些额外的参数。 有关语法设计模式，请参阅 [ForecastingParameter 类参考文档](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters)。

| 参数&nbsp;名称 | 说明 | 必须 |
|-------|-------|-------|
|`time_column_name`|用于指定输入数据中用于生成时序的日期时间列并推断其频率。|✓|
|`forecast_horizon`|定义要预测的未来的时段数。 范围以时序频率为单位。 单位基于预测器应预测出的训练数据的时间间隔，例如每月、每周。|✓|
|`enable_dnn`|[启用预测 DNN]()。||
|`time_series_id_column_names`|列名，用于唯一标识多行数据中具有相同时间戳的时序。 如果未定义时序标识符，则假定该数据集为一个时序。 要详细了解单个时序，请查看 [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。||
|`freq`| 时序数据集频率。 此参数表示事件预计发生的时间段，例如每日、每周、每年等。频率必须是 [pandas 偏移别名](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects)。 详细了解[频率].(#frequency-target-data-aggregation)||
|`target_lags`|要根据数据频率滞后目标值的行数。 此滞后表示为一个列表或整数。 默认情况下，在独立变量和依赖变量之间的关系不匹配或关联时，应使用滞后。 ||
|`feature_lags`| 当设置了 `target_lags` 并且 `feature_lags` 设置为 `auto` 时，要滞后的功能将由自动化 ML 自动确定。 启用功能滞后有助于提高准确性。 默认情况下会禁用功能滞后。 ||
|`target_rolling_window_size`|要用于生成预测值的 *n* 个历史时间段，该值小于或等于训练集大小。 如果省略，则 *n* 为完整训练集大小。 如果训练模型时只想考虑一定量的历史记录，请指定此参数。 详细了解[目标滚动窗口聚合](#target-rolling-window-aggregation)。||
|`short_series_handling_config`| 启用“短时序处理”，以避免在训练期间由于数据不足而失败。 在默认情况下，“短时序处理”设置为 `auto`。 详细了解[短时序处理](#short-series-handling)。||
|`target_aggregation_function`| 此函数将用于根据 `freq` 参数指定的频率聚合时序目标列。 必须设置 `freq` 参数才能使用 `target_aggregation_function`。 默认为 `None`；对于大多数场景，使用 `sum` 就足够了。<br> 详细了解[目标列聚合](#frequency--target-data-aggregation)。 


以下代码 
* 利用 [`ForecastingParameters`](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters) 类来为试验训练定义预测参数
* 将 `time_column_name` 设置为数据集中的 `day_datetime` 字段。 
* 将 `time_series_id_column_names` 参数定义为 `"store"`。 这可确保为数据创建 **两个单独的时序组**，一个用于商店 A，一个用于商店 B。
* 将 `forecast_horizon` 设置为 50 以针对整个测试集进行预测。 
* 使用 `target_rolling_window_size` 将预测窗口设置为 10 个时段
* 使用 `target_lags` 参数指定目标值滞后两个时段。 
* 将 `target_lags` 设置为建议的“auto”设置，这将自动为你检测此值。

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecasting_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                               forecast_horizon=50,
                                               time_series_id_column_names=["store"],
                                               freq='W',
                                               target_lags='auto',
                                               target_rolling_window_size=10)
                                              
```

然后，将这些 `forecasting_parameters` 传入到标准 `AutoMLConfig` 对象中，同时还传入 `forecasting` 任务类型、主要指标、退出条件和训练数据。 

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **forecasting_parameters)
```

通过自动化 ML 成功训练预测模型所需的数据量受在配置 `AutoMLConfig` 时指定的 `forecast_horizon`、`n_cross_validations` 和 `target_lags` 或 `target_rolling_window_size` 值影响。 

下面的公式计算构建时序功能所需的历史数据量。

所需的最小历史数据：(2x `forecast_horizon`) + #`n_cross_validations` + max(max(`target_lags`), `target_rolling_window_size`)

数据集中任何不满足指定的相关设置的所需历史数据量的序列，都将引发错误异常。 

### <a name="featurization-steps"></a>特征化步骤

在每一个自动化机器学习试验中，默认情况下都会将自动缩放和规范化技术应用于数据。 这些技术是特征化的类型，用于帮助对不同规模数据的特征敏感的某些算法。 在 [AutoML 中的特征化](how-to-configure-auto-features.md#automatic-featurization)中详细了解默认特征化步骤

但是，仅对 `forecasting` 任务类型执行以下步骤：

* 检测时序采样频率（例如每小时、每天、每周），并为缺失的时间点创建新记录以使序列连续。
* 通过向前填充估算目标列中缺少的值，通过列值中位数估算特征列中缺少的值
* 创建基于时序标识符的特征，以在不同序列中启用固定效果
* 创建基于时间的特征，以帮助学习季节性模式
* 将分类变量编码为数值数量

若要获取这些步骤所创建的功能的摘要，请参阅[特征化透明度](how-to-configure-auto-features.md#featurization-transparency)

> [!NOTE]
> 自动化机器学习特征化步骤（特征规范化、处理缺失数据，将文本转换为数字等）成为了基础模型的一部分。 使用模型进行预测时，将自动向输入数据应用在训练期间应用的相同特征化步骤。

#### <a name="customize-featurization"></a>自定义特征化

你还可以自定义特征化设置，以确保用于训练 ML 模型的数据和特征能够产生相关的预测。 

`forecasting` 任务支持的自定义项包括：

|自定义|定义|
|--|--|
|列用途更新|重写指定列的自动检测到的特征类型。|
|转换器参数更新 |更新指定转换器的参数。 目前支持 Imputer（fill_value 和中值）。|
|删除列 |指定要从特征化中删除的列。|

若要使用 SDK 来自定义特征化，请在 `AutoMLConfig` 对象中指定 `"featurization": FeaturizationConfig`。 详细了解[自定义特征化](how-to-configure-auto-features.md#customize-featurization)。

>[!NOTE]
> 从 SDK 版本1.19 开始，“删除列”功能已弃用。 在自动化 ML 试验中使用数据集之前，作为数据清理过程的一部分，请将数据集中的列删除。 

```python
featurization_config = FeaturizationConfig()

# `logQuantity` is a leaky feature, so we remove it.
featurization_config.drop_columns = ['logQuantitity']

# Force the CPWVOL5 feature to be of numeric type.
featurization_config.add_column_purpose('CPWVOL5', 'Numeric')

# Fill missing values in the target column, Quantity, with zeroes.
featurization_config.add_transformer_params('Imputer', ['Quantity'], {"strategy": "constant", "fill_value": 0})

# Fill mising values in the `INCOME` column with median value.
featurization_config.add_transformer_params('Imputer', ['INCOME'], {"strategy": "median"})
```

如果使用 Azure 机器学习工作室进行试验，请参阅[如何在工作室中自定义特征化](how-to-use-automated-ml-for-ml-models.md#customize-featurization)。

## <a name="optional-configurations"></a>可选配置

有其他可用于预测任务的可选配置，例如，启用深度学习和指定目标滚动窗口聚合。 

### <a name="frequency--target-data-aggregation"></a>频率和目标数据聚合

利用频率参数 `freq` 来避免由不规则数据（即不遵循固定频率的数据，例如，每小时或每天的数据）导致的故障。 

对于高度不规则的数据或不同的业务需求，用户可以选择设置所需的预测频率 `freq` 并指定 `target_aggregation_function`，以便聚合时序的目标列。 在 `AutoMLConfig` 对象中利用这两个设置有助于节省一些进行数据准备的时间。 

使用 `target_aggregation_function` 参数时，
* 目标列值将基于指定的运算进行聚合。 通常，`sum` 适用于大多数方案。

* 数据中的数值预测器列会按总和、平均值、最小值和最大值进行聚合。 因此，自动 ML 会生成以聚合函数名称为后缀的新列，并应用所选的聚合运算。 

* 对于分类预测器列，数据会按模式（窗口中最醒目的类别）进行聚合。

* 日期预测器列会按最小值、最大值和模式进行聚合。 

目标列值支持的聚合运算包括：

|函数 | description
|---|---
|`sum`| 求目标值的总和
|`mean`| 求目标值的平均值
|`min`| 求目标的最小值  
|`max`| 求目标的最大值  

### <a name="enable-deep-learning"></a>启用深度学习

> [!NOTE]
> DNN 对自动机器学习的预测支持处于预览状态，不支持本地运行或 Databricks 中启动的运行。

还可以通过深度神经网络 (DNN) 应用深度学习来提高模型的分数。 通过自动化 ML 的深度学习，可预测单变量和多变量时序数据。

深度学习模型具有三个固有功能：
1. 可以从任意输入到输出映射进行学习
1. 支持多个输入和输出
1. 可以从跨越较长序列的输入数据中自动提取模式。 

若要启用深度学习，请在 `AutoMLConfig` 对象中设置 `enable_dnn=True`。

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **forecasting_parameters)
```
> [!Warning]
> 为使用 SDK 创建的试验启用 DNN 时，系统会禁用[最佳模型说明](how-to-machine-learning-interpretability-automl.md)。

若要为在 Azure 机器学习工作室中创建的 AutoML 试验启用 DNN，请参阅[工作室操作指南中的任务类型设置](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)。

查看[饮料制造预测笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)，获取使用 DNN 的详细代码示例。

### <a name="target-rolling-window-aggregation"></a>目标滚动窗口聚合
通常，目标的最新值是预测程序能具有的最佳信息。  通过目标滚动窗口聚合，可将数据值的滚动聚合添加为特征。 通过生成和使用这些特征作为额外的上下文数据，帮助提高训练模型的准确性。

例如，假设你想要预测能源需求。 你可能希望添加一项滚动窗口（3 天）特征来解释供暖空间的热变化。 在此示例中，通过在 `AutoMLConfig` 构造函数中设置 `target_rolling_window_size= 3` 来创建此窗口。 

下表显示了在应用窗口聚合后发生的特征工程。 根据定义的设置针对滑动窗口 3 生成表示 **最小值、最大值** 和 **总和** 的列。 每一行有计算得出的一个新特征；如果时间戳为 2017 年 9 月 8 日凌晨 4:00，则使用 2017 年 9 月 8 日凌晨 1:00 至 3:00 的 **需求值** 计算最大值、最小值和总和值。 3 这个窗口将移位填充其余行的数据。

![目标滚动窗口](./media/how-to-auto-train-forecast/target-roll.svg)

请查看应用[目标滚动窗口聚合特征](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)的 Python 代码示例。

### <a name="short-series-handling"></a>短时序处理

如果没有足够的数据点来执行模型开发的训练和验证阶段，自动化 ML 就会将一个时序视为短时序。 数据点的数量因各个试验而异，并且依赖于 max_horizon、交叉验证拆分数以及模型回看的长度，该长度是构建时序功能所需的最长历史记录。 有关精确的计算，请参阅 [short_series_handling_configuration 参考文档](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters#short-series-handling-configuration)。

默认情况下，自动化 ML 通过在 `ForecastingParameters` 对象中使用 `short_series_handling_configuration` 参数来提供“短时序处理”。 

若要启用“短序列处理”，还必须定义 `freq` 参数。 为了定义每小时频率，我们将设置 `freq='H'`。 查看[此处](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects)的频率字符串选项。 若要更改默认行为 `short_series_handling_configuration = 'auto'`，请更新 `ForecastingParameter` 对象中的 `short_series_handling_configuration` 参数。  

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecast_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                            forecast_horizon=50,
                                            short_series_handling_configuration='auto',
                                            freq = 'H',
                                            target_lags='auto')
```
下表总结了可用于 `short_series_handling_config` 的设置。
 
|设置|说明
|---|---
|`auto`| 下面是“短时序处理”的默认行为 <li> 如果所有时序都短，则填充数据。 <br> <li> 如果并非所有时序都短，则删除短时序。 
|`pad`| 如果 `short_series_handling_config = pad`，则自动化 ML 会为找到的每个短时序添加随机值。 下面列出了列类型以及用于填充这些列的内容： <li>对象列，其中包含 NAN <li> 数值列，其中包含 0 <li> 布尔/逻辑列，其中包含 False <li> 目标列填充平均值为零且标准偏差为 1 的随机值。 
|`drop`| 如果 `short_series_handling_config = drop`，则自动化 ML 会删除短时序，并且该短时序不会用于训练或预测。 对这些时序的预测将会返回 NAN。
|`None`| 不会填充或删除任何时序

>[!WARNING]
>填充可能会影响生成的模型的准确性，因为我们引入人工数据只是为了使训练成功而不会发生失败。 <br> <br> 如果许多时序中都是短时序，你可能还会在可说明性结果中看到一些影响

## <a name="run-the-experiment"></a>运行试验 

准备好 `AutoMLConfig` 对象后，可以提交试验。 模型完成后，检索最佳的运行迭代。

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "Tutorial-automl-forecasting")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
``` 
 
## <a name="forecasting-with-best-model"></a>用最佳模型进行预测

使用最佳模型迭代来预测测试数据集的值。

[forecast_quantiles()](/python/api/azureml-train-automl-client/azureml.train.automl.model_proxy.modelproxy#forecast-quantiles-x-values--typing-any--y-values--typing-union-typing-any--nonetype----none--forecast-destination--typing-union-typing-any--nonetype----none--ignore-data-errors--bool---false-----azureml-data-abstract-dataset-abstractdataset) 函数允许指定预测的开始时间，这与通常用于分类和回归任务的 `predict()` 方法不同。 默认情况下，forecast_quantiles() 方法生成点预测或平均值/中值预测，而该预测周围没有不确定因素。 

在下例中，先将 `y_pred` 中的所有值替换为 `NaN`。 在本例中，预测原点位于训练数据的末尾。 但是，如果只将 `y_pred` 的后半部分替换为 `NaN`，则函数不会修改前半部分的数值，而会在后半部分预测 `NaN` 值。 函数将返回预测值和对齐的特征。

还可以在 `forecast_quantiles()` 函数中使用 `forecast_destination` 参数以预测到指定日期为止的值。

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_model.forecast_quantiles(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

通常客户想要了解分布的特定分位数处的预测。 例如，当为了控制库存（如杂货或云服务的虚拟机库存）而进行预测时。 在这种情况下，控制点通常类似“我们希望货品有库存，且 99% 的时间都有货”。 下面演示如何指定要针对预测查看的分位数，例如第 50 或第 95 百分位数。 如果不指定分位数（如上述代码示例中所示），则仅生成第 50 百分位数预测。 

```python
# specify which quantiles you would like 
fitted_model.quantiles = [0.05,0.5, 0.9]
fitted_model.forecast_quantiles(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```
 
计算 `actual_labels` 实际值与 `predict_labels` 中的预测值之间的均方根误差 (RMSE)。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```
 
 
现在确定了模型的整体准确性，最现实的下一步是使用模型来预测未知的未来值。 

提供与测试集 `test_data` 具有相同格式但具有未来日期时间的数据集，生成的预测集就是每个时序步骤的预测值。 假设数据集中最后的时序记录针对的是 2018/12/31。 若要预测次日的需求（或者小于或等于 `forecast_horizon` 的待预测时间段），请为每个商店创建 2019/01/01 的一条时序记录。

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

重复执行必要的步骤，将此未来数据加载到数据帧，然后运行 `best_run.forecast_quantiles(test_data)` 以预测未来值。

> [!NOTE]
> 启用了 `target_lags` 和/或 `target_rolling_window_size` 后，使用自动化 ML 进行预测时不支持样本中预测。

## <a name="forecasting-at-scale"></a>大规模预测 

在有些方案中，单个机器学习模型是不足的，需要提供多个机器学习模型。 例如，预测各个商店某个品牌的销售额，或者为个人用户定制体验。 为每个实例构建模型可以改善许多机器学习的相关问题。 

分组是时序预测中的一种概念，它允许组合时序，以便为每个组训练独立模型。 如果时序需要进行平滑处理、填充或组中包含可从其他实体的历史记录或趋势获益的实体，则此方法可能特别有用。 多模型和分层时序预测是由自动化机器学习提供支持的解决方案，它们针对这些大规模预测方案而提供。 

### <a name="many-models"></a>多模型

使用自动化机器学习的 Azure 机器学习多模型解决方案允许用户并行训练和管理数百万模型。 多模型解决方案加速器利用 [Azure 机器学习管道](concept-ml-pipelines.md)来训练模型。 具体而言，将使用[管道](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29)对象和 [ParalleRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep)，并需要通过 [ParallelRunConfig](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig) 设置的特定配置参数。 


下图显示了多模型解决方案的工作流。 

![多模型概念图](./media/how-to-auto-train-forecast/many-models.svg)

以下代码演示了用户设置和运行多模型所需的关键参数。

```python
from azureml.train.automl.runtime._many_models.many_models_parameters import ManyModelsTrainParameters

partition_column_names = ['Store', 'Brand']
automl_settings = {"task" : 'forecasting',
                   "primary_metric" : 'normalized_root_mean_squared_error',
                   "iteration_timeout_minutes" : 10, #This needs to be changed based on the dataset. Explore how long training is taking before setting this value 
                   "iterations" : 15,
                   "experiment_timeout_hours" : 1,
                   "label_column_name" : 'Quantity',
                   "n_cross_validations" : 3,
                   "time_column_name": 'WeekStarting',
                   "max_horizon" : 6,
                   "track_child_runs": False,
                   "pipeline_fetch_max_batch_size": 15,}

mm_paramters = ManyModelsTrainParameters(automl_settings=automl_settings, partition_column_names=partition_column_names)

```

### <a name="hierarchical-time-series-forecasting"></a>分层时序预测

在大多数应用程序中，客户需要在宏观级别和微观级别了解预测数据，预测不同地理位置的产品销售额，或了解公司不同组织对劳动力的需求。 训练机器学习模型以智能地预测层次结构数据，这是至关重要的。 

分层时序是一种结构，其中每个唯一序列都按维度（例如地理或产品类型）排列到层次结构中。 以下示例显示的数据具有构成层次结构的唯一属性。 层次结构通过这些方式定义：产品类型（例如耳机或平板电脑）、产品类别（将产品类型拆分为配件和设备）以及产品销售区域。 

![分层数据的原始数据表示例](./media/how-to-auto-train-forecast/hierarchy-data-table.svg)
 
为了进一步直观显示数据，层次结构的叶级别包含具有唯一属性值组合的所有时序。 层次结构中的每个更高级别所考虑的、用于定义时序的维度都会少一个，并且会将较低级别的每个子节点集聚合到一个父节点中。
 
![数据的层次结构视觉对象](./media/how-to-auto-train-forecast/data-tree.svg)

分层时序解决方案建立在多模型解决方案之上，它们共享类似的配置设置。

以下代码演示了用于设置分层时序预测运行的关键参数。 

```python

from azureml.train.automl.runtime._hts.hts_parameters import HTSTrainParameters

model_explainability = True

engineered_explanations = False # Define your hierarchy. Adjust the settings below based on your dataset.
hierarchy = ["state", "store_id", "product_category", "SKU"]
training_level = "SKU"# Set your forecast parameters. Adjust the settings below based on your dataset.
time_column_name = "date"
label_column_name = "quantity"
forecast_horizon = 7


automl_settings = {"task" : "forecasting",
                   "primary_metric" : "normalized_root_mean_squared_error",
                   "label_column_name": label_column_name,
                   "time_column_name": time_column_name,
                   "forecast_horizon": forecast_horizon,
                   "hierarchy_column_names": hierarchy,
                   "hierarchy_training_level": training_level,
                   "track_child_runs": False,
                   "pipeline_fetch_max_batch_size": 15,
                   "model_explainability": model_explainability,# The following settings are specific to this sample and should be adjusted according to your own needs.
                   "iteration_timeout_minutes" : 10,
                   "iterations" : 10,
                   "n_cross_validations": 2}

hts_parameters = HTSTrainParameters(
    automl_settings=automl_settings,
    hierarchy_column_names=hierarchy,
    training_level=training_level,
    enable_engineered_explanations=engineered_explanations
)
```

## <a name="example-notebooks"></a>示例笔记本

请参阅[预测示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)，了解高级预测配置的详细代码示例，其中包括：

* [假日检测和特征化](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [滚动原点交叉验证](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [可配置滞后](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [滚动窗口聚合特征](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="next-steps"></a>后续步骤

* 详细了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。
* 了解[可解释性：自动化机器学习中的模型说明（预览版）](how-to-machine-learning-interpretability-automl.md)。 
* 按照[教程：训练回归模型](tutorial-auto-train-models.md)获取端到端示例，使用自动化机器学习创建实验。
