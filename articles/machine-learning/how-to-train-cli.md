---
title: 使用 CLI (v2) 训练模型（创建作业）
titleSuffix: Azure Machine Learning
description: 了解如何使用用于机器学习的 Azure CLI 扩展训练模型（创建作业）。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 06/18/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 9f3a91f9abc472f285139bfac04af7dff5c63e9f
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444555"
---
# <a name="train-models-create-jobs-with-the-cli-v2"></a>使用 CLI (v2) 训练模型（创建作业）

Azure 机器学习 CLI (v2) 是一个 Azure CLI 扩展，可以加速模型训练过程，同时可以在 Azure 计算中横向和纵向扩展，并对模型生命周期进行跟踪和审核。

训练机器学习模型通常是一个迭代过程。 使用新式工具能够更快捷轻松地基于更多数据训练更大的模型。 以前繁琐的手动过程（如超参数优化，甚至算法选择）现在通常都可以自动完成。 借助 Azure 机器学习 CLI，可以使用超参数扫描来跟踪[工作区](concept-workspace.md)中的作业（和模型）、在高性能 Azure 计算中纵向扩展，并利用分布式训练进行横向扩展。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>先决条件

- 若要使用 CLI，必须拥有 Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。
- [安装并设置用于机器学习的 Azure CLI 扩展](how-to-configure-cli.md)

> [!TIP]
> 在全功能的开发环境中，可以使用 Visual Studio Code 和 [Azure 机器学习扩展](how-to-setup-vs-code.md)来[管理Azure 机器学习资源](how-to-manage-resources-vscode.md)和[训练机器学习模型](tutorial-train-deploy-image-classification-model-vscode.md)。

### <a name="clone-examples-repository"></a>克隆示例存储库

若要运行训练示例，请首先克隆示例存储库，然后更改为 `cli` 目录：

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="git_clone":::

请注意 `--depth 1` 仅克隆最新提交到目录，这将减少操作完成的时间。

### <a name="create-compute"></a>创建计算

可以从命令行创建 Azure 机器学习计算群集。 例如，以下命令创建一个名为 `cpu-cluster` 的群集和一个名为 `gpu-cluster` 的群集。

:::code language="azurecli" source="~/azureml-examples-main/cli/create-compute.sh" id="create_computes":::

请注意，此时你无需支付计算费用，因为在提交作业之前，`cpu-cluster` 和 `gpu-cluster` 将保留在 0 个节点上。 详细了解如何[管理和优化 AmlCompute 的成本](how-to-manage-optimize-cost.md#use-azure-machine-learning-compute-cluster-amlcompute)。

本文的以下示例作业使用 `cpu-cluster` 或 `gpu-cluster` 之一。 根据需要将这些设置调整为群集名称。

使用 `az ml compute create -h` 了解有关计算创建选项的更多详细信息。

[!INCLUDE [arc-enabled-kubernetes](../../includes/machine-learning-create-arc-enabled-training-computer-target.md)]

## <a name="introducing-jobs"></a>作业简介

对于 Azure 机器学习 CLI (v2)，作业是以 YAML 格式创作的。 作业聚合：

- 运行内容
- 运行方式
- 运行位置

“hello world”作业包含所有三项：

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/misc/hello-world.yml":::

可运行的对象：

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world":::

不过，这只是一个示例作业，它只会在日志文件中输出一行，但不会输出其他任何内容。 除了系统生成的日志外，你通常还想要生成其他项目，例如模型二进制文件和随附的元数据。

Azure 机器学习自动捕获以下项目：

- `./outputs` 和 `./logs` 目录受到 Azure机器学习的特殊对待。 如果在运行作业期间将任何文件写入这些目录，这些文件将上传到作业的运行历史记录，这样，在该作业完成后，你仍可以访问这些文件。 作业完成时将上传 `./outputs` 文件夹，而写入 `./logs` 的文件将实时上传。 如果你希望在运行作业期间流式传输日志（例如 TensorBoard 日志），请使用后一种上传方式。
- Azure 机器学习与 MLflow 的跟踪功能集成。 可以对多个常用 ML 框架使用 `mlflow.autolog()` 来记录模型参数、性能指标、模型项目甚至特征重要性图形。 还可以使用 `mlflow.log_*()` 方法显式记录参数、指标和项目。 MLflow 记录的所有指标和项目将保存在作业的运行历史记录中。

通常，作业涉及到运行一些在本地编辑和控制的源代码。 可以指定要包含在作业中的源代码目录，命令将从该目录运行。

例如，查看示例存储库中的 `jobs/train/lightgbm/iris` 项目目录：

```tree
.
├── job-sweep.yml
├── job.yml
└── src
    └── main.py
```

此目录包含两个作业文件和一个源代码子目录 (`src`)。 虽然此示例仅在 `src` 下包含单个文件，但整个子目录将以递归方式上传，并可供在作业中使用。

命令作业是通过 `job.yml` 配置的：

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job.yml":::

可运行的对象：

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="lightgbm_iris":::

## <a name="basic-python-training-job"></a>基本 Python 训练作业

让我们详细查看作业 YAML 文件：

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job.yml":::

| 密钥 | 说明 |
| --- | ----------- |
| `$schema` | [可选] YAML 架构。 可以在浏览器中查看上述示例中的[架构](https://azuremlschemas.azureedge.net/latest/commandJob.schema.json)，以查看命令作业 YAML 文件的所有可用选项。 如果使用 Azure 机器学习 VS Code 扩展来创作 YAML 文件，那么通过在该文件的顶部包含此 `$schema` 属性可以调用架构和资源完成。 |
| `code.local_path` | [可选] 要上传并用于作业的源目录的本地路径（相对于 YAML 文件）。 请考虑在作业文件所在的同一目录中使用 `src`，以保持一致。 |
| `command` | 要执行的命令。 `>` 约定通过将换行符精简为空格，使用户可以创作易读的多行命令。 可以将命令行参数显式写入到命令中，或者使用大括号表示法从其他节（具体而言，是 `inputs`或 `search_space`）推断这些参数。 |
| `inputs` | [可选] 输入数据绑定的字典，其中的键是你为输入绑定指定的名称。 每个元素的值是输入绑定，其中包含 `data` 和 `mode` 字段。 `data` 可以是：1) 使用 `azureml:` 前缀对现有版本受控的 Azure 机器学习数据资产的引用（例如，`azureml:iris-url:1` 指向名为“iris-url”的数据资产的版本 1）；或 2) 内联的数据定义。 使用 `data.path` 指定云位置。 使用 `data.local_path` 指定本地文件系统中的数据，这些数据将上传到默认数据存储。 `mode` 指示如何在计算中为作业提供数据。 “mount”和“download”是两个支持的选项。 <br><br> 可以在命令中按名称来引用输入，例如 `{inputs.my_input_name}`。 然后，在运行时期间，Azure 机器学习会将命令中的参数化表示法解析为该数据在计算目标上的位置。 例如，如果数据配置为有待装载，则 `{inputs.my_input_name}` 将解析为装入点。 |
| `environment` | 用于在计算目标上执行命令的环境。 可以通过指定要使用的 Docker 映像或者用于生成映像的 Dockerfile，以内联方式定义环境。 还可以引用工作区中现有的版本受控环境，或者使用 `azureml:` 前缀来引用 Azure ML 的特选环境之一。 例如，`azureml:AzureML-TensorFlow2.4-Cuda11-OpenMpi4.1.0-py36:1` 将引用支持 GPU 的 TensorFlow 的特选环境版本 1。 <br><br> 在用于训练的环境中必须安装 Python。 请根据需要在 Dockerfile 中运行 `apt-get update -y && apt-get install python3 -y` 进行安装。 |
| `compute.target` | 计算目标。 为本地执行指定 `local`，或使用 `azureml:` 前缀来引用工作区中的现有计算资源。 例如，`azureml:cpu-cluster` 将指向名为“cpu-cluster”的计算目标。 |
| `experiment_name` | [可选] 标记作业以更方便地在 Azure 机器学习工作室中进行组织。 每个作业的运行记录将在工作室的“试验”选项卡中的相应试验下进行组织。如果省略此项，则默认按照创建作业的工作目录的名称进行组织。 |
| `name` | [可选] 作业的名称，必须在工作区中的所有作业之间保持唯一。 除非在 YAML 文件中通过 `name` 字段或者在命令行中通过 `--name/-n` 指定了名称，否则系统会自动生成 GUID/UUID 并将其用作名称。 作业名称对应于作业运行记录的工作室 UI 中的“运行 ID”。 |

创建此作业会上传所有指定的本地资产（如源代码目录）、验证 YAML 文件并提交运行。 系统会根据需要生成环境，然后对计算进行纵向扩展和配置，以便运行作业。

若要运行 lightgbm/iris 训练作业，请执行以下命令：

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="lightgbm_iris":::

作业完成后，可以下载输出：

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="download_outputs":::

> [!IMPORTANT]
> 请将 `$run_id` 替换为你的运行 ID，可以在控制台输出或工作室的运行详细信息页中找到该 ID。

这会将日志和捕获的任何项目在本地下载到名为 `$run_id` 的目录。 对于此示例，将下载 MLflow 记录的模型子目录。

## <a name="sweep-hyperparameters"></a>扫描超参数

Azure 机器学习还可让你更有效地优化机器学习模型的超参数。 可以配置一个超参数优化作业（称为扫描作业），然后通过 CLI 提交该作业。

可将 `job.yml` 修改为 `job-sweep.yml` 以扫描超参数：

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job-sweep.yml":::

| 密钥 | 说明 |
| --- | ----------- |
| `$schema` | [可选] 已更改的 YAML 架构现在指向扫描作业[架构](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json)。 |
| `type` | 作业类型。 |
| `algorithm` | 采样算法 -“random”通常是适当的选择。 请参阅架构以查看选项的列表。 |
| `trial` | 要运行的每个试验的命令作业配置。 上一示例中的命令 (`trial.command`) 已修改为使用 `{search_space.<hyperparameter_name>}` 表示法来引用 `search_space` 中定义的超参数。 然后，Azure 机器学习会将每个参数化表示法解析为它为每个试验生成的相应超参数的值。 |
| `search_space` | 要扫描的超参数的字典。 键是超参数的名称，例如 `search_space.learning_rate`。 请注意，该名称不一定要与训练脚本的参数本身相匹配，而只需与命令中的大括号表示法中的搜索空间引用（例如 `{search_space.learning_rate}`）相匹配。 值是超参数分布。 请参阅架构以查看选项的列表。 |
| `objective.primary_metric` | 优化指标，必须与训练代码中记录的指标的名称相匹配。 `objective.goal` 指定方向（“minimize”/“maximize”）。 请参阅架构以查看完整的选项列表。 |
| `max_total_trials` | 要运行的各个试验的最大数目。 |
| `max_concurrent_trials` | [可选] 在计算群集上并发运行的试验的最大数目。 |
| `timeout_minutes` | [可选] 运行扫描作业的最大分钟数。 |
| `experiment_name` | [可选] 要在其中跟踪扫描作业的试验。 如果省略此项，则在创建作业时，默认将使用工作目录的名称。 |

在工作室中创建并打开作业：

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="lightgbm_iris_sweep":::

> [!TIP]
> 超参数扫描可与分布式命令作业配合使用。

## <a name="distributed-training"></a>分布式训练

可以在命令作业中指定 `distribution` 节。 Azure ML 支持对 PyTorch、Tensorflow 和 MPI 兼容的框架进行分布式训练。 PyTorch 和 TensorFlow 原生支持对各自的框架（例如 TensorFlow 的 `tf.distributed.Strategy` API）进行分布式训练。

请务必将 `compute.instance_count`（默认值为 1）设置为作业所需的节点数。

### <a name="pytorch"></a>PyTorch

用于对 CIFAR-10 数据集进行分布式 PyTorch 训练的示例 YAML 文件：

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/pytorch/cifar-distributed/job.yml":::

请注意，此项引用克隆的示例存储库中不存在的本地数据。 首先需要在本地下载、提取并重新放置 CIFAR-10 数据集，应将其放置在项目目录中的适当位置：

:::code language="bash" source="~/azureml-examples-main/cli/train.sh" id="download_cifar":::

在工作室中创建并打开作业：

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="pytorch_cifar":::

### <a name="tensorflow"></a>TensorFlow

用于对 MNIST 数据集进行分布式 TensorFlow 训练的示例 YAML 文件：

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/tensorflow/mnist-distributed/job.yml":::

在工作室中创建并打开作业：

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="tensorflow_mnist":::

### <a name="mpi"></a>MPI

Azure ML 支持跨多个节点以及每个节点的多个进程启动 MPI 作业。 它通过 `mpirun` 启动作业。 例如，如果训练代码使用 Horovod 框架进行分布式训练，则你可以利用此作业类型在 Azure ML 中训练。

若要启动 MPI 作业，请在 `distribution` 节中指定 `mpi` 作为类型，并指定每个节点的要启动的进程数 (`process_count_per_instance`)。 如果不指定此字段，Azure ML 默认将启动每个节点的一个进程。

一个使用 Horovod 对 MNIST 运行 TensorFlow 作业的示例 YAML 规范：

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/tensorflow/mnist-distributed-horovod/job.yml":::

在工作室中创建并打开作业：

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="tensorflow_mnist_horovod":::

## <a name="next-steps"></a>后续步骤

- [使用托管联机终结点（预览版）部署机器学习模型并为其评分](how-to-deploy-managed-online-endpoints.md)
- [使用 REST 训练模型（预览版）](how-to-train-with-rest.md)
