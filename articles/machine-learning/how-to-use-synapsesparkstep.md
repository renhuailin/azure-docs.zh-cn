---
title: '在机器学习管道中使用 Apache Spark (预览版) '
titleSuffix: Azure Machine Learning
description: 将 Synapse 工作区链接到 Azure 机器学习管道，以使用 Spark 进行数据操作。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 02/25/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: a912bc5abcdadf3f8eca46f805c433d3a1058c68
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662128"
---
# <a name="how-to-use-apache-spark-in-your-machine-learning-pipeline-with-azure-synapse-preview"></a>如何使用 Azure Synapse (预览版在机器学习管道中使用 Apache Spark) 

本文介绍如何使用 Synapse 支持的 Apache Spark 池作为 Azure 机器学习管道中的数据准备步骤的计算目标。 你将了解单个管道如何使用适用于特定步骤的计算资源，例如数据准备或定型。 你将了解 Spark 步骤的数据准备情况，以及如何将数据传递到下一步。 

## <a name="prerequisites"></a>先决条件

* 创建用于保存所有管道资源的 [Azure 机器学习工作区](how-to-manage-workspace.md)。

* [配置开发环境](how-to-configure-environment.md)以安装 Azure 机器学习 SDK，或使用已经安装了 SDK 的 [Azure 机器学习计算实例](concept-compute-instance.md)。

* 创建 Synapse 工作区并 Apache Spark 池 (参阅 [快速入门：使用 Synapse Studio 创建无服务器 Apache Spark 池](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)) 。 

## <a name="link-your-machine-learning-workspace-and-synapse-workspace"></a>链接机器学习工作区和 Synapse 工作区 

在 Synapse 工作区中创建和管理 Apache Spark 池。 若要将 Spark 池与 Azure 机器学习工作区集成，你必须链接到 Synapse 工作区。 

可以使用 " **链接服务** " 页通过 AZURE 机器学习 studio UI 附加 Synapse Spark 池。 还可以通过 " **计算** " 页通过 " **附加计算** " 选项来执行此操作。

还可以通过 SDK (附加 Synapse Spark 池) 或通过 ARM 模板详细介绍 (参阅此 [示例 ARM 模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)) 。 

你可以使用命令行来跟随 ARM 模板，添加链接服务，并将 synapse 池附加到以下代码：

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> 若要成功链接到 Synapse 工作区，你必须在 Synapse 工作区资源中具有 "所有者" 角色。 查看 Azure 门户中的访问权限。
> 创建 SAI) 时，链接服务将获取系统分配的标识 (。 必须将此链接服务分配给 Synapse Studio 中的 "Synapse Apache Spark administrator" 角色，使其可以提交 Spark 作业 (请参阅 [如何在 Synapse Studio) 中管理 SYNAPSE RBAC 角色分配](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md) 。 还必须向 "Azure 机器学习" 工作区的用户授予资源管理 Azure 门户的角色 "参与者"。

## <a name="create-or-retrieve-the-link-between-your-synapse-workspace-and-your-azure-machine-learning-workspace"></a>创建或检索 Synapse 工作区与 Azure 机器学习工作区之间的链接

可以通过以下代码检索工作区中的链接服务：

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

首先， `Workspace.from_config()` 使用中的配置访问 Azure 机器学习工作区 `config.json` (参阅 [教程：开发环境中的 Azure 机器学习入门](tutorial-1st-experiment-sdk-setup-local.md)) 。 然后，该代码将打印工作区中所有可用的链接服务。 最后， `LinkedService.get()` 检索名为的链接服务 `'synapselink1'` 。 

## <a name="attach-your-synapse-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>将 Synapse spark 池附加为 Azure 机器学习的计算目标

若要使用你的 Synapse spark 池为你的机器学习管道中的步骤供电，你必须将它附加为 `ComputeTarget` 管道步骤的，如下面的代码所示。

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

第一步是配置 `SynapseCompute` 。 `linked_service`参数是 `LinkedService` 在上一步中创建或检索的对象。 `type`参数必须是 `SynapseSpark` 。 `pool_name`中的参数 `SynapseCompute.attach_configuration()` 必须与 Synapse 工作区中现有池的参数相匹配。 有关在 Synapse 工作区中创建 Apache spark 池的详细信息，请参阅 [快速入门：使用 Synapse Studio 创建无服务器 Apache Spark 池](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)。 类型 `attach_config` 不是 `ComputeTargetAttachConfiguration`。

创建配置后，可以 `ComputeTarget` 通过传入 `Workspace` 、 `ComputeTargetAttachConfiguration` 和名称，并在机器学习工作区内引用计算来创建机器学习。 对的调用 `ComputeTarget.attach()` 是异步的，因此在调用完成之前，示例会被阻止。

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>创建一个 `SynapseSparkStep` 使用链接的 Apache spark 池的。

[Synapse spark 池中](https://github.com/azure/machinelearningnotebooks)的示例笔记本 Spark 作业定义了一个简单的机器学习管道。 首先，笔记本定义由 `synapse_compute` 上一步中定义的所提供的数据准备步骤。 然后，笔记本定义一个由计算目标提供支持的培训步骤，该步骤更适合于培训。 示例笔记本使用 Titanic 生存数据库来演示数据输入和输出;它实际上不会清理数据或生成预测模型。 由于本示例中没有实际的训练，因此定型步骤使用基于 CPU 的廉价计算资源。

数据通过对象传递到机器学习管道 `DatasetConsumptionConfig` ，这些对象可以容纳表格数据或文件集。 数据通常来自工作区的数据存储中的 blob 存储中的文件。 下面的代码演示了一些用于创建机器学习管道输入的典型代码：

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

上面的代码假定该文件 `Titanic.csv` 在 blob 存储中。 此代码演示如何以和的形式读取文件 `TabularDataset` `FileDataset` 。 此代码仅用于演示目的，因为重复输入或将单个数据源解释为表包含的资源，就像文件一样。

> [!Important]
> 若要使用 `FileDataset` 作为输入， `azureml-core` 版本必须至少为 `1.20.0` 。 下面讨论如何使用类指定此 `Environment` 类。

步骤完成后，可以选择使用类似于以下内容的代码来存储输出数据：

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

在这种情况下，数据将存储在名为的 `datastore` 文件中， `test` 并在机器学习工作区中以名称的形式提供 `Dataset` `registered_dataset` 。

除数据外，管道步骤还可能具有每个步骤的 Python 依赖关系。 单个 `SynapseSparkStep` 对象还可以指定其精确的 Synapse 配置。 下面的代码演示了这一点，它指定 `azureml-core` 包版本必须至少为 `1.20.0` 。 如前文所述 (， `azureml-core` 需要使用 `FileDataset` 作为输入。 ) 

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

上面的代码指定了 Azure 机器学习管道中的一个步骤。 此步骤 `environment` 指定特定 `azureml-core` 版本，并根据需要添加其他 conda 或 pip 依赖项。

`SynapseSparkStep`将从本地计算机的子目录中进行压缩和上传 `./code` 。 该目录将在计算服务器上重新创建，该步骤将 `dataprep.py` 从该目录运行该文件。 `inputs` `outputs` 该步骤的和是 `step1_input1` 前面讨论过的、 `step1_input2` 和 `step1_output` 对象。 若要在脚本中访问这些值，最简单的方法 `dataprep.py` 是将它们与命名的关联起来 `arguments` 。

`SynapseSparkStep`构造函数控制 Apache spark 的下一组参数。 `compute_target`是 `'link1-spark01'` 之前附加为计算目标的。 其他参数指定要使用的内存和核心。

示例笔记本对使用以下代码 `dataprep.py` ：

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

此 "数据准备" 脚本不执行任何实际数据转换，但阐释了如何检索数据、将数据转换为 spark 数据帧，以及如何执行一些基本的 spark 操作。 您可以通过打开子运行，选择 " **输出 + 日志** " 选项卡，然后打开该文件，在 Azure 机器学习 Studio 中查找输出 `logs/azureml/driver/stdout` ，如下图所示。

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="显示子运行的 &quot;stdout&quot; 选项卡的 Studio 屏幕截图":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>`SynapseSparkStep`在管道中使用

管道中的其他步骤可能具有自己独特的环境，并在适合手头任务的不同计算资源上运行。 示例笔记本在小型 CPU 群集上运行 "定型步骤"：

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

如果需要，上面的代码会创建新的计算资源。 然后，将 `step1_output` 结果转换为定型步骤的输入。 `as_download()`选项表示数据将移动到计算资源上，从而加快访问速度。 如果数据太大以至于无法放入本地计算硬盘驱动器，则可以使用 `as_mount()` 选项通过保险丝文件系统流式传输数据。 `compute_target`此第二步的是 `'cpucluster'` ，而不是在 `'link1-spark01'` 数据准备步骤中使用的资源。 此步骤使用简单的程序， `train.py` 而不是在 `dataprep.py` 上一步中使用的程序。 可以 `train.py` 在示例笔记本中查看详细信息。

定义完所有步骤后，可以创建并运行管道。 

```
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

上面的代码将创建一个管道，其中包含由 Synapse (提供支持的数据准备步骤 `step_1`) 和 () 的定型步骤 `step_2` 。 Azure 通过检查步骤之间的数据依赖关系来计算执行图。 在这种情况下，只有一个非常简单的依赖项 `step2_input` 需要 `step1_output` 。

`pipeline.submit`如果需要，调用会创建一个名为的实验， `synapse-pipeline` 并在其中异步开始运行。 管道内的各个步骤将作为此主运行的子运行运行，并可在 Studio 的试验页中进行监视和查看。

## <a name="next-steps"></a>后续步骤

* [发布和跟踪机器学习管道](how-to-deploy-pipelines.md) 
* [监视 Azure 机器学习](monitor-azure-machine-learning.md)
* [在 Python 的 Azure 机器学习管道中使用自动化 ML](how-to-use-automlstep-in-pipelines.md)