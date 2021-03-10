---
title: 训练深度学习 Keras 模型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习训练和注册在 TensorFlow 上运行的 Keras 深度神经网络分类模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2b4af9dec2bf397ad2766c68d547eeac85a9a9a3
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518358"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>使用 Azure 机器学习大规模训练 Keras 模型

本文介绍了如何使用 Azure 机器学习运行 Keras 训练脚本。

本文中的示例代码介绍了如何使用 Azure 机器学习来训练和注册使用 TensorFlow 后端生成的 Keras 分类模型。 它凭借使用在 [TensorFlow](https://www.tensorflow.org/overview) 上运行的 [Keras Python 库](https://keras.io)构建的深度神经网络 (DNN)，采用热门的 [MNIST 数据集](http://yann.lecun.com/exdb/mnist/)对手写数字进行分类。

Keras 是一种高级神经网络 API，能够基于其他常用 DNN 框架运行以简化开发。 使用 Azure 机器学习，可以使用弹性云计算资源快速横向扩展训练作业。 此外还可以跟踪训练运行、对模型进行版本控制、部署模型等。

无论是从头开始开发 Keras 模型，还是将现有模型引入云中，Azure 机器学习都可以帮助你生成适用于生产的模型。

> [!NOTE]
> 如果你使用的是 TensorFlow 中内置的 Keras API tf.keras，而不是独立的 Keras 包，请改为参阅[训练 TensorFlow 模型](how-to-train-tensorflow.md)。

## <a name="prerequisites"></a>先决条件

在以下任一环境中运行此代码：

- Azure 机器学习计算实例 - 无需下载或安装

     - 在开始本教程之前完成[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)创建预先加载了 SDK 和示例存储库的专用笔记本服务器。
    - 在笔记本服务器上的示例文件夹中，通过导航到以下目录来查找已完成且已扩展的笔记本：how-to-use-azureml > ml-frameworks > keras > train-hyperparameter-tune-deploy-with-keras 文件夹。

 - 你自己的 Jupyter 笔记本服务器

    - [安装 Azure 机器学习 SDK](/python/api/overview/azure/ml/install) (>= 1.15.0)。
    - [创建工作区配置文件](how-to-configure-environment.md#workspace)。
    - [下载示例脚本文件](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` 和 `utils.py`

    此外，还可以在 GitHub 示例页上找到本指南的完整 [Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)。 该笔记本包含扩展部分，其中涵盖智能超参数优化、模型部署和笔记本小组件。

## <a name="set-up-the-experiment"></a>设置试验

本部分通过加载所需的 Python 包、初始化工作区、为输入训练数据创建 FileDataset、创建计算目标以及定义训练环境设置来训练实验。

### <a name="import-packages"></a>导入程序包

首先，导入必需的 Python 库。

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import Workspace, Run
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

可以使用 `register()` 方法将数据集注册到工作区，以便将其与其他人共享，在各种试验中重复使用，以及在训练脚本中按名称引用。

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>创建计算目标

创建用于运行训练作业的计算目标。 在此示例中，创建启用了 GPU 的 Azure 机器学习计算群集。

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

定义 Azure ML [环境](concept-environments.md)，用于封装训练脚本的依赖项。

首先，在 YAML 文件中定义 conda 依赖项；在本例中，该文件名为 `conda_dependencies.yml`。

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

基于此 conda 环境规范创建 Azure ML 环境。 此环境将在运行时打包到 Docker 容器中。

在默认情况下，如果未指定基础映像，Azure ML 将使用 CPU 映像 `azureml.core.environment.DEFAULT_CPU_IMAGE` 作为基础映像。 由于本示例在 GPU 群集上运行训练，因此你需要指定具有必要 GPU 驱动程序和依赖项的 GPU 基础映像。 Azure ML 维护一组在 Microsoft 容器注册表 (MCR) 上发布的基础映像，你可以使用这些映像，请参阅 [Azure/AzureML 容器](https://github.com/Azure/AzureML-Containers) GitHub 存储库以获取详细信息。

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

若要详细了解如何创建和使用环境，请参阅[在 Azure 机器学习中创建和使用软件环境](how-to-use-environments.md)。

## <a name="configure-and-submit-your-training-run"></a>配置和提交训练运行

### <a name="create-a-scriptrunconfig"></a>创建 ScriptRunConfig
首先，使用 `Dataset` 类从工作区数据存储中获取数据。

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

创建一个 ScriptRunConfig 对象，以指定训练作业的配置详细信息，包括训练脚本、要使用的环境，以及要在其上运行的计算目标。

训练脚本的任何自变量都将通过命令行传递（如果已在 `arguments` 参数中指定）。 FileDataset 的 DatasetConsumptionConfig 会作为自变量传递到训练脚本，用于 `--data-folder` 自变量。 Azure ML 会将此 DatasetConsumptionConfig 解析为后备数据存储的装入点，然后该装入点便可供从训练脚本中访问。

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

若要详细了解如何使用 ScriptRunConfig 配置作业，请参阅[配置并提交训练运行](how-to-set-up-training-targets.md)。

> [!WARNING]
> 如果你以前使用 TensorFlow 估算器来配置 Keras 训练作业，请注意，自 1.19.0 SDK 发行版起，该估算器已弃用。 对于不低于 1.15.0 版本的 Azure ML SDK，建议使用 ScriptRunConfig 作为配置训练作业（包括使用深度学习框架的作业）的方法。 有关常见的迁移问题，请参阅[估算器到 ScriptRunConfig 迁移指南](how-to-migrate-from-estimators-to-scriptrunconfig.md)。

### <a name="submit-your-run"></a>提交运行

[运行对象](/python/api/azureml-core/azureml.core.run%28class%29)在作业运行时和运行后提供运行历史记录的接口。

```Python
run = Experiment(workspace=ws, name='Tutorial-Keras-Minst').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>在运行执行过程中发生的情况
执行运行时，会经历以下阶段：

- **准备**：根据所定义的环境创建 docker 映像。 将映像上传到工作区的容器注册表，缓存以用于后续运行。 还会将日志流式传输到运行历史记录，可以查看日志以监视进度。 如果改为指定特选环境，则会使用支持该特选环境的缓存映像。

- **缩放**：如果 Batch AI 群集执行运行所需的节点多于当前可用节点，则群集将尝试纵向扩展。

- **正在运行**：将脚本文件夹中的所有脚本上传到计算目标，装载或复制数据存储，然后执行 `script`。 将 stdout 和 ./logs 文件夹中的输出流式传输到运行历史记录，即可将其用于监视运行。

- **后期处理**：该运行的 ./outputs 文件夹会复制到运行历史记录。

## <a name="register-the-model"></a>注册模型

训练模型后，可以将其注册到工作区。 凭借模型注册，可以在工作区中存储模型并对其进行版本控制，从而简化[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> 部署指南包含有关模型注册的部分，但由于你已有一个已注册的模型，因而可以直接跳到[创建计算目标](how-to-deploy-and-where.md#choose-a-compute-target)进行部署。

你还可以下载模型的本地副本。 这对于在本地执行其他模型验证工作非常有用。 在训练脚本`keras_mnist.py` 中，TensorFlow 保护程序对象将模型保存到本地文件夹（计算目标本地）。 可以使用 Run 对象从运行历史记录下载副本。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>后续步骤

在本文中，你在 Azure 机器学习上训练和注册了 Keras 模型。 若要了解如何部署模型，请继续参阅模型部署一文。

* [部署模型的方式和位置](how-to-deploy-and-where.md)
* [在训练期间跟踪运行指标](how-to-track-experiments.md)
* [优化超参数](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [Azure 中分布式深度学习训练的参考体系结构](/azure/architecture/reference-architectures/ai/training-deep-learning)
