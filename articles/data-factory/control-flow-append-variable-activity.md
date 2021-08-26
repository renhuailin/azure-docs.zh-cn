---
title: Azure 数据工厂中的追加变量活动
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何设置“追加变量”活动以将值添加到数据工厂管道中定义的现有数组变量
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.date: 10/09/2018
ms.openlocfilehash: 4157ad2fc1cf3619b1fa4c55709f8828415f0dca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638837"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Azure 数据工厂中的追加变量活动
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
使用“追加变量”活动将值添加到数据工厂管道中定义的现有数组变量。

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 必需
-------- | ----------- | --------
name | 管道中活动的名称 | 是
description | 描述活动用途的文本 | 否
type | 活动类型为 AppendVariable | 是
value | 用于追加到指定变量的字符串文本或表达式对象值 | 是
variableName | 活动将修改的变量的名称，该变量必须是“Array”类型 | 是

## <a name="next-steps"></a>后续步骤
了解数据工厂支持的相关控制流活动： 

- [设置变量活动](control-flow-set-variable-activity.md)
