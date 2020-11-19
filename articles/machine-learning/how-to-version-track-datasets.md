---
title: 数据集版本控制
titleSuffix: Azure Machine Learning
description: 了解如何以最佳方式对数据集进行版本控制，以及如何使用机器学习管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 8c9beaca0fb3ee7881559ffcc955f171bc2ddd7b
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886441"
---
# <a name="version-and-track-datasets-in-experiments"></a>在试验中对数据集进行版本控制和跟踪

在本文中，你将了解如何对 Azure 机器学习数据集进行版本控制和跟踪，以实现可再现性。 数据集版本控制是为数据状态设置书签的一种方法，方便为将来的试验应用数据集的特定版本。

典型的版本控制方案：

* 当新数据可用于重新训练时
* 应用不同的数据准备或特征工程方法时

## <a name="prerequisites"></a>必备条件

对于本教程的内容，你需要：

- [安装了适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。 此 SDK 包括 [azureml-datasets](/python/api/azureml-core/azureml.core.dataset?preserve-view=true&view=azure-ml-py) 包。
    
- [Azure 机器学习工作区](concept-workspace.md)。 通过运行以下代码检索现有的工作区，或者[创建新的工作区](how-to-manage-workspace.md)。

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Azure 机器学习数据集](how-to-create-register-datasets.md)。

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>注册和检索数据集版本

通过注册数据集，可以对数据集进行版本控制，在试验之间以及与同事重复使用和共享数据集。 你可以使用相同的名称注册多个数据集，并按名称和版本号检索特定版本。

### <a name="register-a-dataset-version"></a>注册数据集版本

下面的代码通过将 `create_new_version` 参数设置为 `True` 来注册 `titanic_ds` 数据集的新版本。 如果没有向工作区注册现有 `titanic_ds` 数据集，则代码会创建一个名为 `titanic_ds` 的新数据集，并将其版本设置为 1。

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>按名称检索数据集

默认情况下，`Dataset` 类中的 [get_by_name()](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) 方法返回已注册到工作区的数据集的最新版本。 

下面的代码获取 `titanic_ds` 数据集的版本 1。

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>版本控制最佳做法

创建数据集版本时，  不会使用工作区创建额外的数据副本。 由于数据集是对存储服务中数据的引用，因此，你有单个由存储服务管理的事实来源。

>[!IMPORTANT]
> 如果数据集引用的数据被覆盖或删除，则调用特定版本的数据集不会  还原更改。

从数据集加载数据时，始终会加载数据集引用的当前数据内容。 如果要确保每个数据集版本都是可再现的，建议你不要修改数据集版本引用的数据内容。 当新数据进入时，将新数据文件保存到单独的数据文件夹中，然后创建新的数据集版本以包含该新文件夹中的数据。

下图和示例代码展示了对数据文件夹进行构造的建议方式，以及创建引用那些文件夹的数据集版本的建议方式：

![文件夹结构](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-an-ml-pipeline-output-dataset"></a>版本 ML 管道输出数据集

您可以使用数据集作为每个 [ML 管道](concept-ml-pipelines.md) 步骤的输入和输出。 重新运行管道时，每个管道步骤的输出将注册为一个新的数据集版本。

每次管道重新运行时，ML 管道将每个步骤的输出填充到新文件夹中。 此行为允许可重现已进行版本管理的输出数据集。 详细了解 [管道中的数据集](how-to-create-your-first-pipeline.md#steps)。

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-data-in-your-experiments"></a>跟踪试验中的数据

Azure 机器学习在实验中跟踪数据的输入和输出数据集。  

以下情况下，你的数据将作为 **输入数据集** 进行跟踪。 

* 在 `DatasetConsumptionConfig` `inputs` `arguments` `ScriptRunConfig` 提交实验运行时通过对象的或参数作为对象。 

* 如这样的方法，则在脚本中调用 get_by_name ( # A1 或 get_by_id ( # A3。 对于此方案，在将数据集注册到工作区时，分配给该数据集的名称是显示的名称。 

以下情况下，你的数据将作为 **输出数据集** 进行跟踪。  

* 在 `OutputFileDatasetConfig` `outputs` `arguments` 提交实验性运行时通过或参数传递对象。 `OutputFileDatasetConfig` 对象也可用于在管道步骤之间保留数据。 请参阅 [在 ML 管道间移动数据步骤。](how-to-move-data-in-out-of-pipelines.md)
    > [!TIP]
    > [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) 是包含 [实验](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=truestable-vs-experimental) 预览功能的公共预览版，随时可能会更改。

* 在脚本中注册数据集。 对于此方案，在将数据集注册到工作区时，分配给该数据集的名称是显示的名称。 在下面的示例中， `training_ds` 是将显示的名称。

    ```Python
   training_ds = unregistered_ds.register(workspace = workspace,
                                     name = 'training_ds',
                                     description = 'training data'
                                     )
    ```

* 使用脚本中未注册的数据集提交子运行。 这会导致匿名保存的数据集。

### <a name="trace-datasets-in-experiment-runs"></a>试验运行中的跟踪数据集

对于每个机器学习试验，你都可以轻松地使用试验对象跟踪用作输入的数据集 `Run` 。

以下代码使用 [`get_details()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-details--) 方法来跟踪试验运行时所使用的输入数据集：

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

还可以 `input_datasets` 通过使用 [Azure 机器学习 studio]()查找试验。 

下图显示了在何处查找 Azure 机器学习 studio 中实验的输入数据集。 对于此示例，请转到“试验”窗格，并打开试验 `keras-mnist` 的特定运行的“属性”选项卡。

![输入数据集](./media/how-to-version-track-datasets/input-datasets.png)

使用以下代码向数据集注册模型：

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

注册后，可以使用 Python 查看向数据集注册的模型的列表，也可以使用该程序[集。](https://ml.azure.com/)

以下视图来自“资产”下的“数据集”窗格。 选择数据集，然后选择“模型”选项卡以获取向数据集注册的模型的列表。 

![输入数据集模型](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>后续步骤

* [使用数据集进行训练](how-to-train-with-datasets.md)
* [更多示例数据集笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)