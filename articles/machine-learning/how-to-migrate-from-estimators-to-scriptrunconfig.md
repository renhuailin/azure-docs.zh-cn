---
title: 从估算迁移到 ScriptRunConfig
titleSuffix: Azure Machine Learning
description: 迁移指南：从估算迁移到 ScriptRunConfig，用于配置培训作业。
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 64c03b1c9fc18a4e78af9914b893599683069ced
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632654"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>从估算迁移到 ScriptRunConfig

到现在为止，有多种方法可用于在 Azure 机器学习中通过 SDK 配置训练作业，包括估算、ScriptRunConfig 和更低级别的 RunConfiguration。   为了解决这种不一致的问题，我们正在简化 Azure ML 中的作业配置过程。  现在应使用 ScriptRunConfig 作为配置训练作业的建议选项。 

Python SDK 的1.19.0 版本弃用了估算。 通常还应避免自行显式实例化 RunConfiguration 对象，并使用 ScriptRunConfig 类来配置作业。

本文介绍从估算迁移到 ScriptRunConfig 时的常见注意事项。

> [!IMPORTANT]
> 若要从估算迁移到 ScriptRunConfig，请确保使用的是 Python SDK >= 1.15.0。

## <a name="scriptrunconfig-documentation-and-samples"></a>ScriptRunConfig 文档和示例
Azure 机器学习的文档和示例已更新为使用 [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) 进行作业配置和提交。

有关使用 ScriptRunConfig 的信息，请参阅以下文档：
* [配置和提交训练运行](how-to-set-up-training-targets.md)
* [配置 PyTorch 定型运行](how-to-train-pytorch.md)
* [配置 TensorFlow 定型运行](how-to-train-tensorflow.md)
* [配置 scikit-learn-了解定型运行](how-to-train-scikit-learn.md)

此外，请参阅以下示例 & 教程：
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/azureml-examples](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>定义培训环境
尽管各种框架估算具有由 Docker 映像提供支持的预配置环境，但这些映像的 Dockerfile 是专用的。  因此，您对这些环境所包含的内容没有太大的透明性。 此外，估算采用独立于环境的配置作为单个参数 (如 `pip_packages` `custom_docker_image`) 其各自的构造函数。

当使用 ScriptRunConfig 时，所有与环境相关的配置都封装在 `Environment` 传递到 `environment` ScriptRunConfig 构造函数的参数中的对象中。 若要配置训练作业，请提供一个具有训练脚本所需的所有依赖项的环境。 如果未提供任何环境，Azure ML 将使用 Azure ML 基础映像之一，特别是由定义的映像 `azureml.core.environment.DEFAULT_CPU_IMAGE` 作为默认环境。 有多种方法可以提供环境：

* [使用特选环境](how-to-use-environments.md#use-a-curated-environment) -特选环境是默认情况下在工作区中可用的预定义环境。 每个支持每个框架估计器的预配置框架/版本 Docker 映像都有相应的特选环境。
* [定义你自己的自定义环境](how-to-use-environments.md)

下面是使用特选 PyTorch 1.6 环境进行培训的示例：

```python
from azureml.core import Workspace, ScriptRunConfig, Environment

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)

compute_target = ws.compute_targets['my-cluster']
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env)
```

如果要指定将在执行训练脚本的进程上设置的 **环境变量** ，请使用环境对象：
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

有关配置和管理 Azure ML 环境的信息，请参阅：
* [如何使用环境](how-to-use-environments.md)
* [特选环境](resource-curated-environments.md)
* [使用自定义 Docker 映像进行训练](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>使用数据进行定型
### <a name="datasets"></a>数据集
如果使用 Azure ML 数据集进行培训，请使用参数将 dataset 作为参数传递给脚本 `arguments` 。 这样，你就可以通过参数在训练脚本中 (装入点或下载路径) 的数据路径。

下面的示例配置一个训练作业，其中，FileDataset `mnist_ds` 将在远程计算上装入。
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference (旧) 
尽管我们建议使用 Azure ML 数据集来实现旧的 DataReference，但如果出于任何原因仍要使用 DataReferences，则必须按以下方式配置作业：
```python
# if you want to pass a DataReference object, such as the below:
datastore = ws.get_default_datastore()
data_ref = datastore.path('./foo').as_mount()

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', str(data_ref)], # cast the DataReference object to str
                      compute_target=compute_target,
                      environment=pytorch_env)
src.run_config.data_references = {data_ref.data_reference_name: data_ref.to_config()} # set a dict of the DataReference(s) you want to the `data_references` attribute of the ScriptRunConfig's underlying RunConfiguration object.
```

有关使用数据进行定型的详细信息，请参阅：
* [在 Azure ML 中训练数据集](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)

## <a name="distributed-training"></a>分布式训练
如果需要为定型配置分布式作业，请 `distributed_job_config` 在 ScriptRunConfig 构造函数中指定参数来执行此操作。 为各自类型的分布式作业传入 [MpiConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true)、 [PyTorchConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?view=azure-ml-py&preserve-view=true)或 [TensorflowConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py&preserve-view=true) 。

下面的示例将 PyTorch 定型作业配置为对 MPI/Horovod 使用分布式培训：
```python
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2, process_count_per_node=2))
```

有关详细信息，请参阅：
* [使用 PyTorch 进行分布式训练](how-to-train-pytorch.md#distributed-training)
* [使用 TensorFlow 进行分布式训练](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>杂项
如果出于任何原因需要访问 ScriptRunConfig 的底层 RunConfiguration 对象，可以执行以下操作：
```
src.run_config
```

## <a name="next-steps"></a>后续步骤

* [配置和提交训练运行](how-to-set-up-training-targets.md)
