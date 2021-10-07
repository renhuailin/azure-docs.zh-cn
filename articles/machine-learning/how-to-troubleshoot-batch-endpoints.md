---
title: 排查批处理终结点问题（预览版）
titleSuffix: Azure Machine Learning
description: 帮助你顺利使用批处理终结点的提示。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: troubleshooting
ms.custom: troubleshooting, devplatv2
ms.reviewer: laobri
ms.author: tracych
author: tracych
ms.date: 05/05/2021
ms.openlocfilehash: ba0dfb7c86250a55de5967bbffafd40823709dce
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425783"
---
# <a name="troubleshooting-batch-endpoints-preview"></a>排查批处理终结点问题（预览版）

了解如何排查和解决在使用[批处理终结点](how-to-use-batch-endpoint.md)（预览版）进行批量评分时可能遇到的常见错误。

 [!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

下表包含在开发和使用批处理终结点期间可能会遇到的常见问题及其解决方法。

| 问题 | 可能的解决方法 |
|--|--|
| 缺少代码配置或环境。 | 如果使用非 MLflow 模型，请确保提供评分脚本和环境定义。 只有 MLflow 模型支持无代码部署。 有关详细信息，请参阅[使用 MLflow 和 Azure 机器学习跟踪 ML 模型](how-to-use-mlflow.md)|
| 无法更新现有批处理终结点的模型、代码、环境和计算。 | 使用新名称创建新的批处理终结点。 目前尚不支持更新现有批处理终结点的这些资产。 |
| 找不到资源。 | 确保在 CLI 命令中使用 `--type batch`。 如果不指定此参数，则会使用默认的 `online` 类型。|
| 不支持的输入数据。 | 批处理终结点接受以下三种形式的输入数据：1) 已注册的数据 2) 云中的数据 3) 本地数据。 请确保使用正确的格式。 有关详细信息，请参阅[使用批处理终结点（预览版）进行批量评分](how-to-use-batch-endpoint.md)|
| 提供的终结点名称已存在或被删除。 | 使用新名称创建新的批处理终结点。 命令 `endpoint delete` 将终结点标记为待删除。 不能重复使用同一个名称在同一区域中创建新的终结点。 |
| 输出已存在。 | 如果你配置自己的输出位置，请确保为每个终结点调用提供一个新输出。 |

##  <a name="scoring-script-requirements"></a>评分脚本要求

如果使用非 MLflow 模型，则需要提供评分脚本。 评分脚本必须包含两个函数：

- `init()`：此函数适用于后续推理的任何成本高昂或常见的准备工作。 例如，使用它将模型加载到全局对象。 此函数将在进程开始时被调用一次。
-  `run(mini_batch)`：将针对每个 `mini_batch` 实例运行此函数。
    -  `mini_batch`：`mini_batch` 值是文件路径列表。
    -  `response`：`run()` 方法应返回 Pandas 数据帧或数组。 这些返回的元素将追加到通用输出文件。 每个返回的输出元素指示输入微型批中的输入元素的一次成功运行。 确保运行结果中包含足够的数据，以便将单个输入映射到运行输出结果。 运行输出将写入输出文件中，但不保证按顺序写入，因此你应使用输出中的某个键将其映射到正确的输入。

:::code language="python" source="~/azureml-examples-cli-preview/cli/endpoints/batch/mnist/code/digit_identification.py" :::

## <a name="understanding-logs-of-a-batch-scoring-job"></a>了解批量评分作业的日志

### <a name="get-logs"></a>获取日志

使用 Azure CLI 或 REST 调用批处理终结点后，批量评分作业将以异步方式运行。 有两个选项可用于获取批量评分作业的日志。

选项 1：将日志流式传输到本地控制台

可运行以下命令将系统生成的日志流式传输到控制台。 只会流式处理 `azureml-logs` 文件夹中的日志。

```bash
az ml job stream -name <job_name>
```

选项 2：在工作室中查看日志 

若要在工作室中获取运行的链接，请运行： 

```azurecli
az ml job show --name <job_name> --query interaction_endpoints.Studio.endpoint -o tsv
```

1. 使用上述命令返回的值在工作室中打开作业。 
1. 选择“batchscoring”
1. 打开“输出 + 日志”选项卡 
1. 选择要查看的日志

### <a name="understand-log-structure"></a>了解日志结构

有两个顶级日志文件夹：`azureml-logs` 和 `logs`。 

文件 `~/azureml-logs/70_driver_log.txt` 包含用于启动评分脚本的控制器中的信息。  

由于批量评分作业的分布性，存在来自多个不同源的日志。 但是，会创建两个合并的文件来提供概要信息： 

- `~/logs/job_progress_overview.txt`：此文件提供有关到目前为止已创建的微型批（也称为任务）数以及已处理的微型批数的概要信息。 当微型批结束时，日志将记录作业的结果。 如果作业失败，它将显示错误消息以及开始进行故障排除的位置。

- `~/logs/sys/master_role.txt`：此文件提供运行中作业的主节点（也称为业务流程协调程序）视图。 此日志提供有关任务创建、进度监视和运行结果的信息。

要简要了解脚本中的错误，请参阅以下文件：

- `~/logs/user/error.txt`：此文件将尝试汇总脚本中的错误。

有关脚本中错误的详细信息，请参阅以下文件：

- `~/logs/user/error/`：此文件包含在加载和运行入口脚本时引发的异常的完整堆栈跟踪。

如需全面了解每个节点如何执行评分脚本，请查看每个节点单独的进程日志。 进程日志位于 `sys/node` 文件夹中，按工作器节点分组：

- `~/logs/sys/node/<ip_address>/<process_name>.txt`：此文件提供有关每个微型批处理在工作器拾取或完成它时的详细信息。 对于每个微型批处理，此文件包括以下内容：

    - 工作进程的 IP 地址和 PID。 
    - 项总数、成功处理的项数和失败的项数。
    - 开始时间、持续时间、处理时间和运行方法时间。

还可以查看每个节点的资源使用情况的定期检查结果。 日志文件和安装程序文件位于以下文件夹中：

- `~/logs/perf`：设置 `--resource_monitor_interval` 以更改检查时间间隔（以秒为单位）。 默认时间间隔为 `600`，约为 10 分钟。 若要停止监视，请将值设置为 `0`。 每个 `<ip_address>` 文件夹包括：

    - `os/`：节点中所有正在运行的进程的相关信息。 一项检查将运行一个操作系统命令，并将结果保存到文件。 在 Linux 上，该命令为 `ps`。
        - `%Y%m%d%H`：子文件夹名称是到精确到小时的时间。
            - `processes_%M`：文件名以检查时间的分钟结束。
    - `node_disk_usage.csv`：节点的详细磁盘使用情况。
    - `node_resource_usage.csv`：节点的资源使用情况概述。
    - `processes_resource_usage.csv`：每个进程的资源使用情况概述。

### <a name="how-to-log-in-scoring-script"></a>如何在评分脚本中记录日志

可以使用 Python 在评分脚本中记录日志。 日志存储在 `logs/user/stdout/<node_id>/processNNN.stdout.txt` 中。 

```python
import argparse
import logging

# Get logging_level
arg_parser = argparse.ArgumentParser(description="Argument parser.")
arg_parser.add_argument("--logging_level", type=str, help="logging level")
args, unknown_args = arg_parser.parse_known_args()
print(args.logging_level)

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.logging_level.upper())
logger.info("Info log statement")
logger.debug("Debug log statement")
```