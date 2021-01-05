---
title: 监视和查看 ML 运行日志与指标
titleSuffix: Azure Machine Learning
description: 通过 Jupyter 小组件和 Azure 机器学习工作室监视 ML 试验和查看运行指标。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: ea96e1056e6157cfddbdc2f0b6451ed55a74d1de
ms.sourcegitcommit: 90caa05809d85382c5a50a6804b9a4d8b39ee31e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/23/2020
ms.locfileid: "97756052"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>监视和查看 ML 运行日志与指标

了解如何监视 Azure 机器学习运行和查看其日志。 

运行试验时，将流式传输日志和指标。  此外，还可以添加自己的。  若要了解如何操作，请参阅 [在 AZURE ML 定型运行中启用日志记录](how-to-track-experiments.md)。

日志可帮助你诊断运行的错误和警告。 性能指标（如参数和模型准确性）可帮助你跟踪和监视你的运行。

本文介绍如何使用以下方法查看日志：

> [!div class="checklist"]
> * 在工作室中监视运行
> * 使用 Jupyter Notebook 小组件监视运行
> * 监视自动化机器学习运行
> * 完成时查看输出日志
> * 在工作室中查看输出日志

有关如何管理试验的常规信息，请参阅[启动、监视和取消训练运行](how-to-manage-runs.md)。

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>使用 Jupyter Notebook 小组件监视运行

使用 ScriptRunConfig 方法提交运行时，可使用 [Jupyter 小组件](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py)监视运行的进度。 和运行提交一样，该小组件采用异步方式，并每隔 10-15 秒提供实时更新，直到作业完成。

在等待运行完成的期间查看 Jupyter 小组件。
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Jupyter 笔记本小组件的屏幕截图](./media/how-to-track-experiments/run-details-widget.png)

也可以在工作区中找到指向此画面的链接。

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>监视自动化机器学习运行

对于自动化机器学习运行，若要访问根据以前的运行生成的图表，请将 `<<experiment_name>>` 替换为相应的试验名称：

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![自动化机器学习的 Jupyter Notebook 小组件](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>完成时显示输出

使用 ScriptRunConfig 时，可以使用 ```run.wait_for_completion(show_output = True)``` 在模型定型完成时进行显示。 使用 ```show_output``` 标志可查看详细输出。 有关详细信息，请参阅[如何启用日志记录](how-to-track-experiments.md#scriptrun-logs)中的 ScriptRunConfig 部分。

<a id="queryrunmetrics"></a>

## <a name="query-run-metrics"></a>查询运行指标

可以使用 ```run.get_metrics()``` 查看训练的模型的指标。 例如，可以将此方法示例与上面的示例配合使用，通过查找具有最低均方误差 (mse) 值的模型来确定最佳模型。

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-records-in-the-studio"></a>在工作室中查看运行记录

可以在 [Azure 机器学习工作室](https://ml.azure.com)中浏览已完成的运行记录，包括记录的指标。

导航到“试验”选项卡。若要查看工作区中各个试验的所有运行，请选择“所有运行”选项卡。可应用顶部菜单栏中的“试验”筛选器来深入了解特定试验的运行。

对于各个试验视图，请选择“所有试验”选项卡。在“试验运行”仪表板中，可以看到为每次运行跟踪的指标和日志。 

还可以编辑“运行列表”表，以选择多个运行并显示运行的最新记录值、最小记录值或最大记录值。 自定义自己的图表，以比较多个运行上的已记录指标值和聚合。 

![Azure 机器学习工作室中的运行详细信息](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="format-charts"></a>设置图表格式 

使用日志记录 Api 中的以下方法来影响指标的可视化效果。

|记录的值|示例代码| 门户中的格式|
|----|----|----|
|记录一组数值| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|单变量折线图|
|使用重复使用的相同指标名称记录单个数值（例如在 for 循环中）| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 单变量折线图|
|重复记录包含 2 个数字列的行|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|双变量折线图|
|记录包含 2 个数字列的表|`run.log_table(name='Sine Wave', value=sines)`|双变量折线图|


### <a name="view-log-files-for-a-run"></a>查看运行的日志文件 

日志文件是调试 Azure ML 工作负载的重要资源。 向下钻取到特定运行以查看其日志和输出：  

1. 导航到 " **试验** " 选项卡。
1. 选择特定运行的 runID。
1. 选择页面顶部的 " **输出和日志** "。

:::image type="content" source="media/how-to-monitor-view-training-logs/view-logs.png" alt-text="运行的输出和日志部分的屏幕截图":::

下表显示了你将在此部分中看到的文件夹中的日志文件的内容。

> [!NOTE]
> 每次运行时，不一定会显示每个文件。 例如，仅当生成新映像时才会显示 20_image_build_log * .txt (例如，当你更改环境) 时。

#### <a name="azureml-logs-folder"></a>`azureml-logs` 文件夹

|文件  |说明  |
|---------|---------|
|20_image_build_log.txt     | Docker 映像生成定型环境日志，可选，每次运行一次。 仅适用于更新环境的情况。 否则，AML 将重复使用缓存的映像。 如果成功，则包含相应图像的映像注册表详细信息。         |
|55_azureml-<node_id # C1.txt     | 主机工具的 stdout/stderr 日志，每个节点一个。 将映像提取到计算目标。 请注意，只有在保护计算资源后，才会显示此日志。         |
|65_job_prep-<node_id # C1.txt     |   作业准备脚本的 stdout/stderr 日志，每个节点一个。 下载你的代码以计算目标并数据存储 (如果请求) 。       |
|70_driver_log (_x) .txt      |  AML 控制脚本和客户培训脚本中的 stdout/stderr 日志，每个进程一个。 **这是脚本的标准输出。这是代码的日志 (例如，打印语句) 显示。** 在大多数情况下，你将在此处监视日志。       |
|70_mpi_log.txt     |   MPI framework 日志，可选，每次运行一次。 仅适用于 MPI 运行。   |
|75_job_post-<node_id # C1.txt     |  作业发布脚本的 stdout/stderr 日志，每个节点一个。 发送日志，将计算资源释放回 Azure。        |
|process_info.js      |   显示哪个进程在哪个节点上运行。  |
|process_status.js      | 显示进程状态，即，如果进程未启动、正在运行或已完成。         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` 文件夹

|文件  |说明  |
|---------|---------|
|110_azureml .log      |         |
|job_prep_azureml .log     |   用于作业准备的系统日志        |
|job_release_azureml .log     | 作业发布的系统日志        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` 文件夹

当启用挎斗时，作业准备和作业发布脚本将在挎斗容器中运行。  每个节点都有一个文件夹。 

|文件  |说明  |
|---------|---------|
|start_cms.txt     |  挎斗容器启动时启动的进程的日志       |
|prep_cmd.txt      |   运行 (在运行时输入的 ContextManagers 的日志 `job_prep.py` 将流式传输到 `azureml-logs/65-job_prep`)        |
|release_cmd.txt     |  运行时退出的 ComtextManagers 日志 `job_release.py`        |

#### <a name="other-folders"></a>其他文件夹

对于多个计算群集上的作业培训，每个节点 IP 都有日志。 每个节点的结构与单一节点作业相同。 对于总体执行、stderr 和 stdout 日志，还有一个额外的日志文件夹。

Azure 机器学习在训练期间记录会从各种源（例如，运行训练作业的 AutoML 或 Docker 容器）记录信息。 其中的许多日志没有详细的阐述。 如果遇到问题且联系了 Microsoft 支持部门，他们可以在排除故障时使用这些日志。

## <a name="monitor-a-compute-cluster"></a>监视计算群集

若要在浏览器中监视特定计算目标的运行，请执行以下步骤：

1. 在 [Azure 机器学习工作室](https://ml.azure.com/)中选择自己的工作区，然后在页面左侧选择“计算”。

1. 选择“正在训练群集”，显示用于训练的计算目标列表。 然后选择群集。

    ![选择训练群集](./media/how-to-track-experiments/select-training-compute.png)

1. 选择“运行”。 此时显示使用此群集的运行列表。 若要查看某个特定运行的详细信息，请点击“运行”列中的链接。 若要查看试验的详细信息，请点击“试验”列中的链接。

    ![选择训练群集的运行](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > 由于训练计算目标是共享资源，因此它们可以让多个运行排队或在给定时间处于活动状态。
    > 
    > 一个运行可以包含多个子级运行，所以一个训练作业可能会产生多个条目。

完成的运行将不再显示在此页上。 若要查看已完成运行的信息，请访问工作室的“试验”部分，然后选择试验和运行。 有关详细信息，请参阅[查看已完成运行的指标](#view-the-experiment-in-the-web-portal)部分。


## <a name="next-steps"></a>后续步骤

尝试执行以下后续步骤，了解如何使用 Azure 机器学习：

* 了解如何[在 Azure 机器学习设计器中跟踪试验并启用日志](how-to-track-designer-experiments.md)。

* 查看教程[使用 Azure 机器学习训练图像分类模型](tutorial-train-models-with-aml.md)中的示例，了解如何注册和部署最佳模型。
