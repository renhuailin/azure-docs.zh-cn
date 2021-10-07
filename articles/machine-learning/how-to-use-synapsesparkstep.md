---
title: 在机器学习管道中使用 Apache Spark（预览版）
titleSuffix: Azure Machine Learning
description: 将 Azure Synapse Analytics 工作区链接到 Azure 机器学习管道以使用 Apache Spark 进行数据操作。
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: how-to
ms.custom: synapse-azureml
ms.openlocfilehash: 4139b5e2a05ebf4f6355db761d0458b4f367cbaf
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425157"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>如何在机器学习管道中使用由 Azure Synapse Analytics 提供支持的 Apache Spark（预览版）

本文介绍如何使用 Azure Synapse Analytics 提供支持的 Apache Spark 池作为 Azure 机器学习管道中数据准备步骤的计算目标。 你将了解单个管道如何使用适用于特定步骤的计算资源，例如数据准备或训练。 你将了解 Spark 步骤的数据准备情况，以及如何将数据传递到下一步。 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>先决条件

* 创建用于保存所有管道资源的 [Azure 机器学习工作区](how-to-manage-workspace.md)。

* [配置开发环境](how-to-configure-environment.md)以安装 Azure 机器学习 SDK，或使用已经安装了 SDK 的 [Azure 机器学习计算实例](concept-compute-instance.md)。

* 创建 Azure Synapse Analytics 工作区和 Apache Spark 池（请参阅[快速入门：使用 Synapse Studio 创建无服务器 Apache Spark 池](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)）。 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>链接 Azure 机器学习工作区和 Azure Synapse Analytics 工作区 

在 Azure Synapse Analytics 工作区中创建和管理 Apache Spark 池。 若要将 Apache Spark 池与 Azure 机器学习工作区集成，必须[链接到 Azure Synapse Analytics 工作区](how-to-link-synapse-ml-workspaces.md)。 

将 Azure 机器学习工作区和 Azure Synapse Analytics 工作区链接起来后，你可以通过以下方式连接 Apache Spark 池： 
* [Azure 机器学习工作室](how-to-link-synapse-ml-workspaces.md#attach-a-pool-via-the-studio)
* Python SDK（[如下所述](#attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning)）
* Azure 资源管理器 (ARM) 模板（请参阅此[示例 ARM 模板](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-linkedservice-create/azuredeploy.json)）。 
    * 可使用以下代码，通过命令行来遵循 ARM 模板，添加链接服务，并附加 Apache Spark 池：
    ```azurecli
    az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
    ```

> [!Important]
> 若要成功链接到 Azure Synapse Analytics 工作区，必须在 Azure Synapse Analytics 工作区资源中拥有“所有者”角色。 查看 Azure 门户中的访问权限。
>
> 当你创建系统分配的托管标识 (SAI) 时，链接服务会获取此标识。 必须给此链接服务 SAI 分配 Synapse Studio 中的“Synapse Apache Spark 管理员”角色，使其可以提交 Spark 作业（请参阅[如何在 Synapse Studio 中管理 Synapse RBAC 角色分配](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)）。 
> 
> 还必须向 Azure 机器学习工作区的用户授予进行资源管理的 Azure 门户的“参与者”角色。

## <a name="retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>检索 Azure Synapse Analytics 工作区与 Azure 机器学习工作区之间的链接

可以使用以下代码检索工作区中的链接服务：

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

首先，`Workspace.from_config()` 使用 `config.json` 中的配置访问 Azure 机器学习工作区（请参阅[创建工作区配置文件](how-to-configure-environment.md#workspace)）。 然后，该代码将输出工作区中所有可用的链接服务。 最后，`LinkedService.get()` 检索名为 `'synapselink1'` 的链接服务。 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>附加 Apache spark 池为 Azure 机器学习的计算目标

若要使用 Apache Spark 池为机器学习管道中的步骤提供支持，则必须将它附加为管道步骤的 `ComputeTarget`，如下面的代码所示。

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(
        linked_service = linked_service,
        type="SynapseSpark",
        pool_name="spark01") # This name comes from your Synapse workspace

synapse_compute=ComputeTarget.attach(
        workspace=ws,
        name='link1-spark01',
        attach_configuration=attach_config)

synapse_compute.wait_for_completion()
```

第一步是配置 `SynapseCompute`。 `linked_service` 参数是在上一步中创建或检索的 `LinkedService` 对象。 `type` 参数必须为 `SynapseSpark`。 `SynapseCompute.attach_configuration()` 中的参数 `pool_name` 必须与 Azure Synapse Analytics 工作区中现有池的参数匹配。 有关在 Azure Synapse Analytics 工作区中创建 Apache Spark 池的详细信息，请参阅[快速入门：使用 Synapse Studio 创建无服务器 Apache Spark 池](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)。 类型 `attach_config` 不是 `ComputeTargetAttachConfiguration`。

创建配置后，需要创建一个机器学习 `ComputeTarget`，此操作可通过传入 `Workspace`、`ComputeTargetAttachConfiguration` 和在机器学习工作区内引用计算的名称来完成。 对 `ComputeTarget.attach()` 的调用是异步的，因此在调用完成之前，示例将无法使用。

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>创建一个使用链接 Apache Spark 池的 `SynapseSparkStep`

[Apache Spark 池上的 Spark 作业](https://github.com/azure/machinelearningnotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb)示例笔记定义了一个简单的机器学习管道。 首先，此笔记本定义一个数据准备步骤，该步骤由上一步中定义的 `synapse_compute` 提供支持。 然后，此笔记本定义一个训练步骤，该步骤由最适合用于训练的计算目标提供支持。 该示例笔记本使用 Titanic 生存数据库来演示数据输入和输出;它实际上不会清理数据或生成预测模型。 由于此示例不进行实际的训练，因此训练步骤使用基于 CPU 的廉价计算资源。

数据流通过 `DatasetConsumptionConfig` 对象传递到机器学习管道，这些对象可以容纳表格数据或文件集。 数据通常来自工作区数据存储中 blob 存储中的文件。 下面的代码演示了一些用于创建机器学习管道输入的典型代码：

```python
from azureml.core import Dataset

datastore = ws.get_default_datastore()
file_name = 'Titanic.csv'

titanic_tabular_dataset = Dataset.Tabular.from_delimited_files(path=[(datastore, file_name)])
step1_input1 = titanic_tabular_dataset.as_named_input("tabular_input")

# Example only: it wouldn't make sense to duplicate input data, especially one as tabular and the other as files
titanic_file_dataset = Dataset.File.from_files(path=[(datastore, file_name)])
step1_input2 = titanic_file_dataset.as_named_input("file_input").as_hdfs()
```

上述代码假设文件 `Titanic.csv` 位于 blob 存储中。 此代码演示如何以 `TabularDataset` 和 `FileDataset` 的形式读取文件。 此代码仅用于演示目的，因为重复进行输入或将单个数据源解释为表包含的资源和单个文件都会让人感到困惑。

> [!Important]
> 若要使用 `FileDataset` 作为输入，`azureml-core` 版本必须至少为 `1.20.0`。 下面讨论了如何使用 `Environment` 类确定此文件。

步骤完成后，可以选择使用类似于以下内容的代码来存储输出数据：

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

在这种情况下，数据将存储在名为 `test` 文件的 `datastore` 中，并在机器学习工作区中以 `registered_dataset` 名称的 `Dataset` 形式提供。

除数据外，管道步骤还可能具有每个步骤的 Python 依赖项。 单个 `SynapseSparkStep` 对象还可以指定其精确的 Azure Synapse Apache Spark 配置。 下面的代码演示了这一点，它指定 `azureml-core` 包版本必须至少为 `1.20.0`。 （如前文所述，`azureml-core` 的这一要求需要使用 `FileDataset` 作为输入。）

```python
from azureml.core.environment import Environment
from azureml.pipeline.steps import SynapseSparkStep

env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core>=1.20.0")

step_1 = SynapseSparkStep(name = 'synapse-spark',
                          file = 'dataprep.py',
                          source_directory="./code", 
                          inputs=[step1_input1, step1_input2],
                          outputs=[step1_output],
                          arguments = ["--tabular_input", step1_input1, 
                                       "--file_input", step1_input2,
                                       "--output_dir", step1_output],
                          compute_target = 'link1-spark01',
                          driver_memory = "7g",
                          driver_cores = 4,
                          executor_memory = "7g",
                          executor_cores = 2,
                          num_executors = 1,
                          environment = env)
```

上面的代码指定了 Azure 机器学习管道中的单个步骤。 此步骤的 `environment` 指定了特定 `azureml-core` 版本，并根据需要添加其他 conda 或 pip 依赖项。

`SynapseSparkStep` 将从本地计算机的子目录 `./code` 中进行压缩和上传。 该目录将在计算服务器上重新进行创建，该步骤将从该目录运行 `dataprep.py` 文件。 该步骤的 `inputs` 和 `outputs` 是前面讨论过的 `step1_input1`、`step1_input2` 和 `step1_output` 对象。 若要在 `dataprep.py` 脚本中访问这些值，最简单的方法是将它们与命名的 `arguments` 关联起来。

`SynapseSparkStep` 构造函数的下一组参数控制 Apache Spark。 `compute_target` 是之前附加为计算目标的 `'link1-spark01'`。 其他参数用于指定要使用的内存和核心。

示例笔记本将以下代码用于 `dataprep.py`：

```python
import os
import sys
import azureml.core
from pyspark.sql import SparkSession
from azureml.core import Run, Dataset

print(azureml.core.VERSION)
print(os.environ)

import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--tabular_input")
parser.add_argument("--file_input")
parser.add_argument("--output_dir")
args = parser.parse_args()

# use dataset sdk to read tabular dataset
run_context = Run.get_context()
dataset = Dataset.get_by_id(run_context.experiment.workspace,id=args.tabular_input)
sdf = dataset.to_spark_dataframe()
sdf.show()

# use hdfs path to read file dataset
spark= SparkSession.builder.getOrCreate()
sdf = spark.read.option("header", "true").csv(args.file_input)
sdf.show()

sdf.coalesce(1).write\
.option("header", "true")\
.mode("append")\
.csv(args.output_dir)
```

此“数据准备”脚本不执行任何实际数据转换，但阐释了如何检索数据、将数据转换为 spark 数据帧，以及如何执行一些基本 Apache Spark 操作。 可在 Azure 机器学习工作室中查找输出，方法是打开子运行，选择“输出 + 日志”选项卡，然后打开 `logs/azureml/driver/stdout` 文件，如下图所示。

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="显示子运行 stdout 选项卡的工作室屏幕截图":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>在管道中使用 `SynapseSparkStep`

下面的示例使用在[上一部分](#create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool)中创建的 `SynapseSparkStep` 中的输出。 管道中的其他步骤可能具有自己独特的环境，并在适合当前任务的不同计算资源上运行。 示例笔记本在小型 CPU 群集上运行“训练步骤”：

```python
from azureml.core.compute import AmlCompute

cpu_cluster_name = "cpucluster"

if cpu_cluster_name in ws.compute_targets:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
else:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', max_nodes=1)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    print('Allocating new CPU compute cluster')

cpu_cluster.wait_for_completion(show_output=True)

step2_input = step1_output.as_input("step2_input").as_download()

step_2 = PythonScriptStep(script_name="train.py",
                          arguments=[step2_input],
                          inputs=[step2_input],
                          compute_target=cpu_cluster_name,
                          source_directory="./code",
                          allow_reuse=False)
```

如果需要，上面的代码会创建新的计算资源。 然后，`step1_output` 结果会转换为训练步骤的输入。 `as_download()` 选项表示数据将移动到计算资源上，从而加快访问速度。 如果数据太大以至于无法放入本地计算硬盘驱动器，则可以使用 `as_mount()` 选项通过 FUSE 文件系统流式传输数据。 第二步的 `compute_target` 是 `'cpucluster'`，而不是在数据准备步骤中使用的 `'link1-spark01'` 资源。 此步骤使用简单的程序 `train.py` 而不是在上一步中使用的 `dataprep.py`。 可以在示例笔记本中查看 `train.py` 的详细信息。

定义完所有步骤后，可以创建并运行管道。 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

上面的代码将创建一个管道，其中包含由 Azure Synapse Analytics 提供支持的 Apache Spark 池上的数据准备步骤 (`step_1`) 和训练步骤 (`step_2`)。 Azure 通过检查步骤之间的数据依赖项来计算执行图。 在这种情况下，只有一个简单的依赖项，即 `step2_input` 需要 `step1_output`。

如果需要，对 `pipeline.submit` 的调用会创建一个名为 `synapse-pipeline` 的实验，并在其中异步开始运行。 管道内的各个步骤将作为此主运行的子运行而运行，并可在工作室的“试验”页中进行监视和查看。

## <a name="next-steps"></a>后续步骤

* [发布和跟踪机器学习管道](how-to-deploy-pipelines.md) 
* [监视 Azure 机器学习](monitor-azure-machine-learning.md)
* [在 Python 的 Azure 机器学习管道中使用自动化 ML](how-to-use-automlstep-in-pipelines.md)
