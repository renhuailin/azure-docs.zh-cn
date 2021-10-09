---
title: 追加变量活动
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何设置“追加变量”活动以将值添加到数据工厂或 Synapse Analytics 管道中定义的现有数组变量。
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.date: 09/09/2021
ms.openlocfilehash: 9d2a081535f571b139a5cb15cdb85b37e2c3af97
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811701"
---
# <a name="append-variable-activity-in-azure-data-factory-and-synapse-analytics"></a>在 Azure 数据工厂和 Synapse Analytics 中追加变量活动
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
使用“追加变量”活动以将值添加到数据工厂或 Synapse Analytics 管道中定义的现有数组变量。

## <a name="type-properties"></a>Type 属性

属性 | 描述 | 必需
-------- | ----------- | --------
name | 管道中活动的名称 | 是
description | 描述活动用途的文本 | 否
type | 活动类型为 AppendVariable | 是
value | 用于追加到指定变量的字符串文本或表达式对象值 | 是
variableName | 活动将修改的变量的名称，该变量必须是“Array”类型 | 是

## <a name="next-steps"></a>后续步骤
了解相关的控制流活动： 

- [设置变量活动](control-flow-set-variable-activity.md)
