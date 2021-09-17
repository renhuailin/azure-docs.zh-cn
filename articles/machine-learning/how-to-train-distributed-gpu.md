---
title: 分布式 GPU 训练指南
titleSuffix: Azure Machine Learning
description: 了解使用 Azure 机器学习支持的框架执行分布式训练的最佳做法，这些框架包括 MPI、Horovod、DeepSpeed、PyTorch、PyTorch Lightning、Hugging Face Transformers、TensorFlow 和 InfiniBand。
author: fuhuifang
ms.author: fufang
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: ff95cb0004fbde34e70bcb778d93a2f381dc5830
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122661652"
---
# <a name="distributed-gpu-training-guide"></a>分布式 GPU 训练指南

详细了解如何在 Azure 机器学习 (ML) 中使用分布式 GPU 训练代码。 本文不讲解分布式训练，  而是帮助用户在 Azure 机器学习上运行现有的分布式训练代码。 本文针对每种框架提供了提示和示例以供遵照：

* 消息传递接口 (MPI)
    * Horovod
    * DeepSpeed
    * Open MPI 中的环境变量
* PyTorch
    * 进程组初始化
    * 启动选项
    * DistributedDataParallel（每进程启动）
    * 使用 `torch.distributed.launch`（每节点启动）
    * PyTorch Lightning
    * Hugging Face Transformers
* TensorFlow
    * TensorFlow 的环境变量 (TF_CONFIG)
* 使用 InfiniBand 加速 GPU 训练

## <a name="prerequisites"></a>必备条件

回顾这些[分布式 GPU 训练的基本概念](concept-distributed-training.md)，例如数据并行性、分布式数据并行性和模型并行性。

> [!TIP]
> 如果不知道要使用哪种并行性类型，超过 90% 的时间应该使用分布式数据并行性。

## <a name="mpi"></a>MPI

Azure ML 提供了一个 [MPI 作业](https://www.mcs.anl.gov/research/projects/mpi/)，用于在每个节点中启动给定数量的进程。 可以采用此方法，使用每进程启动器或每节点启动器运行分布式训练，具体取决于是对于每节点启动器 `process_count_per_node` 设置为 1（默认值），还是对于每进程启动器该值等于设备/GPU 的数量。 Azure ML 在后台构造完整的 MPI 启动命令 (`mpirun`)。  用户无法提供自己的完整头节点启动器命令，如 `mpirun` 或 `DeepSpeed launcher`。

> [!TIP]
> Azure 机器学习 MPI 作业使用的基础 Docker 映像需要安装 MPI 库。 所有 [AzureML GPU 基础映像](https://github.com/Azure/AzureML-Containers)中均包含 [Open MPI](https://www.open-mpi.org/)。 使用自定义 Docker 映像时，用户负责确保映像包含 MPI 库。 建议使用 Open MPI，但也可以使用不同的 MPI 实现，例如 Intel MPI。 Azure ML 还针对热门框架提供了[特选环境](resource-curated-environments.md)。 

若要使用 MPI 运行分布式训练，请执行以下步骤：

1. 将 Azure ML 环境与首选深度学习框架和 MPI 一起使用。 AzureML 针对热门框架提供了[特选环境](resource-curated-environments.md)。
1. 使用 `process_count_per_node` 和 `node_count` 定义 `MpiConfiguration`。 如果用户脚本将负责每节点启动进程，则对于每进程启动 `process_count_per_node` 应等于每节点的 GPU 数，或者对于每节点启动则设置为 1（默认值）。
1. 将 `MpiConfiguration` 对象传递到 `ScriptRunConfig` 的 `distributed_job_config` 参数。

```python
from azureml.core import Workspace, ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import MpiConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = MpiConfiguration(process_count_per_node=4, node_count=2)

run_config = ScriptRunConfig(
  source_directory= './src',
  script='train.py',
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

# submit the run configuration to start the job
run = Experiment(ws, "experiment_name").submit(run_config)
```

### <a name="horovod"></a>Horovod

当使用 [Horovod](https://horovod.readthedocs.io/en/stable/index.html) 通过深度学习框架进行分布式训练时，请使用 MPI 作业配置。

确保代码遵照以下提示：

* 在添加 Azure ML 部件之前，使用 Horovod 正确检测训练代码
* Azure ML 环境包含 Horovod 和 MPI。 PyTorch 和 TensorFlow 特选 GPU 环境预配置了 Horovod 及其依赖项。
* 使用所需的分布创建 `MpiConfiguration`。

### <a name="horovod-example"></a>Horovod 示例

* [azureml-examples：使用 Horovod 的 TensorFlow 分布式训练](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/tensorflow/mnist-distributed-horovod)

### <a name="deepspeed"></a>DeepSpeed

不要使用 DeepSpeed 的自定义启动器在 Azure ML 上使用 [DeepSpeed](https://www.deepspeed.ai/) 库运行分布式训练。 相反，请配置 MPI 作业以[使用 MPI](https://www.deepspeed.ai/getting-started/#mpi-and-azureml-compatibility) 启动训练作业。

确保代码遵照以下提示：

* Azure ML 环境包含 DeepSpeed 及其依赖项、Open MPI 和 mpi4py。
* 使用分布创建 `MpiConfiguration`。

### <a name="deepseed-example"></a>DeepSeed 示例

* [azureml-examples：CIFAR-10 上使用 DeepSpeed 的分布式训练](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/deepspeed/cifar)

### <a name="environment-variables-from-open-mpi"></a>Open MPI 中的环境变量

使用 Open MPI 映像运行 MPI 作业时，启动每个进程的以下环境变量：

1. `OMPI_COMM_WORLD_RANK` - 进程的排名
2. `OMPI_COMM_WORLD_SIZE` - 世界大小
3. `AZ_BATCH_MASTER_NODE` - 主地址，含端口，`MASTER_ADDR:MASTER_PORT`
4. `OMPI_COMM_WORLD_LOCAL_RANK` - 节点上该进程的本地排名
5. `OMPI_COMM_WORLD_LOCAL_SIZE` - 节点上的进程数

> [!TIP]
> 尽管名称类似，环境变量 `OMPI_COMM_WORLD_NODE_RANK` 并不对应于 `NODE_RANK`。 要使用每节点启动器，请设置 `process_count_per_node=1` 并使用 `OMPI_COMM_WORLD_RANK` 作为 `NODE_RANK`。

## <a name="pytorch"></a>PyTorch

Azure ML 支持使用 PyTorch 的本机分布式训练功能来运行分布式作业（`torch.distributed`）。

> [!TIP]
> 对于数据并行性，[PyTorch 官方指南](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html#comparison-between-dataparallel-and-distributeddataparallel)使用 DistributedDataParallel (DDP) over DataParallel 进行单节点和多节点分布式训练。 PyTorch 还[建议对多处理包使用 DistributedDataParallel](https://pytorch.org/docs/stable/notes/cuda.html#use-nn-parallel-distributeddataparallel-instead-of-multiprocessing-or-nn-dataparallel)。 因此 Azure 机器学习文档和示例将重点介绍 DistributedDataParallel 训练。

### <a name="process-group-initialization"></a>进程组初始化

任何分布式训练的主干都基于一组彼此知晓且可以使用后端相互通信的进程。 对于 PyTorch，通过在所有分布式进程（共同构成一个进程组）中调用 [torch.distributed.init_process_group](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group) 来创建进程组。

```
torch.distributed.init_process_group(backend='nccl', init_method='env://', ...)
```

最常用的通信后端是 `mpi`、`nccl` 和 `gloo`。 对于基于 GPU 的训练，建议使用 `nccl` 以获得最佳性能，并且应尽可能使用。 

`init_method` 告知每个进程如何发现彼此，如何使用通信后端初始化和验证进程组。 默认情况下，如果未指定 `init_method`，PyTorch 将使用环境变量初始化方法 (`env://`)。 `init_method` 是在 Azure ML 上运行分布式 PyTorch 的训练代码中使用的推荐初始化方法。  PyTorch 将查找以下用于初始化的环境变量：

- **`MASTER_ADDR`** - 将托管排名为 0 的进程的计算机的 IP 地址。
- **`MASTER_PORT`** - 将托管排名为 0 的进程的计算机的空闲端口。
- **`WORLD_SIZE`** - 进程总数。 应该等于用于分布式训练的设备 (GPU) 总数。
- **`RANK`** - 当前进程的（全局）排名。 可能的值为 0 到（世界大小 - 1）。

有关进程组初始化详细信息，请参阅 [PyTorch 文档](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group)。

除此之外，许多应用程序还需要以下环境变量：
- **`LOCAL_RANK`** - 节点内进程的本地（相对）排名。 可能的值为 0 到（节点上的进程数 - 1）。 此信息很有用，因为许多操作（例如数据准备）在每个节点上只能执行一次，通常是在 local_rank = 0 上。
- **`NODE_RANK`** - 用于多节点训练的节点的排名。 可能的值为 0 到（节点总数 - 1）。

### <a name="pytorch-launch-options"></a>PyTorch 启动选项

Azure ML PyTorch 作业支持用两种类型的选项来启动分布式训练：

- 每进程启动器：系统将启动所有分布式进程，并包含用于设置进程组的所有相关信息（例如环境变量）。
- 每节点启动器：为 Azure ML 提供一个将在每个节点上运行的实用工具启动器。 该实用工具启动器将负责在给定节点上启动每个进程。 在每个节点内，`RANK` 和 `LOCAL_RANK` 由启动器以本地方式设置。 torch.distributed.launch 实用工具和 PyTorch Lightning 都属于这一类别。

这些启动选项之间没有根本的区别。 选择哪种启动选项主要取决于用户偏好或基于原版 PyTorch 构建的框架/库的约定（如 Lightning 或 Hugging Face）。

以下部分详细介绍了如何为每个启动选项配置 Azure ML PyTorch 作业。

### <a name="distributeddataparallel-per-process-launch"></a><a name="per-process-launch"></a> DistributedDataParallel（每进程启动）

无需使用类似 `torch.distributed.launch` 的启动器实用工具。 要运行分布式 PyTorch 作业：

1. 指定训练脚本和参数
1. 创建 `PyTorchConfiguration` 并指定 `process_count` 和 `node_count`。 `process_count` 对应于要为作业运行的进程总数。 `process_count` 通常应等于 `# GPUs per node x # nodes`。 如果未指定 `process_count`，Azure ML 将默认每节点启动一个进程。

Azure ML 将在每个节点上设置 `MASTER_ADDR`、`MASTER_PORT`、`WORLD_SIZE` 和 `NODE_RANK` 环境变量，并设置进程级的 `RANK` 和 `LOCAL_RANK` 环境变量。

要将此选项用于每节点多进程的训练，请使用 Azure ML Python SDK `>= 1.22.0`。 Process_count 是在 1.22.0 中引入。

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(process_count=8, node_count=2)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=['--epochs', 50],
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

> [!TIP]
> 如果训练脚本将本地排名或排名等信息作为脚本参数传递，则可以在参数中引用环境变量：
>
> ```python
> arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]
> ```

### <a name="pytorch-per-process-launch-example"></a>Pytorch 每进程启动示例

- [azureml-examples：CIFAR-10 上使用 PyTorch 的分布式训练](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/pytorch/cifar-distributed)

### <a name="using-torchdistributedlaunch-per-node-launch"></a><a name="per-node-launch"></a> 使用 `torch.distributed.launch`（每节点启动）

PyTorch 在 [torch.distributed.launch](https://pytorch.org/docs/stable/distributed.html#launch-utility) 中提供了一个启动实用工具，可以使用它在每节点上启动多个进程。 `torch.distributed.launch` 模块将在每个节点上生成多个训练进程。

以下步骤演示了如何在 Azure ML 上使用每节点启动器配置 PyTorch 作业。  作业实现的效果等同于运行以下命令：

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. 向 `ScriptRunConfig` 构造函数的 `command` 参数提供 `torch.distributed.launch` 命令。 Azure ML 在训练群集的每个节点上运行此命令。 `--nproc_per_node` 应小于或等于每个节点上可用的 GPU 数。 MASTER_ADDR、MASTER_PORT 和 NODE_RANK 都是由 Azure ML 设置，因此可以在命令中引用环境变量。 Azure ML 将 MASTER_PORT 设置为 `6105`，但如果需要，可以将不同的值传递给 torch.distributed.launch 命令的 `--master_port` 参数。 （启动实用工具将重置环境变量。）
2. 创建 `PyTorchConfiguration` 并指定 `node_count`。

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(node_count=2)
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 4 --nnodes 2 --node_rank $NODE_RANK --master_addr $MASTER_ADDR --master_port $MASTER_PORT --use_env train.py --epochs 50".split()

run_config = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

> [!TIP]
> 单节点多 GPU 训练：如果使用启动实用工具运行单节点多 GPU PyTorch 训练，则无需指定 ScriptRunConfig 的 `distributed_job_config` 参数。
>
>```python
> launch_cmd = "python -m torch.distributed.launch --nproc_per_node 4 --use_env train.py --epochs 50".split()
>
> run_config = ScriptRunConfig(
>  source_directory='./src',
>  command=launch_cmd,
>  compute_target=compute_target,
>  environment=pytorch_env,
> )
> ```

### <a name="pytorch-per-node-launch-example"></a>PyTorch 每节点启动示例

- [azureml-examples：CIFAR-10 上使用 PyTorch 的分布式训练](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/pytorch/cifar-distributed)

### <a name="pytorch-lightning"></a>PyTorch Lightning

[PyTorch Lightning](https://pytorch-lightning.readthedocs.io/en/stable/) 是一个轻量级的开源库，为 PyTorch 提供高级接口。 Lightning 抽象出了许多原版 PyTorch 所需的较低级别的分布式训练配置。 Lightning 支持在单个 GPU、单节点多 GPU 和多节点多 GPU 设置中运行训练脚本。 Lightning 在后台启动类似于 `torch.distributed.launch` 的多个进程。

对于单节点训练（包括单节点多 GPU），可以在 Azure ML 上运行代码，无需指定 `distributed_job_config`。 对于多节点训练，Lightning 要求在训练群集的每个节点上设置以下环境变量：

- MASTER_ADDR
- MASTER_PORT
- NODE_RANK

若要在 Azure ML 上运行多节点 Lighting 训练，可以大体上遵照[每节点启动指南](#per-node-launch)：

- 定义 `PyTorchConfiguration` 并指定 `node_count`。 不要指定 `process_count`，因为 Lighting 以内部方式处理每个节点的工作进程。
- 对于 PyTorch 作业，Azure ML 将负责设置 Lighting 所需的 MASTER_ADDR、MASTER_PORT 和 NODE_RANK 环境变量。
- Lightning 将负责计算来自 Trainer 标志 `--gpus` 和 `--num_nodes` 的世界大小，并以内部方式管理排名和本地排名。

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.runconfig import PyTorchConfiguration

nnodes = 2
args = ['--max_epochs', 50, '--gpus', 2, '--accelerator', 'ddp', '--num_nodes', nnodes]
distr_config = PyTorchConfiguration(node_count=nnodes)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=args,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

### <a name="pytorch-lightning-example"></a>PyTorch Lightning 示例

* [azureml-examples：使用 PyTorch Lightning 的多节点训练](https://github.com/Azure/azureml-examples/blob/main/python-sdk/experimental/using-pytorch-lightning/4.train-multi-node-ddp.ipynb)

### <a name="hugging-face-transformers"></a>Hugging Face Transformers

Hugging Face 针对将其 Transformers 库与 `torch.distributed.launch` 结合使用来运行分布式训练，提供了很多[示例](https://github.com/huggingface/transformers/tree/master/examples)。 要使用 Transformers Trainer API 运行这些示例和自己的自定义训练脚本，请遵照[使用 `torch.distributed.launch`](#per-node-launch) 一节。

作业配置代码示例，可在一个含 8 个 GPU 的节点上使用 `run_glue.py` 脚本针对文本分类 MNLI 任务微调 BERT 大模型：

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

distr_config = PyTorchConfiguration() # node_count defaults to 1
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 8 text-classification/run_glue.py --model_name_or_path bert-large-uncased-whole-word-masking --task_name mnli --do_train --do_eval --max_seq_length 128 --per_device_train_batch_size 8 --learning_rate 2e-5 --num_train_epochs 3.0 --output_dir /tmp/mnli_output".split()

run_config = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)
```

还可以使用[每进程启动](#per-process-launch)选项在不使用 `torch.distributed.launch` 的情况下运行分布式训练。 如果使用此方法，需要注意的一点是，转换器 [TrainingArguments](https://huggingface.co/transformers/main_classes/trainer.html?highlight=launch#trainingarguments) 预期将本地排名作为参数 (`--local_rank`) 来传递。 为 `--use_env=False` 时，则由 `torch.distributed.launch` 处理这种情况，但如果在使用“每进程启动”，则需要将本地排名作为参数显式传递到训练脚本 `--local_rank=$LOCAL_RANK`，因为Azure ML 仅仅设置 `LOCAL_RANK` 环境变量。

## <a name="tensorflow"></a>TensorFlow

如果在训练代码中使用[本机分布式 TensorFlow](https://www.tensorflow.org/guide/distributed_training)（比如 TensorFlow 2.x 的 `tf.distribute.Strategy` API），则可以使用 `TensorflowConfiguration` 通过 Azure ML 来启动分布式作业。

为此，请为 `ScriptRunConfig` 构造函数的 `distributed_job_config` 参数指定 `TensorflowConfiguration` 对象。 如果在使用 `tf.distribute.experimental.MultiWorkerMirroredStrategy`，请在与训练作业的节点数相对应的 `TensorflowConfiguration` 中指定 `worker_count`。

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import TensorflowConfiguration

curated_env_name = 'AzureML-TensorFlow-2.3-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  compute_target=compute_target,
  environment=tf_env,
  distributed_job_config=distr_config,
)

# submit the run configuration to start the job
run = Experiment(ws, "experiment_name").submit(run_config)
```

如果训练脚本使用参数服务器策略进行分布式训练（例如，对于传统的 TensorFlow 1.x），则还需要指定要在作业中使用的参数服务器数量，例如 `tf_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)`。

### <a name="tf_config"></a>TF_CONFIG

在 TensorFlow 中，在多台计算机上训练需要 TF_CONFIG 环境变量。 对于 TensorFlow 作业，在执行训练脚本之前，Azure ML 会相应地为每个工作器配置和设置 TF_CONFIG 变量。

如果需要，可以从训练脚本访问 TF_CONFIG：`os.environ['TF_CONFIG']`。

在主要工作器节点上设置的 TF_CONFIG 示例：
```json
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

### <a name="tensorflow-example"></a>TensorFlow 示例

- [azureml-examples：使用 MultiWorkerMirroredStrategy 的分布式 TensorFlow 训练](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/tensorflow/mnist-distributed)

## <a name="accelerating-gpu-training-with-infiniband"></a><a name="infiniband"></a> 使用 InfiniBand 加速 GPU 训练

某些 Azure VM 系列，特别是 NC、ND 和 H 系列，现在具有即支持 RDMA 功能又支持 SR-IOV 和 Infiniband 的 VM。 这些 VM 在低延迟、高带宽的 InfiniBand 网络上进行通信，这比基于以太网的连接的性能更高。 适用于 InfiniBand 的 SR-IOV 可为任何 MPI 库提供接近裸机的性能（MPI 被许多分布式训练框架和工具采用，包括 NVIDIA 的 NCCL 软件）。这些 SKU 旨在满足计算密集型、GPU 加速的机器学习工作负荷的需求。 有关详细信息，请参阅[在 Azure 机器学习中采用 SR-IOV 加速分布式训练](https://techcommunity.microsoft.com/t5/azure-ai/accelerating-distributed-training-in-azure-machine-learning/ba-p/1059050)。

如果创建了其中一个即支持 RDMA 功能又支持 InfiniBand 大小的 `AmlCompute` 群集（如 `Standard_ND40rs_v2`），则操作系统映像将附带启用预安装和预配置 InfiniBand 所需的 Mellanox OFED 驱动程序。

## <a name="next-steps"></a>后续步骤

* [将机器学习模型部署到 Azure](how-to-deploy-and-where.md)
* [使用托管联机终结点部署机器学习模型并为其评分（预览版）](how-to-deploy-managed-online-endpoints.md)
* [Azure 中分布式深度学习训练的参考体系结构](/azure/architecture/reference-architectures/ai/training-deep-learning)
