---
title: 使用 SQL 池存储过程活动来转换数据
description: 说明如何使用 SQL 池存储过程活动来调用 Azure Synapse Analytics 中的存储过程。
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: pipeline
author: linda33wj
ms.author: jingwang
ms.reviewer: jrasnick
ms.date: 05/13/2021
ms.openlocfilehash: 4c1f12d4e61f254f753f3feb59ca46997f88b751
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257077"
---
# <a name="transform-data-by-using-sql-pool-stored-procedure-activity-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用 SQL 池存储过程活动来转换数据

[!INCLUDE[appliesto-xxx-asa-md](../../data-factory/includes/appliesto-xxx-asa-md.md)]

可以使用[管道](../../data-factory/concepts-pipelines-activities.md)中的数据转换活动将原始数据转换并处理为预测和见解。 本文基于[转换数据](../../data-factory/transform-data.md)一文编写，它概述了数据转换和支持的转换活动。

在 Azure Synapse Analytics 中，可以使用 SQL 池存储过程活动来调用专用 SQL 池中的存储过程。

## <a name="syntax-details"></a>语法详细信息

SQL 池存储过程活动支持以下设置：

| 属性                  | 说明                              | 必需 |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | 活动名称                     | 是      |
| description               | 描述活动用途的文本 | 否       |
| type                      | 对于 SQL 池存储过程活动，活动类型为 SqlPoolStoredProcedure | 是      |
| sqlPool         | 引用当前 Azure Synapse 工作区中的[专用 SQL 池](../sql/overview-architecture.md)。 | 是      |
| storedProcedureName       | 指定要调用的存储过程的名称。 | 是      |
| storedProcedureParameters | 指定存储过程的参数值。 使用 `"param1": { "value": "param1Value","type":"param1Type" }` 传递数据源支持的参数值及其类型。 如果需要为参数传递 null，请使用 `"param1": { "value": null }`（全部小写）。 | 否       |

示例：

```json
{
    "name": "SQLPoolStoredProcedureActivity",
    "description":"Description",
    "type": "SqlPoolStoredProcedure",
    "sqlPool": {
        "referenceName": "DedicatedSQLPool",
        "type": "SqlPoolReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

## <a name="next-steps"></a>后续步骤
 
- [管道和活动](../../data-factory/concepts-pipelines-activities.md)
- [专用 SQL 池](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
