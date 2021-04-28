---
title: Azure 数据工厂中的设置变量活动
description: 了解如何使用“设置变量”活动来设置数据工厂管道中定义的现有变量的值
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/07/2020
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.openlocfilehash: 5110160234131bd105ac860efe818506db48f6b0
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107903913"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Azure 数据工厂中的设置变量活动
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用“设置变量”活动可设置数据工厂管道中定义的 String、Bool 或 Array 类型的现有变量的值。

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 必需
-------- | ----------- | --------
name | 管道中活动的名称 | 是
description | 描述活动用途的文本 | 否
type | 必须设置为“SetVariable” | 是
值 | 变量分配到的字符串文本或表达式对象值 | 是
variableName | 此活动设置的变量的名称 | 是

## <a name="incrementing-a-variable"></a>递增变量

涉及 Azure 数据工厂中的变量的常见情景是将变量用作 until 或 foreach 活动中的迭代器。 在设置变量活动中，无法引用在 `value` 字段中设置的变量。 若要解决此限制，请设置一个临时变量，然后创建第二个设置变量活动。 第二个设置变量活动会将迭代器的值设置为临时变量。 

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


## <a name="next-steps"></a>后续步骤
了解数据工厂支持的相关控制流活动： 

- [追加变量活动](control-flow-append-variable-activity.md)
