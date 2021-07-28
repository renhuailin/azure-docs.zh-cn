---
title: 训练 scikit-learn 机器学习模型
titleSuffix: Azure Machine Learning
description: 了解如何通过 Azure 机器学习使用弹性云计算资源横向扩展 scikit-learn 训练作业。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 16f436c4b81aac60075be8cb3bf34a9706f739ab
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108746776"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>使用 Azure 机器学习大规模训练 scikit-learn 模型

本文介绍如何使用 Azure 机器学习运行 scikit-learn 训练脚本。

本文中的示例脚本用来对鸢尾花图像进行分类，以基于 scikit-learn 的 [iris 数据集](https://archive.ics.uci.edu/ml/datasets/iris)构建机器学习模型。

无论是从头开始训练机器学习 scikit-learn 模型，还是将现有模型引入云中，都可以通过 Azure 机器学习使用弹性云计算资源来横向扩展开源训练作业。 你可以通过 Azure 机器学习来构建、部署和监视生产级模型以及对其进行版本控制。

## <a name="prerequisites"></a>先决条件

在以下任一环境中运行此代码：
 - Azure 机器学习计算实例 - 无需下载或安装

    - 完成[快速入门：开始使用 Azure 机器学习](quickstart-create-resources.md)，以创建一个预加载了 SDK 和示例存储库的专用笔记本服务器。
    - 在笔记本服务器上的示例训练文件夹中，导航到以下目录，查找一个已完成且已展开的笔记本：how-to-use-azureml > ml-frameworks > scikit-learn > train-hyperparameter-tune-deploy-with-sklearn 文件夹。

 - 你自己的 Jupyter 笔记本服务器

    - [安装 Azure 机器学习 SDK](/python/api/overview/azure/ml/install) (>= 1.13.0)。
    - [创建工作区配置文件](how-to-configure-environment.md#workspace)。

## <a name="set-up-the-experiment"></a>设置试验

本部分通过加载所需的 Python 包、初始化工作区、定义训练环境以及准备训练脚本来设置训练实验。

### <a name="initialize-a-workspace"></a>初始化工作区

[Azure 机器学习工作区](concept-workspace.md)是服务的顶级资源。 它提供了一个集中的位置来处理创建的所有项目。 在 Python SDK 中，可以通过创建 [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) 对象来访问工作区项目。

根据在[先决条件部分](#prerequisites)中创建的 `config.json` 文件创建工作区对象。

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>准备脚本

[这里](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py)为你提供了在本教程中使用的训练脚本 train_iris.py。 实际上，你应该能够原样获取任何自定义训练脚本，并使用 Azure ML 运行它，而无需修改你的代码。

注意：
- 提供的训练脚本展示了如何在脚本中使用 `Run` 对象将一些指标记录到 Azure ML 运行中。
- 提供的训练脚本使用了来自 `iris = datasets.load_iris()` 函数的示例数据。  若要使用和访问自己的数据，请参阅[如何使用数据集进行训练](how-to-train-with-datasets.md)，以便使数据在训练期间可用。

### <a name="define-your-environment"></a>定义环境

若要定义封装训练脚本依赖项的 Azure ML [环境](concept-environments.md)，可以定义自定义环境或使用 Azure ML 特选环境。

#### <a name="use-a-curated-environment"></a>使用特选环境
或者，如果不想定义你自己的环境，也可使用 Azure ML 提供的预生成的特选环境。 有关详细信息，请参阅[此文](resource-curated-environments.md)。
若要使用特选环境，可以改为运行以下命令：

```python
from azureml.core import Environment

sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>创建自定义环境

你还可以创建自己的自定义环境。 在 YAML 文件中定义 conda 依赖项；在本例中，该文件名为 `conda_dependencies.yml`。

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

基于此 Conda 环境规范创建 Azure ML 环境。 此环境将在运行时打包到 Docker 容器中。
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

有关创建和使用环境的详细信息，请参阅[在 Azure 机器学习中创建和使用软件环境](how-to-use-environments.md)。

## <a name="configure-and-submit-your-training-run"></a>配置和提交训练运行

### <a name="create-a-scriptrunconfig"></a>创建 ScriptRunConfig
创建一个 ScriptRunConfig 对象，以指定训练作业的配置详细信息，包括训练脚本、要使用的环境，以及要在其上运行的计算目标。
如果在 `arguments` 参数中指定，训练脚本的任何自变量都将通过命令行传递。

下面的代码将配置一个 ScriptRunConfig 对象，用于提交在本地计算机上执行的作业。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

如果要改为在远程群集上运行作业，则可以为 ScriptRunConfig 的 `compute_target` 参数指定所需的计算目标。

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>提交运行
```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-TrainIRIS').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure 机器学习通过复制整个源目录来运行训练脚本。 如果你有不想上传的敏感数据，请使用 [.ignore 文件](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)或不将其包含在源目录中。 改为使用 Azure ML [数据集](how-to-train-with-datasets.md)来访问数据。

### <a name="what-happens-during-run-execution"></a>在运行执行过程中发生的情况
执行运行时，会经历以下阶段：

- **准备**：根据所定义的环境创建 docker 映像。 将映像上传到工作区的容器注册表，缓存以用于后续运行。 还会将日志流式传输到运行历史记录，可以查看日志以监视进度。 如果改为指定特选环境，则会使用支持该特选环境的缓存映像。

- **缩放**：如果 Batch AI 群集执行运行所需的节点多于当前可用节点，则群集将尝试纵向扩展。

- **正在运行**：将脚本文件夹中的所有脚本上传到计算目标，装载或复制数据存储，然后执行 `script`。 将 stdout 和 ./logs 文件夹中的输出流式传输到运行历史记录，即可将其用于监视运行。

- **后期处理**：将运行的 ./outputs 文件夹复制到运行历史记录。

## <a name="save-and-register-the-model"></a>保存并注册模型

训练模型后，可以将其保存并注册到工作区。 凭借模型注册，可以在工作区中存储模型并对其进行版本控制，从而简化[模型管理和部署](concept-model-management-and-deployment.md)。

将以下代码添加到训练脚本 train_iris.py 以保存模型。 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

使用以下代码将模型注册到工作区。 通过指定参数 `model_framework`、`model_framework_version` 和 `resource_configuration`，无代码模型部署将可供使用。 无代码模型部署允许你通过已注册模型直接将模型部署为 Web 服务，[`ResourceConfiguration`](/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration) 对象定义 Web 服务的计算资源。

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>部署

可采用与 Azure ML 中任何其他已注册模型完全相同的方式部署你刚才注册的模型。 部署指南包含有关模型注册的部分，但由于你已有一个已注册的模型，因而可以直接跳到[创建计算目标](how-to-deploy-and-where.md#choose-a-compute-target)进行部署。

### <a name="preview-no-code-model-deployment"></a>（预览版）无代码模型部署

还可以为 scikit-learn 使用无代码部署功能（预览版）来代替传统的部署路线。 所有内置 scikit-learn 模型类型都支持无代码模型部署。 通过使用 `model_framework`、`model_framework_version` 和 `resource_configuration` 参数注册你的模型（如上所示），可以简单地使用 [`deploy()`](/python/api/azureml-core/azureml.core.model%28class%29#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 静态函数来部署模型。

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

注意：这些依赖项包含在预建的 scikit-learn 推理容器中。

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

完整的[操作指南](how-to-deploy-and-where.md)更深入地介绍了 Azure 机器学习。


## <a name="next-steps"></a>后续步骤

在本文中，你训练并注册了一个 scikit-learn 模型，并了解了部署选项。 有关 Azure 机器学习的详细信息，请参阅以下其他文章。

* [在训练期间跟踪运行指标](how-to-log-view-metrics.md)
* [优化超参数](how-to-tune-hyperparameters.md)
