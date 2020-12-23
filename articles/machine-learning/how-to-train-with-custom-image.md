---
title: 使用自定义 Docker 映像为模型定型
titleSuffix: Azure Machine Learning
description: 了解如何使用自己的 Docker 映像，或者从 Microsoft 特选，在 Azure 机器学习中训练模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: a441d1d22f938e1d1e05aea547929fa3b315d406
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95012887"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>使用自定义 Docker 映像为模型定型

本文介绍如何在使用 Azure 机器学习训练模型时使用自定义 Docker 映像。 使用本文中的示例脚本，通过创建卷积神经网络来分类宠物映像。 

Azure 机器学习提供了一个默认 Docker 基本映像。 你还可以使用 Azure 机器学习环境来指定不同的基础映像，例如某个维护的 [Azure 机器学习基本](https://github.com/Azure/AzureML-Containers) 映像或你自己的 [自定义映像](how-to-deploy-custom-docker-image.md#create-a-custom-base-image)。 自定义基本映像使你可以严密管理依赖关系，并在运行定型作业时保持对组件版本的更紧密控制。

## <a name="prerequisites"></a>先决条件

在以下任一环境中运行代码：

* Azure 机器学习计算实例 (无需下载或安装) ：
  * 完成 " [设置环境和工作区](tutorial-1st-experiment-sdk-setup.md) " 教程，以创建使用 SDK 和示例存储库预加载的专用笔记本服务器。
  * 在 Azure 机器学习 [示例存储库](https://github.com/Azure/azureml-examples)中，通过转到 "**笔记本**  >  **fastai**"  >  **resnet34. ipynb** 目录查找已完成的笔记本。 
* 你自己的 Jupyter Notebook 服务器：
  * 创建[工作区配置文件](how-to-configure-environment.md#workspace)。
  * 安装 [Azure 机器学习 SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。 
  * 创建可在 internet 上使用的 [Azure 容器注册表](../container-registry/index.yml) 或其他 Docker 注册表。

## <a name="set-up-a-training-experiment"></a>设置训练实验

在本部分中，将通过初始化工作区、定义环境和配置计算目标来设置训练实验。

### <a name="initialize-a-workspace"></a>初始化工作区

[Azure 机器学习工作区](concept-workspace.md)是服务的顶级资源。 它为您提供了一个集中的位置来处理您创建的所有项目。 在 Python SDK 中，可以通过创建 [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) 对象来访问工作区项目。

`Workspace`从创建的 config.js文件创建一个作为[必备组件](#prerequisites)的对象。

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>定义环境

创建 `Environment` 对象并启用 Docker。

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

下面代码中的指定基本图像支持 fast.ai 库，这允许分布式深入学习功能。 有关详细信息，请参阅 [Fast.ai Docker Hub 存储库](https://hub.docker.com/u/fastdotai)。 

使用自定义 Docker 映像时，可能已正确设置了 Python 环境。 在这种情况下，将 `user_managed_dependencies` 标志设置为， `True` 以使用自定义映像的内置 Python 环境。 默认情况下，Azure 机器学习生成包含指定依赖项的 Conda 环境。 该服务将在该环境中运行该脚本，而不是使用在基础映像上安装的任何 Python 库。

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>使用专用容器注册表 (可选) 

若要使用工作区中没有的专用容器注册表中的映像，请使用 `docker.base_image_registry` 指定存储库的地址以及用户名和密码：

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>使用自定义 Dockerfile (可选) 

还可以使用自定义 Dockerfile。 如果需要安装非 Python 包作为依赖项，请使用此方法。 请记住将基本映像设置为 `None` 。

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

有关创建和管理 Azure 机器学习环境的详细信息，请参阅 [创建和使用软件环境](how-to-use-environments.md)。 

### <a name="create-or-attach-a-compute-target"></a>创建或附加计算目标

需要为模型定型创建 [计算目标](concept-azure-machine-learning-architecture.md#compute-targets) 。 在本教程中，将创建 `AmlCompute` 为训练计算资源。

创建 `AmlCompute` 会花费几分钟时间。 如果 `AmlCompute` 资源已在工作区中，则此代码将跳过创建过程。

与其他 Azure 服务一样，某些资源的限制 (例如， `AmlCompute`) 与 Azure 机器学习服务关联。 有关详细信息，请参阅 [默认限制和如何请求更高的配额](how-to-manage-quotas.md)。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>配置训练作业

对于本教程，请使用 [GitHub](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py)上的培训脚本 *train.py* 。 在实践中，您可以采用任何自定义训练脚本，并按原样运行它，Azure 机器学习。

创建 `ScriptRunConfig` 资源，将作业配置为在所需的 [计算目标](how-to-set-up-training-targets.md)上运行。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>提交训练作业

使用对象提交定型运行时 `ScriptRunConfig` ，该 `submit` 方法返回类型的对象 `ScriptRun` 。 返回的 `ScriptRun` 对象使您能够以编程方式访问有关定型运行的信息。 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure 机器学习通过复制整个源目录来运行训练脚本。 如果你有不想要上传的敏感数据，请使用 [. ignore 文件](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 或不要将其包含在源目录中。 而是使用数据 [存储](/python/api/azureml-core/azureml.data?preserve-view=true&view=azure-ml-py)访问数据。

## <a name="next-steps"></a>后续步骤
本文介绍了如何使用自定义 Docker 映像对模型进行训练。 若要详细了解 Azure 机器学习，请参阅以下文章：
* 在定型期间[跟踪运行指标](how-to-track-experiments.md)。
* 使用自定义 Docker 映像[部署模型](how-to-deploy-custom-docker-image.md)。
