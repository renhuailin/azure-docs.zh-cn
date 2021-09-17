---
title: 训练和部署 TensorFlow 模型
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习如何让你能够使用弹性云计算资源横向扩展 TensorFlow 训练作业。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: how-to
ms.openlocfilehash: 8e53d67dacff8337d4a6832fc5febe3b83ec6126
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606824"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>使用 Azure 机器学习大规模训练 TensorFlow 模型

本文介绍如何使用 Azure 机器学习大规模运行 [TensorFlow](https://www.tensorflow.org/overview) 训练脚本。

此示例使用深度神经网络 (DNN) 训练并注册 TensorFlow 模型来对手写数字进行分类。

无论是从头开始开发 TensorFlow 模型，还是将[现有模型](how-to-deploy-and-where.md)引入云中，都可以使用 Azure 机器学习来横向扩展开源训练作业，以便构建、部署和监视生产级模型以及对其进行版本控制。

## <a name="prerequisites"></a>先决条件

在以下任一环境中运行此代码：

 - Azure 机器学习计算实例 - 无需下载或安装

     - 完成[快速入门：开始使用 Azure 机器学习](quickstart-create-resources.md)，以创建一个预加载了 SDK 和示例存储库的专用笔记本服务器。
    - 在笔记本服务器上的示例深度学习文件夹中，导航到以下目录，查找已完成且已展开的笔记本：**how-to-use-azureml > ml-frameworks > tensorflow > train-hyperparameter-tune-deploy-with-tensorflow** 文件夹。 
 
 - 你自己的 Jupyter 笔记本服务器

    - [安装 Azure 机器学习 SDK](/python/api/overview/azure/ml/install) (>= 1.15.0)。
    - [创建工作区配置文件](how-to-configure-environment.md#workspace)。
    - [下载示例脚本文件](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` 和 `utils.py`
     
    此外，还可以在 GitHub 示例页上找到本指南的完整 [Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb)。 该笔记本包含扩展部分，其中涵盖智能超参数优化、模型部署和笔记本小组件。

## <a name="set-up-the-experiment"></a>设置试验

本部分通过加载所需的 Python 包、初始化工作区、创建计算目标和定义训练环境来设置训练实验。

### <a name="import-packages"></a>导入程序包

首先，导入必需的 Python 库。

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>初始化工作区

[Azure 机器学习工作区](concept-workspace.md)是服务的顶级资源。 它提供了一个集中的位置来处理创建的所有项目。 在 Python SDK 中，可以通过创建 [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) 对象来访问工作区项目。

根据在[先决条件部分](#prerequisites)中创建的 `config.json` 文件创建工作区对象。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>创建文件数据集

`FileDataset` 对象引用工作区数据存储或公共 URL 中的一个或多个文件。 文件可以是任何格式，该类提供将文件下载或装载到计算机的功能。 通过创建 `FileDataset`，可以创建对数据源位置的引用。 如果将任何转换应用于数据集，则它们也会存储在数据集中。 数据会保留在其现有位置，因此不会产生额外的存储成本。 有关详细信息，请参阅 `Dataset` 包中的[操作](./how-to-create-register-datasets.md)指南。

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

使用 `register()` 方法将数据集注册到工作区，以便将其与其他人共享，在各种试验中重复使用，以及在训练脚本中按名称引用。

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>创建计算目标

创建用于运行 TensorFlow 作业的计算目标。 在此示例中，创建启用了 GPU 的 Azure 机器学习计算群集。

```Python
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

有关计算目标的详细信息，请参阅[什么是计算目标](concept-compute-target.md)一文。

### <a name="define-your-environment"></a>定义环境

若要定义封装训练脚本依赖项的 Azure ML [环境](concept-environments.md)，可以定义自定义环境或使用和 Azure ML 特选环境。

#### <a name="use-a-curated-environment"></a>使用特选环境
如果不想定义自己的环境，Azure ML 会提供预生成的特选环境。 Azure ML 具有几个针对 TensorFlow 的 CPU 和 GPU 特选环境，这些环境对应不同版本的 TensorFlow。 有关详细信息，请参阅[此文](resource-curated-environments.md)。

若要使用特选环境，可以改为运行以下命令：

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

若要查看特选环境中包含的包，可以将 conda 依赖项写入磁盘：
```python
tf_env.save_to_directory(path=curated_env_name)
```

确保特选环境包括训练脚本所需的所有依赖项。 如果没有，则必须修改环境以包含缺少的依赖项。 请注意，如果修改了环境，则必须为它提供新名称，因为“AzureML”前缀是为特选环境保留的。 如果修改了 conda 依赖项 YAML 文件，则可以使用新名称从该文件创建新环境，例如：
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

如果改为直接修改了特选环境对象，则可以使用新名称克隆该环境：
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>创建自定义环境

还可以创建自己的 Azure ML 环境，以封装训练脚本的依赖项。

首先，在 YAML 文件中定义 conda 依赖项；在本例中，该文件名为 `conda_dependencies.yml`。

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

基于此 conda 环境规范创建 Azure ML 环境。 此环境将在运行时打包到 Docker 容器中。

在默认情况下，如果未指定基础映像，Azure ML 将使用 CPU 映像 `azureml.core.environment.DEFAULT_CPU_IMAGE` 作为基础映像。 由于本示例在 GPU 群集上运行训练，因此你需要指定具有必要 GPU 驱动程序和依赖项的 GPU 基础映像。 Azure ML 维护一组在 Microsoft 容器注册表 (MCR) 上发布的基础映像，你可以使用这些映像，请参阅 [Azure/AzureML 容器](https://github.com/Azure/AzureML-Containers) GitHub 存储库获取详细信息。

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> 或者，也可以直接在自定义 Docker 映像或 Dockerfile 中捕获所有依赖项，然后从中创建环境。 有关详细信息，请参阅[通过自定义映像进行训练](how-to-train-with-custom-image.md)。

有关创建和使用环境的详细信息，请参阅[在 Azure 机器学习中创建和使用软件环境](how-to-use-environments.md)。

## <a name="configure-and-submit-your-training-run"></a>配置和提交训练运行

### <a name="create-a-scriptrunconfig"></a>创建 ScriptRunConfig

创建一个 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) 对象，以指定训练作业的配置详细信息，包括训练脚本、要使用的环境，以及要在其上运行的计算目标。 如果在 `arguments` 参数中指定，训练脚本的任何参数都将通过命令行传递。

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Azure 机器学习通过复制整个源目录来运行训练脚本。 如果你有不想上传的敏感数据，请使用 [.ignore 文件](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)或不将其包含在源目录中。 改为使用 Azure ML [数据集](how-to-train-with-datasets.md)来访问数据。

有关通过 ScriptRunConfig 配置作业的详细信息，请参阅[配置并提交训练运行](how-to-set-up-training-targets.md)。

> [!WARNING]
> 如果你以前使用 TensorFlow 估算器来配置 TensorFlow 训练作业，请注意，自 1.19.0 SDK 发行版起，该估算器已弃用。 对于不低于 1.15.0 版本的 Azure ML SDK，建议使用 ScriptRunConfig 作为配置训练作业（包括使用深度学习框架的作业）的方法。 有关常见的迁移问题，请参阅[估算器到 ScriptRunConfig 迁移指南](how-to-migrate-from-estimators-to-scriptrunconfig.md)。

### <a name="submit-a-run"></a>提交运行

[运行对象](/python/api/azureml-core/azureml.core.run%28class%29)在作业运行时和运行后提供运行历史记录的接口。

```Python
run = Experiment(workspace=ws, name='Tutorial-TF-Mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>在运行执行过程中发生的情况
执行运行时，会经历以下阶段：

- **准备**：根据所定义的环境创建 docker 映像。 将映像上传到工作区的容器注册表，缓存以用于后续运行。 还会将日志流式传输到运行历史记录，可以查看日志以监视进度。 如果改为指定特选环境，则会使用支持该特选环境的缓存映像。

- **缩放**：如果 Batch AI 群集执行运行所需的节点多于当前可用节点，则群集将尝试纵向扩展。

- **正在运行**：将脚本文件夹中的所有脚本上传到计算目标，装载或复制数据存储，然后执行 `script`。 将 stdout 和 ./logs 文件夹中的输出流式传输到运行历史记录，即可将其用于监视运行。

- **后期处理**：将运行的 ./outputs 文件夹复制到运行历史记录。

## <a name="register-or-download-a-model"></a>注册或下载模型

训练模型后，可以将其注册到工作区。 凭借模型注册，可以在工作区中存储模型并对其进行版本控制，从而简化[模型管理和部署](concept-model-management-and-deployment.md)。 可选：通过指定参数 `model_framework`、`model_framework_version` 和 `resource_configuration`，无代码模型部署将可供使用。 这允许你通过已注册模型直接将模型部署为 Web服务，`ResourceConfiguration` 对象定义 Web 服务的计算资源。

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

此外，还可以使用“运行”对象下载模型的本地副本。 在训练脚本 `tf_mnist.py` 中，TensorFlow 保护程序对象将模型保存到本地文件夹（计算目标本地）。 可以使用“运行”对象下载副本。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>分布式训练

Azure 机器学习还支持多节点分布式 TensorFlow 作业，以便可以扩展训练工作负载。 你可以轻松运行分布式 TensorFlow 作业，Azure ML 将为你管理业务流程。

Azure ML 支持使用 Horovod 和 TensorFlow 的内置分布式训练 API 运行分布式 TensorFlow 作业。

有关分布式训练的更多信息，请参阅[分布式 GPU 训练指南](how-to-train-distributed-gpu.md)。

## <a name="deploy-a-tensorflow-model"></a>部署 TensorFlow 模型

部署指南包含有关模型注册的部分，但由于你已有一个已注册的模型，因而可以直接跳到[创建计算目标](how-to-deploy-and-where.md#choose-a-compute-target)进行部署。

### <a name="preview-no-code-model-deployment"></a>（预览版）无代码模型部署

除了传统的部署路线之外，还可以为 TensorFlow 使用无代码部署功能（预览版）。 通过如上所示使用 `model_framework`、`model_framework_version` 和 `resource_configuration` 参数注册你的模型，可以简单地使用 `deploy()` 静态函数来部署模型。

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

完整的[操作指南](how-to-deploy-and-where.md)更深入地介绍了 Azure 机器学习。

## <a name="next-steps"></a>后续步骤

在本文中，你训练并注册了一个 TensorFlow 模型，并了解了部署选项。 有关 Azure 机器学习的详细信息，请参阅以下其他文章。

* [在训练期间跟踪运行指标](how-to-log-view-metrics.md)
* [优化超参数](how-to-tune-hyperparameters.md)
* [Azure 中分布式深度学习训练的参考体系结构](/azure/architecture/reference-architectures/ai/training-deep-learning)
