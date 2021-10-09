---
title: 排查 ParallelRunStep 问题
titleSuffix: Azure Machine Learning
description: 有关在机器学习管道中使用 ParallelRunStep 出现错误时如何进行故障排除的提示。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: pansav
author: psavdekar
ms.date: 09/23/2020
ms.openlocfilehash: 30a23150fc3d3de18cdde48c9ab07743613c5a15
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426257"
---
# <a name="troubleshooting-the-parallelrunstep"></a>排查 ParallelRunStep 问题

本文介绍在 [Azure 机器学习 SDK](/python/api/overview/azure/ml/intro) 中使用 [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) 出现错误时如何进行故障排除。

有关对管道进行故障排除的一般提示，请参阅[对机器学习管道进行故障排除](how-to-debug-pipelines.md)。

## <a name="testing-scripts-locally"></a>在本地测试脚本

 ParallelRunStep 作为 ML 管道中的一个步骤运行。 第一步，需要[在本地测试脚本](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)。

## <a name="entry-script-requirements"></a>入口脚本要求

`ParallelRunStep` 的入口脚本必须包含 `run()` 函数，还可以选择包含 `init()` 函数：
- `init()`：此函数适用于后续处理的任何成本高昂或常见的准备工作。 例如，使用它将模型加载到全局对象。 此函数将在进程开始时调用一次。
    > [!NOTE]
    > 如果 `init` 方法创建输出目录，请指定 `parents=True` 和 `exist_ok=True`。 每个运行该作业的节点上的每个工作进程都会调用 `init` 方法。
-  `run(mini_batch)`：将针对每个 `mini_batch` 实例运行此函数。
    -  `mini_batch``ParallelRunStep` 将调用 run 方法，并将列表或 pandas `DataFrame` 作为参数传递给该方法。 如果输入是 `FileDataset`，则 mini_batch 中的每个条目都将是文件路径；如果输入是 `TabularDataset`，则是 pandas `DataFrame`。
    -  `response`：run() 方法应返回 pandas `DataFrame` 或数组。 对于 append_row output_action，这些返回的元素将追加到公共输出文件中。 对于 summary_only，将忽略元素的内容。 对于所有的输出操作，每个返回的输出元素都指示输入微型批处理中输入元素的一次成功运行。 确保运行结果中包含足够的数据，以便将输入映射到运行输出结果。 运行输出将写入输出文件中，并且不保证按顺序写入，你应使用输出中的某个键将其映射到输入。
        > [!NOTE]
        > 一个输入元素应该对应一个输出元素。

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

如果推理脚本所在的同一目录中包含另一个文件或文件夹，可以通过查找当前工作目录来引用此文件或文件夹。

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>ParallelRunConfig 的参数

`ParallelRunConfig` 是 `ParallelRunStep` 实例在 Azure 机器学习管道中的主要配置。 使用它来包装脚本并配置所需的参数，包括所有以下条目：
- `entry_script`：作为将在多个节点上并行运行的本地文件路径的用户脚本。 如果 `source_directory` 存在，则使用相对路径。 否则，请使用计算机上可访问的任何路径。
- `mini_batch_size`：传递给单个 `run()` 调用的微型批处理的大小。 （可选；默认值对于 `FileDataset` 是 `10` 个文件，对应 `TabularDataset` 是 `1MB`。）
    - 对于 `FileDataset`，它是最小值为 `1` 的文件数。 可以将多个文件合并成一个微型批处理。
    - 对于 `TabularDataset`，它是数据的大小。 示例值为 `1024`、`1024KB`、`10MB` 和 `1GB`。 建议值为 `1MB`。 `TabularDataset` 中的微批永远不会跨越文件边界。 例如，如果你有各种大小的 .csv 文件，最小的文件为 100 KB，最大的文件为 10 MB。 如果设置 `mini_batch_size = 1MB`，则大小小于 1 MB 的文件将被视为一个微型批处理。 大小大于 1 MB 的文件将被拆分为多个微型批处理。
        > [!NOTE]
        > 不能对 SQL 支持的 TabularDataset 进行分区。
        > 无法对单个 parquet 文件和单个行组中的 TabularDataset 进行分区。

- `error_threshold`：在处理过程中应忽略的 `TabularDataset` 记录失败数和 `FileDataset` 文件失败数。 如果整个输入的错误计数超出此值，则作业将中止。 错误阈值适用于整个输入，而不适用于发送给 `run()` 方法的单个微型批处理。 范围为 `[-1, int.max]`。 `-1` 部分指示在处理过程中忽略所有失败。
- `output_action`：以下值之一指示将如何组织输出：
    - `summary_only`：用户脚本将存储输出。 `ParallelRunStep` 仅将输出用于错误阈值计算。
    - `append_row`：对于所有输入，仅在输出文件夹中创建一个文件来追加所有按行分隔的输出。
- `append_row_file_name`：用于自定义 append_row output_action 的输出文件名（可选；默认值为 `parallel_run_step.txt`）。
- `source_directory`：文件夹的路径，这些文件夹包含要在计算目标上执行的所有文件（可选）。
- `compute_target`：仅支持 `AmlCompute`。
- `node_count`：用于运行用户脚本的计算节点数。
- `process_count_per_node`：每个节点并行运行入口脚本的工作进程数。 对于 GPU 计算机，默认值为 1。 对于 CPU 计算机，默认值是每个节点的核心数。 工作进程会通过传递它获取的微型批来反复调用 `run()`。 作业中的工作进程总数为 `process_count_per_node * node_count`，这个数字决定了要并行执行的 `run()` 的最大数目。
- `environment`：Python 环境定义。 可以将其配置为使用现有的 Python 环境或设置临时环境。 定义还负责设置所需的应用程序依赖项（可选）。
- `logging_level`：日志详细程度。 递增详细程度的值为：`WARNING`、`INFO` 和 `DEBUG`。 （可选；默认值为 `INFO`）
- `run_invocation_timeout`：`run()` 方法调用超时（以秒为单位）。 （可选；默认值为 `60`）
- `run_max_try`：微型批处理的 `run()` 的最大尝试次数。 如果引发异常，则 `run()` 失败；如果达到 `run_invocation_timeout`，则不返回任何内容（可选；默认值为 `3`）。

可以指定 `mini_batch_size`、`node_count`、`process_count_per_node`、`logging_level`、`run_invocation_timeout` 和 `run_max_try` 作为 `PipelineParameter` 以便在重新提交管道运行时，可以微调参数值。 在本例中，你将 `PipelineParameter` 用于 `mini_batch_size` 和 `Process_count_per_node`。重新提交另一运行时，需更改这些值。

#### <a name="cuda-devices-visibility"></a>CUDA 设备可见性
对于配备 GPU 的计算目标，将在工作进程中设置环境变量 `CUDA_VISIBLE_DEVICES`。 在 AmlCompute 中，可以在环境变量 `AZ_BATCHAI_GPU_COUNT_FOUND` 中查找 GPU设备的总数，它是自动设置的。 如果希望每个工作进程都有专用 GPU，请将 `process_count_per_node` 设置为等于计算机上 GPU 设备的数量。 每个工作进程都将为 `CUDA_VISIBLE_DEVICES` 分配唯一索引。 如果工作进程因任何原因停止，则下一个启动的工作进程将使用已发布的 GPU 索引。

如果 GPU 设备的总数小于 `process_count_per_node`，则工作进程将会被分配 GPU 索引，直至使用完所有索引。

假设 GPU 设备总数为 2，并以 `process_count_per_node = 4` 为例，进程 0 和进程 1 将具有索引 0 和 1。 进程 2 和 3 将不会有环境变量。 对于使用此环境变量进行 GPU 分配的库，进程 2 和 3 将不会有 GPU，也不会尝试获取 GPU 设备。 如果进程 0 停止，它将会释放 GPU 索引 0。 下一个进程（即进程 4）将会被分配 GPU 索引 0。

有关详细信息，请参阅 [CUDA Pro 提示：使用CUDA_VISIBLE_DEVICES 控制 GPU 可见性](https://developer.nvidia.com/blog/cuda-pro-tip-control-gpu-visibility-cuda_visible_devices/)。

### <a name="parameters-for-creating-the-parallelrunstep"></a>用于创建 ParallelRunStep 的参数

使用脚本、环境配置和参数创建 ParallelRunStep。 将已附加到工作区的计算目标指定为推理脚本的执行目标。 使用 `ParallelRunStep` 创建批处理推理管道步骤，该步骤采用以下所有参数：
- `name`：步骤的名称，但具有以下命名限制：唯一、3-32 个字符和正则表达式 ^\[a-z\]([-a-z0-9]*[a-z0-9])?$。
- `parallel_run_config`：`ParallelRunConfig` 对象，如前文所述。
- `inputs`：要分区以进行并行处理的一个或多个单类型 Azure 机器学习数据集。
- `side_inputs`：无需分区就可以用作辅助输入的一个或多个参考数据或数据集。
- `output`：一个 `OutputFileDatasetConfig` 对象，代表用于存储输出数据的目录路径。
- `arguments`：传递给用户脚本的参数列表。 使用 unknown_args 在入口脚本中检索它们（可选）。
- `allow_reuse`：当使用相同的设置/输入运行时，该步骤是否应重用以前的结果。 如果此参数为 `False`，则在管道执行过程中将始终为此步骤生成新的运行。 （可选；默认值为 `True`。）

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-scripts-from-remote-context"></a>从远程上下文调试脚本

要实现从在本地调试评分脚本到在实际管道中调试评分脚本这一飞跃可能很困难。 若要了解如何在门户中查找日志，请参阅[有关从远程上下文调试脚本的机器学习管道部分](how-to-debug-pipelines.md)。 该部分中的信息也适用于 ParallelRunStep。

例如，日志文件 `70_driver_log.txt` 包含来自启动 ParallelRunStep 代码的控制器的信息。

由于 ParallelRunStep 作业具有分布式特性，因此存在来自多个不同源的日志。 但是，会创建两个合并文件来提供高级信息：

- `~/logs/job_progress_overview.txt`：此文件提供了有关到目前为止已创建的微型批处理数（也称为任务数）以及已处理的微型批处理数的高级信息。 最后，它会显示作业的结果。 如果作业失败，它将显示错误消息以及开始进行故障排除的位置。

- `~/logs/sys/master_role.txt`：此文件提供运行中作业的主节点（也称为业务流程协调程序）视图。 包括任务创建、进度监视和运行结果。

使用 EntryScript 帮助程序和 print 语句，通过入口脚本生成的日志将显示在以下文件中：

- `~/logs/user/entry_script_log/<node_id>/<process_name>.log.txt`：这些文件是使用 EntryScript 帮助程序从 entry_script 写入的日志。

- `~/logs/user/stdout/<node_id>/<process_name>.stdout.txt`：这些文件是 entry_script 的 stdout（例如，print 语句）的日志。

- `~/logs/user/stderr/<node_id>/<process_name>.stderr.txt`：这些文件是 entry_script 的 stderr 的日志。

要简要了解脚本中的错误，请参阅以下文件：

- `~/logs/user/error.txt`：此文件将尝试汇总脚本中的错误。

有关脚本中错误的详细信息，请参阅以下文件：

- `~/logs/user/error/`：包含加载和运行入口脚本时引发的异常的完整堆栈跟踪。

如需全面了解每个节点如何执行评分脚本，请查看每个节点单独的进程日志。 进程日志位于 `sys/node` 文件夹中，按工作器节点分组：

- `~/logs/sys/node/<node_id>/<process_name>.txt`：此文件提供有关每个微型批处理在工作器拾取或完成它时的详细信息。 对于每个微型批处理，此文件包括以下内容：

    - 工作进程的 IP 地址和 PID。
    - 总项数、成功处理的项计数和失败的项计数。
    - 开始时间、持续时间、处理时间和运行方法时间。

你还可以查看每个节点的资源使用情况的定期检查结果。 日志文件和安装程序文件位于以下文件夹中：

- `~/logs/perf`：设置 `--resource_monitor_interval` 以更改检查时间间隔（以秒为单位）。 默认时间间隔为 `600`，约为 10 分钟。 若要停止监视，请将值设置为 `0`。 每个 `<node_id>` 文件夹包括：

    - `os/`：节点中所有正在运行的进程的相关信息。 一项检查将运行一个操作系统命令，并将结果保存到文件。 在 Linux 上，该命令为 `ps`。 在 Windows 上，请使用 `tasklist`。
        - `%Y%m%d%H`：子文件夹名称是到精确到小时的时间。
            - `processes_%M`：文件名以检查时间的分钟结束。
    - `node_disk_usage.csv`：节点的详细磁盘使用情况。
    - `node_resource_usage.csv`：节点的资源使用情况概述。
    - `processes_resource_usage.csv`：每个进程的资源使用情况概述。

## <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>如何从远程上下文中的用户脚本记录？

ParallelRunStep 可以基于 process_count_per_node 在一个节点上运行多个进程。 为了组织节点上每个进程的日志并结合 print 和 log 语句，我们建议使用 ParallelRunStep 记录器，如下所示。 从 EntryScript 获取记录器，使日志显示在门户的 logs/user 文件夹中。

使用记录器的示例入口脚本：
```python
from azureml_user.parallel_run import EntryScript

def init():
    """Init once in a worker process."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """Call once for a mini batch. Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

## <a name="where-does-the-message-from-python-logging-sink-to"></a>消息从 Python `logging` 发送到何处？
ParallelRunStep 会在根记录器上设置一个处理程序，该程序可将消息发送到 `logs/user/stdout/<node_id>/processNNN.stdout.txt`。

`logging` 默认为 `WARNING` 级别。 默认情况下，以下级别 `WARNING` 不会显示，如 `INFO` 或 `DEBUG`。

## <a name="how-could-i-write-to-a-file-to-show-up-in-the-portal"></a>如何写入到文件，以便在门户中显示？
`logs` 文件夹中的文件将被上传并显示在门户中。
你可以获得如下所示的文件夹 `logs/user/entry_script_log/<node_id>` 并编写要写入的文件路径：

```python
from pathlib import Path
from azureml_user.parallel_run import EntryScript

def init():
    """Init once in a worker process."""
    entry_script = EntryScript()
    log_dir = entry_script.log_dir
    log_dir = Path(entry_script.log_dir)  # logs/user/entry_script_log/<node_id>/.
    log_dir.mkdir(parents=True, exist_ok=True) # Create the folder if not existing.

    proc_name = entry_script.agent_name  # The process name in pattern "processNNN".
    fil_path = log_dir / f"{proc_name}_<file_name>" # Avoid conflicting among worker processes with proc_name.
```

## <a name="how-to-handle-log-in-new-processes"></a>如何处理新进程中的日志？
可以通过 [`subprocess`](https://docs.python.org/3/library/subprocess.html) 模块生成新的进程，连接到它们的输入/输出/错误管道并获取其返回代码。

建议的方法是在 `capture_output=True` 中使用 [`run()`](https://docs.python.org/3/library/subprocess.html#subprocess.run) 函数。 错误将显示在 `logs/user/error/<node_id>/<process_name>.txt` 中。

如果要使用 `Popen()`，应将 stdout/stderr 重定向到文件，如下所示：
```python
from pathlib import Path
from subprocess import Popen

from azureml_user.parallel_run import EntryScript


def init():
    """Show how to redirect stdout/stderr to files in logs/user/entry_script_log/<node_id>/."""
    entry_script = EntryScript()
    proc_name = entry_script.agent_name  # The process name in pattern "processNNN".
    log_dir = Path(entry_script.log_dir)  # logs/user/entry_script_log/<node_id>/.
    log_dir.mkdir(parents=True, exist_ok=True) # Create the folder if not existing.
    stdout_file = str(log_dir / f"{proc_name}_demo_stdout.txt")
    stderr_file = str(log_dir / f"{proc_name}_demo_stderr.txt")
    proc = Popen(
        ["...")],
        stdout=open(stdout_file, "w"),
        stderr=open(stderr_file, "w"),
        # ...
    )

```

> [!NOTE]
> 一个工作进程在同一进程中运行“系统”代码和条目脚本代码。
>
> 如果未指定 `stdout` 或 `stderr`，则入口脚本中通过 `Popen()` 创建的子进程将继承工作进程设置。
>
> `stdout` 将写入 `logs/sys/node/<node_id>/processNNN.stdout.txt`，而 `stderr` 将写入 `logs/sys/node/<node_id>/processNNN.stderr.txt`。


## <a name="how-do-i-write-a-file-to-the-output-directory-and-then-view-it-in-the-portal"></a>如何将文件写入到输出目录，然后在门户中查看它？

可以从 `EntryScript` 类获取输出目录并写入到该目录。 若要查看写入的文件，请在 Azure 机器学习门户中的“单步运行”视图中，选择“输出 + 日志”选项卡。选择“数据输出”链接，然后完成对话框中所述的步骤 。

在入口脚本中使用 `EntryScript`，如以下示例所示：

```python
from pathlib import Path
from azureml_user.parallel_run import EntryScript

def run(mini_batch):
    output_dir = Path(entry_script.output_dir)
    (Path(output_dir) / res1).write...
    (Path(output_dir) / res2).write...
```

## <a name="how-can-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>如何将端输入（如包含查找表的一个或多个文件）传递到所有工作器？

用户可以使用 ParalleRunStep 的 side_inputs 参数将引用数据传递给脚本。 作为 side_inputs 提供的所有数据集将装载到每个工作器节点上。 用户可以通过传递参数获取装载的位置。

构造一个包含参考数据的[数据集](/python/api/azureml-core/azureml.core.dataset.dataset)，指定本地装载路径并将其注册到工作区。 将其传递到 `ParallelRunStep` 的 `side_inputs` 参数。 此外，还可以在 `arguments` 部分中添加其路径，以便轻松访问其已装载的路径。

> [!NOTE]
> FileDataset 只用于 side_inputs。

```python
local_path = "/tmp/{}".format(str(uuid.uuid4()))
label_config = label_ds.as_named_input("labels_input").as_mount(local_path)
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

之后，可以在推理脚本中访问它（例如在 init() 方法中），如下所示：

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

## <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>如何通过服务主体身份验证使用输入数据集？
用户可以通过工作区中使用的服务主体身份验证传递输入数据集。 若要在 ParallelRunStep 中使用此类数据集，需要为其注册该数据集以构造 ParallelRunStep 配置。

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")

ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )

default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***')
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="how-to-check-progress-and-analyze-it"></a>如何检查进度和分析进度
本部分介绍如何检查 ParallelRunStep 作业的进度并检查意外行为的原因。

### <a name="how-to-check-job-progress"></a>如何检查作业进度？
在 `~/logs/job_progress_overview.<timestamp>.txt` 中，除了可以查看 StepRun 的总体状态外，还可以查看已计划的/已处理的微型批的计数以及生成输出的进度。 该文件每天轮换，你可以检查时间戳最大的文件，以了解最新信息。

### <a name="what-should-i-check-if-there-is-no-progress-for-a-while"></a>如果一段时间没有进度，应该检查什么？
可以转到 `~/logs/sys/errror` 查看是否有异常。 如果没有，则有可能是因为输入脚本耗时较长，你可以在代码中输出进度信息，以查找耗时部分，或将 `"--profiling_module", "cProfile"` 添加到 `ParallelRunStep` 的 `arguments`，以便在 `~/logs/sys/node/<node_id>` 文件夹下生成名为 `<process_name>.profile` 的配置文件。

### <a name="when-will-a-job-stop"></a>作业将何时运行？
如果未取消，该作业将会停止，状态为：
- 已完成。 如果已经为 `append_row` 模式处理了所有微型批并生成了输出。
- 已失败。 如果已超出 [`Parameters for ParallelRunConfig`](#parameters-for-parallelrunconfig) 中的 `error_threshold`，或在作业期间发生了系统错误。

### <a name="where-to-find-the-root-cause-of-failure"></a>在哪里查找到失败的根本原因？
可以按照 `~logs/job_result.txt` 中的线索来查找原因和详细的错误日志。

### <a name="will-node-failure-impact-the-job-result"></a>节点故障是否会影响作业结果？
如果在指定的计算群集中有其他可用节点，则不会影响。 业务流程协调程序将会启动新节点作为替代节点，并且 ParallelRunStep 对于此类操作是可复原的。

### <a name="what-happens-if-init-function-in-entry-script-fails"></a>如果入口脚本中的 `init` 函数失败，会发生什么情况？
ParallelRunStep 具有重试特定次数的机制，以提供从暂时性问题恢复的可能性，这样不会使作业失败延迟过久，该机制如下所示：
1. 如果在节点启动后，所有代理上的 `init` 仍然失败，我们将会在发生 `3 * process_count_per_node` 次故障后停止尝试。
2. 如果在作业启动后，所有节点的所有代理上的 `init` 都继续失败，我们将在作业运行时间超过 2 分钟并且出现 `2 * node_count * process_count_per_node` 次故障后停止尝试。
3. 如果所有代理都在 `init` 上停滞超过 `3 * run_invocation_timeout + 30` 秒，则该作业会由于长时间没有进度而失败。

### <a name="what-will-happen-on-outofmemory-how-can-i-check-the-cause"></a>出现 OutOfMemory 时会发生什么情况？ 如何检查原因？
ParallelRunStep 会将当前用于处理微型批的尝试设置为失败状态，并尝试重启失败的进程。 可以检查 `~logs/perf/<node_id>` 以查找消耗内存的进程。

### <a name="why-do-i-have-a-lot-of-processnnn-files"></a>为什么有很多 processNNN 文件？
ParallelRunStep 将启动新的工作进程来替换异常退出的工作进程，并且每个进程都将生成 `processNNN` 文件作为日志。 但是，如果该进程由于用户脚本的 `init` 函数期间出现异常而失败，并且该错误持续重复 `3 * process_count_per_node` 次，则不会启动新工作进程。

## <a name="next-steps"></a>后续步骤

* 请参阅这些[展示了 Azure 机器学习管道的 Jupyter 笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)

* 请查看 SDK 参考，获取有关 [azureml-pipeline-steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) 包的帮助。

* 查看有关 ParallelRunConfig 类的参考[文档](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig)和 ParallelRunStep 类[文档](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep)。

* 按照[高级教程](tutorial-pipeline-batch-scoring-classification.md)操作，将管道与 ParallelRunStep 配合使用。 本教程演示如何将另一个文件作为旁路输入。
