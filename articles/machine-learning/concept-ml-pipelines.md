---
title: 什么是机器学习管道？
titleSuffix: Azure Machine Learning
description: 了解如何借助机器学习管道生成、优化和管理机器学习工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.custom: devx-track-python
ms.openlocfilehash: caa9d95d57cb8ef6bfe6223a09376614bd254336
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122604029"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>什么是 Azure 机器学习管道？

本文介绍如何借助机器学习管道来生成、优化和管理机器学习工作流。 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>我应该使用哪种 Azure 管道技术？

Azure 云提供了多种类型的管道，每一种都有不同用途。 下表列出了各种不同的管道及其用途：

| 方案 | 主要角色 | Azure 产品/服务 | OSS 产品/服务 | 规范管道 | 优势 | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| 模型业务流程（机器学习） | 数据科学家 | Azure 机器学习管道 | Kubeflow 管道 | 数据 -> 模型 | 分布、缓存、代码优先、重用 | 
| 数据业务流程（数据准备） | 数据工程师 | [Azure 数据工厂管道](../data-factory/concepts-pipelines-activities.md) | Apache Airflow | 数据 -> 数据 | 强类型的移动，以数据为中心的活动 |
| 代码和应用业务流程 (CI/CD) | 应用开发人员/Ops | [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | 代码 + 模型 -> 应用/服务 | 最开放和灵活的活动支持、审批队列、门控相位 | 

## <a name="what-can-machine-learning-pipelines-do"></a>机器学习管道的作用是什么？

Azure 机器学习管道是整个机器学习任务的可独立执行的工作流。 子任务封装为管道中的一系列步骤。 Azure 机器学习管道可以像调用 Python 脚本一样简单，因此几乎可以执行任何操作。 管道应专注于机器学习任务，例如：

+ 数据准备，包括导入、验证、清理、修整、转换、规范化和暂存
+ 训练配置，包括参数化自变量、文件路径和日志记录/报告配置
+ 反复有效地进行训练和验证。 可以通过指定特定的数据子集、不同的硬件计算资源、分布式处理和进度监视来提高有效性
+ 部署，包括版本控制、缩放、预配和访问控制

由于各个步骤相互独立，因此多个数据科学家可以同时处理同一个管道，且不会过度占用计算资源。 由于各个步骤相互分离，因此可以轻松地针对每个步骤使用不同的计算类型/大小。

管道设计好以后，通常会有更多围绕着管道的训练循环进行的优化。 重新运行某个管道时，运行会跳到需要重新运行的步骤（例如，更新后的训练脚本）。 不需要重新运行的步骤则会跳过。 

通过管道，你可以选择使用不同的硬件来执行不同的任务。 Azure 协调会你使用的各种[计算目标](concept-azure-machine-learning-architecture.md)，因此中间数据可以无缝流向下游计算目标。

可以直接在 Azure 门户中或在[工作区登陆页面（预览）](https://ml.azure.com)[跟踪管道试验的指标](./how-to-log-view-metrics.md)。 发布管道之后，可以配置 REST 终结点，这样就能够从任何平台或堆栈重新运行管道。

简而言之，可以通过管道处理机器学习生命周期的所有复杂任务。 其他 Azure 管道技术各有各的优势。 [Azure 数据工厂管道](../data-factory/concepts-pipelines-activities.md)擅长处理数据，[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) 适合用于持续集成和部署。 但如果关注点是机器学习，则 Azure 机器学习管道可能是满足工作流需求的最佳选择。 

### <a name="analyzing-dependencies"></a>分析依赖项

许多编程生态系统都安装了用于协调资源、库或编译依赖项的工具。 通常，这些工具使用文件时间戳来计算依赖项。 文件发生更改时，只更新（下载、重新编译或打包）文件及其依赖项。 Azure 机器学习管道扩展了这个概念。 与传统的生成工具一样，管道会计算各个步骤之间的依赖项，并且只执行必要的重新计算。 

不过，Azure 机器学习管道中的依赖项分析比简单的时间戳更为复杂。 每个步骤都可以在不同的硬件和软件环境中运行。 数据准备可能是一个耗时的过程，但不需要在具有强大 GPU 的硬件上运行，某些步骤可能需要特定于操作系统的软件，因此建议使用[分布式训练](how-to-train-distributed-gpu.md)等等。 

Azure 机器学习可自动协调管道步骤之间的所有依赖项。 此业务流程可能包括启动和关闭 Docker 映像、附加和拆离计算资源，以及以一致且自动的方式在各步骤之间移动数据。

### <a name="coordinating-the-steps-involved"></a>协调涉及的步骤

创建和运行 `Pipeline` 对象时，将执行以下概要步骤：

+ 对于每个步骤，服务将计算以下各项的要求：
    + 硬件计算资源
    + OS 资源（Docker 映像）
    + 软件资源（Conda/virtualenv 依赖项）
    + 数据输入 
+ 该服务确定步骤之间的依赖项，以生成动态执行图
+ 当执行图中的每个节点运行时：
    + 该服务配置必要的硬件和软件环境（可能重用现有资源）
    + 步骤将运行，从而向其包含的 `Experiment` 对象提供日志记录和监视信息
    + 步骤完成后，其输出将准备作为下一步的输入和/或写入存储
    + 最终确定并分离不再需要的资源

![管道步骤](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>使用 Python SDK 生成管道

在 [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/install) 中，管道是 `azureml.pipeline.core` 模块中定义的 Python 对象。 [Pipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29) 对象包含一个或多个 [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep) 对象的有序序列。 `PipelineStep` 类是抽象类，而实际步骤属于子类，如 [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)、[PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep) 或 [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep)。 [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep) 类包含一系列可重用的步骤，这些步骤可在管道之间共享。 `Pipeline` 作为 `Experiment` 的一部分运行。

Azure 机器学习管道与 Azure 机器学习工作区相关联，而管道步骤与该工作区中提供的计算目标相关联。 有关详细信息，请参阅[在 Azure 门户中创建和管理 Azure 机器学习工作区](./how-to-manage-workspace.md)或[什么是 Azure 机器学习中的计算目标？](./concept-compute-target.md)。

### <a name="a-simple-python-pipeline"></a>简单的 Python 管道

此代码片段显示了创建和运行 `Pipeline` 所需的对象和调用：

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))
prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    source_directory="prep_src",
    compute_target=compute_target,
    arguments=["--prepped_data_path", prepped_data_path],
    inputs=[input_dataset.as_named_input('raw_data').as_mount() ]
    )

prepped_data = prepped_data_path.read_delimited_files()

train_step = PythonScriptStep(
    name="train",
    script_name="train.py",
    compute_target=compute_target,
    arguments=["--prepped_data", prepped_data],
    source_directory="train_src"
)
steps = [ dataprep_step, train_step ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

代码片段以常用 Azure 机器学习对象（`Workspace`、`Datastore`、[ComputeTarget](/python/api/azureml-core/azureml.core.computetarget) 和 `Experiment`）开头。 然后，该代码将创建用于保存 `input_data` 和 `prepped_data_path` 的对象。 `input_data` 是 [FileDataset](/python/api/azureml-core/azureml.data.filedataset) 的实例，`prepped_data_path` 是 [OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig) 的实例。 对于 `OutputFileDatasetConfig`，默认行为是将输出复制到路径 `/dataset/{run-id}/{output-name}` 下的 `workspaceblobstore` 数据存储，其中 `run-id` 是运行的 ID，`output-name` 是自动生成的值（如果未由开发人员指定）。

数据准备代码（不显示）将带分隔符文件写入 `prepped_data_path`。 数据准备步骤的这些输出作为 `prepped_data` 传递到训练步骤。 

数组 `steps` 包含两个 `PythonScriptStep`：`dataprep_step` 和 `train_step`。 Azure 机器学习将分析 `prepped_data` 的数据依赖项，并在 `train_step` 之前运行 `dataprep_step`。 

然后，代码将实例化 `Pipeline` 对象本身，并将其传入工作区和步骤数组。 对 `experiment.submit(pipeline)` 的调用开始 Azure ML 管道运行。 在管道完成之前，对 `wait_for_completion()` 的调用会被阻止。 

若要详细了解如何将管道连接到数据，请参阅以下文章：[Azure 机器学习中的数据访问](concept-data.md)和[将数据移入 ML 管道并在管道之间移动的步骤 (Python)](how-to-move-data-in-out-of-pipelines.md)。 

## <a name="building-pipelines-with-the-designer"></a>使用设计器生成管道

偏好可视化设计图面的开发人员可以使用 Azure 机器学习设计器创建管道。 可以从工作区主页上的“设计器”选项使用此工具。  通过设计器可以将步骤拖放到设计图面上。 

在直观地设计管道时，步骤的输入和输出显示为可见。 通过拖放数据连接，可以快速了解和修改管道的数据流。

![Azure 机器学习设计器示例](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>主要优点

为机器学习工作流使用管道的主要优点包括：

|主要优点|说明|
|:-------:|-----------|
|**无人参与&nbsp;运行**|对步骤进行计划，使之以可靠且无人参与的方式并行或按顺序运行。 数据准备和建模可以持续几天或几周，而管道可帮助你在进程运行时将精力集中在其他任务上。 |
|**异类计算**|使用多个管道，这些管道已跨异类的可缩放计算资源和存储位置进行可靠的协调。 通过在不同的计算目标（例如 HDInsight、GPU Data Science VM 和 Databricks）上运行单独的管道步骤，可以有效地利用可用的计算资源。|
|**可重用性**|创建适用于特定方案（例如，重新训练和批量评分）的管道模板。 触发器通过简单的 REST 调用从外部系统发布管道。|
|**跟踪和版本控制**|可以使用管道 SDK 显式对数据源、输入和输出进行命名和版本控制，而不是在循环访问时手动跟踪数据和结果路径。 还可以将脚本和数据分开管理以提高工作效率。|
| **模块化** | 分离关注区域和隔离更改可加快软件开发速度并提高质量。 | 
|**协作**|使用管道，数据科学家能够在机器学习设计过程的所有方面进行协作，同时能够并行处理管道步骤。|

## <a name="next-steps"></a>后续步骤

Azure 机器学习管道是一个功能强大的工具，在早期开发阶段就开始产生价值。 价值随着团队和项目的发展而增加。 本文介绍了如何通过 Azure 机器学习 Python SDK 指定管道，并在 Azure 上进行协调。 你已了解一些简单的源代码和一些可用的 `PipelineStep` 类。 你应该了解何时使用 Azure 机器学习管道以及 Azure 如何运行这些管道。 

+ 了解如何[创建你的第一个管道](./how-to-create-machine-learning-pipelines.md)。

+ 了解如何[对大数据运行批量预测](tutorial-pipeline-batch-scoring-classification.md )。

+ 若要了解[管道核心](/python/api/azureml-pipeline-core/)和[管道步骤](/python/api/azureml-pipeline-steps/)，请参阅 SDK 参考文档。

+ 试用展示了 [Azure 机器学习管道](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)的示例 Jupyter 笔记本。 了解如何[运行笔记本以了解此服务](samples-notebooks.md)。