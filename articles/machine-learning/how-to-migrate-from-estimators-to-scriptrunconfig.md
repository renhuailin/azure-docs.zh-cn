---
title: 从 Estimators 迁移到 ScriptRunConfig
titleSuffix: Azure Machine Learning
description: 迁移指南：从 Estimators 迁移到 ScriptRunConfig 以配置训练作业
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: ae0623a11b940a4d142f6bfae02d4b20727a6f55
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518866"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>从 Estimators 迁移到 ScriptRunConfig

到目前为止，已经有多种方法可用于通过 SDK 在 Azure 机器学习中配置训练作业，这些方法包括 Estimators、ScriptRunConfig 和较低级别的 RunConfiguration。   为了解决这种模糊性和不一致性问题，我们正在简化 Azure ML 中的作业配置过程。  现在应该使用 ScriptRunConfig 作为配置训练作业的推荐选项。 

Python SDK 的 1.19.0 版本弃用了 Estimators。 你通常还应避免自行显式实例化 RunConfiguration 对象，而应改为使用 ScriptRunConfig 类来配置作业。

本文介绍了从 Estimators 迁移到 ScriptRunConfig 时的常见注意事项。

> [!IMPORTANT]
> 若要从 Estimators 迁移到 ScriptRunConfig，请确保使用的 Python SDK 版本 >= 1.15.0。

## <a name="scriptrunconfig-documentation-and-samples"></a>ScriptRunConfig 文档和示例
Azure 机器学习文档和示例已更新为使用 [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) 进行作业配置和提交。

有关使用 ScriptRunConfig 的信息，请参阅以下文档：
* [配置和提交训练运行](how-to-set-up-training-targets.md)
* [配置 PyTorch 训练运行](how-to-train-pytorch.md)
* [配置 TensorFlow 训练运行](how-to-train-tensorflow.md)
* [配置 scikit-learn 训练运行](how-to-train-scikit-learn.md)

此外，请参阅以下示例和教程：
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/azureml-examples](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>定义训练环境
虽然各种框架估算器都预先配置了由 Docker 映像提供支持的环境，但这些映像的 Dockerfile 是专用的。  因此，你无法详细了解这些环境包含的内容。 此外，估算器会采用与环境相关的配置作为其相应构造函数的单个参数（例如 `pip_packages`、`custom_docker_image`）。

使用 ScriptRunConfig 时，所有与环境相关的配置都封装在 `Environment` 对象中，该对象被传递到 ScriptRunConfig 构造函数的 `environment` 参数中。 若要配置训练作业，请提供一个包含训练脚本所需的所有依赖项的环境。 如果未提供任何环境，Azure ML 会使用 Azure ML 基础映像之一（具体而言，是由 `azureml.core.environment.DEFAULT_CPU_IMAGE` 定义的映像）作为默认环境。 有多种方法可以提供环境：

* [使用特选环境](how-to-use-environments.md#use-a-curated-environment) - 特选环境是默认情况下工作区中可用的预定义环境。 对于 Docker 映像的支持每个框架估算器的每个预配置框架/版本，都有一个相应的特选环境。
* [定义你自己的自定义环境](how-to-use-environments.md)

下面是使用特选 PyTorch 1.6 环境进行训练的示例：

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

如果要指定将在执行训练脚本的进程上设置的环境变量，请使用 Environment 对象：
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

有关配置和管理 Azure ML 环境的信息，请参阅：
* [如何使用环境](how-to-use-environments.md)
* [特选环境](resource-curated-environments.md)
* [使用自定义 Docker 映像进行训练](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>使用数据进行训练
### <a name="datasets"></a>数据集
如果使用 Azure ML 数据集进行训练，请使用 `arguments` 参数将数据集作为参数传递给脚本。 这样，你就可以通过参数在训练脚本中获取数据路径（装入点或下载路径）。

下面的示例配置一个训练作业，其中的 FileDataset `mnist_ds` 会装载到远程计算机上。
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference（旧方式）
虽然我们建议使用 Azure ML 数据集而不是旧的 DataReference 方式，但如果你由于任何原因而仍在使用 DataReference，你必须按以下方式配置作业：
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

有关使用数据进行训练的详细信息，请参阅：
* [使用 Azure ML 中的数据集进行训练](./how-to-train-with-datasets.md)

## <a name="distributed-training"></a>分布式训练
如果需要配置用于训练的分布式作业，请在 ScriptRunConfig 构造函数中指定 `distributed_job_config` 参数。 传入 [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration)、[PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) 或 [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration)（适用于相应类型的分布式作业）。

以下示例将 PyTorch 训练作业配置为对 MPI/Horovod 使用分布式训练：
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
如果出于任何原因而需要访问 ScriptRunConfig 的基础 RunConfiguration 对象，可以使用以下命令来那样做：
```
src.run_config
```

## <a name="next-steps"></a>后续步骤

* [配置和提交训练运行](how-to-set-up-training-targets.md)