---
title: 排查 ParallelRunStep 问题
titleSuffix: Azure Machine Learning
description: 有关在机器学习管道中使用 ParallelRunStep 出现错误时如何进行故障排除的提示。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: pansav
author: psavdekar
ms.date: 09/23/2020
ms.openlocfilehash: 619123cc2723fcf8e4bd80410c6b098b113d61c6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286311"
---
# <a name="troubleshooting-the-parallelrunstep"></a>排查 ParallelRunStep 问题

本文介绍在 [Azure 机器学习 SDK](/python/api/overview/azure/ml/intro) 中使用 [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) 出现错误时如何进行故障排除。

有关对管道进行故障排除的一般提示，请参阅[对机器学习管道进行故障排除](how-to-debug-pipelines.md)。

## <a name="testing-scripts-locally"></a>在本地测试脚本

 ParallelRunStep 作为 ML 管道中的一个步骤运行。 第一步，需要[在本地测试脚本](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)。

##  <a name="script-requirements"></a>脚本要求

`ParallelRunStep` 的脚本必须包含两个函数：
- `init()`：此函数适用于后续推理的任何成本高昂或常见的准备工作。 例如，使用它将模型加载到全局对象。 此函数将在进程开始时调用一次。
-  `run(mini_batch)`：将针对每个 `mini_batch` 实例运行此函数。
    -  `mini_batch``ParallelRunStep` 将调用 run 方法，并将列表或 pandas `DataFrame` 作为参数传递给该方法。 如果输入是 `FileDataset`，则 mini_batch 中的每个条目都将是文件路径；如果输入是 `TabularDataset`，则是 pandas `DataFrame`。
    -  `response`：run() 方法应返回 pandas `DataFrame` 或数组。 对于 append_row output_action，这些返回的元素将追加到公共输出文件中。 对于 summary_only，将忽略元素的内容。 对于所有的输出操作，每个返回的输出元素都指示输入微型批处理中输入元素的一次成功运行。 确保运行结果中包含足够的数据，以便将输入映射到运行输出结果。 运行输出将写入输出文件中，并且不保证按顺序写入，你应使用输出中的某个键将其映射到输入。

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

- `error_threshold`：在处理过程中应忽略的 `TabularDataset` 记录失败数和 `FileDataset` 文件失败数。 如果整个输入的错误计数超出此值，则作业将中止。 错误阈值适用于整个输入，而不适用于发送给 `run()` 方法的单个微型批处理。 范围为 `[-1, int.max]`。 `-1` 部分指示在处理过程中忽略所有失败。
- `output_action`：以下值之一指示将如何组织输出：
    - `summary_only`：用户脚本将存储输出。 `ParallelRunStep` 仅将输出用于错误阈值计算。
    - `append_row`：对于所有输入，仅在输出文件夹中创建一个文件来追加所有按行分隔的输出。
- `append_row_file_name`：用于自定义 append_row output_action 的输出文件名（可选；默认值为 `parallel_run_step.txt`）。
- `source_directory`：文件夹的路径，这些文件夹包含要在计算目标上执行的所有文件（可选）。
- `compute_target`：仅支持 `AmlCompute`。
- `node_count`：用于运行用户脚本的计算节点数。
- `process_count_per_node`：每个节点的进程数。 最佳做法是设置为一个节点具有的 GPU 或 CPU 数量（可选；默认值为 `1`）。
- `environment`：Python 环境定义。 可以将其配置为使用现有的 Python 环境或设置临时环境。 定义还负责设置所需的应用程序依赖项（可选）。
- `logging_level`：日志详细程度。 递增详细程度的值为：`WARNING`、`INFO` 和 `DEBUG`。 （可选；默认值为 `INFO`）
- `run_invocation_timeout`：`run()` 方法调用超时（以秒为单位）。 （可选；默认值为 `60`）
- `run_max_try`：微型批处理的 `run()` 的最大尝试次数。 如果引发异常，则 `run()` 失败；如果达到 `run_invocation_timeout`，则不返回任何内容（可选；默认值为 `3`）。 

可以指定 `mini_batch_size`、`node_count`、`process_count_per_node`、`logging_level`、`run_invocation_timeout` 和 `run_max_try` 作为 `PipelineParameter` 以便在重新提交管道运行时，可以微调参数值。 在本例中，你将 `PipelineParameter` 用于 `mini_batch_size` 和 `Process_count_per_node`。重新提交另一运行时，需更改这些值。 

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

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`：这些文件是使用 EntryScript 帮助程序从 entry_script 写入的日志。

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`：这些文件是 entry_script 的 stdout（例如，print 语句）的日志。

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`：这些文件是 entry_script 的 stderr 的日志。

要简要了解脚本中的错误，请参阅以下文件：

- `~/logs/user/error.txt`：此文件将尝试汇总脚本中的错误。

有关脚本中错误的详细信息，请参阅以下文件：

- `~/logs/user/error/`：包含加载和运行入口脚本时引发的异常的完整堆栈跟踪。

如需全面了解每个节点如何执行评分脚本，请查看每个节点单独的进程日志。 进程日志位于 `sys/node` 文件夹中，按工作器节点分组：

- `~/logs/sys/node/<ip_address>/<process_name>.txt`：此文件提供有关每个微型批处理在工作器拾取或完成它时的详细信息。 对于每个微型批处理，此文件包括以下内容：

    - 工作进程的 IP 地址和 PID。 
    - 总项数、成功处理的项计数和失败的项计数。
    - 开始时间、持续时间、处理时间和运行方法时间。

你还可以查看每个节点的资源使用情况的定期检查结果。 日志文件和安装程序文件位于以下文件夹中：

- `~/logs/perf`：设置 `--resource_monitor_interval` 以更改检查时间间隔（以秒为单位）。 默认时间间隔为 `600`，约为 10 分钟。 若要停止监视，请将值设置为 `0`。 每个 `<ip_address>` 文件夹包括：

    - `os/`：节点中所有正在运行的进程的相关信息。 一项检查将运行一个操作系统命令，并将结果保存到文件。 在 Linux 上，该命令为 `ps`。 在 Windows 上，请使用 `tasklist`。
        - `%Y%m%d%H`：子文件夹名称是到精确到小时的时间。
            - `processes_%M`：文件名以检查时间的分钟结束。
    - `node_disk_usage.csv`：节点的详细磁盘使用情况。
    - `node_resource_usage.csv`：节点的资源使用情况概述。
    - `processes_resource_usage.csv`：每个进程的资源使用情况概述。

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>如何从远程上下文中的用户脚本记录？

ParallelRunStep 可以基于 process_count_per_node 在一个节点上运行多个进程。 为了组织节点上每个进程的日志并结合 print 和 log 语句，我们建议使用 ParallelRunStep 记录器，如下所示。 从 EntryScript 获取记录器，使日志显示在门户的 logs/user 文件夹中。

使用记录器的示例入口脚本：
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>如何将端输入（如包含查找表的单个或多个文件）传递到所有工作器？

用户可以使用 ParalleRunStep 的 side_inputs 参数将引用数据传递给脚本。 作为 side_inputs 提供的所有数据集将装载到每个工作器节点上。 用户可以通过传递参数获取装载的位置。

构造一个包含参考数据的[数据集](/python/api/azureml-core/azureml.core.dataset.dataset)，指定本地装载路径并将其注册到工作区。 将其传递到 `ParallelRunStep` 的 `side_inputs` 参数。 此外，还可以在 `arguments` 节中添加其路径，以便轻松访问其已装载的路径：

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

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>如何通过服务主体身份验证使用输入数据集？

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

## <a name="next-steps"></a>后续步骤

* 请参阅这些[展示了 Azure 机器学习管道的 Jupyter 笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)

* 请查看 SDK 参考，获取有关 [azureml-pipeline-steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) 包的帮助。 查看 ParallelRunStep 类的参考[文档](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep)。

* 按照[高级教程](tutorial-pipeline-batch-scoring-classification.md)操作，将管道与 ParallelRunStep 配合使用。 本教程演示如何将另一个文件作为旁路输入。
