---
title: 将触发器信息传递给管道
description: 了解如何在管道中引用触发器元数据
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 57024d6cb23ab273aa69bf59f4ec436a6f873a05
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193406"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>管道运行中的引用触发器元数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍了触发器元数据（如触发器开始时间）如何在管道运行中使用。

管道有时需要了解并从调用它的触发器中读取元数据。 例如，根据窗口的开始时间和结束时间，翻转窗口触发器运行时，管道将处理不同的数据切片或文件夹。 在 Azure 数据工厂中，使用参数化和 [系统变量](control-flow-system-variables.md) 将元数据从触发器传递到管道。

此模式对 [翻转窗口触发器](how-to-create-tumbling-window-trigger.md)特别有用，其中触发器提供了窗口开始时间和结束时间。

> [!NOTE]
> 不同的触发器类型提供了不同的元数据信息。 有关详细信息，请参阅 [系统变量](control-flow-system-variables.md)

## <a name="data-factory-ui"></a>数据工厂 UI

本部分介绍如何在 Azure 数据工厂用户界面中将元数据信息从 trigger 传递到管道。

1. 前往 **创作画布** 并编辑管道

1. 单击空白画布以显示管道设置。 不要选择任何活动。 可能需要从画布底部提取设置面板，因为它可能已折叠

1. 选择 " **参数** " 部分，然后选择 " **+ 新建** " 以添加参数

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="管道设置的屏幕截图，显示如何在管道中定义参数。":::

1. 通过单击 " **+ 触发器**" 将触发器添加到管道。

1. 创建触发器或将触发器附加到管道，然后单击 **"确定"**

1. 在下面的页中，为每个参数填写 "触发器元数据"。 使用 [系统变量](control-flow-system-variables.md) 中定义的格式来检索触发器信息。 不需要填写所有参数的信息，只需填写将采用触发器元数据值的参数。 例如，在这里，我们将触发器运行开始时间分配给 *parameter_1*。

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="&quot;触发器定义&quot; 页的屏幕截图，显示如何将触发器信息传递给管道参数。":::

1. 若要在管道中使用这些值，请在管道定义中使用参数 _@pipeline () parameterName_，__而不__ 是系统变量。 例如，在本例中，若要读取触发器开始时间，我们将引用 @pipeline () .parameters.parameter_1。

## <a name="json-schema"></a>JSON 架构

若要将触发器信息传递给管道运行，触发器和管道 json 都需要更新为 _parameters_ 部分。

### <a name="pipeline-definition"></a>管道定义

在 " **属性** " 部分下，将 "参数定义添加到 **参数** " 部分

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
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
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>触发器定义

在 " **管道** " 部分，在 " **参数** " 部分中分配参数值。 不需要填写所有参数的信息，只需填写将采用触发器元数据值的参数。

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>在管道中使用触发器信息

若要在管道中使用这些值，请在管道定义中使用参数 _@pipeline () parameterName_，__而不__ 是系统变量。

## <a name="next-steps"></a>后续步骤

有关触发器的详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md#trigger-execution)。
