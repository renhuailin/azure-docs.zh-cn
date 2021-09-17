---
title: 设置变量活动
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用“设置变量”活动来设置在 Azure 数据工厂或 Azure Synapse Analytics 管道中定义的现有变量的值。
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.openlocfilehash: c9c489ab15f7b6a44e4aadeef8cd98b0d935e1ba
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821642"
---
# <a name="set-variable-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>在 Azure 数据工厂和 Azure Synapse Analytics 中的“设置变量”活动
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用“设置变量”活动可设置数据工厂或 Synapse 管道中定义的 String、Bool 或 Array 类型的现有变量的值。

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 必需
-------- | ----------- | --------
name | 管道中活动的名称 | 是
description | 描述活动用途的文本 | 否
type | 必须设置为“SetVariable” | 是
值 | 变量分配到的字符串文本或表达式对象值 | 是
variableName | 此活动设置的变量的名称 | 是

## <a name="incrementing-a-variable"></a>递增变量

涉及变量的常见情景是将变量用作 until 或 foreach 活动中的迭代器。 在设置变量活动中，无法引用在 `value` 字段中设置的变量。 若要解决此限制，请设置一个临时变量，然后创建第二个设置变量活动。 第二个设置变量活动会将迭代器的值设置为临时变量。 

下面是此模式的示例：

![递增变量](media/control-flow-set-variable-activity/increment-variable.png "递增变量")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```

变量目前在管道级别范围内。 这意味着变量不是线程安全的，如果从并行迭代活动（如 foreach 循环）中访问变量，尤其是当值也在该 foreach 活动中被修改时，可能会导致意外的、你不希望其出现的行为。

## <a name="next-steps"></a>后续步骤
详细了解其他相关的控制流活动： 

- [追加变量活动](control-flow-append-variable-activity.md)
