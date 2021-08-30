---
title: 使用自定义 Docker 映像训练模型
titleSuffix: Azure Machine Learning
description: 了解如何使用你自己的 Docker 映像或 Microsoft 提供的特选映像在 Azure 机器学习中训练模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 08/11/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 12da538cfbf258aa8e447d5b5832cbc1865600ab
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745227"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>使用自定义 Docker 映像训练模型

在本文中，你将了解在通过 Azure 机器学习训练模型时如何使用自定义 Docker 映像。 你将使用本文中的示例脚本通过创建卷积神经网络来对宠物图像进行分类。 

Azure 机器学习提供了一个默认的 Docker 基础映像。 你还可以使用 Azure 机器学习环境来指定一个不同的基础映像，例如系统维护的 [Azure 机器学习基础映像](https://github.com/Azure/AzureML-Containers)之一或你自己的[自定义映像](./how-to-deploy-custom-container.md)。 借助自定义基础映像，你可以在运行训练作业时密切管理依赖项，以及更加严格地控制组件版本。

## <a name="prerequisites"></a>先决条件

在以下任一环境中运行此代码：

* Azure 机器学习计算实例（无需下载或安装）：
  * 完成[快速入门：Azure 机器学习入门](quickstart-create-resources.md)教程，以创建一个预加载了 SDK 和示例存储库的专用笔记本服务器。
  * 在 Azure 机器学习 [示例存储库](https://github.com/Azure/azureml-examples)中，通过导航到以下目录找到一个完整的笔记本：**notebooks** > **fastai** > **train-pets-resnet34.ipynb**。 
* 你自己的 Jupyter Notebook 服务器：
  * 创建[工作区配置文件](how-to-configure-environment.md#workspace)。
  * 安装 [Azure 机器学习 SDK](/python/api/overview/azure/ml/install)。 
  * 创建可在 Internet 上访问的 [Azure 容器注册表](../container-registry/index.yml)或其他 Docker 注册表。

## <a name="set-up-a-training-experiment"></a>设置训练试验

在本部分中，你将通过初始化工作区、定义你的环境并配置计算目标来设置训练试验。

### <a name="initialize-a-workspace"></a>初始化工作区

[Azure 机器学习工作区](concept-workspace.md)是服务的顶级资源。 它提供了一个集中化位置来处理你创建的所有项目。 在 Python SDK 中，可以通过创建 [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) 对象来访问工作区项目。

通过作为[先决条件](#prerequisites)创建的 config.json 文件创建一个 `Workspace` 对象。

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>定义环境

创建一个 `Environment` 对象。

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
```

以下代码中指定的基础映像支持 fast.ai 库，后者支持分布式深度学习功能。 有关详细信息，请参阅 [fast.ai Docker Hub 存储库](https://hub.docker.com/u/fastdotai)。 

使用自定义 Docker 映像时，你可能已经正确设置了 Python 环境。 在这种情况下，请将 `user_managed_dependencies` 标志设置为 `True`，以使用自定义映像的内置 Python 环境。 默认情况下，Azure 机器学习构建包含指定依赖项的 Conda 环境。 该服务在该环境中运行脚本，而不是使用已安装在基础映像中的任何 Python 库。

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>使用专用容器注册表（可选）

若要使用不在工作区中的专用容器注册表中的映像，请使用 `docker.base_image_registry` 指定存储库的地址以及用户名和密码：

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>使用自定义 Dockerfile（可选）

还可以使用自定义 Dockerfile。 如果需要安装非 Python 包作为依赖项，请使用此方法。 记得将基础映像设置为 `None`。

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210615.v1
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

>[!IMPORTANT]
> Azure 机器学习仅支持提供以下软件的 Docker 映像：
> * Ubuntu 18.04 或更高版本。
> * Conda 4.7.# 或更高版本。
> * Python 3.6+。
> * 在用于训练的任何容器映像中，都需要在 /bin/sh 处有符合 POSIX 标准的 shell。 

若要详细了解如何创建和管理 Azure 机器学习环境，请参阅[创建和使用软件环境](how-to-use-environments.md)。 

### <a name="create-or-attach-a-compute-target"></a>创建或附加计算目标

你需要创建一个[计算目标](concept-azure-machine-learning-architecture.md#compute-targets)来训练模型。 在本教程中，你将创建 `AmlCompute` 作为训练计算资源。

创建 `AmlCompute` 需要几分钟时间。 如果你的工作区中已有 `AmlCompute` 资源，则此代码会跳过创建流程。

与其他 Azure 服务一样，与 Azure 机器学习服务关联的某些资源（例如 `AmlCompute`）存在限制。 有关详细信息，请参阅[默认限制以及如何请求更高的配额](how-to-manage-quotas.md)。

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


>[!IMPORTANT]
>为计算上的任何映像生成使用 CPU SKU。 


## <a name="configure-your-training-job"></a>配置训练作业

对于本教程，请使用 [GitHub](https://github.com/Azure/azureml-examples/blob/main/python-sdk/workflows/train/fastai/pets/src/train.py) 上的训练脚本 train.py。 实际上，你可以原样接受任何自定义的训练脚本并使用 Azure 机器学习运行它。

创建 `ScriptRunConfig` 资源，以将作业配置为在所需的[计算目标](how-to-set-up-training-targets.md)上运行。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>提交训练作业

使用 `ScriptRunConfig` 对象提交训练运行时，`submit` 方法返回 `ScriptRun` 类型的对象。 返回的 `ScriptRun` 对象使你能够以编程方式访问有关训练运行的信息。 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure 机器学习通过复制整个源目录来运行训练脚本。 如果你有不想上传的敏感数据，请使用 [.ignore 文件](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)或不将其包含在源目录中。 请改为使用[数据存储](/python/api/azureml-core/azureml.data)来访问数据。

## <a name="next-steps"></a>后续步骤
在本文中，你已使用自定义 Docker 映像训练了一个模型。 有关 Azure 机器学习的详细信息，请参阅下述其他文章：
* 在训练期间[跟踪运行指标](how-to-log-view-metrics.md)。
* 使用自定义 Docker 映像来[部署模型](./how-to-deploy-custom-container.md)。
