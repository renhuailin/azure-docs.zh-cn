---
title: 数据集版本控制
titleSuffix: Azure Machine Learning
description: 了解如何对机器学习数据集进行版本控制以及如何将版本控制用于机器学习管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.author: cgronlun
author: cjgronlund
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: how-to
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 5711d58d8772faf724c8ea82e72356e37af7f12c
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428020"
---
# <a name="version-and-track-azure-machine-learning-datasets"></a>对 Azure 机器学习数据集进行版本控制和跟踪

在本文中，你将了解如何对 Azure 机器学习数据集进行版本控制和跟踪，以实现可再现性。 数据集版本控制是为数据状态设置书签的一种方法，方便为将来的试验应用数据集的特定版本。

典型的版本控制方案：

* 当新数据可用于重新训练时
* 应用不同的数据准备或特征工程方法时

## <a name="prerequisites"></a>必备条件

对于本教程的内容，你需要：

- [安装了适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/install)。 此 SDK 包括 [azureml-datasets](/python/api/azureml-core/azureml.core.dataset) 包。
    
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

默认情况下，`Dataset` 类中的 [get_by_name()](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) 方法返回已注册到工作区的数据集的最新版本。 

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

## <a name="version-an-ml-pipeline-output-dataset"></a>对 ML 管道输出数据集进行版本控制

可以使用数据集作为每个 [ML 管道](concept-ml-pipelines.md)步骤的输入和输出。 重新运行管道时，每个管道步骤的输出将注册为一个新的数据集版本。

ML 管道每次重新运行时都会将每个步骤的输出填充到一个新文件夹中。 此行为使得已进行版本控制的输出数据集可重现。 详细了解 [管道中的数据集](./how-to-create-machine-learning-pipelines.md#steps)。

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

Azure 机器学习在整个试验过程中跟踪数据作为输入和输出数据集。  

以下是将数据作为输入数据集进行跟踪的场景。 

* 提交试验运行时，通过 `ScriptRunConfig` 对象的 `inputs` 或 `arguments` 参数传递 `DatasetConsumptionConfig` 对象。 

* 在脚本中调用 get_by_name() 或 get_by_id() 之类的方法时。 对于此场景，将数据集注册到工作区时分配给它的名称就是显示的名称。 

以下是将数据作为输出数据集进行跟踪的场景。  

* 提交试验运行时，通过 `outputs` 或 `arguments` 参数传递 `OutputFileDatasetConfig` 对象。 `OutputFileDatasetConfig` 对象也可用于在管道步骤之间保留数据。 请参阅[在 ML 管道步骤之间移动数据。](how-to-move-data-in-out-of-pipelines.md)
  
* 在脚本中注册数据集。 对于此场景，将数据集注册到工作区时分配给它的名称就是显示的名称。 在以下示例中，`training_ds` 是将显示的名称。

    ```Python
   training_ds = unregistered_ds.register(workspace = workspace,
                                     name = 'training_ds',
                                     description = 'training data'
                                     )
    ```

* 使用脚本中未注册的数据集提交子运行。 这会生成一个匿名保存数据集。

### <a name="trace-datasets-in-experiment-runs"></a>在试验运行中跟踪数据集

对于每个机器学习试验，可以通过试验 `Run` 对象轻松跟踪用作输入的数据集。

下面的代码使用 [`get_details()`](/python/api/azureml-core/azureml.core.run.run#get-details--) 方法跟踪试验运行时使用哪些输入数据集：

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

还可以使用 [Azure 机器学习工作室]()从试验中查找 `input_datasets`。 

下图展示了在 Azure 机器学习工作室中从何处查找试验的输入数据集。 对于此示例，请转到“试验”窗格，并打开试验 `keras-mnist` 的特定运行的“属性”选项卡。

![输入数据集](./media/how-to-version-track-datasets/input-datasets.png)

使用以下代码向数据集注册模型：

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

注册后，可以使用 Python 或转到[工作室](https://ml.azure.com/)查看已注册到数据集中的模型列表。

以下视图来自“资产”下的“数据集”窗格。 选择数据集，然后选择“模型”选项卡以获取向数据集注册的模型的列表。 

![输入数据集模型](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>后续步骤

* [使用数据集进行训练](how-to-train-with-datasets.md)
* [更多示例数据集笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)