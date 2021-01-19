---
title: 自动机器学习中的数据拆分和交叉验证
titleSuffix: Azure Machine Learning
description: 了解如何为自动机器学习试验配置数据集拆分和交叉验证
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 06/16/2020
ms.openlocfilehash: 8e749e5f6ea6bcf76a1b4f143bce03ceb41cbb07
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573286"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>在自动化机器学习中配置数据拆分和交叉验证

在本文中，你将了解用于配置培训/验证数据拆分和交叉验证的不同选项，这些选项用于配置自动机器学习、自动 ML、试验。

在 Azure 机器学习中，当你使用自动 ML 来构建多个 ML 模型时，每个子运行都需要通过计算该模型的质量指标来验证相关的模型，例如准确性或 AUC 加权。 这些指标的计算方法是将每个模型所做的预测与验证数据中过去观察到的实际标签进行比较。 [了解有关如何根据验证类型计算指标的详细信息](#metric-calculation-for-cross-validation-in-machine-learning)。 

自动 ML 试验会自动执行模型验证。 下面的各个部分介绍了如何使用 [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py) 进一步自定义验证设置。 

对于低代码或无代码体验，请参阅[在 Azure 机器学习工作室中创建自动化机器学习试验](how-to-use-automated-ml-for-ml-models.md)。 

> [!NOTE]
> 工作室当前支持训练/验证数据拆分和交叉验证选项，但它不支持为验证集指定单独的数据文件。 

## <a name="prerequisites"></a>先决条件

在本文中，你需要：

* Azure 机器学习工作区。 若要创建工作区，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* 熟悉如何使用 Azure 机器学习 SDK 设置自动化机器学习试验。 按照[教程](tutorial-auto-train-models.md)或[操作方法](how-to-configure-auto-train.md)操作，了解基本的自动化机器学习试验设计模式。

* 了解定型/验证数据拆分和交叉验证作为机器学习的概念。 有关概要说明，请参阅：

    * [关于机器学习中的训练、验证和测试集](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [了解机器学习中的交叉验证](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) 

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>机器学习中的默认数据拆分和交叉验证

使用 [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py) 对象定义试验和训练设置。 请注意，在下面的代码片段中，只定义了必需的参数，也就是说，**未** 包括 `n_cross_validation` 或 `validation_ data` 的参数。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

如果未显式指定 `validation_data` 或 `n_cross_validation` 参数，则 AutoML 将根据提供的单个数据集 `training_data` 中的行数来应用默认技术：

|训练数据大小| 验证技术 |
|---|-----|
|**大于 20,000 行**| 将应用训练/验证数据拆分。 默认行为是将初始训练数据集的 10% 用作验证集。 然后，该验证集将用于指标计算。
|**小于 20,000 行**| 将应用交叉验证方法。 默认折数取决于行数。 <br> **如果数据集小于 1,000 行**，则使用 10 折。 <br> **如果行数在 1,000 到 20,000 之间**，则使用 3 折。

## <a name="provide-validation-data"></a>提供验证数据

在这种情况下，你可以从单个数据文件开始，将其拆分为训练集和验证集，也可以为验证集提供单独的数据文件。 无论采用哪种方式，`AutoMLConfig` 对象中的 `validation_data` 参数都将分配要用作验证集的数据。 此参数仅接受 [Azure 机器学习数据集](how-to-create-register-datasets.md) 或 pandas 数据帧格式的数据集。   

下面的代码示例显式定义了要将 `dataset` 中所提供数据的哪部分用于训练和验证。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>提供验证集大小

在这种情况下，只为试验提供单个数据集。 也就是说，**未** 指定 `validation_data` 参数，提供的数据集将分配给 `training_data` 参数。  在 `AutoMLConfig` 对象中，你可以设置 `validation_size` 参数来保存一部分用于验证的训练数据。 这意味着，验证集将由 AutoML 从提供的初始 `training_data` 中拆分出来。 此值的范围应为 0.0 到 1.0（不含，例如，0.2 表示保留 20% 的数据用作验证数据）。

请参阅以下代码示例：

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="set-the-number-of-cross-validations"></a>设置交叉验证次数

若要执行交叉验证，请包括 `n_cross_validations` 参数并将其设置为某个值。 此参数基于相同的折数设置要执行的交叉验证次数。

在下面的代码中，定义了要将 5 折用于交叉验证。 因此有五个不同的训练，每个训练使用 4/5 的数据，每个验证使用 1/5 的数据，且每次都使用不同的维持数据折。

因此，使用五个验证度量值计算度量值。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>指定自定义交叉验证数据折数

你还可以提供自己的交叉验证 (CV) 数据折数。 这被视为更高级的方案，因为你需要指定要将哪些列拆分出来用于验证。  请在训练数据中包括自定义 CV 拆分列，并通过在 `cv_split_column_names` 参数中填充列名来指定列。 每个列表示一个交叉验证拆分，并用整数值1或 0-表示，其中1表示行应用于定型，0表示行应用于验证。

下面的代码片段包含具有两个 CV 拆分列（“cv1”和“cv2”）的银行营销数据。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> 若要将 `cv_split_column_names` 与 `training_data` 和 `label_column_name` 一起使用，请升级到 Azure 机器学习 Python SDK 1.6.0 或更高版本。 对于以前的 SDK 版本，请参阅 `cv_splits_indices` 使用方面的内容，但请注意，它仅可与 `X` 和 `y` 数据集输入一起使用。 


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>机器学习中交叉验证的指标计算

当使用 k 折叠或 Monte Carlo 交叉验证时，将对每个验证折叠计算度量值，然后对其进行聚合。 聚合运算是针对标量度量值的平均值和图表的总和。 交叉验证期间计算得出的度量值基于所有折叠，因此来自定型集的所有示例。 [在自动机器学习中了解有关指标的详细信息](how-to-understand-automated-ml.md)。

当使用自定义验证集或自动选择的验证集时，只会从该验证集计算模型评估指标，而不是从定型数据中计算。

## <a name="next-steps"></a>后续步骤

* [防止不均衡数据和过度拟合](concept-manage-ml-pitfalls.md)。
* [教程：使用自动化机器学习预测出租车费 -“拆分数据”部分](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets)。
* 如何[自动训练时序预测模型](how-to-auto-train-forecast.md)。
