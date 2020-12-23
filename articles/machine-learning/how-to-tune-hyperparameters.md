---
title: 优化模型的超参数
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习，有效地优化深度学习和机器学习模型的超参数。
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: a1a0b89dee5a6d57e6f317c5f6c8b61ffeda3c33
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029067"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>使用 Azure 机器学习优化模型的超参数


使用 Azure 机器学习 [HyperDrive 程序包](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py)自动执行高效的超参数优化。 了解如何完成通过 [Azure 机器学习 SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py) 优化超参数所需的步骤：

1. 定义参数搜索空间
1. 指定要优化的主要指标  
1. 为低性能运行指定提前终止策略
1. 分配资源
1. 使用所定义的配置启动试验
1. 将训练运行可视化
1. 为模型选择最佳配置

## <a name="what-are-hyperparameters"></a>什么是超参数？

**超参数** 是可调整的参数，可用于控制模型训练过程。 例如，使用神经网络时，你决定隐藏层的数目以及每个层中的节点数。 模型性能很大程度上取决于超参数。

 **超参数优化** 是确定能够获得最佳性能的超参数配置的过程。 通常，该过程在计算方面成本高昂，并且是手动的。

Azure 机器学习使你能够自动执行超参数优化，并且并行运行试验以有效地优化超参数。


## <a name="define-the-search-space"></a>定义搜索空间

通过探索针对每个超参数定义的值范围来优化超参数。

超参数可以是离散的，也可以是连续的，并具有由[参数表达式](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?preserve-view=true&view=azure-ml-py)描述的值分布。

### <a name="discrete-hyperparameters"></a>离散超参数 

离散超参数将指定为离散值中的一个 `choice`。 `choice` 可以是：

* 一个或多个逗号分隔值
* `range` 对象
* 任意 `list` 对象


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

在这种情况下，`batch_size` 采用 [16、32、64、128] 中的一个值，`number_of_hidden_layers` 采用 [1、2、3、4] 中的一个值。

也可以使用一个分布来指定以下高级离散超参数：

* `quniform(low, high, q)` - 返回类似于 round(uniform(low, high) / q) * q 的值
* `qloguniform(low, high, q)` - 返回类似于 round(exp(uniform(low, high)) / q) * q 的值
* `qnormal(mu, sigma, q)` - 返回类似于 round(normal(mu, sigma) / q) * q 的值
* `qlognormal(mu, sigma, q)` - 返回类似于 round(exp(normal(mu, sigma)) / q) * q 的值

### <a name="continuous-hyperparameters"></a>连续超参数 

将连续超参数指定为一个连续值范围内的分布：

* `uniform(low, high)` - 返回高低之间的均匀分布值
* `loguniform(low, high)` - 返回根据 exp(uniform(low, high)) 绘制的值，使返回值的对数均匀分布
* `normal(mu, sigma)` - 返回正态分布的实际值，包括平均值 μ 和标准方差 σ
* `lognormal(mu, sigma)` - 返回根据 exp(normal(mu, sigma)) 绘制的值，使返回值的对数呈正态分布

参数空间定义的示例：

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

此代码定义具有两个参数（`learning_rate` 和 `keep_probability`）的搜索空间。 `learning_rate` 包含平均值为 10、标准偏差为 3 的正态分布。 `keep_probability` 包含最小值为 0.05、最大值为 0.1 的均匀分布。

### <a name="sampling-the-hyperparameter-space"></a>超参数空间采样

指定参数采样方法来取代超参数空间。 Azure 机器学习支持以下方法：

* 随机采样
* 网格采样
* 贝叶斯采样

#### <a name="random-sampling"></a>随机采样

[随机采样](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?preserve-view=true&view=azure-ml-py)支持离散和连续超参数。 它支持提前终止低性能运行。 某些用户使用随机采样执行初始搜索，然后优化搜索空间来改善结果。

在随机采样中，超参数值是从定义的搜索空间中随机选择的。 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>网格采样

[网格采样](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?preserve-view=true&view=azure-ml-py)支持离散超参数。 如果你的预算允许在搜索空间中彻底进行搜索，请使用网格采样。 支持提前终止低性能运行。

对所有可能的值执行简单的网格搜索。 网格采样只能与 `choice` 超参数一起使用。 例如，以下空间有 6 个样本：

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>贝叶斯采样

[贝叶斯采样](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?preserve-view=true&view=azure-ml-py)基于贝叶斯优化算法。 它根据先前样本的表现情况来选取样本，以便新样本可以改善主要指标。

如果你有足够的预算来探索超参数空间，则建议使用贝叶斯采样。 为获得最佳结果，建议最大运行次数大于或等于正在优化的超参数数目的 20 倍。 

并发运行的数目会影响优化过程的有效性。 数目较小的并发运行可能带来更好的采样收敛，因为较小的并行度会增加可从先前完成的运行中获益的运行的数量。

Bayesian 采样仅支持搜索空间中的 `choice`、`uniform` 和 `quniform` 分布。

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> 指定主要指标

指定你希望让超参数优化对其进行优化的[主要指标](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?preserve-view=true&view=azure-ml-py)。 将根据此主要指标评估每个训练运行。 提前终止策略使用主要指标来识别低性能运行。

请为主要指标指定以下属性：

* `primary_metric_name`：主要指标的名称需要与训练脚本记录的指标的名称完全匹配。
* `primary_metric_goal`：可以是 `PrimaryMetricGoal.MAXIMIZE` 或 `PrimaryMetricGoal.MINIMIZE`，确定在评估运行时是要将主要指标最大化还是最小化。 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

此示例最大程度地提高了“准确度”。

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>记录用于超参数优化的指标

模型的训练脚本在模型训练期间 **必须** 记录主要指标，以便 HyperDrive 可以访问它以进行超参数优化。

使用以下示例代码片段在训练脚本中记录主要指标：

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

训练脚本会计算 `val_accuracy`，并将其记录为主要指标“准确度”。 每次记录指标时，超参数优化服务都会收到该指标。 你需要确定报告频率。

若要详细了解如何在模型训练运行中记录值，请参阅[在 Azure ML 训练运行中启用日志记录](how-to-track-experiments.md)。

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> 指定提前终止策略

使用提前终止策略自动终止性能不佳的运行。 提前终止提高了计算效率。

你可以配置以下参数来控制何时应用该策略：

* `evaluation_interval`：应用策略的频率。 每次训练脚本都会将主要指标计数记录为一个间隔。 如果 `evaluation_interval` 为 1，则训练脚本每次报告主要指标时，都会应用策略。 如果 `evaluation_interval` 为 2，则会每隔一次应用该策略。 如果未指定，则默认将 `evaluation_interval` 设置为 1。
* `delay_evaluation`：将第一个策略评估延迟指定的间隔数。 这是一个可选参数，可让所有配置运行最小间隔数，避免训练运行过早终止。 如果已指定，则每隔大于或等于 delay_evaluation 的 evaluation_interval 倍数应用策略。

Azure 机器学习支持以下提前终止策略：
* [老虎机策略](#bandit-policy)
* [中间值停止策略](#median-stopping-policy)
* [截断选择策略](#truncation-selection-policy)
* [无终止策略](#no-termination-policy-default)


### <a name="bandit-policy"></a>老虎机策略

[老虎机策略](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?preserve-view=true&view=azure-ml-py#&preserve-view=truedefinition)基于松驰因子/松驰数量和评估间隔。 与最佳性能运行相比，如果主要指标不在指定的松驰因子/松驰数量范围内，则老虎机会终止运行。

> [!NOTE]
> 贝叶斯采样不支持提前终止。 使用贝叶斯采样时，请设置 `early_termination_policy = None`。

指定以下配置参数：

* `slack_factor` 或 `slack_amount`：在最佳性能训练运行方面允许的松驰。 `slack_factor` 以比率的形式指定允许的松驰。 `slack_amount` 以绝对数量（而不是比率）的形式指定允许的松驰。

    例如，假设以间隔 10 应用老虎机策略。 另外，假设性能最佳的运行以间隔 10 报告了主要指标 0.8，目标是最大化主要指标。 如果策略指定的 `slack_factor` 为 0.2，则间隔为 10 时其最佳指标小于 0.66 (0.8/(1+`slack_factor`)) 的任何训练运行都会被终止。
* `evaluation_interval`：（可选）应用策略的频率
* `delay_evaluation`：（可选）将第一个策略评估延迟指定的间隔数


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

在此示例中，指标报告时将在每个间隔应用提前终止策略，从评估间隔 5 开始。 其最佳指标小于最佳性能运行的 (1/(1+0.1) 或 91% 的任何运行将被终止。

### <a name="median-stopping-policy"></a>中间值停止策略

[中值停止](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?preserve-view=true&view=azure-ml-py)是基于运行报告的主要指标的运行平均值的提前终止策略。 此策略计算所有训练运行的运行平均值，并终止其主要指标值比平均值的中间值更差的运行。

此策略采用以下配置参数：
* `evaluation_interval`：应用策略的频率（可选参数）。
* `delay_evaluation`：将第一个策略评估延迟指定的间隔数（可选参数）。


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

在此示例中，将在每个间隔应用提前终止策略，从评估间隔 5 开始。 如果某个运行的最佳主要指标比所有训练运行中间隔 1:5 的运行平均值的中间值更差，则会在间隔 5 处终止该运行。

### <a name="truncation-selection-policy"></a>截断选择策略

[截断选择](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?preserve-view=true&view=azure-ml-py)在每个评估间隔取消给定百分比的性能最差的运行。 使用主要指标对运行进行比较。 

此策略采用以下配置参数：

* `truncation_percentage`：要在每个评估间隔终止的性能最低的运行百分比。 一个介于 1 到 99 之间的整数值。
* `evaluation_interval`：（可选）应用策略的频率
* `delay_evaluation`：（可选）将第一个策略评估延迟指定的间隔数


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

在此示例中，将在每个间隔应用提前终止策略，从评估间隔 5 开始。 如果某个运行采用间隔 5 时的性能在采用间隔 5 的所有运行中处于 20% 的性能最低范围内，则会在间隔 5 处终止该运行。

### <a name="no-termination-policy-default"></a>无终止策略（默认设置）

如果未指定策略，则超参数优化服务将让所有训练运行一直执行到完成。

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>选择提前终止策略

* 对于既可以节省成本又不会终止有前景的作业的保守策略，请考虑使用 `evaluation_interval` 为 1 且 `delay_evaluation` 为 5 的中间值停止策略。 这属于保守的设置，可以提供大约 25%-35% 的节省，且不会造成主要指标损失（基于我们的评估数据）。
* 如果想节省更多成本，则可以使用老虎机策略或截断选择策略，因为前者的可用可宽延时间更短，后者的截断百分比更大。

## <a name="allocate-resources"></a>分配资源

通过指定最大训练运行数来控制资源预算。

* `max_total_runs`：最大训练运行数。 必须是介于 1 到 1000 之间的整数。
* `max_duration_minutes`：（可选）超参数优化试验的最长持续时间（分钟）。 此持续时间之后的运行会被取消。

>[!NOTE] 
>如果同时指定了 `max_total_runs` 和 `max_duration_minutes`，在达到其中的第一个阈值时，会终止超参数优化试验。

此外，指定在超参数优化搜索期间要并行运行的最大训练运行数。

* `max_concurrent_runs`：（可选）可以并发运行的最大运行数。 如果未指定此项，所有运行都将并行启动。 如果指定了此项，则必须是 1 和 100 之间的整数。

>[!NOTE] 
>并发运行数根据指定计算目标中的可用资源进行限制。 请确保计算目标能够为所需的并发性提供足够的可用资源。

```Python
max_total_runs=20,
max_concurrent_runs=4
```

此代码会将超参数优化试验配置为总共最多使用 20 个运行，每次运行四个配置。

## <a name="configure-experiment"></a>配置试验

若要[配置超参数优化](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?preserve-view=true&view=azure-ml-py)试验，请提供以下信息：
* 所定义的超参数搜索空间
* 你的提前终止策略
* 主要指标
* 资源分配设置
* ScriptRunConfig `src`

ScriptRunConfig 是将通过采样的超参数运行的训练脚本。 它定义每个作业的资源（单节点或多节点）和要使用的计算目标。

> [!NOTE]
>`src` 中指定的计算目标必须具有足够的资源来满足你的并发级别。 有关 ScriptRunConfig 的详细信息，请参阅[配置训练运行](how-to-set-up-training-targets.md)。

配置超参数优化试验：

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>提交试验

定义超参数优化配置后，请[提交试验](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-)：

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>热启动超参数优化试验（可选）

确定模型的最佳超参数值可能是一个迭代过程。 你可以重复使用从前五个运行中获得的知识来加速超参数优化。


对热启动的处理方式不同，具体取决于采样方法：
- **贝叶斯采样**：来自先前运行的试用将用作先验知识来选取新样本，并改善主要指标。
- **随机采样** 或 **网格采样**：提前终止使用从以前的运行获得的知识来确定性能不佳的运行。 

指定要从中开始热启动的父运行的列表。

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

如果取消了某个超参数优化试验，则可以从上一个检查点恢复训练运行。 但是，你的训练脚本必须处理检查点逻辑。

训练运行必须使用相同的超参数配置，并装载输出文件夹。 训练脚本必须接受 `resume-from` 参数，该参数包含要从中恢复训练运行的检查点或模型文件。 可以使用以下代码片段恢复单个训练运行：

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

可以配置超参数优化试验，使其从之前的试验中热启动，或者使用可选参数 `resume_from` 和 `resume_child_runs` 在配置中恢复单个训练运行：

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>将实验可视化

使用 [Notebook 小组件](/python/api/azureml-widgets/azureml.widgets.rundetails?preserve-view=true&view=azure-ml-py)来可视化你的训练运行的进度。 以下代码片段可在 Jupyter 笔记本中的一个位置可视化所有的超参数优化运行：

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

此代码会显示一个表格，其中详细描述了每个超参数配置的训练运行。

![超参数优化表](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

还可以将每个运行的性能可视化为训练进度。 

![超参数优化绘图](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

你可以使用“并行坐标绘图”来直观识别各个超参数的性能与值之间的关联。 

[![超参数优化并行坐标](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

还可将 Azure Web 门户中的所有超参数优化运行可视化。 若要详细了解如何在门户中查看试验，请参阅[如何跟踪试验](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal)。

## <a name="find-the-best-model"></a>找到最佳模型

完成所有超参数优化运行后，确定性能最佳的配置和超参数值：

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>示例 Notebook
请参阅以下文件夹中 train-hyperparameter-* 笔记本：
* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤
* [跟踪试验](how-to-track-experiments.md)
* [部署定型的模型](how-to-deploy-and-where.md)