---
title: 使用 Azure 开放数据集创建数据集
titleSuffix: Azure Open Datasets
description: 了解如何根据 Azure 开放数据集创建 Azure 机器学习数据集。
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: ed43f73de5296a465d1878647a80135aa95c065a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724271"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>根据 Azure 开放数据集创建 Azure 机器学习数据集

在本文中，你将了解如何使用 [Azure 机器学习](../machine-learning/overview-what-is-azure-machine-learning.md)数据集和 [Azure 开放数据集](./index.yml)将精选的扩充数据引入本地或远程机器学习实验中。 

创建 [Azure 机器学习数据集](../machine-learning/how-to-create-register-datasets.md)时，将会创建对数据源位置的引用及其元数据的副本。 由于数据集是延迟计算的，并且数据仍保留在其现有位置，因此
* 不会产生额外的存储成本。
* 不会无意中更改原始数据源。 
* 会提高 ML 工作流性能速度。

若要了解在 Azure 机器学习总体数据访问工作流中的哪些位置使用数据集，请参阅[安全地访问数据](../machine-learning/concept-data.md#data-workflow)一文。

Azure 开放数据集是精选的公共数据集，可用于添加方案特定的功能，以扩充预测性解决方案并提高其准确度。 请参阅[开放数据集目录](https://azure.microsoft.com/services/open-datasets/catalog/)，了解可帮助训练机器学习模型的公共领域数据，例如：

* [天气](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [人口统计](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [holidays](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [公共安全](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* location

开放数据集位于 Microsoft Azure 上的云中，包含在 [Azure 机器学习 Python SDK](#create-datasets-with-the-sdk) 和 [Azure 机器学习工作室](#create-datasets-with-the-studio)中。


## <a name="prerequisites"></a>先决条件

要完成本文，你需要：

* Azure 订阅。 如果没有订阅，请在开始之前创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* 一个 [Azure 机器学习工作区](../machine-learning/how-to-manage-workspace.md)。

* [已安装适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/install)，其中包含 `azureml-datasets` 包。

    * 创建一个 [Azure 机器学习计算实例](../machine-learning/how-to-create-manage-compute-instance.md)，它是一个完全配置且托管的开发环境，其中包括集成的笔记本和已安装的 SDK。

    **或者**

    * 使用自己的 Python 环境，按照[这些说明](/python/api/overview/azure/ml/install)自行安装 SDK。

> [!NOTE]
> 某些数据集类依赖于 azureml-dataprep 包，此包仅兼容64位 Python。 对于 Linux 用户，只有以下分发版支持这些类：Red Hat Enterprise Linux（7、8）、Ubuntu（14.04、16.04、18.04）、Fedora（27、28）、Debian（8、9）和 CentOS (7)。

## <a name="create-datasets-with-the-sdk"></a>使用 SDK 创建数据集

若要在 Python SDK 中通过 Azure 开放数据集类创建 Azure 机器学习数据集，请确保已使用 `pip install azureml-opendatasets` 安装包。 每个离散数据集在 SDK 中均由其自己的类表示，并且某些类可用作 Azure 机器学习 [`TabularDataset` 和/或 `FileDataset`](../machine-learning/how-to-create-register-datasets.md#dataset-types)。 有关 `opendatasets` 类的完整列表，请查看[参考文档](/python/api/azureml-opendatasets/azureml.opendatasets)。

可检索 `TabularDataset` 或 `FileDataset` 形式的某些 `opendatasets` 类，这样就可直接操控和/或下载文件。 其他类只能在 Python SDK 中从 `Dataset` 类使用 `get_tabular_dataset()` 或 `get_file_dataset()` 函数来获取数据集。

以下代码显示 MNIST `opendatasets` 类可返回 `TabularDataset` 或 `FileDataset`。 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

在此示例中，糖尿病 `opendatasets` 类仅可用作 `TabularDataset`，因此使用 `get_tabular_dataset()`。

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>注册数据集

在工作区中注册 Azure 机器学习数据集，以便可与其他人共享并可在工作区中的各实验中重复使用。 注册从开放数据集创建的 Azure 机器学习数据集时，不会立即下载数据，之后请求时（例如在训练期间）将从集中存储位置访问数据。

若要在工作区中注册数据集，请使用 [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#register-workspace--name--description-none--tags-none--create-new-version-false-) 方法。 

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>使用工作室创建数据集

还可使用 [Azure 机器学习工作室](https://ml.azure.com)根据 Azure 开放数据集创建 Azure 机器学习数据集。该工作室是一个整合的 Web 界面，其中有机器学习工具用于为各种技能水平的数据科学实践者执行数据科学方案。

> [!Note]
> 通过 Azure 机器学习工作室创建的数据集会自动注册到工作区。

1. 在工作区中，选择“资产”下的“数据集”选项卡。  在“创建数据集”下拉菜单中，选择“从开放数据集”。 

    ![UI 中的开放数据集](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. 选择数据集对应的磁贴将其选中。 （可以选择使用搜索栏进行筛选。）选择“**下一步**”。

    ![选择数据集](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. 选择数据集的注册名称，并（可选）使用可用的筛选器筛选数据。 在本例中，对于“公共节假日”数据集，可将时间段设置为 1 年并将国家/地区代码设置为“仅限美国”来进行筛选。 有关字段描述和日期范围等数据详细信息，请参阅 [Azure 开放数据集目录](https://azure.microsoft.com/services/open-datasets/catalog)。 选择“创建” 。

    ![设置数据集参数并创建数据集](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    现在，该数据集已显示在工作区中的“数据集”下。 可以像使用创建的其他数据集一样来使用它。


## <a name="access-datasets-for-your-experiments"></a>访问供试验使用的数据集

在机器学习试验中使用数据集来训练 ML 模型。 [详细了解如何使用数据集进行训练](../machine-learning/how-to-train-with-datasets.md)。

## <a name="example-notebooks"></a>示例笔记本

有关开放数据集功能的示例和演示，请参阅这些[示例笔记本](samples.md)。

## <a name="next-steps"></a>后续步骤

* [训练你的第一个 ML 模型](../machine-learning/tutorial-1st-experiment-sdk-train.md)。

* [使用数据集进行训练](../machine-learning/how-to-train-with-datasets.md)。

* [创建 Azure 机器学习数据集](../machine-learning/how-to-create-register-datasets.md)。
