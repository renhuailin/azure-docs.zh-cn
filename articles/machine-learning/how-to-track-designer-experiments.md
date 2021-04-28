---
title: 设计器中的日志指标
titleSuffix: Azure Machine Learning
description: 监视 Azure ML 设计器试验。 使用“执行 Python 脚本”模块启用日志记录，并在工作室中查看记录的结果。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 01/11/2021
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: bbf4168e59f04829603008bedea380f76a67264a
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107884563"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>在 Azure 机器学习设计器管道中启用日志记录


本文介绍如何将日志记录代码添加到设计器管道。 此外，还介绍了如何使用 Azure 机器学习工作室 Web 门户查看这些日志。

有关如何使用 SDK 创作体验来记录指标的详细信息，请参阅[监视 Azure ML 试验运行和指标](how-to-log-view-metrics.md)。

## <a name="enable-logging-with-execute-python-script"></a>使用“执行 Python 脚本”启用日志记录

使用“执行 Python 脚本”模块在设计器管道中启用日志记录。 虽然可以使用此工作流记录任何值，但是记录“评估模型”模块中的指标以跟踪各运行期间的模型性能特别有用。

以下示例演示如何使用“评估模型”和“执行 Python 脚本”模块来记录两个训练模型的均方误差。

1. 将“执行 Python 脚本”模块连接到“评估模型”模块的输出 。

    ![将“执行 Python 脚本”模块连接到“评估模型”模块](./media/how-to-log-view-metrics/designer-logging-pipeline.png)

1. 将以下代码粘贴到“执行 Python 脚本”代码编辑器中，以记录训练模型的平均绝对误差。 可以使用类似的模式在设计器中记录任何其他值：

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        parent_run = Run.get_context().parent
        
        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        parent_run.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])
        # Log right output port result of Evaluate Model. The following line should be deleted if you only connect one Score Module to the` left port of Evaluate Model module.
        parent_run.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])

        return dataframe1,
    ```
    
此代码使用 Azure 机器学习 Python SDK 来记录值。 它使用 Run.get_context() 来获取当前运行的上下文。 然后，它会使用 run.parent.log() 方法将值记录到该上下文中。 它使用 `parent` 将值记录到父管道运行，而不是模块运行。

有关如何使用 Python SDK 来记录值的详细信息，请参阅[在 Azure ML 训练运行中启用日志记录](how-to-log-view-metrics.md)。

## <a name="view-logs"></a>查看日志

管道运行完成后，“试验”页中将显示 Mean_Absolute_Error。

1. 导航到“试验”部分。
1. 选择试验。
1. 选择要查看的试验中的运行。
1. 选择“指标”。

    ![在工作室中查看运行指标](./media/how-to-log-view-metrics/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>后续步骤

本教程已介绍如何在设计器中使用日志。 有关后续步骤，请参阅以下相关文章：


* 若要了解如何对设计器管道进行故障排除，请参阅[对 ML 管道进行调试和故障排除](how-to-debug-pipelines.md#azure-machine-learning-designer)。
* 若要了解如何使用 Python SDK 在 SDK 创作体验中记录指标，请参阅[在 Azure ML 训练运行中启用日志记录](how-to-log-view-metrics.md)。
* 了解如何在设计器中使用[执行 Python 脚本](./algorithm-module-reference/execute-python-script.md)。
