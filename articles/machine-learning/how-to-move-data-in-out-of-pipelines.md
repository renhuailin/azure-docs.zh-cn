---
title: 在 ML 管道中移动数据
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习管道如何引入数据，以及如何在管道步骤之间管理和移动数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.topic: how-to
ms.custom: contperf-fy20q4, devx-track-python, data4ml
ms.openlocfilehash: cfbb7d9d5144c0e91b9bf68e332300317dc2f76f
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425935"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>将数据移入 ML 管道和在 ML 管道之间移动数据的步骤 (Python)

本文提供了用于在 Azure 机器学习管道中的步骤之间导入、转换和移动数据的代码。 有关数据在 Azure 机器学习中的工作原理的概述，请参阅[访问 Azure 存储服务中的数据](how-to-access-data.md)。 有关 Azure 机器学习管道的优点和结构，请参阅[什么是 Azure 机器学习管道？](concept-ml-pipelines.md)

本文将向你介绍如何：

- 将 `Dataset` 对象用于预先存在的数据
- 在步骤中访问数据
- 将 `Dataset` 数据拆分为子集，例如训练子集和验证子集
- 创建 `OutputFileDatasetConfig` 对象来将数据传输到下一管道步骤
- 使用 `OutputFileDatasetConfig` 对象作为管道步骤的输入
- 基于 `OutputFileDatasetConfig` 创建你要持久保存的新 `Dataset` 对象

## <a name="prerequisites"></a>先决条件

需要：

- Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。

- [适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/intro)，或 [Azure 机器学习工作室](https://ml.azure.com/)的访问权限。

- Azure 机器学习工作区。
  
  [创建 Azure 机器学习工作区](how-to-manage-workspace.md)或通过 Python SDK 使用现有工作区。 导入 `Workspace` 和 `Datastore` 类，并使用函数 `from_config()` 从文件 `config.json` 中加载订阅信息。 默认情况下，此函数会查找当前目录中的 JSON 文件，但你也可以使用 `from_config(path="your/file/path")` 指定一个路径参数，使之指向该文件。

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- 某些预先存在的数据。 本文简要介绍了如何使用 [Azure blob 容器](../storage/blobs/storage-blobs-overview.md)。

- 可选：现有机器学习管道，例如[使用 Azure 机器学习 SDK 创建和运行机器学习管道](./how-to-create-machine-learning-pipelines.md)中所述的管道。

## <a name="use-dataset-objects-for-pre-existing-data"></a>将 `Dataset` 对象用于预先存在的数据 

将数据引入到管道的首选方法是使用 [Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29) 对象。 `Dataset` 对象表示在整个工作区中可用的持久性数据。

可以通过许多方法来创建和注册 `Dataset` 对象。 表格数据集用于一个或多个文件中可用的分隔数据。 文件数据集用于二进制数据（例如图像）或你要分析的数据。 创建 `Dataset` 对象的最简单编程方式是使用工作区存储或公共 URL 中的现有 blob：

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

datastore_path = [
    DataPath(datastore, 'animals/dog/1.jpg'),
    DataPath(datastore, 'animals/dog/2.jpg'),
    DataPath(datastore, 'animals/cat/*.jpg')
]
cats_dogs_dataset = Dataset.File.from_files(path=datastore_path)
```

若要详细了解如何使用各种选项以及基于各种源创建数据集、在 Azure 机器学习 UI 中注册数据集并查看它们、了解数据大小如何与计算容量交互，以及如何对数据集进行版本控制，请参阅[创建 Azure 机器学习数据集](how-to-create-register-datasets.md)。 

### <a name="pass-datasets-to-your-script"></a>将数据集传递给脚本

若要将数据集的路径传递给你的脚本，请使用 `Dataset` 对象的 `as_named_input()` 方法。 你可以将生成的 `DatasetConsumptionConfig` 对象作为参数传递给你的脚本，也可以通过使用管道脚本的 `inputs` 参数来使用 `Run.get_context().input_datasets[]` 检索数据集。

创建命名输入后，可以选择其访问模式：`as_mount()` 或 `as_download()`。 如果你的脚本处理数据集中的所有文件，而计算资源上的磁盘足以容纳数据集，则下载访问模式是更好的选择。 下载访问模式可避免在运行时流式传输数据所产生的开销。 如果你的脚本访问数据集的子集或它对于你的计算而言太大，请使用装载访问模式。 有关详细信息，请阅读[装载与下载](./how-to-train-with-datasets.md#mount-vs-download)

若要将数据集传递给管道步骤，请执行以下操作：

1. 使用 `TabularDataset.as_named_input()` 或 `FileDataset.as_named_input()`（末尾没有“s”）创建一个 `DatasetConsumptionConfig` 对象
1. 使用 `as_mount()` 或 `as_download()` 设置访问模式
1. 使用 `arguments` 或 `inputs` 参数将数据集传递给管道步骤

以下代码片段显示了在 `PythonScriptStep` 构造函数中组合这些步骤的常见模式： 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

> [!NOTE]
> 你需要将所有这些参数（即，`"train_data"`、`"train.py"`、`cluster` 和 `iris_dataset`）的值替换为自己的数据。 上面的代码片段仅演示了调用的形式，不是 Microsoft 示例的一部分。 

你还可以使用 `random_split()` 和 `take_sample()` 等方法来创建多个输入或减少传递给管道步骤的数据量：

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_input('train').as_download(), test.as_named_input('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>在脚本中访问数据集

管道步骤脚本的命名输入可以在 `Run` 对象中用作字典。 使用 `Run.get_context()` 检索活动 `Run` 对象，然后使用 `input_datasets` 检索命名输入的字典。 如果你使用 `arguments` 参数而非 `inputs` 参数传递 `DatasetConsumptionConfig` 对象，请使用 `ArgParser` 代码访问数据。 以下代码片段演示了这两种技术。

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()],
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

传递的值将是数据集文件的路径。

还可以直接访问已注册的 `Dataset`。 由于已注册的数据集是持久性的，并且在整个工作区中共享，因此你可以直接检索它们：

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

> [!NOTE]
> 前面的代码片段演示了调用的形式，不是 Microsoft 示例的一部分。 你必须将各种参数替换为自己项目中的值。

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>将 `OutputFileDatasetConfig` 用于中间数据

虽然 `Dataset` 对象仅代表持久性数据，但 [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig) 对象可用于从管道步骤输出的临时数据以及持久性输出数据。 `OutputFileDatasetConfig` 支持将数据写入到 Blob 存储、文件共享、adlsgen1 或 adlsgen2。 它同时支持装载模式和上传模式。 在装载模式下，当文件关闭时，写入到装载的目录中的文件将永久存储。 在上传模式下，在作业结束时，将上传写入到输出目录中的文件。 如果作业失败或被取消，将不会上传输出目录。

 `OutputFileDatasetConfig` 对象的默认行为是写入到工作区的默认数据存储。 可使用 `arguments` 参数将 `OutputFileDatasetConfig` 对象传递给 `PythonScriptStep`。

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

> [!NOTE]
> 向 `OutputFileDatasetConfig` 进行的并发写入会失败。 请勿尝试共用单个 `OutputFileDatasetConfig`。 不要在多处理情况下共享单个 `OutputFileDatasetConfig`，例如在使用[分布式训练](how-to-train-distributed-gpu.md)时。 

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>将 `OutputFileDatasetConfig` 用作训练步骤的输出

在管道的 `PythonScriptStep` 中，可以使用程序的参数检索可用输出路径。 如果此步骤是第一个步骤并将初始化输出数据，则必须在指定的路径创建目录。 然后，你可以写入要包含在 `OutputFileDatasetConfig` 中的任何文件。

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>读取 `OutputFileDatasetConfig` 作为非初始步骤的输入

当初始管道步骤将一些数据写入到 `OutputFileDatasetConfig` 路径并且这些数据成为该初始步骤的输出后，可将其用作后面步骤的输入。 

在以下代码中： 

* `step1_output_data` 指示在上传访问模式下将 PythonScriptStep `step1` 的输出写入到 ADLS Gen 2 数据存储 `my_adlsgen2` 中。 详细了解如何[设置角色权限](how-to-access-data.md#azure-data-lake-storage-generation-2)以将数据写回 ADLS Gen 2 数据存储。 

* 在 `step1` 完成并将输出写入到 `step1_output_data` 所指示的目标后，步骤 2 便可使用 `step1_output_data` 作为输入。 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=(datastore, "mypath/{run-id}/{output-name}")).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input()]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>注册 `OutputFileDatasetConfig` 对象供重复使用

如果你想要使你的 `OutputFileDatasetConfig` 的可用时间比试验持续时间更长，请将其注册到你的工作区，以便在不同的试验之间共享和重复使用。

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="delete-outputfiledatasetconfig-contents-when-no-longer-needed"></a>不再需要 `OutputFileDatasetConfig` 内容时将其删除

Azure 不会自动删除用 `OutputFileDatasetConfig` 编写的中间数据。 若要避免大量不需要的数据的存储费用，应执行以下操作之一：

* 当不再需要时，可在管道运行结束时以编程方式删除中间数据
* 将 blob 存储与中间数据的短期存储策略仪器结合使用（请参阅[通过 Azure Blob 存储访问层自动化来优化成本](../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal)） 
* 定期检查并删除不再需要的数据

有关详细信息，请参阅[计划和管理 Azure 机器学习的成本](concept-plan-manage-cost.md)。

## <a name="next-steps"></a>后续步骤

* [创建 Azure 机器学习数据集](how-to-create-register-datasets.md)
* [使用 Azure 机器学习 SDK 创建和运行机器学习管道](./how-to-create-machine-learning-pipelines.md)