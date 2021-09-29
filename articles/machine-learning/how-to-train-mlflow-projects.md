---
title: 使用 MLflow 项目进行训练
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习设置 MLflow 以记录 ML 模型的指标和项目
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 06/16/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 11358869e727948627e76c0f56c049b95c0abbb9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784774"
---
# <a name="train-ml-models-with-mlflow-projects-and-azure-machine-learning"></a>使用 MLflow 项目和 Azure 机器学习训练 ML 模型

本文介绍如何启用 MLflow 的跟踪 URI 和日志记录 API（统称为 [MLflow 跟踪](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)），以使用 [MLflow 项目](https://www.mlflow.org/docs/latest/projects.html)和 Azure 机器学习后端支持提交训练作业。 你可以使用 Azure 机器学习跟踪在本地提交作业，也可以像通过 [Azure 机器学习计算](./how-to-create-attach-compute-cluster.md)那样将运行迁移到云中。

[MLflow 项目](https://mlflow.org/docs/latest/projects.html)允许你组织和描述你的代码，使其他数据科学家（或自动化工具）可以运行它。 使用 Azure 机器学习的 MLflow 项目使你可以在工作区中跟踪和管理训练运行。

[MLflow](https://www.mlflow.org) 是一个开放源代码库，用于管理机器学习试验的生命周期。 MLFlow 跟踪是 MLflow 的一个组件，它可以记录和跟踪训练运行指标及模型项目，无论试验环境是在本地计算机上、远程计算目标上、虚拟机上，还是在 [Azure Databricks 群集](how-to-use-mlflow-azure-databricks.md)上。

[详细了解 MLflow 和 Azure 机器学习集成](how-to-use-mlflow.md)。

> [!TIP]
> 本文档中的信息主要面向需要监视模型训练过程的数据科学家与开发人员。 如果你是一名管理员并想要了解如何监视 Azure 机器学习的资源使用情况和事件（例如配额、已完成的训练运行或已完成的模型部署），请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="prerequisites"></a>先决条件

* 安装 `azureml-mlflow` 包。
    * 此包会自动引入 [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/install) 的 `azureml-core`，它为 MLflow 访问工作区提供了连接。
* [创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
    * 查看[使用工作区执行 MLflow 操作所需的访问权限](how-to-assign-roles.md#mlflow-operations)。

## <a name="train-mlflow-projects-on-local-compute"></a>在本地计算上训练 MLflow 项目

此示例演示如何使用 Azure 机器学习跟踪在本地提交 MLflow 项目。

安装 `azureml-mlflow` 包，以通过 Azure 机器学习对本地试验使用 MLflow 跟踪。 可以通过 Jupyter Notebook 或代码编辑器运行试验。

```shell
pip install azureml-mlflow
```

导入 `mlflow` 和 [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) 类以访问 MLflow 的跟踪 URI 并配置工作区。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

使用 `set_experiment()` 设置 MLflow 试验名称，并通过 `start_run()` 启动训练运行。 然后使用 `log_metric()` 激活 MLflow 记录 API 并开始记录训练运行指标。

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

创建后端配置对象以存储集成所需的信息，如计算目标以及要使用的托管环境的类型。

```python
backend_config = {"USE_CONDA": False}
```

将 `azureml-mlflow` 包作为 pip 依赖项添加到环境配置文件，以便在工作区中跟踪指标和关键项目。 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```

提交本地运行，并确保设置参数 `backend = "azureml" `。 利用此设置，你可以在本地提交运行，并在工作区中获得对自动输出跟踪、日志文件、快照和打印错误的附加支持。

在 [Azure 机器学习工作室](overview-what-is-machine-learning-studio.md)中查看运行和指标。

```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="train-mlflow-projects-with-remote-compute"></a>使用远程计算训练 MLflow 项目

此示例演示如何使用 Azure 机器学习跟踪在远程计算上提交 MLflow 项目。

安装 `azureml-mlflow` 包，以通过 Azure 机器学习对本地试验使用 MLflow 跟踪。 可以通过 Jupyter Notebook 或代码编辑器运行试验。

```shell
pip install azureml-mlflow
```

导入 `mlflow` 和 [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) 类以访问 MLflow 的跟踪 URI 并配置工作区。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

使用 `set_experiment()` 设置 MLflow 试验名称，并通过 `start_run()` 启动训练运行。 然后使用 `log_metric()` 激活 MLflow 记录 API 并开始记录训练运行指标。

```Python
experiment_name = 'train-mlflow-project-amlcompute'
mlflow.set_experiment(experiment_name)
```

创建后端配置对象以存储集成所需的信息，如计算目标以及要使用的托管环境的类型。

集成接受“COMPUTE”和“USE_CONDA”作为参数，其中“COMPUTE”设置为远程计算群集的名称，而“USE_CONDA”将通过环境配置文件为项目创建新环境。 如果对象中存在“COMPUTE”，则项目将自动提交到远程计算，并忽略“USE_CONDA”。 MLflow 接受字典对象或 JSON 文件。

```python
# dictionary
backend_config = {"COMPUTE": "cpu-cluster", "USE_CONDA": False}
```

将 `azureml-mlflow` 包作为 pip 依赖项添加到环境配置文件，以便在工作区中跟踪指标和关键项目。 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```

提交 mlflow 项目运行，并确保设置参数 `backend = "azureml" `。 利用此设置，可将运行提交到远程计算，并在工作区中获得对自动输出跟踪、日志文件、快照和打印错误的附加支持。

在 [Azure 机器学习工作室](overview-what-is-machine-learning-studio.md)中查看运行和指标。

```python
remote_mlflow_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    backend_config = backend_config, 
                                    )

```


## <a name="clean-up-resources"></a>清理资源

如果不打算使用工作区中记录的指标和项目，目前尚未提供单独删除它们的功能。 可以改为删除包含存储帐户和工作区的资源组，这样就不会产生任何费用：

1. 在 Azure 门户中，选择最左侧的“资源组”。

   ![在 Azure 门户中删除](./media/how-to-use-mlflow/delete-resources.png)

1. 从列表中选择已创建的资源组。

1. 选择“删除资源组”。

1. 输入资源组名称。 然后选择“删除”。

## <a name="example-notebooks"></a>示例笔记本

[将 MLflow 与 Azure ML 笔记本配合使用](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow)演示了本文中所述的概念，并在这些概念的基础上有所延伸。

  * [在本地计算上训练 MLflow 项目](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow/train-projects-local/train-projects-local.ipynb)
  * [在远程计算上训练 MLflow 项目](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow/train-projects-remote/train-projects-remote.ipynb)。

> [!NOTE]
> 可在 https://github.com/Azure/azureml-examples 找到使用 mlflow 的社区主导的示例存储库。

## <a name="next-steps"></a>后续步骤

* [使用 MLflow 部署模型](how-to-deploy-mlflow-models.md)。
* 监视生产模型中的[数据偏移](./how-to-enable-data-collection.md)。
* [使用 MLflow 跟踪 Azure Databricks 运行](how-to-use-mlflow-azure-databricks.md)。
* [管理模型](concept-model-management-and-deployment.md)。
