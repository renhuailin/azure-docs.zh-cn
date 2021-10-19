---
title: 特选环境
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习的特选环境，这是一组预先配置的环境，有助于减少试验和部署准备时间。
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 07/08/2021
ms.openlocfilehash: d86abb52b5282c70e99abc68da75c4b5f85ecbb8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712059"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure 机器学习的特选环境

本文列出了 Azure 机器学习中使用最新框架版本的特选环境。 特选环境由 Azure 机器学习提供，且默认可用于你的工作区。 它们由使用最新版本的 Azure 机器学习 SDK 的缓存 Docker 映像提供支持，降低了运行准备成本，缩短了部署时间。 使用这些环境可以快速完成各种机器学习框架的入门。

> [!NOTE]
> 使用 [Python SDK](how-to-use-environments.md)、[CLI](/cli/azure/ml/environment?view=azure-cli-latest&preserve-view=true#az_ml_environment_list) 或 Azure 机器学习[工作室](how-to-manage-environments-in-studio.md)获取环境及其依赖项的完整列表。 有关详细信息，请参阅[环境](how-to-use-environments.md#use-a-curated-environment)一文。 

## <a name="training-curated-environments"></a>训练特选环境

### <a name="pytorch"></a>PyTorch

名称：AzureML-pytorch-1.9-ubuntu18.04-py37-cuda11-gpu  
说明：使用 PyTorch 进行深度学习的环境，其中包含 AzureML Python SDK 和其他 Python 包。  

可以为个人工作流自定义以下 Dockerfile。

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.1-cudnn8-ubuntu18.04:20211005.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/pytorch-1.9

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 \
    pip=20.2.4 \
    pytorch=1.9.0 \
    torchvision=0.10.0 \
    torchaudio=0.9.0 \
    cudatoolkit=11.1.1 \
    nvidia-apex=0.1.0 \
    -c anaconda -c pytorch -c conda-forge

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_PYTORCH=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[pytorch]==0.21.3' \
                'future==0.17.1' \
                'torch-tb-profiler==0.2.1'


# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

其他可用的 PyTorch 环境：
* AzureML-pytorch-1.8-ubuntu18.04-py37-cuda11-gpu
* AzureML-pytorch-1.7-ubuntu18.04-py37-cuda11-gpu

### <a name="lightgbm"></a>LightGBM

名称：AzureML-lightgbm-3.2-ubuntu18.04-py37-cpu  
说明：使用 Scikit-learn、LightGBM、XGBoost 和 Dask 进行机器学习的环境，其中包含 AzureML Python SDK 和其他包。  

可以为个人工作流自定义以下 Dockerfile。

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20211005.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/lightgbm

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_TENSORFLOW=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'numpy>=1.10,<1.20' \
                'scipy~=1.5.0' \
                'scikit-learn~=0.24.1' \
                'xgboost~=1.4.0' \
                'lightgbm~=3.2.0' \
                'dask~=2021.6.0' \
                'distributed~=2021.6.0' \
                'dask-ml~=1.9.0' \
                'adlfs~=0.7.0' \
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

### <a name="sklearn"></a>Sklearn
名称：AzureML-sklearn-0.24-ubuntu18.04-py37-cuda11-gpu  
说明：使用 Scikit-learn 完成回归、聚类分析和分类等任务的环境。 包含 AzureML Python SDK 和其他 python 包。  

可以为个人工作流自定义以下 Dockerfile。

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20211005.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/sklearn-0.24.1

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0' \
                'scikit-learn==0.24.1'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

### <a name="tensorflow"></a>TensorFlow

名称：AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11-gpu  
说明：使用 Tensorflow 进行深度学习的环境，其中包含 AzureML Python SDK 和其他 Python 包。  

可以为个人工作流自定义以下 Dockerfile。

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20211005.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/tensorflow-2.4

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_TENSORFLOW=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'tensorflow-datasets==4.3.0' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[tensorflow-gpu]==0.21.3'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="automated-ml-automl"></a>自动化 ML (AutoML)

使用 AutoML 的 Azure ML 管道训练工作流会根据计算类型以及是否启用 DNN 自动选择策展环境。 AutoML 提供以下策展环境：

| 名称 | 计算类型 | DNN 已启用 |
| --- | --- | --- |
|AzureML-AutoML | CPU | 否 |
|AzureML-AutoML-DNN | CPU | 是 |
| AzureML-AutoML-GPU | GPU | 否 |
| AzureML-AutoML-DNN-GPU | GPU | 是 |

有关 AutoML 和 Azure ML 管道的详细信息，请参阅[通过 Python 在 Azure 机器学习管道中使用自动化 ML](how-to-use-automlstep-in-pipelines.md)。

## <a name="inference-curated-environments-and-prebuilt-docker-images"></a>推理特选环境和预生成的 Docker 映像

[!INCLUDE [list-of-inference-prebuilt-docker-images](../../includes/aml-inference-list-prebuilt-docker-images.md)]

## <a name="security"></a>安全性
受支持环境的版本更新每两周发布一次，以解决不超过 30 天的漏洞。 

