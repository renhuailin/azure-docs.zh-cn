---
title: 通过运行 Synapse 笔记本转换数据
description: 本文介绍如何创建和开发 Synapse 笔记本活动和 Synapse 管道。
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 05/19/2021
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: d103e52ec4a2ba105c6eb5c756ac450a15d7538e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745028"
---
# <a name="transform-data-by-running-a-synapse-notebook"></a>通过运行 Synapse 笔记本转换数据

[!INCLUDE[appliesto-adf-xxx-md](../data-factory/includes/appliesto-xxx-asa-md.md)]

[Synapse 管道](../data-factory/concepts-pipelines-activities.md)中的 Azure Synapse 笔记本活动在 Azure Synapse 工作区中运行 Synapse 笔记本。 本文基于[数据转换活动](../data-factory/transform-data.md)一文，它概述了数据转换和受支持的转换活动。 

## <a name="create-a-synapse-notebook-activity"></a>创建 Synapse 笔记本活动

可以直接从 Synapse 管道画布或笔记本编辑器创建 Synapse 笔记本活动。 Synapse 笔记本活动在 Synapse 笔记本中选择的 Spark 池上运行。 

### <a name="add-a-synapse-notebook-activity-from-pipeline-canvas"></a>从管道画布添加 Synapse 笔记本活动

将“活动”下的“Synapse 笔记本”拖放到 Synapse 管道画布上 。 在 Synapse 笔记本活动框中选择，并在“设置”中为当前活动配置笔记本内容。 可以从当前工作区中选择现有笔记本或添加一个新笔记本。 

![screenshot-showing-create-notebook-activity](./media/synapse-notebook-activity/create-synapse-notebook-activity.png)

### <a name="add-a-notebook-to-synapse-pipeline"></a>将笔记本添加到 Synapse 管道

选择右上角的“添加到管道”按钮，将笔记本添加到现有管道或创建新管道。

![screenshot-showing-add-notebook-to-pipeline](./media/synapse-notebook-activity/add-to-pipeline.png)

## <a name="passing-parameters"></a>快速参考

### <a name="designate-a-parameters-cell"></a>指定参数单元格

# <a name="classic-notebook"></a>[经典笔记本](#tab/classical)

若要对笔记本进行参数化，请选择省略号 (…) 以访问最右侧的其他单元格操作菜单。 然后选择“切换参数单元格”，将该单元格指定为参数单元格。

[![screenshot-showing-toggle-parameter](./media/synapse-notebook-activity/toggle-parameter-cell.png)](./media/synapse-notebook-activity/toggle-parameter-cell.png#lightbox)

# <a name="preview-notebook"></a>[预览笔记本](#tab/preview)

若要对笔记本进行参数化，选择省略号 (…) 以访问单元格工具栏上的“更多命令”。 然后选择“切换参数单元格”，将该单元格指定为参数单元格。

[![screenshot-showing-azure-notebook-toggle-parameter](./media/synapse-notebook-activity/azure-notebook-toggle-parameter-cell.png)](./media/synapse-notebook-activity/azure-notebook-toggle-parameter-cell.png#lightbox)

---

Azure 数据工厂查找参数单元格，并将这些值作为执行时传入的参数的默认值。 执行引擎将使用输入参数在参数单元格下面添加新的单元格，以覆盖默认值。 如果未指定参数单元格，则插入的单元格将插入笔记本的顶部。


### <a name="assign-parameters-values-from-a-pipeline"></a>从管道分配参数值

使用参数创建笔记本后，可以从具有 Synapse 笔记本活动的管道执行它。 将活动添加到管道画布后，将能够在“设置”选项卡的“基参数”部分设置参数值 。 

[![screenshot-showing-assign-a-parameter](./media/synapse-notebook-activity/assign-parameter.png)](./media/synapse-notebook-activity/assign-parameter.png#lightbox)

分配参数值时，可以使用[管道表达式语言](../data-factory/control-flow-expression-language-functions.md)或[系统变量](../data-factory/control-flow-system-variables.md)。


## <a name="read-synapse-notebook-cell-output-value"></a>读取 Synapse 笔记本单元输出值

可以按照以下步骤在后续活动中读取笔记本单元格输出值：
1. 在 Synapse 笔记本活动中调用 [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook) API，以返回要在活动输出中显示的值，例如：  

    ```python
    mssparkutils.notebook.exit("hello world") 
    ```
    
    保存笔记本内容并重新触发管道，笔记本活动输出将包含可用于步骤 2 中以下活动的 exitValue。 

2.  从笔记本活动输出中读取 exitValue 属性。 下面是一个用于检查从笔记本活动输出中提取的 exitValue 是否等于“hello world”的示例表达式： 

    [![screenshot-showing-read-exit-value](./media/synapse-notebook-activity/synapse-read-exit-value.png)](./media/synapse-notebook-activity/synapse-read-exit-value.png#lightbox)


## <a name="run-another-synapse-notebook"></a>运行另一个 Synapse 笔记本 

可以通过调用 [%run magic](./spark/apache-spark-development-using-notebooks.md#notebook-reference) 或 [mssparkutils 笔记本实用程序](./spark/microsoft-spark-utilities.md#notebook-utilities)，在 Synapse 笔记本活动中引用其他笔记本。 两者都支持嵌套函数调用。 根据你的方案，你应该考虑的这两种方法的主要区别是：

- [%run magic](./spark/apache-spark-development-using-notebooks.md#notebook-reference) 将引用的笔记本中的所有单元格复制到 %run 单元格，并共享变量上下文。 当 notebook1 通过 `%run notebook2` 引用 notebook2，以及 notebook2 调用 [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook) 函数时，将停止 notebook1 中的单元格执行。 如果要“包含”笔记本文件，建议使用 %run magic。
- [mssparkutils 笔记本实用程序](./spark/microsoft-spark-utilities.md#notebook-utilities)将引用的笔记本作为方法或函数进行调用。 变量上下文不共享。 当 notebook1 通过 `mssparkutils.notebook.run("notebook2")` 引用 notebook2，以及 notebook2 调用 [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook) 函数时，将继续 notebook1 中的单元格执行。 如果要“导入”笔记本，建议使用 mssparkutils 笔记本实用程序。

>[!Note]
> 在 Synapse 管道中运行另一个 Synapse 笔记本仅适用于启用了预览的笔记本。


## <a name="see-notebook-activity-run-history"></a>请参阅笔记本活动运行历史记录

转到“监视”选项卡下的“管道运行”，将看到已触发的管道 。 打开包含笔记本活动的管道以查看运行历史记录。 

可以通过单击“打开笔记本”按钮查看最新的笔记本运行快照，包括单元格输入和输出。 
![see-notebook-activity-history](./media/synapse-notebook-activity/input-output-open-notebook.png)


可以通过选择“输入”或“输出”按钮查看笔记本活动输入或输出 。 如果管道因用户错误而失败，可以选择“输出”来检查“结果”字段，以查看详细的用户错误回溯 。

![screenshot-showing-see-output-user-error](./media/synapse-notebook-activity/notebook-output-user-error.png)


## <a name="synapse-notebook-activity-definition"></a>Synapse 笔记本活动定义

下面是 Synapse 笔记本活动的示例 JSON 定义：

```json
{
    "name": "parameter_test",
    "type": "SynapseNotebook",
    "dependsOn": [],
    "policy": {
        "timeout": "7.00:00:00",
        "retry": 0,
        "retryIntervalInSeconds": 30,
        "secureOutput": false,
        "secureInput": false
    },
    "userProperties": [],
    "typeProperties": {
        "notebook": {
            "referenceName": "parameter_test",
            "type": "NotebookReference"
        },
        "parameters": {
            "input": {
                "value": {
                    "value": "@pipeline().parameters.input",
                    "type": "Expression"
                }
            }
        }
    }
}

```


## <a name="synapse-notebook-activity-output"></a>Synapse 笔记本活动输出

下面是 Synapse 笔记本活动输出的示例 JSON：

```json

{
    "status": {
        "Status": 1,
        "Output": {
            "status": <livySessionInfo>
            },
            "result": {
                "runId": "<GUID>",
                "runStatus": "Succeed",
                "message": "Notebook execution is in Succeeded state",
                "lastCheckedOn": "2021-03-23T00:40:10.6033333Z",
                "errors": {
                    "ename": "",
                    "evalue": ""
                },
                "sessionId": 4,
                "sparkpool": "sparkpool",
                "snapshotUrl": "https://myworkspace.dev.azuresynapse.net/notebooksnapshot/{guid}",
                "exitCode": "abc" // return value from user notebook via mssparkutils.notebook.exit("abc")
            }
        },
        "Error": null,
        "ExecutionDetails": {}
    },

    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US 2)",
    "executionDuration": 234,
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    },
    "billingReference": {
        "activityType": "ExternalActivity",
        "billableDuration": [
            {
                "meterType": "AzureIR",
                "duration": 0.06666666666666667,
                "unit": "Hours"
            }
        ]
    }
}

```
